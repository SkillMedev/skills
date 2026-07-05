---
name: Replication Lag Debugger
description: Diagnoses read-replica lag and the stale-read bugs it causes, then applies read-after-write consistency strategies to fix them. Use when someone says "users see stale data right after saving", "my read replica is lagging behind the primary", "data written a second ago is missing from the next request", when replica lag monitoring alerts or grows after a backfill, or when deciding which reads must hit the primary versus a replica.
---
# Replication Lag Debugger

Stop stale-read bugs caused by reads racing ahead of replication. A read replica is eventually consistent: a write committed on the primary is not instantly visible on replicas, so most "the data disappeared after I saved it" reports are reads hitting a replica that hasn't caught up — not data loss.

## Workflow

1. **Confirm it's a lag bug, not data loss.** Re-read the same row from the primary. If it's present on the primary but missing/stale on a replica, it's replication lag. If it's missing on the primary too, this is the wrong skill — it's a write/transaction bug.
2. **Measure the lag with real numbers — never prescribe a fix without this.**
   - Postgres: on the primary capture `pg_current_wal_lsn()`; on each replica read `pg_last_wal_replay_lsn()` and `pg_last_xact_replay_timestamp()`; inspect `pg_stat_replication` for the write/flush/replay LSN gaps.
   - MySQL: read `Seconds_Behind_Source` from `SHOW REPLICA STATUS` (treat as approximate — it stalls at 0 then jumps).
   - Record the actual gap (LSN delta and time) and whether it's steady or spiking. For calibration: healthy same-region replicas typically sit well under 1 second (often tens of milliseconds); sustained lag above 10–30 seconds under normal traffic means replay genuinely cannot keep up, not just a blip.
3. **Split send lag from replay lag.** Compare flush-LSN gap (network/send) against replay-LSN gap (replica busy applying). The cause and fix differ; don't guess.
4. **Find why it lags**, matched to the split above:
   - Single-threaded replay can't keep a write-heavy primary — enable parallel apply (MySQL `replica_parallel_workers`; Postgres has limited parallel recovery).
   - A long-running query on the replica conflicts with WAL replay and pauses it (Postgres `hot_standby_feedback` / `max_standby_streaming_delay` tradeoff).
   - A bulk write, backfill migration, or VACUUM floods the WAL stream — throttle the backfill into smaller batches (1,000–10,000 rows per batch with a pause between batches is a common working range).
   - Cross-region network saturation adds send lag.
5. **Fix the stale read with read-after-write, cheapest first:**
   - Route a user's reads to the primary for a short window after their write (session stickiness keyed by user) — 5–10 seconds comfortably covers normal lag without pinning traffic to the primary for long.
   - Or always read your own writes from the primary, replicas for other users' data.
   - Or use LSN/GTID tracking: capture the write's LSN, then have the read wait until the chosen replica's replay LSN passes it (Postgres poll `pg_last_wal_replay_lsn()`; MySQL `WAIT_FOR_EXECUTED_GTID_SET`).
6. **Make read routing deliberate.** Tag every query strongly-consistent (primary) or staleness-tolerant (replica). Dashboards, analytics, and lists tolerate seconds of lag; a user re-reading the form they just submitted does not. Build routing into the data layer, not ad hoc per call site.

## Deliverable

Produce a lag diagnosis and fix plan containing: the measured lag (LSN delta and seconds, with the send-vs-replay split, steady vs spiking), the identified root cause from step 4, the chosen read-after-write strategy and its scope (which users/queries, what window), the query routing classification (which reads go primary vs replica and why), and the alert thresholds tied to your consistency SLA rather than an arbitrary number.

## Quality bar

- A captured lag measurement (LSN gap and time, with send-vs-replay split) exists before any fix is proposed.
- The fix scopes primary-reads to the read-after-write window — it does not force all reads to the primary.
- Lag alarm thresholds map to your consistency SLA, not an arbitrary number.

## Do NOT

- Do not force every read to the primary to dodge lag — that defeats the replica and overloads the primary.
- Do not alarm on sub-second lag, and do not page on lag spikes that coincide with a known backfill — fix the backfill batching instead.
- Do not prescribe parallel apply, `hot_standby_feedback`, or routing changes from the symptom alone; measure first (step 2).
- Do NOT use when the problem is connection-limit, pool-exhaustion, or "too many connections" errors — use connection-pool-tuner instead.
- Do NOT use when a single query is slow on the replica with no staleness involved — that's query tuning; use query-rewriter or n-plus-one-hunter instead.
