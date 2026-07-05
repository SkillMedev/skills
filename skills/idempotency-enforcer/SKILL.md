---
name: Idempotency Enforcer
description: Designs client-supplied idempotency keys, deduplication storage, and replay semantics so at-least-once delivery and client retries return the first result instead of double-charging or double-shipping. Use when someone asks "how do I stop duplicate charges on retry", "add an Idempotency-Key header to this endpoint", "the queue redelivered a job and we shipped twice", or before exposing any unsafe POST behind a retrying client or queue. Do NOT use for inbound third-party webhook handlers whose dedup is keyed on a provider event ID - use webhook-receiver-hardener instead and reference this skill only for the storage pattern; do NOT use for designing the client-side retry/backoff policy itself - use rate-limit-handler instead.
---
# Idempotency Enforcer

Make the second arrival of any non-reversible write a no-op that replays the first result. Without this, every retrying client, queue redelivery, and impatient double-click is a potential duplicate charge, duplicate shipment, or duplicate email - and the failure only shows up in production, under load, as a customer complaint.

## Operating procedure

Steps 1-3 are design decisions; 4-7 are the runtime protocol. Do them in order - the storage decision (step 3) depends on the scope decision (step 2), and the replay rules (step 6) are meaningless until intent is persisted atomically (step 5).

### Step 1: Gather inputs

Collect before designing. Label guesses as guesses.

- Which endpoints have non-reversible side effects (payments, orders, emails, inventory)? Only those get the machinery.
- Is the side effect in the same database as the API (one transaction possible) or an external call (a charge, a third-party API)?
- What is the client's maximum retry window - how long after the first attempt can a retry legitimately arrive? Default assumption: 24 hours.
- Expected write volume, to size the key store and the sweep job.

### Step 2: Require and scope the key

- Accept an Idempotency-Key header (client-generated UUID or token, cap length at 255 characters) on every unsafe POST. Reject requests missing it with 400 on endpoints where a duplicate causes irreversible harm.
- Do not derive the key server-side from the payload - two legitimate identical orders must both succeed, so the client owns intent.
- Scope uniqueness as (tenant/account + endpoint + key value). Never share a namespace across tenants or routes, or one user's retry collides with another's.

### Step 3: Choose storage by the TTL and transaction rules

- **Default: a relational table with a unique constraint**, in the same database as the side effect whenever possible, so the key write and the effect commit together. The unique constraint is what wins the race.
- Use Redis (SET NX with TTL) only when the side effect lives outside your database anyway and you accept that an eviction loses a stored response. Never use best-effort storage when the duplicate costs money.
- **TTL: 24 hours minimum, 7 days typical maximum.** The red line: TTL must exceed the client's maximum retry window, or a late retry re-executes. Keys are dedup tokens, not an audit log - sweep expired rows on a schedule and document the window so clients know how long a retry stays safe.
- Store the full response (status code + body) for replay. If bodies exceed roughly 64 KB, store a reference to the resource instead and rebuild the response on replay.

### Step 4: Bind the key to the request body

Store a hash of the request body with the key. If the same key returns with a different body, respond 422 - that is a client bug, not a retry. Never execute it.

### Step 5: Persist intent atomically, then do the work

In one transaction: insert the key row behind the unique constraint with status=in_progress, perform the side effect, then persist the response and set status=succeeded. The side-effect write and the key write must commit together - no committed charge without a stored key, no stored key claiming success without the effect.

For external side effects that cannot share your transaction (a card charge): write your key row first, pass the provider's own idempotency key on the outbound call, and on crash reconcile via the provider's status lookup - never re-issue the call blind.

### Step 6: Replay by state

| Stored state | Response to a duplicate |
|---|---|
| succeeded | Replay the stored status + body verbatim, byte-for-byte |
| in_progress | 409 (or Retry-After) - never run the work twice concurrently |
| failed (definitive) | Replay the stored error; the client must send a new key to retry the operation |
| absent / expired | Treat as a first request |

### Step 7: Sweep and monitor

Delete rows past TTL. Alert on rows stuck in_progress longer than your slowest legitimate operation (typically 60 seconds for a synchronous API) - each one is a crashed request needing reconciliation.

## Worked artifact: middleware design

```sql
CREATE TABLE idempotency_keys (
  tenant_id    UUID NOT NULL,
  endpoint     TEXT NOT NULL,
  key          TEXT NOT NULL CHECK (length(key) <= 255),
  request_hash TEXT NOT NULL,
  status       TEXT NOT NULL DEFAULT 'in_progress', -- in_progress|succeeded|failed
  response_code INT,
  response_body JSONB,
  created_at   TIMESTAMPTZ NOT NULL DEFAULT now(),
  expires_at   TIMESTAMPTZ NOT NULL DEFAULT now() + interval '24 hours',
  PRIMARY KEY (tenant_id, endpoint, key)
);
```

```js
// Express-style middleware sketch - the shape, not a library
async function idempotency(req, res, next) {
  const key = req.header('Idempotency-Key');
  if (!key) return res.status(400).json({ error: 'Idempotency-Key required' });
  const hash = sha256(req.rawBody);
  const row = await db.tryInsertKey(req.tenantId, req.route.path, key, hash); // INSERT .. ON CONFLICT DO NOTHING RETURNING *
  if (row.inserted) return next(); // first arrival: handler runs, then persists response + status
  if (row.request_hash !== hash) return res.status(422).json({ error: 'key reused with different body' });
  if (row.status === 'in_progress') return res.status(409).set('Retry-After', '2').end();
  return res.status(row.response_code).json(row.response_body); // replay
}
```

## Deliverable

Produce an idempotency design doc containing: the list of protected endpoints, the key scope tuple, the storage choice with TTL and sweep schedule, the state/replay table, the body-hash conflict rule, and the reconciliation path for external side effects.

## Do NOT

- Do not derive the key server-side from the payload alone - identical legitimate requests must both succeed.
- Do not let status=in_progress allow a second concurrent execution.
- Do not split the side effect and the key write into separate transactions when one transaction is possible.
- Do not treat a same-key-different-body request as a retry - return 422.
- Do not keep keys forever; they are short-lived tokens, not an audit log.
- Do not set TTL shorter than the client's retry window - a late retry then re-executes the work.
- Do not add the machinery to pure reads, full-resource PUTs replacing a resource by ID, or operations already guarded by a unique business constraint (one row per user per day). Add it only where a duplicate costs money, inventory, or notifications.

## Quality bar

- Every unsafe POST with a non-reversible effect rejects a missing key and replays a duplicate key's stored response byte-for-byte.
- A concurrent duplicate cannot execute the work twice - the unique constraint or in_progress state blocks it, proven by a test that fires two identical requests simultaneously.
- The side effect and the key record are durable together: no committed charge without a stored key, no stored key claiming success without the effect.
- TTL exceeds the documented client retry window, and the sweep job exists.

## Escalation

Inbound webhook dedup keyed on provider event IDs belongs to webhook-receiver-hardener. Client-side retry and backoff policy belongs to rate-limit-handler. If duplicates cross a service boundary you are extracting, the consistency design belongs with monolith-decomposer.
