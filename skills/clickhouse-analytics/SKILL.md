---
name: ClickHouse Analytics
description: Designs ClickHouse schemas and queries for fast analytics — MergeTree engine selection, ORDER BY key design, partitioning, materialized views, and projections. Use when someone asks "why is my ClickHouse query slow", "how should I order my sorting key", "should I use a materialized view or projection", "how do I deduplicate events", or is modeling an event or metrics table for OLAP. Do NOT use for tuning row-store OLTP databases like Postgres or MySQL — use sql-query-optimizer instead; for general relational schema design use database-schema; for reading query plans on traditional databases use explain-plan-reader; for the streaming ingestion side use kafka-pipelines.
---

# ClickHouse Analytics

ClickHouse is fast only when the schema matches the queries: columnar, sorted storage means the ORDER BY key does the work a row-store's indexes would. The expensive mistake is designing the table like Postgres — wrong sorting key, per-row inserts, SELECT * — and concluding ClickHouse is slow when it is scanning every granule because nothing let it skip.

## Operating procedure

Design in this order because the sorting key depends on the queries, the engine on the write semantics, and everything downstream (partitions, MVs, projections) on those two.

### Step 1: gather inputs

Collect before writing any DDL, labeling estimates as estimates:

- The top 5-10 queries by frequency and pain: which columns they filter on, group by, and over what time ranges.
- Ingest shape: rows/second, batch or streaming, and whether the source can send duplicates or updates.
- Retention: how long data lives and whether deletion happens by time.
- Cardinality of candidate key columns (`uniq(col)` on a sample).

### Step 2: choose the engine from write semantics

- MergeTree — the default workhorse for append-only analytical tables.
- ReplacingMergeTree — when the source sends duplicates or row versions; dedup happens during background merges, so it is **eventual**. Read deduped with `FINAL` (costly) or `argMax`-style aggregation.
- SummingMergeTree / AggregatingMergeTree — pre-aggregate on merge; pair with materialized views for rollups.
- ReplicatedMergeTree variants — production replication; assume them for anything serving real traffic.

### Step 3: design ORDER BY — it IS the index

There is no traditional secondary index by default; the sorting key defines the sparse primary index (one mark per `index_granularity` rows, default 8192) and the physical sort. Queries that filter on a **prefix** of the key skip granules; everything else scans.

Rules: put the most frequently filtered columns first, ordered low-cardinality to high — a low-cardinality first column keeps runs long so later columns stay locally sorted and skippable. Keep the key to 3-4 columns; columns after the point where each prefix combination matches fewer than ~8192 rows add cost without adding skipping.

Bad / good contrast for a query load dominated by `WHERE event_type = X AND event_date BETWEEN ...`:

```sql
-- Bad: high-cardinality first; event_type filters skip nothing
ORDER BY (user_id, event_date, event_type)

-- Good: filters match the key prefix, granules skip
CREATE TABLE events (
  event_date Date,
  user_id UInt64,
  event_type LowCardinality(String),
  value Float64
)
ENGINE = MergeTree
PARTITION BY toYYYYMM(event_date)
ORDER BY (event_type, event_date, user_id);
```

### Step 4: partition coarsely

Partition by a coarse time column — `toYYYYMM(date)` is the standard. Partitions buy cheap `DROP PARTITION` retention and whole-partition pruning. Red lines: keep total partitions in the low hundreds (over ~1000 degrades merges and startup), and never partition by anything high-cardinality like user_id — each insert should touch a single-digit number of partitions.

### Step 5: pick types that compress

- `LowCardinality(String)` for columns under roughly 10,000 distinct values — it dictionary-encodes them.
- The smallest integer type that fits; `DateTime` (not strings) for timestamps.
- Avoid `Nullable` unless NULL is semantically required — it adds a mask column to every read.

### Step 6: batch inserts

Insert in batches of 10,000-500,000 rows, at most about one insert per second per table. Per-row inserts create a part per insert and merges fall behind ("too many parts" errors are this). If the producer cannot batch, use async_insert or buffer through kafka-pipelines.

### Step 7: add rollups only after the base table is right

A materialized view is an **insert trigger** — it transforms rows as they arrive into a target table; it never backfills history by itself:

```sql
CREATE MATERIALIZED VIEW daily_mv
TO daily_agg AS
SELECT event_date, event_type, count() AS cnt, sum(value) AS total
FROM events GROUP BY event_date, event_type;
```

Combine with an AggregatingMergeTree target and `-State` / `-Merge` combinators to maintain rollups incrementally. Use a projection instead when you need an alternative sort order or aggregation **inside the same table**, chosen automatically per query:

```sql
ALTER TABLE events ADD PROJECTION by_user
( SELECT user_id, count() GROUP BY user_id );
```

Rule of thumb: projection for a second access path on one table; MV when the rollup feeds different consumers, needs its own retention, or joins.

### Step 8: verify with the query log

- `EXPLAIN indexes = 1` shows granules selected vs total — the skipping ratio is the number that matters.
- `system.query_log` shows parts and rows read per query; a "fast" query reading 90% of the table is a schema problem waiting for data growth.

## Query rules

- Never `SELECT *`; storage is columnar, every column named is I/O paid.
- Filter on sorting-key prefixes to enable granule skipping.
- Use `PREWHERE` for highly selective filters on small columns — it reads the filter column first and only fetches the rest for surviving rows.
- Prefer approximate functions (`uniq`, `uniqHLL12`, `quantile`) when exactness is not required; `uniqExact` on billions of rows is a memory bomb.
- Put the smaller table on the right side of a JOIN; ClickHouse builds the hash table from the right.

## Deliverable

Produce a schema document containing: the DDL (engine, ORDER BY, PARTITION BY, column types) with a one-line justification per choice tied to a named query, the insert batching plan, any MV/projection definitions, and an `EXPLAIN indexes = 1` result for the top queries showing granules skipped.

## Do NOT

- Do not front-load a high-cardinality column in ORDER BY — it destroys skipping for every other filter.
- Do not rely on ReplacingMergeTree for read-time correctness; merges are eventual, so reads must use FINAL or aggregation if duplicates matter.
- Do not partition by day "for granularity" on multi-year data — partition count, not partition size, is what hurts.
- Do not use mutations (`ALTER ... UPDATE/DELETE`) as an operational write path; they rewrite parts and are for rare corrections only.
- Do not add a projection and an MV for the same rollup; pick one and name why.

## Quality bar

Ship only when: every top query filters on an ORDER BY prefix or has a projection that covers it; `EXPLAIN indexes = 1` shows meaningful granule skipping (not a full scan) on the top queries; inserts are batched; total partition count stays bounded in the low hundreds for the data's lifetime; and dedup semantics are stated explicitly (exact at read, or eventual and tolerated).
