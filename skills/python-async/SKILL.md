---
name: Python Async
description: Writes and reviews correct asyncio code — structured concurrency with TaskGroup, gather vs create_task decisions, cancellation and timeout handling, bridging blocking code with to_thread, and bounded parallelism — with good/bad pairs for the pitfalls that silently freeze the event loop. Use when someone asks "why is my async code slow", "gather or create_task", "how do I run these requests concurrently", "my FastAPI endpoint blocks everything", or is converting sync Python to asyncio. Do NOT use for general pandas or data-analysis performance — use pandas-expert instead — and do NOT use for API endpoint design itself — use api-design instead.
---

# Python Async

Asyncio gives one thread the throughput of hundreds of connections, but only if nothing on the event loop ever blocks — a single stray `time.sleep` or `requests.get` freezes every coroutine in the process. The costly mistakes this skill prevents are the silent ones: code that runs correctly but sequentially (awaiting in a loop), tasks that vanish mid-flight (un-referenced fire-and-forget), and a "fast" service whose p99 collapses because one handler does CPU work on the loop.

## Mental model

One event loop runs many coroutines cooperatively on a single thread. `await` is the only place control can switch; between awaits, your code has an exclusive lock on the whole process. Concurrency comes from running tasks, not from awaiting sequentially — `await a(); await b()` is exactly as slow as sync code.

## Operating procedure

### Step 1: Gather inputs

1. Python version — 3.11+ unlocks `TaskGroup` and `asyncio.timeout`; below that, use `gather` and `wait_for`.
2. The workload type per operation: network I/O (async-friendly), disk/blocking-library I/O (needs `to_thread`), or CPU-bound (needs a process pool).
3. Every library on the I/O path, checked for an async API: `httpx`/`aiohttp` not `requests`, `asyncpg` not `psycopg2`, `asyncio.sleep` not `time.sleep`. If unsure whether a call blocks, treat it as blocking.
4. The concurrency target and the downstream limits (DB pool size, API rate limits).

### Step 2: Choose the concurrency primitive

- Known, finite set of operations that should all complete or all fail together: `asyncio.TaskGroup` (3.11+). It cancels siblings on failure and propagates exceptions as a group — the structured default.
- Need the list of results in order, possibly tolerating individual failures: `asyncio.gather(..., return_exceptions=True)`, then check each result for an exception.
- Background work that outlives the current function (consumers, listeners): `asyncio.create_task`, and store the reference — the loop holds tasks weakly, so an unreferenced task can be garbage-collected mid-flight. Keep a set, add a done-callback that logs exceptions and discards the task.
- Decision rule: `gather`/`TaskGroup` when the caller waits for the results here; `create_task` only when the work genuinely detaches from the current scope.

```python
import asyncio

async def main():
    async with asyncio.TaskGroup() as tg:
        t1 = tg.create_task(fetch(url1))
        t2 = tg.create_task(fetch(url2))
    # both done here; any failure cancelled the sibling and raised

asyncio.run(main())
```

### Step 3: Bound the parallelism

Unbounded fan-out is a self-inflicted DDoS on your own database or a partner API.

- `asyncio.Semaphore(n)` around the operation, with n sized to the bottleneck (e.g. DB pool size minus headroom, or the API's rate limit).
- For pipelines, a bounded `asyncio.Queue(maxsize=...)` between producer and consumers — the maxsize is your backpressure.

### Step 4: Keep blocking work off the loop

- Blocking I/O (sync drivers, file reads, DNS in odd libraries): `await asyncio.to_thread(blocking_fn, arg)`.
- CPU-bound work (parsing, crypto, image processing): `ProcessPoolExecutor` via `loop.run_in_executor` — threads don't help CPU work under the GIL.
- Rule of thumb: anything that could hold the loop for more than ~50ms doesn't belong on it.
- Call async from sync only at the top level via `asyncio.run`; never nest event loops.

### Step 5: Handle cancellation and timeouts deliberately

- Every external await gets a timeout: `async with asyncio.timeout(5):` (3.11+) or `asyncio.wait_for`. Untimed awaits are where services hang forever.
- Cancellation raises `CancelledError` at the next await point. Never swallow it — catch, clean up in `try/finally`, re-raise.
- `asyncio.shield` only for genuinely must-finish work (e.g. committing a payment record), and sparingly — shielded work outlives its caller.

### Step 6: Verify under the debug loop

Run tests with `PYTHONASYNCIODEBUG=1` (or `loop.set_debug(True)`): it reports coroutines never awaited and callbacks that held the loop longer than 100ms — the two failure classes that are invisible in normal runs. Use `async with`/`async for` for async context managers and iterators; a bare `with` around an async resource leaks it.

## Worked artifact: the three classic bugs, bad/good

Bad — sequential awaits masquerading as concurrency; blocking call on the loop; lost task:

```python
async def handler(urls):
    results = []
    for u in urls:                      # runs one at a time
        results.append(await fetch(u))
    data = requests.get(META_URL)       # freezes the entire event loop
    asyncio.create_task(audit_log(data))  # unreferenced: may vanish silently
    return results
```

Good:

```python
import asyncio, httpx

_background: set[asyncio.Task] = set()

def spawn(coro):
    t = asyncio.create_task(coro)
    _background.add(t)
    t.add_done_callback(lambda t: (_background.discard(t),
        t.exception() and log.error("bg task failed", exc_info=t.exception())))
    return t

async def handler(urls, client: httpx.AsyncClient):
    sem = asyncio.Semaphore(10)         # bounded fan-out
    async def bounded(u):
        async with sem:
            async with asyncio.timeout(5):
                return await fetch(client, u)
    async with asyncio.TaskGroup() as tg:
        tasks = [tg.create_task(bounded(u)) for u in urls]
    data = await client.get(META_URL)   # async client, not requests
    spawn(audit_log(data))              # referenced, exceptions logged
    return [t.result() for t in tasks]
```

## Deliverable

Produce working async code (or a review) in which every concurrent operation uses the right primitive with a one-line justification, every external await has a timeout, fan-out is bounded with a number tied to the downstream limit, no blocking call runs on the loop, and background tasks are referenced with exception logging.

## Do NOT

- Do not call blocking I/O (`requests`, `time.sleep`, sync DB drivers) inside a coroutine — one call stalls every request in the process.
- Do not await in a loop when the operations are independent; that is sync code with extra syntax.
- Do not fire-and-forget with a bare `create_task` — hold the reference and log its exceptions.
- Do not swallow `CancelledError`; it breaks timeouts and shutdown for everything above you.
- Do not use threads for CPU-bound work — the GIL makes that a no-op; use a process pool.
- Do not create event loops manually or nest `asyncio.run`; one loop, entered once at the top.
- Do not fan out unbounded to a database or API; size a semaphore to the real limit.

## Quality bar

- Every `await` on an external system sits under an explicit timeout.
- `gather` vs `TaskGroup` vs `create_task` choice is justified per call site.
- Grep for `requests.`, `time.sleep`, and sync DB drivers inside `async def` comes back empty.
- Concurrency bounds are numbers with a stated source, not defaults.
- The test suite passes with `PYTHONASYNCIODEBUG=1` and no slow-callback warnings.

## Escalation

For the HTTP API's shape rather than its concurrency, use api-design. For rate-limit handling strategy against third parties, pair with rate-limit-handler. Queue-based cross-service pipelines belong to kafka-pipelines.
