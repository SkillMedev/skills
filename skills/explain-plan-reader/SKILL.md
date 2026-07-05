---
name: EXPLAIN Plan Reader
description: Read a captured EXPLAIN ANALYZE plan, name the single bottleneck node, and prescribe a targeted fix for it. Use when a specific query is slow and you have (or can capture) its execution plan from Postgres or MySQL and need to know WHICH node is burning the time and why.
---
# EXPLAIN Plan Reader

Turn one captured execution plan into a named bottleneck node and a specific fix. A plan is a tree executed bottom-up and inside-out; your job is to find the one node where time or rows explode, not to audit every line.

## Workflow

1. **Demand a real plan first.** Do not diagnose from query text alone. In Postgres, require `EXPLAIN (ANALYZE, BUFFERS, FORMAT TEXT)` - ANALYZE runs the query for real timings, BUFFERS shows cache-vs-disk reads. In MySQL, require `EXPLAIN ANALYZE`. Plain `EXPLAIN` shows estimates only and lies under data skew; if that is all you have, say so and ask for ANALYZE output before prescribing.
2. **Run it twice.** The first run can be cold cache. Trust the warm run's timings unless cold-cache I/O is itself the problem (confirm via BUFFERS: high `read=` vs `hit=`).
3. **Find the bottleneck node.** Postgres reports cumulative `actual time`; a node's own cost is its time minus its children's. Locate the node with the largest delta or the largest `loops` - a real bottleneck typically owns 50% or more of total runtime; if no single node does, the time is spread and the query shape (not one node) is the problem. `(actual rows=R loops=N)` means the node ran N times - multiply R×N for true rows. A Nested Loop with high `loops` driving a Seq Scan is the classic disaster. In MySQL, scan for `type=ALL` (full scan), large `rows` examined, and `Extra: Using filesort` / `Using temporary`.
4. **Confirm with the estimate gap.** Compare estimated vs actual rows on the hot node. A gap of 10x or more (e.g. estimated 1, actual 500000) means the planner is flying blind - that misestimate usually causes the bad node choice upstream and is the root cause, not the node itself. A gap under 2-3x is normal noise; do not chase it.
5. **Diagnose by node type and prescribe one fix:**
   - Seq Scan on a large table filtered to few rows → the predicate is not served by an index. Name the column(s) and predicate shape; check whether the WHERE wraps the column in a function (defeats a plain index). Hand the access pattern to index-advisor for the actual index choice.
   - Bad row estimate → stale or insufficient statistics. Run `ANALYZE the_table`; for skewed columns raise `default_statistics_target` (default 100) to 500-1000 for the affected column and re-ANALYZE.
   - External merge / disk-spilling Sort → raise `work_mem` for the session (the Postgres default is a conservative 4MB; sorts spill the moment they exceed it), or provide pre-sorted input via an index ordering.
   - Hash Join with `Batches > 1` (spilling to disk) → raise `work_mem`.
   - Nested Loop chosen for a large set → almost always a row underestimate upstream; fix the estimate (step 4) and the planner switches to Hash/Merge Join on its own.
   - MySQL `Using filesort` with large `rows` → unindexed ORDER BY; provide an index covering the sort order.
6. **State the verdict in one line:** the node, its share of total time, the root cause, and the single highest-leverage fix.

## Deliverable

Produce a one-page diagnosis containing: the verdict line (bottleneck node, its percentage of total runtime, root cause, single fix), the supporting numbers quoted directly from the plan (actual time, loops, estimated vs actual rows, batches or buffer reads as relevant), the prescribed fix as a runnable statement or setting change, and the instruction to re-capture the plan after applying it.

## Quality bar

- Every diagnosis cites a number FROM THE PLAN (actual time, loops, rows estimated vs actual, batches, buffer reads). No folklore fixes.
- Exactly one bottleneck named per pass. If a fix shifts the bottleneck, ask for a fresh plan and repeat.
- Trust `actual time`, never the estimated `cost=` units (they are not milliseconds), once ANALYZE output is available.

## Do NOT

- Do NOT tune nodes consuming under ~5% of total time, or flag a node for its scary name alone - a Seq Scan on a 50-row lookup table is correct and fast.
- Do NOT design the index (column order, partial/covering, included columns). Name the access pattern and defer the index design to index-advisor.
- Do NOT use this when the symptom is many similar queries fired in a loop from application/ORM code (the plan of any single one looks fine) - that is an N+1 pattern; use n-plus-one-hunter instead.
- Do NOT prescribe from plain `EXPLAIN` estimates or from query text; require an ANALYZE-captured plan first.
- Do NOT raise `work_mem` globally as a reflex - it is allocated per sort/hash node per connection, so a global bump can exhaust memory under concurrency; scope it to the session or query first.
