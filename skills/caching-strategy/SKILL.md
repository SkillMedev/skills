---
name: Caching Strategy
description: Designs multi-layer caching - CDN, application, and database - choosing the pattern, TTL, and invalidation plan per data type, and produces a cache-decision table plus stampede protection. Use when someone asks "should I cache this", "Redis or CDN for this endpoint", "how do I invalidate this cache", "why is my cache hit rate so low", or is bolting Redis onto a slow service. Do NOT use for sizing or tuning database connection pools - use connection-pool-tuner instead - and do NOT use for frontend bundle, image, or Core Web Vitals work - use web-performance instead.
---

# Caching Strategy

A cache is a correctness liability you accept in exchange for latency, so every cache needs an explicit answer to "how stale can this be?" and "how does it get invalidated?" before it ships. The costly mistake this skill prevents is the write-only cache: a layer that adds a network hop and an invalidation bug surface while its hit rate quietly sits below 50 percent, making the system slower and wronger at the same time.

## Operating procedure

Work per data type, not per system - "cache the API" is not a decision, "cache the product listing for 60s at the CDN" is.

### Step 1: Gather inputs

Collect these for each candidate data type. If a number is an estimate, label it a guess and refine from production metrics later.

1. Read:write ratio. How many reads per write to the same key.
2. Recompute cost. Latency of a cache miss (DB query, computation, upstream call).
3. Staleness tolerance. How stale a value can be before it causes a user-visible or business-logic error. Get a number in seconds, not "pretty fresh".
4. Key cardinality and object size. Millions of small keys behave differently from thousands of megabyte blobs.
5. Whether the data is user-specific or shared.

### Step 2: Decide whether to cache at all

- Read:write below 3:1 or recompute cost under 5ms: do not cache. The invalidation complexity buys almost nothing.
- Read:write at 10:1 or higher, or recompute cost over 20ms: strong candidate.
- In between: cache only if you can use plain TTL invalidation (Step 5). Event-based invalidation is not worth it for marginal wins.

### Step 3: Pick the layer

Cache as close to the consumer as correctness allows.

1. Browser/CDN for static assets and shared, cacheable responses. Hashed static assets get `Cache-Control: public, max-age=31536000, immutable`; dynamic-but-shared responses get short `max-age` plus `stale-while-revalidate`; conditional revalidation uses `ETag`/`If-None-Match`.
2. Application layer (in-memory or Redis) for computed results, hot objects, and session-adjacent data.
3. Database layer (materialized views, read replicas) when the expensive part is the query itself and many app nodes need the same answer.

Never cache user-specific data at a shared/public layer - that is an auth-leak class of bug, not a performance bug.

### Step 4: Pick the pattern

- Cache-aside (lazy) is the default: on miss, load from source, store with TTL, return. Choose anything else only with a stated reason.
- Read-through/write-through when you want the cache library to own consistency and you accept write latency.
- Write-behind only for loss-tolerant data (metrics, counters) - buffered writes can vanish on crash.

### Step 5: Set TTL and invalidation from staleness tolerance

- Staleness tolerance ≥ 5 minutes: TTL-only. Set TTL to roughly half the tolerance and stop - this is the cheapest correct design.
- Tolerance between ~10 seconds and 5 minutes: short TTL plus best-effort delete-on-write for the hot paths.
- Tolerance under ~10 seconds ("must be fresh"): event-based invalidation (delete or update the key inside the write path), with a short TTL (60-300s) as the escape hatch. Never cache without an expiry escape hatch.
- Invalidate whole sets with version keys (`user:123:v2` - bump the version, old keys age out) rather than trying to enumerate and delete members.

### Step 6: Protect against stampedes and penetration

When a hot key expires, every concurrent request rebuilds it at once.

- Add ±10-20% random jitter to TTLs so keys don't expire simultaneously.
- Single-flight lock: one request recomputes, others wait or serve stale.
- Serve-stale-while-revalidate: return the old value, refresh in the background.
- Cache penetration (repeated queries for keys that don't exist): cache the negative result for 30-60s.
- Redis hot-key or big-key: replicate the hot value across shards or split the big value; a single 10MB key on one shard becomes the whole cluster's bottleneck.

### Step 7: Instrument and set the kill threshold

Emit hit rate, miss latency, and eviction rate per cache from day one. Expectations: 90%+ for CDN static assets, 80%+ for application-layer caches of hot objects, 60%+ minimum for anything else. A cache holding under 50% hit rate after tuning TTL and key design should be removed - it is adding a hop and a failure mode for nothing. Choose an eviction policy deliberately: LRU is the default; LFU when a stable hot set is scanned by long-tail traffic.

## Worked artifact: cache-decision table

Produce one row per data type. Filled example for an e-commerce API:

| Data | Read:write | Recompute | Staleness tol. | Layer | Pattern | TTL | Invalidation | Target hit rate |
|---|---|---|---|---|---|---|---|---|
| Product detail | 500:1 | 45ms | 5 min | CDN + Redis | cache-aside | 120s ±15% jitter | TTL only | 85% |
| Cart contents | 4:1 | 8ms | 0s | none | - | - | - | - |
| Price by SKU | 200:1 | 30ms | 5s | Redis | cache-aside | 60s escape hatch | delete-on-write | 80% |
| Search results | 50:1 | 300ms | 10 min | Redis | stale-while-revalidate | 300s ±20% | TTL only | 70% |
| User session | 20:1 | 12ms | 0s | Redis (keyed per user) | write-through | 24h idle | delete on logout | 95% |

Note the cart row: high write rate and zero staleness tolerance means the correct decision is no cache.

Copy-paste skeleton:

```
CACHE DECISION TABLE - [FILL: service name]
| Data | Read:write | Recompute cost | Staleness tolerance | Layer | Pattern | TTL + jitter | Invalidation | Target hit rate |
| [FILL] | [FILL] | [FILL]ms | [FILL]s | [FILL] | [FILL] | [FILL] | [FILL] | [FILL]% |
Stampede protection: [FILL: jitter / single-flight / stale-while-revalidate]
Kill threshold: remove any row that holds < 50% hit rate after tuning.
```

## Deliverable

Produce a cache-decision table (one row per data type with layer, pattern, TTL, invalidation, and target hit rate), the stampede-protection choice for each hot key, and the monitoring plan naming the hit-rate threshold at which each cache gets removed.

## Do NOT

- Do not cache without a TTL escape hatch - event-based invalidation always has a missed-event path, and a key with no expiry serves wrong data forever.
- Do not cache user-specific data in a shared cache; that is how one user sees another's account.
- Do not add event-based invalidation to data that tolerates minutes of staleness - TTL-only is simpler and equally correct.
- Do not judge success by "the endpoint got faster" alone; a 40% hit rate cache made the p50 faster and the p99 worse.
- Do not let all keys share an exact TTL - synchronized expiry is a self-inflicted stampede.
- Do not accept eventual consistency silently across nodes; either state it in the design or add pub/sub invalidation.

## Quality bar

- Every cached data type has a numeric staleness tolerance and a TTL derived from it.
- Every cache has a named invalidation mechanism and an expiry escape hatch.
- Hot keys have explicit stampede protection (jitter at minimum).
- Hit rate, miss latency, and evictions are instrumented, with the sub-50% removal rule written down.
- The table contains at least one deliberate "do not cache" row - a design that caches everything was not analyzed.

## Escalation

If the miss path is slow because of a bad query plan, fix the query before caching over it - route to sql-query-optimizer or index-advisor. If latency comes from connection acquisition rather than compute, use connection-pool-tuner. For browser-side performance beyond HTTP cache headers, use web-performance.
