---
name: Pagination and Sync Engineer
description: Designs correct cursor pagination and incremental delta sync against a mutating dataset — cursor contracts, updated_at watermarks, delete propagation, checkpointing, and idempotent reprocessing. Use when someone says "my sync is skipping rows", "should I use cursor or offset pagination", "design the pagination contract for this list endpoint", "keep a local copy of this API in sync", or has an offset/page=N loop against changing data. Do NOT use for generating a typed API client or SDK from a spec — use api-client-generator instead; do NOT use for on-device offline-first sync with conflict resolution between a mobile client and server — use mobile-offline-sync instead; this skill owns server-to-server paging and one-way replication correctness.
---
# Pagination and Sync Engineer

Make paging and delta sync correct against a dataset that mutates mid-walk: never skip, never double-count, and survive interruption. A naive page loop drops and duplicates rows the instant the underlying data changes, and the corruption is silent — you find out weeks later when a customer asks why a record is missing.

## Operating procedure

Steps 1–2 fix the read contract; 3–4 fix the sync semantics; 5–6 make it survivable. Order matters: a watermark on top of broken ordering is still broken.

### Step 1: Gather inputs

- Dataset size and write rate. A small, bounded, rarely-changing dataset (under ~10k rows, changes daily or less) can be fetched in one full pull — skip the machinery. Label estimates as estimates.
- Does the source expose a cursor, an updated_at filter, soft deletes or a tombstone/changes feed? Each missing capability forces a workaround below.
- Consistency target: how stale may the local copy be?

### Step 2: Choose cursor over offset — the decision rule

Offset (LIMIT/OFFSET, page=N) is correct only when the dataset is immutable for the duration of the walk AND small enough that deep offsets stay fast. Everything else gets keyset/cursor pagination, because an insert or delete before your offset shifts every later row, skipping or repeating records, and OFFSET N scans N rows server-side.

Cursor contract rules when you own the API:

- Encode the last item's full sort key (e.g. base64 of `{"updated_at": "...", "id": "..."}`) and treat it as opaque to clients — document that parsing it voids the contract.
- Default page size 50–100; hard cap 500–1000. Reject larger with 400 rather than silently clamping without documentation.
- Return `next_cursor: null` (or omit it) as the explicit end-of-list signal; never make clients infer the end from a short page, since a filtered page can legitimately be short.
- A cursor pointing at a deleted row must still work: seek strictly-greater-than the encoded key, not to the row itself.

When consuming someone else's API: pass the cursor back exactly as returned — never parse, fabricate, persist a derived form, or compare cursors.

### Step 3: Sort by a total order

Pagination correctness requires a unique, stable ordering. Sort by a monotonic unique key, or a (timestamp, id) tiebreak — sorting by a non-unique column drops rows at page boundaries when ties straddle a page. For delta sync the ordering key is normally (updated_at, id).

### Step 4: Drive incremental sync from a persisted watermark

- Store the max updated_at fully processed. Next run, request `updated_at >= watermark` — **`>=`, never `>`** — and dedupe by id, because rows can share a timestamp and a strict `>` skips them.
- Subtract a safety margin from the watermark: 5–10 seconds covers clock skew and commit lag on most sources; widen it if the source has long-running transactions whose rows become visible with old timestamps.
- Capture deletes explicitly. A changed-rows feed cannot express deletions — the row is just absent. Prefer soft deletes (deleted_at) or a tombstone feed and propagate removals. If neither exists, reconcile periodically (daily or weekly, by staleness tolerance) with a full ID sweep and delete local rows the source no longer returns.

### Step 5: Checkpoint after every durably-processed page

Persist the cursor or watermark only after the page's rows are committed locally, so a crash resumes mid-stream instead of from zero. Never advance the checkpoint before the page is durable — that ordering is the entire resumability guarantee.

### Step 6: Make reprocessing harmless

Upsert by primary key so replaying a page is a no-op. Honor rate limits with exponential backoff on 429/5xx, and retry transient failures on the same cursor. Pair with rate-limit-handler for the backoff policy and idempotency-enforcer if the sync triggers writes with side effects.

## Worked artifact: cursor pagination contract

```
GET /v1/orders?limit=100&cursor=eyJ1cGRhdGVkX2F0IjoiMjAyNC0uLi4iLCJpZCI6Im9yZF85In0

200 OK
{
  "data": [ { "id": "ord_10", "updated_at": "...", ... } ],   // <= limit items
  "next_cursor": "eyJ1cGRhdGVkX2F0IjoiLi4uIiwiaWQiOiJvcmRfMTA5In0", // null when done
  "has_more": true
}

Rules:
- limit: default 100, max 500 -> 400 on violation
- cursor: opaque; server sorts by (updated_at ASC, id ASC); seek is (updated_at, id) > (cursor.updated_at, cursor.id)
- cursors expire only if the sort key is removed; a deleted anchor row still resolves (strict-greater seek)
```

Sync loop skeleton:

```
watermark = load_checkpoint() or EPOCH
cursor = null
loop:
  page = GET /changes?since={watermark - 10s}&cursor={cursor}&limit=100
  upsert_by_id(page.data)            # idempotent
  commit_local_transaction()
  save_checkpoint(cursor=page.next_cursor or max(row.updated_at))  # AFTER commit
  if not page.has_more: break
```

## Deliverable

Produce a paging/sync design containing: the pagination mode with the decision rationale, the total-order sort key, the cursor contract (encoding, limits, end signal), the watermark rule (>= plus margin plus id-dedupe), the delete-propagation mechanism, the checkpoint placement, and the idempotent-write strategy.

## Do NOT

- Do NOT use OFFSET/page-number paging for any dataset that can change during the walk.
- Do NOT parse, reconstruct, or compare cursors from an API you consume — they are opaque server tokens.
- Do NOT use `>` for the watermark or assume timestamps are unique.
- Do NOT advance the watermark or cursor before the page is committed locally.
- Do NOT infer end-of-list from a short page — require an explicit next_cursor/has_more signal.
- Do NOT leave deletions unhandled because "the feed doesn't have them" — schedule the reconciliation sweep instead.
- Do NOT scaffold a typed client or SDK here — that is api-client-generator's job.

## Quality bar

- Inserting or deleting rows in the source mid-walk never causes a skipped or duplicated row downstream.
- Killing the process at any point and rerunning produces the same final local state (idempotent, resumable from checkpoint).
- Deletions in the source are reflected locally, not left as orphans.
- The watermark comparison is `>=` with id-level dedupe and a stated safety margin, and it advances only after durable processing.

## Escalation

Two-way sync with client-side edits and conflict resolution (mobile/offline apps) is mobile-offline-sync territory. Query-side performance of the keyset seek itself belongs with index-advisor.
