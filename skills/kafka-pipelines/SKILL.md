---
name: Kafka Pipelines
description: Designs Kafka streaming pipelines end to end — partition-count sizing math, key choice, consumer-group sizing, offset strategy, delivery semantics, poll tuning, and dead-letter handling — with production configs. Use when someone asks "how many partitions should this topic have", "my consumer group keeps rebalancing", "consumer lag keeps growing", "how do I handle poison messages", or is designing a new topic or event-driven pipeline. Do NOT use for Spark batch or PySpark job tuning — use spark-jobs instead; do NOT use for HTTP webhook ingestion reliability — use webhook-receiver-hardener instead.
---

# Kafka Pipelines

Partition count is the one Kafka decision that is nearly permanent: it caps consumer parallelism forever, and increasing it later rehashes keys and breaks per-key ordering for every keyed consumer. The two costly mistakes this skill prevents are under-partitioning a topic that later gets hot, and committing offsets before processing — which silently loses records on the first crash.

## Operating procedure

### Step 1: Gather inputs

Collect these before choosing any number. Label estimates as guesses and revisit after load testing.

1. Peak produce throughput (MB/s and messages/s) — peak, not average. Default guess: 3x the average.
2. Average message size.
3. Per-record consumer processing time, including downstream calls (DB writes, HTTP). This usually dominates.
4. Ordering requirement: which records must stay ordered relative to each other? That defines the key.
5. Replay window: how far back must consumers be able to re-read? Drives retention.
6. Delivery requirement: at-least-once (default) or exactly-once.
7. Growth expectation over the topic's lifetime.

### Step 2: Size the partition count

Compute both bounds and take the larger:

- Producer bound = peak throughput ÷ per-partition produce throughput. Plan conservatively at 5–10 MB/s per partition; well-tuned clusters do more, but size for the floor.
- Consumer bound = peak throughput ÷ per-partition consume-and-process throughput. With downstream I/O this is often 1–5 MB/s and is usually the binding constraint.

Then multiply by a 2x growth headroom factor and round to a highly divisible number (12, 24, 30, 60) so consumer counts divide evenly. Keep per-broker partition counts in the low thousands (roughly under 4,000 per broker); thousands of near-idle partitions inflate rebalance and recovery time.

### Step 3: Choose the partition key

Records with the same key go to the same partition and preserve order. Pick a key that both balances load and groups what must stay ordered (e.g. `customerId` for per-customer ordering). Check cardinality: a key with fewer distinct values than roughly 10x the partition count risks hot partitions. Never key on a low-cardinality field like country or status.

### Step 4: Set retention and durability

- Retention = replay window + recovery buffer. Set it to cover the longest plausible consumer outage plus reprocessing time; the common default of 7 days is a floor to justify, not a habit.
- Use log compaction for changelog-style topics keyed by entity, where only the latest value per key matters.
- Replication factor at least 3 in production with `min.insync.replicas=2`, so one broker can die without losing acks or availability.

### Step 5: Configure producers

```properties
acks=all
enable.idempotence=true
max.in.flight.requests.per.connection=5
compression.type=zstd
```

Idempotent producers with `acks=all` give exactly-once delivery to a partition and prevent duplicates from retries. Batch with `linger.ms` (5–20 ms is a reasonable throughput/latency trade) and `batch.size` for throughput.

### Step 6: Size the consumer group and manage offsets

- Consumers in a group ≤ partitions; each partition is consumed by exactly one member, so extra consumers sit idle. Start with enough consumers that per-consumer throughput has ~50% headroom over its share of peak load.
- Disable auto-commit and commit only after successful processing for at-least-once semantics:

```java
props.put("enable.auto.commit", "false");
// process records, then:
consumer.commitSync();
```

Commit after processing, not before, or a crash loses every record in the uncommitted batch.

### Step 7: Tune poll settings against the rebalance clock

`max.poll.interval.ms` defaults to 300,000 ms (5 minutes); exceed it between polls and the broker evicts the consumer and rebalances. Budget: `max.poll.records` × worst-case per-record time must stay under ~50% of `max.poll.interval.ms`. At 200 ms per record, the default 500 records needs 100 s — fine; at 2 s per record, cap `max.poll.records` at ~75 or raise the interval.

### Step 8: Add dead-letter handling

When a record cannot be processed after retries, route it to a dead-letter topic rather than blocking the partition:

```
orders -> [process] --fail--> orders.DLQ (with headers: error, original-offset, timestamp)
```

- Apply 3–5 bounded retries with exponential backoff before dead-lettering; infinite retries on a poison message stall the whole partition.
- Add headers capturing the exception, source topic, partition, and offset for debugging.
- Alert when DLQ depth is nonzero and growing, and build tooling to inspect and replay DLQ messages.

### Step 9: Decide the exactly-once question

For read-process-write flows staying within Kafka, use transactions to commit offsets and output atomically, or use Kafka Streams, which manages this. When the sink is external (a database, an API), transactions do not reach it — make processing idempotent instead (idempotency keys, upserts) and accept at-least-once. Idempotent processing is mandatory regardless, because rebalances reprocess records.

### Step 10: Instrument

Monitor consumer lag per partition. Alert on lag that grows monotonically for 10+ minutes, or lag exceeding the equivalent of a few minutes of peak production — rising lag means undersized or stalled consumers. Use a schema registry (Avro or Protobuf) with a compatibility mode enforced so producers cannot break consumers.

## Worked example: partitioning decision

Orders topic: peak 40 MB/s produce rate, ~2 KB messages. A load test shows one consumer sustains ~4 MB/s per partition once the Postgres write (the dominant cost) is batched; producers comfortably push 8 MB/s per partition.

- Producer bound: 40 ÷ 8 = 5 partitions.
- Consumer bound: 40 ÷ 4 = 10 partitions ← binding.
- With 2x headroom: 20 → round to 24 (divisible by 2, 3, 4, 6, 8, 12).
- Key: `customerId` (millions of distinct values, per-customer ordering required).
- Consumer group: start at 8 members (3 partitions each), scale to a max of 24.
- Retention: replay window of 3 days + 1 day recovery buffer → 4 days; keep 7.

## Deliverable

Produce a topic design document containing: partition count with the sizing math shown, partition key with its cardinality justification, retention and compaction settings, replication and `min.insync.replicas`, producer and consumer config blocks, poll-budget arithmetic, DLQ policy (retry count, backoff, headers, replay plan), and the lag-alert thresholds.

## Do NOT

- Do NOT add consumers beyond the partition count expecting more throughput — they idle.
- Do NOT commit offsets before processing, and do NOT leave auto-commit on for anything that must not lose records.
- Do NOT key by a low-cardinality field — one hot partition caps the whole pipeline at single-consumer speed.
- Do NOT casually increase partitions on a keyed topic in production; it rehashes keys and breaks ordering across the boundary.
- Do NOT retry a poison message forever in-line; it blocks every record behind it in the partition.
- Do NOT skip idempotent processing because delivery is "exactly-once" — rebalances and external sinks reintroduce duplicates.

## Quality bar

A finished design passes only when: the partition count shows both producer and consumer bounds and names which one binds; the key's cardinality is stated; retention is derived from a replay window, not defaulted; the poll budget arithmetic is shown against `max.poll.interval.ms`; the DLQ has bounded retries and a replay path; and every "measured" number is either measured or labeled a guess with a plan to load-test it.
