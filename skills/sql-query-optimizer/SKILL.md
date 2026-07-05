---
name: SQL Query Optimizer
description: Diagnoses slow SQL from the execution plan, names the cost driver, and delivers the rewritten query plus index DDL with the expected plan change and write-side cost stated. The general entry point for slow-query work that routes deep cases to specialist skills. Use when someone asks "why is this query slow", "can you optimize this SQL", "what index do I need", "this endpoint got slow and it's the database", or pastes an EXPLAIN output. Do NOT use for deep index strategy across a whole schema — use index-advisor instead; for pure query-shape rewrites when indexes are already right — use query-rewriter instead; for ORM-driven repeated-query patterns — use n-plus-one-hunter instead; for line-by-line EXPLAIN interpretation training — use explain-plan-reader instead; for table partitioning decisions — use partition-planner instead.
---

# SQL Query Optimizer

Make queries fast by understanding the plan, not by guessing. The costly mistake this skill prevents is the shotgun index: adding indexes on instinct, which slows every write and bloats storage while the actual cost driver — a non-sargable predicate, a mis-ordered composite, an N+1 in the ORM — survives untouched.

## Operating procedure

Diagnosis strictly precedes any fix, because the right fix depends on which cost driver the plan reveals.

### Step 1: gather inputs

Collect (label guesses as guesses):

1. The query text and, if ORM-generated, the ORM code that emits it.
2. `EXPLAIN ANALYZE` output (or `EXPLAIN` if production-unsafe to execute; note that estimates only).
3. Approximate row counts of the tables involved, and existing indexes (`\d table` or equivalent).
4. Current latency, target latency, and how often the query runs. A 200 ms query at 500 QPS matters more than a 5 s nightly report.
5. Write volume on the tables — this prices any new index.

### Step 2: read the plan for cost drivers

Scan for these signals, in rough order of frequency:

- **Sequential scan on a large table** (over ~100k rows) under a selective WHERE — the classic missing-index signature. A seq scan on a small table is fine and often optimal; do not "fix" it.
- **Estimated vs actual rows off by 10x or more** — stale statistics or correlated columns; run ANALYZE (or create extended statistics) before trusting any other conclusion from the plan.
- **Nested loop whose inner side executes thousands of times** (`loops=N` large) over a non-trivial row count — join order or missing join index.
- **Sort spilling to disk** (`Sort Method: external merge`) — the sort exceeds work_mem; fix with an index providing the order, or raise work_mem only if the sort is genuinely necessary.
- **Repeated subquery execution** — a correlated subquery in the plan as a per-row SubPlan.
- **Filter applied after an index scan removes most rows** (`Rows Removed by Filter` large) — the index does not cover the real predicate.

### Step 3: check for N+1 before optimizing the single query

Symptoms: one query is fast in isolation but the endpoint is slow; the DB log shows the same query shape hundreds of times per request with only the ID changing; latency scales with result count. If present, the fix is in the application (batch/JOIN/preload), not in this query — route to n-plus-one-hunter and stop tuning a query that should not run N times.

### Step 4: apply the matching fix

- **Missing index**: B-tree on the columns in WHERE/JOIN/ORDER BY. Composite indexes follow the left-to-right rule — a query must use a leftmost prefix; order columns by the access pattern (equality columns first, then the range or sort column) and selectivity. For a whole-schema indexing strategy, route to index-advisor.
- **Non-sargable predicate**: never wrap the indexed column in a function. Rewrite `WHERE date(created_at) = '...'` as `WHERE created_at >= '...' AND created_at < '...' + 1 day`. Same for `LOWER(email) = ...` (use a functional index or citext) and leading-wildcard LIKE.
- **SELECT \***: project only needed columns so a covering index can satisfy the query without heap fetches.
- **Correlated subquery**: rewrite as a JOIN or a single grouped aggregate. Deep shape rewrites route to query-rewriter.
- **OR across different columns**: rewrite as a UNION of two independently-indexable queries.
- **LIMIT with large OFFSET**: replace with keyset pagination (`WHERE id > last_seen ORDER BY id LIMIT n`) — OFFSET still reads and discards every skipped row.

### Step 5: verify and price it

Re-run `EXPLAIN ANALYZE` after the change. Claim a speedup only with before/after plan evidence. State the write-side cost of any new index: each index adds work to every INSERT/UPDATE on the table — on a write-heavy table (thousands of writes/minute), an index must earn its keep, and 5+ indexes on such a table deserves scrutiny.

## Worked example

Before — 4.2 s on a 6M-row orders table:

```sql
SELECT * FROM orders
WHERE date(created_at) = '2026-06-01' AND status = 'shipped'
ORDER BY total DESC LIMIT 20;
-- Plan: Seq Scan on orders (rows=6,012,340), Filter removes 99.9%,
-- Sort Method: external merge  Disk: 214MB
```

Reasoning: `date(created_at)` defeats any index on created_at (non-sargable), so the planner seq-scans all 6M rows, then sorts the survivors on disk. Fix the predicate to a range, then give the planner one composite index serving the equality filter, the range, and the sort.

After — 8 ms:

```sql
CREATE INDEX idx_orders_status_created_total
  ON orders (status, created_at, total DESC);

SELECT id, customer_id, total, created_at FROM orders
WHERE status = 'shipped'
  AND created_at >= '2026-06-01' AND created_at < '2026-06-02'
ORDER BY total DESC LIMIT 20;
-- Plan: Index Scan using idx_orders_status_created_total (rows=20)
```

Equality column (status) leads, range (created_at) next, sort key last; the projection drops `SELECT *`. Write cost: one more index maintained per order write — acceptable unless orders sustains very high write rates.

## Deliverable

Produce: the rewritten query, the index DDL, a one-or-two-sentence expected plan change, the before/after EXPLAIN evidence (or a note that verification is pending), and the write-side cost statement for each new index.

## Do NOT

- Do not add an index before reading the plan — you may index around the real problem and tax every write for nothing.
- Do not trust a plan whose row estimates are 10x off from actuals; fix statistics first.
- Do not tune a query that runs N times per request when it should run once — that is an application bug (n-plus-one-hunter).
- Do not "optimize" seq scans on small tables or queries that return most of a table; a seq scan is the right plan there.
- Do not claim a speedup without post-change EXPLAIN ANALYZE evidence.

## Quality bar

- The named cost driver is visible in the quoted plan, not asserted.
- The rewrite AND the index DDL are both shown, with composite column order justified.
- Expected plan change is stated in one or two sentences.
- Write-side cost of every new index is noted.
- Verification with EXPLAIN is done or explicitly flagged as the next step.
