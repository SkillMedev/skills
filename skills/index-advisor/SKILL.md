---
name: Index Advisor
description: Recommends, orders, and prunes indexes for a specific query or table — composite column order, selectivity rules, partial and covering indexes, duplicate/unused cleanup, and write-amplification tradeoffs. Use when a query is slow and EXPLAIN shows a Seq Scan or a sort, before adding a CREATE INDEX, or when auditing a table's index set for bloat or duplicates. Do NOT use to diagnose an unknown slow query from scratch — start with sql-query-optimizer; do NOT use when the query shape itself is the problem (function-wrapped predicates, leading wildcards, correlated subqueries) — use query-rewriter instead; do NOT use when the table is too large and needs partitioning or a time-series strategy — use partition-planner instead.
---

# Index Advisor

An index trades write speed and disk for read speed. Add the fewest indexes that serve real query shapes, order their columns deliberately, and prove every change with a plan. The costly mistake is the speculative index: it fails to help the read (wrong column order, predicate too unselective) while permanently taxing every write. This skill owns index design only — plan diagnosis belongs to sql-query-optimizer, and query restructuring belongs to query-rewriter.

## Inputs to collect

1. The slow query text and its `EXPLAIN (ANALYZE, BUFFERS)` output (Postgres) or `EXPLAIN ANALYZE` / `EXPLAIN FORMAT=JSON` (MySQL). No plan, no recommendation.
2. The table's row count and the existing index list (`\d table` / `SHOW INDEX`).
3. Predicate selectivity: roughly what fraction of rows each WHERE predicate matches. Estimate from `n_distinct` in `pg_stats` or a quick `COUNT(*)` ratio; label estimates as guesses.
4. The write profile: is this table write-hot (high INSERT/UPDATE rate), and which columns get updated?
5. For an audit: `pg_stat_user_indexes` (or the MySQL equivalent) over a representative window.

## Workflow

1. **Capture evidence first.** Get the actual access path from the plan. Note the Seq Scan, expensive Sort, or rows-removed-by-filter that an index would eliminate. Never recommend an index without a plan that shows the problem.
2. **Check selectivity before designing anything.** A B-tree index pays off when the predicate selects roughly under 5–10% of rows; once a query fetches more than ~10–20% of a table, the planner rightly prefers a Seq Scan and the index will sit unused. A column with very low cardinality (boolean, status, enum with a handful of values) is not worth a plain index — that is partial-index territory (step 4) or no index at all.
3. **Order composite columns: equality first, then one range/sort.** Extract the query shape — equality predicates (`=`, `IN`), the one range/sort column (`<`, `>`, `BETWEEN`, `ORDER BY`), and the returned columns. Equality columns lead, then a single range or sort column. `(tenant_id, created_at)` serves `WHERE tenant_id = ? ORDER BY created_at`; reversing it does not. By the leftmost-prefix rule, `(a, b, c)` already serves `(a)` and `(a, b)` — so do not also create `(a)`; it is redundant for lookups. Among equality columns, order for prefix reuse across the workload's query shapes.
4. **Make it partial when one predicate is always present.** If queries always filter the same constant, scope the index: `CREATE INDEX ON orders (created_at) WHERE status = 'pending'` is tiny and hot. Prefer a partial index over a plain index on a low-cardinality column.
5. **Make it covering when the heap fetch dominates.** To get an index-only scan: in Postgres add `INCLUDE (amount, status)`; in MySQL InnoDB the PK is already appended, so add the covered columns to the index key. Confirm with the plan showing `Index Only Scan` (Postgres) or `Using index` (MySQL).
6. **Prune dead weight.** Find unused indexes via `pg_stat_user_indexes` where `idx_scan = 0` over a representative window (long enough to cover monthly jobs). Find duplicates by identical or prefix-redundant column lists and drop the subset. Apply with `CREATE INDEX CONCURRENTLY` / `DROP INDEX CONCURRENTLY` (Postgres) to avoid table locks.
7. **Weigh the write cost before keeping it.** Every secondary index adds roughly one extra write per row change — five indexes means an insert does ~6x the index maintenance of an unindexed table — and bloats the WAL/redo log. Updating an indexed column defeats HOT/in-place updates, turning cheap updates into full row versions. More than ~5 indexes on a write-hot table is a red flag demanding per-index justification; prefer one well-ordered composite over several single-column indexes.
8. **Verify.** Re-run the plan and confirm the planner uses the new index AND the query is actually faster (compare actual times, not costs). If it does not, drop the index.

## Worked example

Query: `SELECT id, total FROM orders WHERE tenant_id = 42 AND status = 'pending' ORDER BY created_at DESC LIMIT 20` on 40M rows.

**Bad:** `CREATE INDEX ON orders (created_at);` — the plan still filters 40M rows' worth of other tenants after the index scan; `tenant_id` (the selective equality, ~0.1% of rows) is not in the index, and `status` alone matches 30% of rows so it cannot carry the query either.

**Good:**

```sql
CREATE INDEX CONCURRENTLY idx_orders_tenant_pending
ON orders (tenant_id, created_at DESC)
INCLUDE (total)
WHERE status = 'pending';
```

Equality column leads, the sort column follows so the `ORDER BY ... LIMIT 20` reads pre-sorted, the always-present `status = 'pending'` becomes a partial predicate keeping the index small, and `INCLUDE (total)` yields an `Index Only Scan`. Verified: plan goes from `Seq Scan + Sort (4.8s)` to `Index Only Scan (2ms)`.

## Deliverable

Produce an index change plan containing: each `CREATE INDEX` / `DROP INDEX` statement (CONCURRENTLY where supported), the plan line each addition fixes and the selectivity estimate justifying it, the evidence for each drop (`idx_scan = 0` window or the index it duplicates), the net index count and write-cost delta for the table, and the before/after `EXPLAIN ANALYZE` comparison.

## Quality bar

- Every recommendation cites the plan line it fixes; no speculative indexes.
- Every recommended index carries a selectivity estimate showing the predicate lands under the ~10% usefulness line.
- Column order is justified by the equality-then-range rule, not guessed.
- Every drop names the evidence (idx_scan = 0, or the index it duplicates).
- Net index count and write cost are stated, not just the additions.

## Do NOT

- Do NOT index tiny tables (under a few hundred rows) — a Seq Scan beats an index lookup.
- Do NOT index a predicate that matches more than ~10–20% of the table and expect the planner to use it.
- Do NOT index a column only ever used inside a function or with a leading `%` wildcard unless you build the matching expression index — otherwise the planner cannot use it.
- Do NOT create a single-column index whose column is already the leftmost prefix of an existing composite.
- Do NOT add an index without re-running the plan to confirm it is used and faster.
- Do NOT solve a too-large table by indexing — that is partition-planner's job; do NOT rewrite the query shape — that is query-rewriter's job.
