---
name: Error Handling
description: Designs typed, observable, recoverable error handling - an explicit taxonomy of retryable vs terminal failures, Result types at boundaries, single-point logging, and retry policies with real backoff numbers. Use when someone asks "should I retry this error", "how do I structure error types", "why are my logs full of duplicate stack traces", "where do I put the try/catch", or is designing failure paths for an API, job, or client. Do NOT use for building the circuit breaker component itself - use circuit-breaker-builder instead; do NOT use for handling 429s against third-party APIs - use rate-limit-handler instead; do NOT use for writing user-facing error copy - use error-message-writer instead.
---

# Error Handling

Most production incidents are not caused by the original failure - they are caused by the handling: a retry storm that turns one slow dependency into a full outage, a swallowed exception that surfaces as silent data corruption three weeks later, or duplicate logging at five layers that buries the root cause. This skill designs error handling as a first-class part of the system: every failure classified, every class given one owner and one response.

## Operating procedure

### Step 1: Gather inputs

- The unit of work being protected (a request, a job, a batch, a UI subtree).
- Every external dependency it touches and which operations are idempotent.
- Where errors currently surface (logs, tracker, user) and who consumes each.
- Latency budget - it caps how much retrying is even allowed.

### Step 2: Build the error taxonomy

Classify every failure the unit can hit into exactly one row. The classification decides the response mechanically - no per-call-site judgment.

| Class | Examples | Retry? | Response |
|---|---|---|---|
| Transient infrastructure | network reset, DNS blip, connection pool timeout | Yes, with backoff | Retry, then degrade or fail with correlation id |
| Server transient | 502/503/504, database failover | Yes, with backoff | Retry; circuit-break the dependency if sustained |
| Rate limiting | 429 | Yes - honor Retry-After | Back off to the server's hint, never your own schedule (pair with rate-limit-handler) |
| Client error | 400, 401, 403, 404, 422, validation failure | Never | Fix the request or surface to the caller; retrying a 4xx just repeats the failure |
| Business rule | insufficient funds, seat already booked | Never | Return as a typed value; this is a domain outcome, not an exception |
| Bug | null deref, type error, assertion failure | Never | Crash the unit of work, log loudly, alert - retrying a bug corrupts state |

Principles behind the table: distinguish expected failures (validation, not-found) from bugs; model expected failures as values and let truly exceptional bugs throw; fail fast and loud in development, degrade gracefully in production.

### Step 3: Type the errors

```ts
type Result<T, E> = { ok: true; value: T } | { ok: false; error: E };

class NotFoundError extends Error {
  readonly code = 'NOT_FOUND';
  readonly retryable = false;
  constructor(readonly resource: string, opts?: { cause?: unknown }) {
    super(`${resource} not found`, opts);
  }
}
```

- Give each error a stable `code` for programmatic handling and i18n, and a `retryable` flag so the taxonomy travels with the error.
- Use a Result type at API boundaries so callers must handle the error path; never swallow with an empty `catch {}`.
- Preserve the cause chain: `new Error(msg, { cause })` - a re-wrapped error without its cause is undebuggable.

### Step 4: Place the boundaries

- Wrap each independent unit (a request, a React subtree, a job) in exactly one boundary that catches, logs, and renders/returns a fallback.
- The boundary owns the recovery decision and never re-throws silently.
- Log once, at the boundary, with full context - not at every layer. Duplicate logs at each layer hide the real cause behind noise. Inner layers add context to the error and re-throw; only the boundary writes the log line.
- Validate input at the edge so the core can assume well-formed data instead of defensively checking everywhere.

### Step 5: Set the retry policy

Retry only rows the taxonomy marks retryable, and only on idempotent operations (non-idempotent writes need an idempotency key first - see idempotency-enforcer).

- Exponential backoff with full jitter: delay = random(0, min(cap, base × 2^attempt)). Sane defaults: base 200ms, cap 20-30s.
- Max 3 attempts for user-facing request paths (the user is waiting); up to 5 for background jobs.
- Total retry time must fit the latency budget: a 3-attempt policy at base 200ms adds up to ~1.4s worst case before jitter - check that against the SLO.
- Jitter is not optional: synchronized retries after a shared failure create a thundering herd that re-kills the recovering dependency.
- Add a circuit breaker in front of any dependency where sustained failure is possible, so a dead dependency sheds load instead of amplifying it (breaker mechanics: circuit-breaker-builder).

### Step 6: Wire observability

- Attach context (request id, user id, key inputs) where the error is captured, not only at the throw site.
- Send to an error tracker grouping by `code` + stack; alert on new error codes and on rate spikes, not on every occurrence.
- Return the user a safe message plus a correlation id; never leak stack traces, SQL, or internal hostnames to clients.
- Make cleanup run on every path - `finally`, defer, context managers - including the error paths.

## Worked artifact: boundary with policy applied

```ts
async function handleRequest(req: Request): Promise<Response> {
  const correlationId = req.headers.get('x-request-id') ?? crypto.randomUUID();
  try {
    const input = parseInput(req);              // 400 on failure - terminal
    const order = await withRetry(              // taxonomy-driven retry
      () => orders.fetch(input.orderId),
      { attempts: 3, baseMs: 200, capMs: 20_000, retryOn: (e) => e.retryable === true },
    );
    return json(200, order);
  } catch (err) {
    logger.error({ err, correlationId, path: req.url }); // logged ONCE, here
    const code = err instanceof AppError ? err.code : 'INTERNAL';
    const status = err instanceof AppError ? err.status : 500;
    return json(status, { code, message: safeMessage(code), correlationId });
  }
}
```

## Deliverable

Produce an error-handling design containing: the filled taxonomy table for this system, the typed error hierarchy with codes and retryable flags, a boundary map (which layer logs, which layers wrap-and-rethrow), the retry policy with concrete numbers checked against the latency budget, and the observability plan (grouping, alert rules, correlation-id flow).

## Do NOT

- Do not retry 4xx/validation/business errors - it repeats the failure and doubles load.
- Do not retry non-idempotent operations without an idempotency key.
- Do not retry without jitter; synchronized backoff is a self-inflicted DDoS.
- Do not log the same error at multiple layers.
- Do not swallow errors in empty catch blocks - silent corruption is the most expensive failure mode there is.
- Do not leak internals to clients; safe message + correlation id, always.
- Do not put dependencies in the fallback path - an error handler that can itself fail (network call, template render) turns one incident into two. Keep it dependency-free and simple.

## Quality bar

- Every failure the unit can hit maps to exactly one taxonomy row; no call site makes an ad-hoc retry decision.
- Every error carries a stable code, a retryable flag, and its cause chain.
- One log line per failure, at the boundary, with correlation id.
- Retry math verified against the latency budget; circuit breaker in front of every breakable dependency.
- Node services install a global unhandled-rejection handler that logs and exits cleanly - an unhandled rejection crashes the process.
- Batch jobs collect per-item errors and continue, reporting a summary, instead of dying on item 3 of 10,000.
