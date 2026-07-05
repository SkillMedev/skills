---
name: Rate Limit Handler
description: Adds retry-with-backoff, Retry-After handling, and client-side throttling so a caller stays under an upstream API's rate limits instead of hammering it. Use when you call a rate-limited or quota-enforced third-party API, see 429 or 503 responses or Retry-After headers, or have a worker fleet that needs to share one upstream's quota. Do NOT use to fail fast when a dependency is down - use circuit-breaker-builder instead; do NOT use to scaffold the HTTP client itself - use api-client-generator instead.
---
# Rate Limit Handler

Stay under an upstream's quota by backing off cooperatively, obeying what the server tells you, and de-synchronizing your fleet - retrying harder is how a transient limit becomes an outage.

## Workflow

1. **Classify the response before reacting.** Retry only on 429, 502, 503, 504, and connection/timeout errors. Never retry 400, 401, 403, 404, or 422 - they will fail identically. Retry only idempotent or idempotency-keyed operations; a bare non-idempotent POST can duplicate effects.
2. **Honor the server first.** If the response carries `Retry-After`, obey it exactly and skip local backoff math - parse both the delta-seconds and the HTTP-date form. Read `X-RateLimit-Remaining` and `X-RateLimit-Reset` proactively and slow down as remaining approaches zero rather than waiting to be rejected - throttling when remaining drops below ~10% of the window's quota is a reasonable trigger.
3. **Back off with full jitter when there is no `Retry-After`.** Compute `delay = base * 2^attempt` with a base of 100-500ms, clamp it to a max (e.g. 30-60s), then sleep a uniform random value between 0 and that clamped cap - not the cap itself. Full jitter is what de-synchronizes many clients retrying the same downed service.
4. **Bound the retries.** Cap both the attempt count (e.g. 5) and the total elapsed budget (e.g. 30-60s, and always below the caller's own timeout) so a request fails fast instead of hanging a caller for minutes. Surface the final failure to the caller; do not swallow it.
5. **Throttle proactively with a token bucket.** Put a client-side limiter in front of the API sized to roughly 80% of the published quota - the headroom absorbs retries, clock skew, and other callers you forgot about - so bursts are smoothed and you stay under the limit by design rather than reacting to 429s. When many workers hit one upstream, back the bucket with a shared store (e.g. Redis) so the aggregate rate - not the per-process rate - stays bounded.
6. **Make it observable.** Emit metrics for retry count, backoff duration, and 429 rate. A rising 429 rate is the early signal to lower concurrency or request a quota increase before users see errors; sustained 429s above ~1% of requests means the token bucket is mis-sized or missing.

## Deliverable

Produce the retry/throttle layer (a wrapper, interceptor, or middleware around the upstream client) plus a short configuration summary stating: the retryable status set, base delay, cap, and jitter mode, the attempt and total-time budgets, the token-bucket rate and burst size relative to the published quota, whether the bucket is per-process or shared across the fleet, and the metrics emitted. The summary is what the next engineer tunes when the upstream changes its limits.

## Quality bar

- No fixed-delay or no-jitter retry loop survives - every backoff path applies jitter.
- `Retry-After` always wins over computed backoff.
- Every retry path is bounded by both attempt count and total time budget.
- Non-idempotent operations are never retried without an idempotency key.
- The 4xx set above is never retried.

## Do NOT

- Do NOT retry on 4xx client errors (except 429) - the request is malformed or unauthorized and will fail again.
- Do NOT use fixed or cap-only backoff - synchronized clients recreate the thundering herd you are trying to avoid.
- Do NOT retry forever or without a total-time budget.
- Do NOT enforce per-process limits when a fleet shares one quota - the aggregate will still blow the limit.
- Do NOT add the full backoff-plus-bucket stack to a single best-effort background call; let it fail and move on, and reserve the machinery for hot paths and high-volume integrations.
