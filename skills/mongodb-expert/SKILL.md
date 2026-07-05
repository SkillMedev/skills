---
name: MongoDB Expert
description: Designs MongoDB schemas, indexes, and aggregation pipelines that perform - embed-vs-reference decision rules, the 16MB document limit, ESR compound-index ordering, explain-plan verification, and operational settings for replica sets and sharding. Use when someone asks "should I embed or reference this", "why is my Mongo query slow", "design a MongoDB schema for X", "how do I structure this aggregation pipeline", or "what should my shard key be". Do NOT use for relational or SQL schema design - use database-schema instead; for tuning SQL queries use sql-query-optimizer; for Postgres-on-Supabase work use supabase-expert.
---

# MongoDB Expert

Model data, index it, and query it the MongoDB way: around access patterns, not normalization. The costly mistake this skill prevents is a relationally-normalized schema ported into Mongo - a design that forces application-side joins on every read and, in the opposite failure, unbounded embedded arrays that grow until documents hit the 16MB limit and writes start failing in production.

## Operating procedure

Schema first, indexes second, pipelines third - indexes serve queries, and queries are determined by the schema, so working backwards wastes every downstream decision.

### Step 1: Gather inputs

1. The top read and write patterns, ranked by frequency: which entities are fetched together, filtered by what, sorted by what.
2. Cardinality and growth of every one-to-many relationship: bounded ("an order has 5-50 line items") or unbounded ("a user accrues events forever"). Label estimates as guesses.
3. Read/write ratio per collection and expected document counts.
4. Deployment shape: replica set or sharded cluster, and durability requirements.

### Step 2: Decide embed vs reference per relationship

Apply these rules in order; the first that fires decides.

1. Accessed together and the child side is bounded (a known, modest maximum) → embed. One read serves the whole query.
2. Child is unbounded, large, or shared by multiple parents → reference. Unbounded arrays are the canonical anti-pattern: array growth relocates documents, bloats indexes, and marches toward the 16MB document cap.
3. Need the most recent few children with the parent, but the full set is unbounded → subset pattern: embed the latest N (say 10) and keep the full history in a referenced collection.
4. High-volume time-ordered children (events, readings) → bucket pattern: one document per parent per time window (for example, per hour) holding an array capped by the window.

Budget documents well under 16MB - treat a typical document above a few hundred KB as a design smell to justify, not a limit to approach.

### Step 3: Index for the actual queries

Indexes are the single biggest performance lever.

- Follow the ESR rule for compound indexes: Equality fields first, then Sort fields, then Range fields. A query filtering on customerId and sorting by createdAt is fully served by `{ customerId: 1, createdAt: -1 }`.
- Verify with `explain("executionStats")`: require IXSCAN, never COLLSCAN, on any hot path; check that `totalDocsExamined` is close to `nReturned` - a 100:1 ratio means the index is not selective for that query.
- Use partial indexes to index only the documents a query can match (for example, only `status: "active"`).
- Use covered queries - projection limited to indexed fields - to skip fetching documents entirely.
- Cap the index count: every index taxes every write. If a collection carries more than 5-8 indexes, audit for unused ones with `$indexStats`.

### Step 4: Build pipelines that shrink early

Filter early so indexes apply and less data flows downstream:

```js
db.orders.aggregate([
  { $match: { status: "shipped", createdAt: { $gte: start } } },
  { $group: { _id: "$customerId", total: { $sum: "$amount" }, n: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 100 }
])
```

- Place `$match` and `$project` as early as possible; only a leading `$match` can use indexes.
- `$lookup` is a join - index the foreign field or the lookup degenerates to a per-document collection scan.
- Use `$facet` to compute multiple aggregations in one pass over the data.
- Stages have memory limits; pass `{ allowDiskUse: true }` for large groupings and sorts, and treat needing it on a hot path as a signal to pre-aggregate.

### Step 5: React to changes with change streams

```js
const stream = db.collection("orders").watch([
  { $match: { operationType: "insert" } }
])
for await (const change of stream) { handle(change.fullDocument) }
```

Persist the resume token and resume from it after failures, or events are silently lost. Change streams require a replica set.

### Step 6: Set the operational posture

- Run a replica set for durability; use write concern `w: "majority"` for anything you cannot afford to lose on failover.
- Choose read concern to match consistency needs; defaults favor speed over read-your-own-writes.
- Shard on a high-cardinality key that distributes writes evenly. Never shard on a monotonically increasing key (timestamps, ObjectId) - all inserts land on one shard. Use a hashed or compound key instead.
- Enable the database profiler (or slow-query log) with a threshold around 100ms and review it routinely.
- Use multi-document transactions only when truly needed; single-document operations are already atomic, and a schema that embeds what changes together rarely needs transactions at all.

## Worked artifact: schema pair, bad vs good

The domain: an e-commerce store with users, orders, and order events (status changes, notifications - unbounded over time).

Bad - relational normalization ported into Mongo, plus an unbounded embed:

```js
// users:   { _id, name, email }
// orders:  { _id, userId, status,
//            itemIds: [ObjectId, ...],          // items referenced -> N+1 reads
//            events: [{ ts, type, note }, ...]  // UNBOUNDED array
//          }
// items:   { _id, sku, name, price }
```

Rendering one order page costs 1 read for the order, N reads for the items, and the events array grows forever - busy orders churn toward the 16MB cap, every event push rewrites a growing document, and the `events` field bloats every index that includes it.

Good - embed the bounded, reference the unbounded, subset the recent:

```js
// orders: {
//   _id, userId, status, createdAt,
//   items: [                       // EMBEDDED: bounded (5-50), read together,
//     { sku, name, price, qty }    // price snapshotted at purchase time
//   ],
//   recentEvents: [                // SUBSET: latest 10 only, capped with
//     { ts, type }                 // $push + $slice on every insert
//   ]
// }
// order_events: {                  // REFERENCED: unbounded full history
//   _id, orderId, ts, type, note   // index: { orderId: 1, ts: -1 }
// }
// users: { _id, name, email }      // shared by many orders -> referenced
```

One read renders the order page (items and recent activity embedded); the full audit trail lives in `order_events`, indexed for the rare "show all history" query; documents stay small and stable-sized. Items embed a price snapshot deliberately - the order must show what was paid, not the current catalog price.

## Deliverable

Produce a schema and query design document containing: each relationship with its embed/reference/subset/bucket ruling and the rule that decided it, the collection sketches with representative documents, the index list with the query each serves and its ESR ordering, explain-plan verification notes for the hot paths, and the operational settings (write concern, shard key if sharded, profiler threshold).

## Do NOT

- Do not normalize by habit; every referenced relationship is a join the application pays for on each read.
- Do not embed unbounded arrays; use the subset or bucket pattern before growth forces an emergency migration.
- Do not order compound index fields by intuition; violate ESR and the index serves the filter but not the sort, forcing in-memory sorts.
- Do not ship a query whose explain plan shows COLLSCAN on a hot path, or whose docs-examined dwarfs docs-returned.
- Do not shard on a monotonically increasing key; a single hot shard absorbs all writes.
- Do not reach for transactions to patch a schema that split data which changes together - restructure instead.

## Quality bar

The design ships only when: every relationship's embed/reference decision cites cardinality and access pattern, not preference; no document can grow without bound under any workload; every hot query shows IXSCAN with docs-examined near docs-returned in `explain("executionStats")`; leading `$match` stages hit indexes; and the write-concern and shard-key choices are stated with their failure trade-offs.
