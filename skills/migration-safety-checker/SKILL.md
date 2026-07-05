---
name: Migration Safety Checker
description: Rewrite a schema migration into independently deployable, zero-downtime steps that never take a long-held blocking lock on a live table. Use when you are about to run an ALTER TABLE, CREATE INDEX, add a constraint, rename a column, or change a column type against a production database with live traffic. Do NOT use for greenfield table/column design with no rows yet — use database-schema instead; do NOT use for broad correctness/integrity review of a migration — use review-db instead.
---
# Migration Safety Checker

Turn a migration that is correct in a test DB but takes a production-halting lock into a sequence of small, reversible, zero-downtime steps. The outage is rarely the DDL itself — it is the queue of fast queries that pile up behind a statement waiting for an ACCESS EXCLUSIVE lock.

## Workflow

1. **Identify the lock each statement takes.** In Postgres, ACCESS EXCLUSIVE (blocks reads AND writes) is taken by: changing a column type, adding a non-concurrent index, adding a column with a volatile default on old engines, and most constraint additions via direct ALTER. Flag every statement that takes a heavy lock on a large, write-hot table — as a rule of thumb, treat anything above a few million rows or with sustained write traffic as hot.
2. **Guard the lock acquisition.** Set `lock_timeout` (2–5 seconds is a common production setting) before any DDL so a blocked statement aborts instead of stalling traffic behind it. A long ALTER waiting for its lock blocks every query that arrives after it, including fast SELECTs — that queue is the outage.
3. **Add columns nullable, then backfill, then constrain.** Add the column NULLable with no volatile default (instant on modern Postgres). Backfill in batches of 1,000–10,000 rows (`UPDATE ... WHERE id BETWEEN ...`), committing each batch and pausing briefly between them, to avoid one giant transaction and table bloat. Add NOT NULL via a CHECK constraint with `NOT VALID` then `VALIDATE CONSTRAINT` — a weaker lock than direct `SET NOT NULL`.
4. **Build indexes and constraints without blocking.** Use `CREATE INDEX CONCURRENTLY` (cannot run in a transaction — disable the migration's transaction wrapper, e.g. `disable_ddl_transaction!` in Rails). Add foreign keys and check constraints as `NOT VALID` first (fast, light lock), then `VALIDATE CONSTRAINT` in a separate step (scans without blocking writes). Drop with `DROP INDEX CONCURRENTLY`.
5. **Use expand-contract for renames and type changes.** Never rename or retype a column in one deploy — running code still references the old shape. Expand: add the new column, dual-write from the app, backfill. Migrate reads to the new column. Contract: stop writing the old column, then drop it in a later deploy. Each step is independently deployable and reversible.
6. **Verify the actual lock before shipping.** Confirm the rewritten statement's lock with a lock-impact migration linter (e.g. squawk) or by inspecting `pg_locks` while the statement runs on a staging clone — never assume from the SQL alone.

## Deliverable

Produce the rewritten migration as an ordered list of independently deployable steps, each annotated with the exact statements to run, the lock it takes and why that is safe, the batch size for any backfill, and its rollback — plus the `lock_timeout` setting to apply before each DDL step.

## Quality bar

- Every step is independently deployable and individually reversible.
- No statement holds ACCESS EXCLUSIVE on a large write-hot table for longer than the `lock_timeout`.
- Backfills are batched and committed incrementally; no single unbounded UPDATE.
- The output names the specific lock each statement takes and why each rewrite is safer.

## Do NOT

- Do not ship a bare `ALTER TABLE ... TYPE`, `RENAME COLUMN`, or `SET NOT NULL` against a hot table — expand-contract or NOT VALID/VALIDATE instead.
- Do not run DDL without a `lock_timeout`; an unbounded wait is the failure mode, not the DDL.
- Do not wrap `CREATE INDEX CONCURRENTLY` in a transaction; it will error.
- Do not add expand-contract ceremony to a small table (under roughly 10,000 rows with light write traffic) where a brief ACCESS EXCLUSIVE lock is invisible — match rigor to table size and write traffic.
- Do not leave a failed concurrent index in place; it leaves an INVALID index — drop and retry. Plain `CREATE INDEX` is fine inside a maintenance window.
