---
name: Mobile Offline Sync
description: Builds local-first mobile storage with an optimistic local store, durable mutation outbox, per-entity conflict-resolution rules, incremental pull, and idempotent background retry. Use when someone says "the app has to work offline", "how do I sync Room or SQLite or Realm with the server", "what happens when two devices edit the same record", or offline edits must reconcile with the server later. Do NOT use for paginating, caching, and refreshing read-heavy server lists - use pagination-and-sync-engineer instead; and do NOT build offline write support for strong-consistency operations like payments or inventory counts - disable those offline.
---
# Mobile Offline Sync

Make the local store the source of truth the UI reads and writes, and treat the network as eventual replication. The hard part is never the happy path - it is reconciling divergent edits made while offline. The costly failure is an ad-hoc "retry the request later" queue that double-applies mutations, resurrects deleted rows, and silently destroys one user's edits with another's.

## Operating procedure

### Step 1: Gather inputs

Collect per feature before designing:

1. The entities that must work offline, and for each: single-owner or multi-writer? Whole-object edits or field-level edits?
2. The maximum realistic offline window (a field-service app may need 7-30 days; a consumer app usually hours). This sets tombstone retention.
3. The local store (Room, SwiftData, SQLite, Realm) and whether the server API can accept client-generated UUIDs and return per-record versions.
4. Which operations are strong-consistency (payments, inventory decrements, seat booking) - these get disabled offline, not synced.
5. Expected data volume per user, which decides pull batch sizes.

Label unknowns as guesses and validate them against the server team before building.

### Step 2: Make the local store the UI's truth

Write to the local database synchronously and reflect it in the UI immediately (optimistic). Never block the UI on a network round-trip. Tag every row with a sync state - pending, syncing, synced, failed - so the UI can show subtle pending indicators and surface failures.

### Step 3: Queue mutations as intents, not raw requests

Record each offline change in a durable outbox table: operation (create/update/delete), entity id, changed fields, client timestamp, the base version the client last saw, and a client-generated UUID. Send the outbox in order, batched (up to about 50 mutations per request keeps payloads small and retries cheap). The server dedupes on the UUID so a retried request never double-applies.

### Step 4: Choose a conflict-resolution rule per entity and write it down

There is no universal default. Apply this decision table per entity, top to bottom:

| Question | If yes | If no |
|---|---|---|
| Does the server enforce a business invariant on this entity (state machines, balances, assignments)? | Server-authoritative: client proposes, server decides, client rebases local state on the server's answer | Next question |
| Do multiple writers concurrently edit the same record in normal use? | Next question | Last-write-wins on server-assigned version or hybrid logical clock - never raw device clocks |
| Do concurrent edits usually touch different fields? | Field-level merge: per-field versions, merge disjoint field sets, LWW per field on true collisions | Next question |
| Is it collaborative text or a shared set/list needing convergence without a coordinator? | CRDT (text CRDT for documents, OR-Set for collections) - accept the metadata and complexity cost | Server-authoritative with an explicit user-facing conflict prompt |

Rules of thumb: server-authoritative is the simplest correct default when the server can decide. LWW is acceptable only for single-owner, whole-object data where losing an overwritten edit is tolerable. CRDTs are the last resort, not the default - they buy convergence at real complexity cost.

### Step 5: Pull, detect, reconcile

Pull incrementally with a server cursor or updated_since watermark - never full table scans. Detect a conflict by comparing the base version the client last saw against the server's current version. Apply the entity's resolution rule, write the reconciled result back locally, and clear the pending flag. Keep tombstones for deletes - retained at least as long as the maximum offline window from Step 1 - so a deletion is not resurrected by a stale upsert.

### Step 6: Retry as if the network is hostile

Retry with exponential backoff plus jitter: 2s base, doubling, capped at 10 minutes, with plus-or-minus 50 percent jitter; cap at roughly 8 attempts, then park the mutation as failed and surface it to the user. Schedule background sync with WorkManager (Android) or BGTaskScheduler (iOS) under OS constraints - the OS decides when it runs. Make every sync operation idempotent so a process kill mid-flight is safe to repeat.

## Worked artifact: conflict-resolution decision for a field-service app

| Entity | Writers | Edit shape | Rule chosen | Why |
|---|---|---|---|---|
| Job status (assigned -> en route -> done) | Technician + dispatcher | State transition | Server-authoritative | Server enforces the legal state machine; client rebases if its transition was stale |
| Technician visit notes | One technician | Append text | LWW on server version | Single owner; concurrent edits effectively impossible |
| Customer record (phone, address, gate code) | Technician + office staff | Different fields | Field-level merge | Office edits billing fields while tech edits access notes; merging disjoint fields loses nothing |
| Shared parts checklist on a job | Multiple technicians | Add/remove items | CRDT (OR-Set) | Concurrent adds/removes of the same list must converge without losing anyone's items |
| Invoice payment capture | Anyone | Money movement | Disabled offline | Strong consistency; show "reconnect to take payment" |

The artifact to keep: this table, one row per synced entity, committed alongside the schema.

## Deliverable

Produce an offline-sync design containing: the entity list with a written conflict rule per entity (table above), the outbox schema (operation, entity id, changed fields, base version, client UUID, client timestamp), the pull watermark strategy, the backoff/retry parameters, tombstone retention matched to the offline window, and the list of operations deliberately disabled offline.

## Quality bar

- The UI never waits on the network for a write; every local write is visible instantly with a sync-state tag.
- Every mutation carries a client-generated UUID and base version; replaying the entire outbox produces the same server state every time.
- Each synced entity has a documented conflict rule from the decision table, and no rule trusts the device clock for correctness.
- Deletes leave tombstones retained longer than the maximum offline window; a stale upsert cannot resurrect a deleted row.
- Killing the app mid-sync and relaunching converges to a consistent state with no duplicates and no lost edits.

## Do NOT

- Do NOT replay raw HTTP requests from a log - that loses ordering and idempotency. Queue intents in an outbox instead.
- Do NOT decide conflicts by comparing raw device clocks; a skewed clock silently destroys data. Use server-assigned versions or a hybrid logical clock.
- Do NOT default to CRDTs to avoid choosing - they are for genuine multi-writer convergence and cost metadata and complexity.
- Do NOT pull full tables on every sync; use a cursor or watermark.
- Do NOT hard-delete rows without a tombstone.
- Do NOT fake offline support for strong-consistency operations (payments, inventory counts). Disable the action while offline and tell the user why.
- Do NOT build full offline sync where the UX does not demand it - it is expensive; for read-heavy list caching and refresh, pagination-and-sync-engineer is the right tool.
