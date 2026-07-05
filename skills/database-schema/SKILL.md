---
name: Database Schema Designer
description: Designs normalized, constrained, migration-friendly relational schemas - entity modeling, key and type selection, indexes derived from real query patterns, and safe forward/rollback migrations. Use when someone asks "design a schema for X", "should I normalize or denormalize this", "what should my primary key be", "how do I add this column without downtime", or "why is my unique constraint broken with soft deletes". Do NOT use for tuning a slow query on an existing schema - use sql-query-optimizer instead; for picking indexes from a live workload, use index-advisor; for sizing database connection pools, use connection-pool-tuner; for range/hash partitioning decisions, use partition-planner.
---

# Database Schema Designer

A schema is the one part of a system you cannot refactor with a find-and-replace: every shortcut taken at design time becomes a locked-table migration on a hot production database later. This skill produces schemas that stay correct and fast as data grows - invalid states unrepresentable, indexes matched to real queries, and every change shippable without downtime.

## Operating procedure

Follow the steps in order. Constraints come before indexes because constraints define correctness and indexes only define speed; migrations come last because they depend on both.

### Step 1: Gather inputs

Collect these before writing any DDL. If a number is a guess, label it a guess and proceed.

1. Entities and relationships - nouns of the domain and their cardinality (1:1, 1:N, M:N).
2. The top 5-10 queries by expected frequency, with their filter and sort columns. Indexes come from this list, not from intuition.
3. Expected row counts at 1 year and 3 years per table, and read:write ratio per hot table. Default assumption if unknown: 10:1 read-heavy.
4. Multi-tenancy - single tenant, or `tenant_id` on every row?
5. Deletion semantics - hard delete, soft delete, or audit-retained?

### Step 2: Model, normalize, then denormalize only on evidence

Normalize to third normal form (3NF) by default: every non-key column depends on the key, the whole key, and nothing but the key. Denormalize a specific read path only when ALL of these hold:

- The path is measured hot (p95 latency above target, or it dominates query volume), not predicted hot.
- The normalized query joins 3+ tables or aggregates on every read.
- The duplicated data has a read:write ratio of at least 10:1 - copies of frequently-written data rot.
- You name the sync mechanism (trigger, transactional write-through in the app, or materialized view with a refresh cadence) in the same design doc as the copy. A denormalized column with no owner is a data-corruption bug on a timer.

For M:N relationships, use a join table with a composite primary key on both foreign keys.

### Step 3: Choose keys and types

- Primary keys: `BIGINT GENERATED ALWAYS AS IDENTITY`, or UUIDv7 when IDs are generated client-side or must not be guessable. Never UUIDv4 as a clustered/primary key - random inserts fragment the index and wreck insert throughput on large tables.
- Time: `TIMESTAMPTZ`, never naive local time.
- Money: `NUMERIC(19,4)` or integer minor units (`total_cents BIGINT`), never float.
- Fixed value sets: a `CHECK (col IN (...))` constraint, a Postgres enum, or a lookup table. Lookup table when values change at runtime; CHECK/enum when they change with a deploy.
- Use the narrowest correct type; width costs cache and index size on every row forever.

### Step 4: Make invalid states unrepresentable

Add `NOT NULL` by default, foreign keys with an explicit `ON DELETE` behavior (`RESTRICT` unless cascade is a designed feature), and `CHECK` constraints for domain rules (`total_cents >= 0`). Every constraint you skip becomes application code that at least one code path forgets.

### Step 5: Index from the query list

- Index every foreign key you join or filter on.
- Composite index column order: equality-filtered columns first, then range/sort columns. `(user_id, created_at DESC)` serves "this user's recent orders"; the reverse order does not.
- Covering index (`INCLUDE` selected columns) when a top query can be served from the index alone.
- Partial index for skewed predicates: `WHERE status = 'pending'` when pending is under ~5% of rows but queried constantly.
- Every index taxes every write. After launch, drop unused indexes - check `pg_stat_user_indexes` for zero-scan indexes.
- Multi-tenant: `tenant_id` leads every index prefix and appears in every unique key.

### Step 6: Write migrations that ship without downtime

- One logical change per migration, always with a rollback.
- Adding a NOT NULL column to a large table is three steps: add nullable, backfill in batches of 1,000-10,000 rows (larger batches hold locks and bloat WAL), then add the constraint (`NOT VALID` then `VALIDATE CONSTRAINT` on Postgres to avoid a full-table lock).
- `CREATE INDEX CONCURRENTLY` - a plain CREATE INDEX blocks writes for the whole build.
- Never rename-and-reuse a column in the same deploy as the code reading it. Split into two deploys: add new + dual-write, then drop old.
- Soft deletes: `deleted_at TIMESTAMPTZ`, and every unique constraint becomes a partial unique index `WHERE deleted_at IS NULL` - otherwise a deleted row blocks re-creation forever.

## Worked contrast: the schema and the migration it costs you

Bad - three common shortcuts:

```sql
CREATE TABLE orders (
  id         UUID DEFAULT gen_random_uuid() PRIMARY KEY,  -- UUIDv4: fragments the index
  user_email TEXT,                                        -- denormalized copy, no sync owner
  status     TEXT,                                        -- free text: 'Paid', 'paid', 'PAID '
  total      FLOAT                                        -- float money: rounding drift
);
```

Migration bill this schema generates within a year: a data-cleaning pass to canonicalize every status typo before a CHECK constraint can be validated; a backfill joining users to repair stale `user_email` copies; a `FLOAT -> NUMERIC` type change that rewrites the whole table under lock; and insert throughput decay from UUIDv4 fragmentation that no migration fixes without a new PK.

Good - the same table designed forward:

```sql
CREATE TABLE orders (
  id          BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  user_id     BIGINT NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
  status      TEXT NOT NULL CHECK (status IN ('pending','paid','shipped')),
  total_cents BIGINT NOT NULL CHECK (total_cents >= 0),
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_orders_user_created ON orders (user_id, created_at DESC);
```

Future migrations against this table are additive: new columns arrive nullable-then-backfilled, new statuses extend the CHECK, and nothing requires a cleanup pass first.

## Deliverable

Produce a schema design containing: the DDL for every table with constraints inline; the index list annotated with the specific query each index serves; the denormalization decisions (if any) each naming its sync mechanism; and the migration plan for getting from the current state to the target, ordered and reversible.

## Do NOT

- Do not denormalize preemptively "for performance" - you inherit a sync bug without evidence of a speed problem.
- Do not index every column "to be safe"; each index slows every write and most never get scanned.
- Do not use UUIDv4 as a primary key on high-insert tables; use BIGINT identity or UUIDv7.
- Do not store money in floats or timestamps without timezone - both corrupt silently.
- Do not run a plain `CREATE INDEX` or an immediate `SET NOT NULL` on a large production table; both take locks that stall writes.
- Do not forget `deleted_at` in unique constraints when soft-deleting - the second "delete and recreate" fails in production.

## Quality bar

A finished design passes only if:

- Every index maps to a named query from Step 1; zero speculative indexes.
- Every table has a primary key, and every foreign key has an explicit ON DELETE behavior.
- No float money, no naive timestamps, no free-text enums.
- Every denormalized copy names its sync mechanism and owner.
- Every migration in the plan is reversible and lock-safe on the stated table sizes.

## Escalation

For query plans that stay slow on a sound schema, hand off to explain-plan-reader or sql-query-optimizer. When a table's growth projection crosses ~100M rows or needs time-based pruning, bring in partition-planner before it gets there. Gate risky production migrations through migration-safety-checker.
