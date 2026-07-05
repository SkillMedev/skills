---
name: Spark & PySpark
description: Writes and tunes PySpark jobs - join strategy and broadcast size limits, shuffle-partition sizing, skew diagnosis and salting, UDF avoidance, caching, and output file layout - with concrete size and skew thresholds. Use when someone asks "why is my Spark job slow", "should I broadcast this join", "one task takes forever while the rest finish", "my job OOMs during a join", or is writing a new PySpark ETL job. Do NOT use for Kafka topic, consumer-group, or streaming-pipeline design - use kafka-pipelines instead; do NOT use for single-machine dataframe work that fits in memory - use pandas-expert instead.
---

# Spark & PySpark

Almost every slow Spark job is slow for one of three reasons: an avoidable shuffle, a skewed key, or rows leaking through Python one at a time. The costly mistake is tuning cluster size before reading the plan - paying for more executors to run the same wasteful DAG faster.

## Operating procedure

### Step 1: Gather inputs

1. The job code and, for a slow job, the Spark UI view of the slow stage (task duration distribution, shuffle read/write sizes) and `df.explain(True)` output.
2. Input data size and format, and the sizes of both sides of every join.
3. Cluster shape: executor count, cores, and memory.
4. Whether the job is new authoring or a tuning pass - for tuning, diagnose from the plan before touching code.

### Step 2: Start from a sane session

```python
from pyspark.sql import SparkSession
from pyspark.sql import functions as F

spark = (
    SparkSession.builder
    .appName("etl")
    .config("spark.sql.shuffle.partitions", "200")
    .config("spark.sql.adaptive.enabled", "true")
    .getOrCreate()
)
```

Enable Adaptive Query Execution (AQE); it coalesces shuffle partitions and switches join strategies at runtime. Then size `spark.sql.shuffle.partitions` deliberately: target 100-200 MB per shuffle partition, so partitions ≈ shuffle data size ÷ 128 MB, and at least 2-3x the total executor cores so no core idles.

### Step 3: Stay in the DataFrame API and out of Python

DataFrame operations go through Catalyst and Tungsten, giving query optimization and off-heap memory. Drop to RDDs only when no DataFrame primitive exists. Python UDFs serialize each row to the Python worker and back, breaking codegen. Order of preference:

1. Built-in `pyspark.sql.functions` (e.g. `F.regexp_extract`, `F.when`).
2. Pandas (vectorized) UDFs when Python is unavoidable.
3. Scalar Python UDFs only as a last resort.

```python
from pyspark.sql.functions import pandas_udf

@pandas_udf("double")
def normalize(s):
    return (s - s.mean()) / s.std()
```

### Step 4: Pick the join strategy by size

- Broadcast the smaller side when it fits in memory. Spark auto-broadcasts below `spark.sql.autoBroadcastJoinThreshold` (default 10 MB); explicitly broadcast tables up to a few hundred MB when executor memory allows, and treat ~1 GB as the practical ceiling - broadcast copies the table to every executor and OOMs the job when misjudged.

```python
from pyspark.sql.functions import broadcast
result = large.join(broadcast(small), "key")
```

- Filter and project columns before joining to shrink the shuffle; every dropped column and row is shuffle bytes saved.
- Two large tables → accept the sort-merge join, but check for skew first (Step 5).

### Step 5: Diagnose and fix skew

Skew symptoms: the stage is done except for one or two tasks; max task duration exceeds ~3-5x the median; one shuffle-read partition is far larger than the rest in the Spark UI. AQE's skew-join handling splits a partition when it is both over 5x the median partition size and over 256 MB (the defaults) - verify it fired in the plan. When AQE cannot help (e.g. skewed aggregation), salt the hot keys: append a random suffix 0..N to the key on the big side, explode the small side across all N suffixes, join, then strip the salt. Choose N ≈ the skew factor (a key 20x the median gets N=20).

### Step 6: Control partitioning and output layout

- `repartition(n, col)` does a full shuffle to balance data; use before wide writes.
- `coalesce(n)` reduces partitions without a shuffle; use to avoid tiny output files.
- Target output files of 128 MB-1 GB; thousands of small files punish every downstream reader.
- Partition output only by low-cardinality columns (date, region - dozens to hundreds of values, never IDs):

```python
df.write.partitionBy("dt").mode("overwrite").parquet("/data/out")
```

### Step 7: Cache only what is reused

Cache only when a DataFrame is used multiple times in the DAG, materialize it, and unpersist when done:

```python
df.cache()
df.count()  # materialize
# ... reuse df ...
df.unpersist()
```

Caching a once-used DataFrame wastes memory and can evict data that mattered.

### Step 8: Verify with the plan

Re-run `df.explain(True)` and confirm the intended strategy appears (`BroadcastHashJoin`, pushed filters, no `Exchange` where one was eliminated). Read columnar formats (Parquet, ORC) so predicate pushdown works, never `collect()` large data to the driver, and prefer `F.col` references over string columns when chaining.

## Worked example: bad vs good join

**Bad** - a 2 TB events table joined to a 200 MB dimension, full width, Python UDF for parsing:

```python
result = (events.join(dims, "store_id")          # sort-merge: shuffles all 2 TB
    .withColumn("region", parse_region_udf("meta"))  # row-at-a-time Python
    .filter(F.col("dt") == "2024-06-01"))            # filter AFTER the join
```

The plan shows an `Exchange` on both sides (2 TB shuffled), and the UDF blocks codegen.

**Good** - filter and project first, broadcast the dimension, use a built-in:

```python
result = (events
    .filter(F.col("dt") == "2024-06-01")              # prune to ~30 GB first
    .select("store_id", "amount", "meta")
    .join(broadcast(dims.select("store_id", "region_code")), "store_id")
    .withColumn("region", F.regexp_extract("meta", r"region=(\w+)", 1)))
```

The plan now shows `BroadcastHashJoin` with no large-side `Exchange`: ~30 GB scanned, zero shuffle of the big table, no Python boundary.

## Deliverable

Produce the revised job code plus a tuning note stating: the join strategy per join with the size evidence, the shuffle-partition setting with its arithmetic, any skew found (max-vs-median task ratio) and the fix applied, caching decisions, output file-size expectations, and the before/after `explain` deltas for a tuning pass.

## Do NOT

- Do NOT broadcast a table you have not sized - a misjudged broadcast OOMs every executor at once.
- Do NOT write a Python UDF before checking `pyspark.sql.functions`; nearly all string, date, and conditional logic has a built-in.
- Do NOT `collect()` or `toPandas()` a large DataFrame; it funnels the cluster's data through one driver.
- Do NOT partition output by a high-cardinality column - millions of directories, one row each.
- Do NOT throw executors at a job whose slow stage is one skewed task; more cores cannot split one partition.
- Do NOT cache by reflex; cache only DataFrames reused downstream, and unpersist them.

## Quality bar

A finished job passes only when: every join names its strategy and the size evidence for it; `spark.sql.shuffle.partitions` is derived from data size, not left at a habit value; the Spark UI shows no task above ~3x the median duration in shuffle stages (or the skew is explained and fixed); no scalar Python UDF survives where a built-in exists; output files land in the 128 MB-1 GB band; and the final `explain` confirms the intended plan.
