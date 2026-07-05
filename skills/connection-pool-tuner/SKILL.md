---
name: Connection Pool Tuner
description: Diagnoses connection pool exhaustion from captured evidence and sets correct pool sizes and timeouts across app pools and PgBouncer. Use when someone says "we keep hitting database connection limit errors under load", "my API times out waiting for a connection from the pool", "the database shows hundreds of idle connections", or sees "too many clients", checkout/acquire timeouts, "idle in transaction" pile-up, or serverless functions storming the database on a traffic spike. Do NOT use when the problem is an unreliable external service (third-party API, downstream microservice) needing retries or fallbacks — use circuit-breaker-builder instead.
---
# Connection Pool Tuner

Pool exhaustion shows up as checkout timeouts and "too many clients" errors; the fix is almost never "raise max_connections." Diagnose from evidence first, then size to the database.

## Workflow

1. **Capture the evidence before touching any number.** Query `pg_stat_activity` grouped by `state` under load. Record total connections, count per state, and the longest-running query/transaction age. Also record every pool that points at the DB: app replicas times per-replica pool size, plus migrations and any superuser sessions.

2. **Classify the failure from the evidence:**
   - Many `idle in transaction` rows -> code opens a transaction and does slow work (an HTTP call, sleep, batch) before commit. Fix the transaction scope, not the pool.
   - Many `active` long-runners -> slow queries saturating connections. Tune the queries.
   - Checkout/acquire timeouts while DB connections sit mostly idle -> the app pool is too small or connections leak (not returned to the pool). Find the leak before enlarging.
   - Total connections at or above `max_connections` -> the sum of pools is too large; reduce per-replica pool or front with a proxy. Do not raise `max_connections`.

3. **Size the pool to the database, not the app.** Postgres performs best at a small connection count; each backend is a process with real memory cost. Target tens of active connections, not thousands; a common ceiling is roughly `(core_count * 2) + effective_spindle_count`. The sum of all pools must stay under `max_connections` with headroom for migrations and superuser — keep at least 10–15% of `max_connections` free. Twenty replicas with a pool of 20 each is 400 connections — usually far too many.

4. **Set timeouts that fail fast.** Set a pool checkout/acquire timeout (2-5s) so a request errors instead of piling up. Set `idle_in_transaction_session_timeout` (30-60s is a sane starting range for web workloads) to kill stuck transactions. Set `statement_timeout` to cap runaway queries — anchor it to your slowest legitimate query plus headroom, often 30s for OLTP paths. In HikariCP, set `connectionTimeout` and keep `maxLifetime` a minute or two below the DB/proxy idle cutoff so you never hand out a dead socket.

5. **Multiplex with PgBouncer when connection count is the bottleneck.** Run PgBouncer in transaction pooling mode to fold many client connections onto few server connections. Transaction mode forbids session-level features — prepared-statement caches, `SET`, advisory locks held across statements — so configure clients accordingly.

6. **Tame serverless storms.** Serverless/Lambda functions each open their own connection and storm the DB on a spike. Route them through PgBouncer or a serverless pooler/driver (Neon or Supabase pooler, RDS Proxy, Prisma Accelerate) and keep per-function pool size at 1.

7. **Verify under load.** Re-run the `pg_stat_activity` query and confirm the failing state is gone and total connections sit safely under `max_connections`.

## Deliverable

Produce a tuning record alongside the config change: the before/after `pg_stat_activity` readings, the failure classification from step 2 with the evidence that supports it, the full connection budget (every pool times its replicas, plus migration/superuser headroom, summed against `max_connections`), and each changed setting — pool size, checkout timeout, `idle_in_transaction_session_timeout`, `statement_timeout`, `maxLifetime`, PgBouncer mode — with its one-line justification. The record proves the change fixed the diagnosed cause rather than masking it.

## Quality bar

- Every size or timeout change is justified by a captured `pg_stat_activity` reading, not a guess.
- The sum of all pools, plus migration and superuser headroom, stays under `max_connections`.
- Each failure mode is traced to its real cause (transaction scope, slow query, or leak) before any pool is enlarged.

## Do NOT

- Do NOT raise `max_connections` or enlarge the pool as the first move — that masks a leak or a slow transaction and adds per-backend memory pressure.
- Do NOT prescribe numbers without a captured `pg_stat_activity` reading.
- Do NOT budget pools per replica in isolation — the database sees the sum across every replica, sidecar, and cron job, and that sum is what exhausts `max_connections`.
- Do NOT use transaction-mode PgBouncer with clients that rely on session prepared statements, `SET`, or cross-statement advisory locks unless reconfigured.
- Do NOT leave per-function serverless pools larger than 1.
- Do NOT use this skill for external-service flakiness (retries, fallbacks, backoff) — that is circuit-breaker-builder's job.

## Defaults to reach for

Start small: an app pool of 5-10 per replica, a checkout timeout of 2-5s, and a proxy in transaction mode if connection count is the bottleneck. If raising the pool fixes latency, you had a leak or a slow transaction — find it rather than masking it.
