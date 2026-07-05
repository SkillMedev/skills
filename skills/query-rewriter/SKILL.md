---
name: SQL Query Rewriter
description: Rewrites a structurally inefficient SQL query into a faster equivalent that returns identical results - correlated subqueries to joins or LATERAL, accidental cross joins to explicit ON predicates, OR-across-columns to UNION, SELECT * to projected columns, and deep OFFSET pagination to keyset. Use when a query is slow because of its shape rather than its indexes - EXPLAIN shows a Cartesian blowup, a per-row subquery, or OFFSET discarding tens of thousands of rows - and the indexes are already in place. Do NOT use when the fix is adding or reordering an index - use index-advisor instead; do NOT use to diagnose an unknown slow query from its plan - use sql-query-optimizer instead.
---

# SQL Query Rewriter

Reshape a slow query into a faster equivalent that returns identical rows. Shape beats tuning when the query does avoidable work - but a rewrite that changes results is worse than a slow query, so equivalence is proven, never assumed. This skill owns the structural rewrite only: index choice belongs to index-advisor, and plan diagnosis belongs to sql-query-optimizer.

## Inputs to collect

1. The query text and its `EXPLAIN (ANALYZE)` output - ANALYZE if the query can be run safely, plain EXPLAIN otherwise (label conclusions from estimates as provisional).
2. Approximate row counts of each table involved, to spot Cartesian blowups.
3. The existing indexes on the touched tables - a rewrite often assumes a supporting index; confirm it exists or hand off to index-advisor.
4. What the caller actually consumes: which columns, whether order matters, whether duplicates are possible.
5. A way to run both versions against the same data for the equivalence diff.

## Workflow

1. **Confirm the shape is the problem.** Read the EXPLAIN. Only rewrite if the plan shows a structural cost driver: actual rows far exceeding any single table (Cartesian product), a subquery re-executed per outer row (`SubPlan` with high loop counts), a Sort/Limit after scanning a huge OFFSET, or an OR that forces a seq scan. If the plan is already optimal, stop - modern Postgres unnests many subqueries itself.
2. **Fix accidental cross joins first.** A FROM listing N tables with a missing or incomplete join predicate multiplies row counts. Write one explicit `JOIN ... ON` per table relationship. If actual rows dwarf every source table, suspect a dropped ON clause.
3. **Decorrelate per-row subqueries.** A scalar subquery in SELECT/WHERE that references the outer row runs once per row. Rewrite it as a JOIN to a grouped derived table; in Postgres use a `LATERAL` join for per-row top-N. Replace `WHERE EXISTS` chains with a single JOIN where multiplicity allows. Keep `EXISTS` over `IN` when the inner set is large - EXISTS short-circuits and `NOT IN` returns wrong results on NULLs.
4. **Split OR across different columns.** `WHERE a = 1 OR b = 2` can use neither column cleanly. Rewrite as `UNION` of two single-predicate queries (`UNION ALL` plus dedup only if duplicates are possible). An OR on the *same* column collapses to `IN (...)`.
5. **Project explicit columns.** Replace `SELECT *` with the exact columns the caller uses. This shrinks I/O and lets an existing covering index answer the query without a heap fetch.
6. **Convert deep OFFSET to keyset pagination.** `OFFSET 100000 LIMIT 20` scans and throws away 100k rows and degrades per page. Rewrite as keyset: `WHERE (created_at, id) < (:last_ts, :last_id) ORDER BY created_at DESC, id DESC LIMIT 20`. Keep OFFSET only for small, bounded sets where a jump-to-page-N UI is required.
7. **Prove equivalence.** Run the original and the rewrite against the same data and diff the full output, including ordering and row count. Re-check EXPLAIN to confirm the structural cost driver is gone.

## Worked example: decorrelating a per-row subquery

**Bad** - latest order date per customer via a correlated scalar subquery:

```sql
SELECT c.id, c.name,
       (SELECT MAX(o.created_at) FROM orders o
        WHERE o.customer_id = c.id) AS last_order
FROM customers c
WHERE c.region = 'EMEA';
```

EXPLAIN shows the cost driver: `SubPlan 1` under the customers scan with `loops=48213` - the orders aggregate re-executes once per EMEA customer, ~48k index probes.

**Good** - aggregate once, join once:

```sql
SELECT c.id, c.name, o.last_order
FROM customers c
LEFT JOIN (
  SELECT customer_id, MAX(created_at) AS last_order
  FROM orders
  GROUP BY customer_id
) o ON o.customer_id = c.id
WHERE c.region = 'EMEA';
```

EXPLAIN now shows one `HashAggregate` over orders and a single `Hash Left Join` - the per-row loop is gone. The LEFT JOIN (not INNER) preserves customers with no orders, matching the original's NULL behavior; the equivalence diff confirms identical rows. If only a few customers are EMEA, the LATERAL variant (`LEFT JOIN LATERAL (SELECT MAX(...) FROM orders o WHERE o.customer_id = c.id) ...`) probes per row but only for the filtered set - pick whichever EXPLAIN proves cheaper on the real data.

## Deliverable

Produce the rewritten query plus a rewrite note containing: the structural cost driver quoted from the original EXPLAIN, the before/after plan comparison showing that driver removed, the equivalence evidence (row-count and full-output diff, including NULL and ordering behavior), and any index the rewrite assumes - with a hand-off to index-advisor if it does not exist.

## Quality bar

- Output is identical to the original on the same data - same rows, same order, same NULL handling.
- Every rewrite removes a cost driver visible in EXPLAIN, not a suspected one.
- The rewrite is justified by the plan, not by folklore; if EXPLAIN does not change, the rewrite is not done.
- Every LEFT-vs-INNER, DISTINCT, and UNION-vs-UNION ALL choice is explicitly justified against multiplicity and NULL behavior.

## Do NOT

- Do NOT recommend creating or reordering an index - that is index-advisor's job. State that a rewrite assumes the supporting index exists, and hand off if it does not.
- Do NOT rewrite a query the planner already optimizes; check EXPLAIN before refactoring.
- Do NOT trade correctness for speed - never ship a rewrite whose output has not been diffed against the original.
- Do NOT use `NOT IN` with a subquery that can yield NULLs; use `NOT EXISTS`.
- Do NOT convert an outer join to an inner join (or EXISTS to JOIN) without checking whether unmatched rows or duplicate multiplicity change the result.
