---
name: N+1 Query Hunter
description: Detect ORM loop-of-queries (N+1) patterns from query logs and eliminate them with batched eager loading, keeping the N+1s that are cheap. Use when a list endpoint is slow, a request issues many near-identical SELECTs differing only by an id, query logs show repeated SELECTs in a loop, or a tool like the bullet gem flags an N+1 in ActiveRecord, Prisma, SQLAlchemy, or Hibernate.
---
# N+1 Query Hunter

Find ORM N+1 patterns from captured query logs and replace them with batched eager loading, leaving cheap bounded ones alone. An N+1 is one query to fetch a list, then one more query per row to fetch its association — the most common ORM performance bug, hidden because each query is individually fast.

Do NOT use when reading or interpreting a single statement's EXPLAIN/EXPLAIN ANALYZE plan — use explain-plan-reader instead. Do NOT use when the fix is restructuring one query's SQL (rewriting joins, subqueries, or window functions) — use query-rewriter instead. This skill owns the loop-of-queries case where the fix is preloading, not rewriting one statement.

## Workflow

1. Capture the evidence before guessing. Turn on query logging and count the SELECTs for the slow request. ActiveRecord: tail the log or add the `bullet` gem. SQLAlchemy: `create_engine(..., echo='debug')`. Prisma: enable the `query` log event. Hibernate: `hibernate.show_sql=true` and `generate_statistics=true`. The tell is a burst of near-identical SELECTs differing only in the `WHERE id = ?` value, scaling with collection size — 20+ of them in one request is a near-certain N+1. Confirm server-side with `pg_stat_statements`: a normalized query whose `calls` count is orders of magnitude above the endpoint's request rate.
2. Confirm it scales with input, and that it's worth fixing. Note how N grows: with collection size, page size, or user-controlled input. A useful bar: fix it when the request issues 10+ queries that grow linearly with rows, or when the loop's aggregate time is 20%+ of request latency — each query may cost only 0.5–1ms, but 200 of them adds 100–200ms plus per-query round-trip overhead. A 2-query "N+1" or one bounded to a handful of rows is not the target — move on.
3. Replace lazy per-row loads with one batched preload. ActiveRecord: `Post.includes(:author, comments: :user)` — `preload` forces separate batched queries, `eager_load` forces a LEFT JOIN, `includes` lets Rails choose. SQLAlchemy: `selectinload(Post.comments)` (second IN query, best default for collections) or `joinedload` for one-to-one. Prisma: pass `include`/`select` with nested relations in one call. Hibernate: `JOIN FETCH` in JPQL or an `@EntityGraph`; never rely on global `FetchType.EAGER`. If a batched IN list can exceed roughly 1,000 ids, chunk it — huge IN lists degrade planning and can hit protocol parameter limits.
4. Avoid the fan-out trap. `joinedload`/`eager_load` on a one-to-many multiplies rows (cartesian fan-out) and can be slower than the N+1 it replaced — prefer `selectinload`/`preload` for collections. Eager loading inside a method that is itself called in a loop just moves the N+1 up one level; hoist the preload to the outermost collection.
5. Use counts, not rows, for sizes. If the code loads children only to call `.size`/`.count`, use `counter_cache` or a grouped `COUNT` instead of materializing the association.
6. Re-measure. Re-run the request with logging on and confirm the SELECT count dropped to a constant, and that p95 latency improved on the hot path.

## Deliverable

Produce an N+1 fix report containing: the before evidence (query count and latency for the request, or the `pg_stat_statements` calls count), each offending call site with the preload change applied, the after query count (a constant, independent of collection size), the before/after p95 on the hot path, and a short list of N+1s deliberately left alone with the reason (bounded, cached, or cold path).

## Quality bar

- Every fix is justified by a captured query log or `pg_stat_statements` count, before and after.
- The chosen strategy matches cardinality: IN-batched (`selectinload`/`preload`) for collections, JOIN-based only for one-to-one.
- The request issues a constant number of queries regardless of collection size.
- Only N+1s on hot paths or scaling with user-controlled input are touched.

## Do NOT

- Do not optimize by guesswork — never "fix" an N+1 you have not seen in a log or `pg_stat_statements`.
- Do not preload associations the response never serializes; that is a reverse-N+1 fetching unused data.
- Do not chase a 2-query "N+1" or one on a page rendered once a day.
- Do not use `joinedload`/`eager_load` on one-to-many collections by default — the row fan-out can regress latency.
- Do not set `FetchType.EAGER` globally in Hibernate to "solve" N+1; scope eager loading per query.
- Do not skip per-row loads that hit an identity map or cache where the marginal cost is already near zero.
