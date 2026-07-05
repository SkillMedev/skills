---
name: Circuit Breaker Builder
description: Wraps flaky upstream dependencies in circuit breakers, aggressive timeouts, and per-dependency bulkheads so a slow or failing service degrades gracefully instead of cascading into a full outage. Use when a slow or unavailable upstream is stalling your threads, outbound calls hang with no timeout, one dependency's outage is taking down unrelated traffic, or you are integrating a network call that can realistically be slow or down. Do NOT use when the goal is staying under a provider's request quota or handling 429s - use rate-limit-handler instead; do NOT use to size a database connection pool - use connection-pool-tuner instead.
---
# Circuit Breaker Builder

Protect a service from slow or unavailable upstreams: fail fast, fall back, and isolate the blast radius so one sick dependency can't drain your whole capacity.

## Workflow

1. **Set an explicit timeout on every outbound call.** Configure connect and read timeouts separately; never rely on library defaults (often minutes or infinite). A sane starting point: connect timeout of 1-2 seconds, read timeout anchored to the dependency's observed p99 latency plus modest headroom - not a round number pulled from the air. The total budget must be shorter than your own caller's timeout so you fail before they give up. A call with no timeout is a guaranteed resource leak under load.

2. **Wrap the call in a three-state breaker.** Closed: calls pass through. Open: calls fail instantly without touching the dependency. Half-open: a few probe calls test recovery. Implement these states explicitly per dependency, not a single global breaker.

3. **Trip on a rolling error rate, not a raw count.** Open the breaker when the error rate crosses a threshold over a sliding window with a minimum request volume. Widely-used defaults from Hystrix and resilience4j are a good anchor: trip at ≥50% errors over the window, but only once at least 10-20 requests have been observed in it. A raw failure count flaps at low traffic and trips on noise.

4. **Define a fallback per call site.** While open, return immediately: stale cache for reads, queue-for-later or accept-and-reconcile for writes, a sane default or degraded response where one exists, and a clean fast error only when there is no safe degradation. The point is to stop spending threads, connections, and latency on a call you expect to fail.

5. **Probe and recover from half-open.** After a cooldown - 30-60 seconds is a reasonable starting range - allow a limited number of probe calls (single digits, e.g. 3-5). If they succeed, close the breaker. If any fail, re-open and reset (and back off) the cooldown. Never flood the recovering dependency with full traffic the instant the cooldown expires.

6. **Give each dependency its own bulkhead.** Isolate every upstream behind its own concurrency limit - a dedicated connection pool, thread pool, or concurrency semaphore. A saturated dependency then exhausts only its own bulkhead and leaves capacity for unrelated traffic. Never share one pool across multiple upstreams.

7. **Emit metrics and alert.** Record state transitions, trip counts, fallback rates, and timeout rates per dependency. Alert when a breaker opens. Tune thresholds and cooldowns against this real data, not against guesses.

## Deliverable

Produce a resilience wrapper per protected dependency plus a one-page configuration record: the connect/read timeouts with the latency evidence behind them, the breaker parameters (error-rate threshold, window size, minimum volume, cooldown, probe count), the named fallback for every call site, the bulkhead type and size, and the metrics/alerts emitted. The record is what lets the next engineer tune the breaker from data instead of re-deriving it.

## Quality bar

- Every outbound network call has explicit connect and read timeouts, each shorter than the inherited caller budget.
- Breaker trip logic uses a rolling error rate with a minimum-volume guard, not a bare count.
- Each protected dependency has its own bulkhead; no two upstreams share a pool.
- Every call site behind a breaker has a defined open-state fallback.
- State transitions are observable and alert on open.

## Do NOT

- Do NOT trust default library timeouts, or ship any outbound call without one - a hung call holds a thread and a connection until something else breaks.
- Do NOT retry through an open breaker - that defeats the fail-fast behavior; let the breaker reject.
- Do NOT trip on a raw failure count at low volume; it flaps and masks real signal. Guard with a minimum request volume.
- Do NOT share a connection or thread pool across dependencies; one slow upstream then stalls all of them.
- Do NOT set the read timeout at or above your caller's budget - you will time out after they have already given up, doing the work for nobody.
- Do NOT wrap fast, in-process, or highly reliable local calls - the overhead and false trips outweigh the benefit.
- Do NOT use a breaker to ration calls against a provider quota (that is rate-limit-handler's job) or to decide a DB pool's maximum size (that is connection-pool-tuner's job).
