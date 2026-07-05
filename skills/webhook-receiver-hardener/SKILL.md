---
name: Webhook Receiver Hardener
description: Hardens an inbound webhook endpoint so it verifies the sender signature on the raw body, resists replays, and acknowledges fast by persisting-then-enqueueing before any processing. Use when building or reviewing a handler that receives webhooks from Stripe, GitHub, or any third party, when adding HMAC signature verification, or when a sender is replaying events or hammering you with retries after slow acks.
---
# Webhook Receiver Hardener

Turn an inbound webhook route into hostile-input-safe code: verify before you trust, persist before you ack, ack before you process.

A webhook endpoint is an unauthenticated, internet-facing write path that an attacker can spam and a flaky sender will hammer with retries. Treat every byte as untrusted until the signature checks out.

## Workflow

1. **Capture the raw body.** Read the exact request bytes before any JSON parse or body-parsing middleware runs. A re-encoded body changes whitespace and ordering and silently breaks the HMAC. If the framework buffers/reserializes by default, disable it for this route.
2. **Verify the signature.** Compute the HMAC over the raw bytes with the signing secret loaded from config. Compare with a constant-time equality function - never `==` or string compare. Support two active secrets so secret rotation does not drop traffic. Reject failures with 401 before doing any other work.
3. **Reject replays by timestamp.** Most providers sign a timestamp alongside the payload. Verify it is part of the signed material, then reject requests whose timestamp falls outside a tolerance window (commonly 5 minutes) to blunt captured-request replay.
4. **Persist the raw event, then enqueue, then ack.** Synchronously do only the minimum: store the verified raw payload and its event ID durably, enqueue a background job, return 2xx. Return success only after the event is durably stored so a crash mid-processing causes safe redelivery rather than silent loss.
5. **Process asynchronously and order-tolerantly.** Run all business logic in the background worker, not the request. Webhooks arrive out of order, so never assume sequence: act on the event's own version/timestamp, or refetch current state from the provider's API rather than mutating from a possibly-stale payload. For strict ordering, partition the queue by resource ID.
6. **Return the right status.** 2xx tells the sender to stop retrying; non-2xx requests a retry. A malformed-but-authentic event goes to a dead-letter queue and is acked, not retried forever. Log event ID and type on every receipt.

## Worked example

The classic broken handler versus the hardened one (Express + Stripe-style signatures):

**Bad:**

```js
app.use(express.json()); // global parser destroys the raw body

app.post("/webhooks/stripe", async (req, res) => {
  if (req.headers["stripe-signature"] !== computeSig(JSON.stringify(req.body))) {
    return res.status(401).end();       // re-serialized body ≠ signed bytes; string compare leaks timing
  }
  await fulfillOrder(req.body);          // business logic inside the request
  await sendReceiptEmail(req.body);      // slow third-party call before the ack
  res.status(200).end();                 // sender already timed out and is retrying
});
```

Three failures: the HMAC is computed over a re-encoded body (verification breaks or, worse, gets disabled "temporarily"), `!==` is not constant-time, and the ack waits on business logic - so the sender times out, retries, and double-fulfills.

**Good:**

```js
app.post("/webhooks/stripe",
  express.raw({ type: "application/json" }),   // raw bytes, this route only
  async (req, res) => {
    const sig = req.headers["stripe-signature"];
    if (!verifySignatureConstantTime(req.body, sig, [SECRET_CURRENT, SECRET_PREVIOUS])) {
      return res.status(401).end();
    }
    if (!timestampWithinTolerance(sig, 300)) {   // 5-minute replay window
      return res.status(401).end();
    }
    const event = JSON.parse(req.body);          // parse only after verification
    await store.saveRawEvent(event.id, req.body); // durable first
    await queue.enqueue("webhook", { eventId: event.id });
    res.status(200).end();                        // ack in milliseconds; work happens in the worker
  });
```

## Quality bar

- Signature is verified against raw bytes with a constant-time compare; tampered or unsigned requests get 401 and touch nothing else.
- Two signing secrets are accepted during rotation; neither is hardcoded.
- Replays outside the timestamp window are rejected.
- The synchronous path is verify → persist → enqueue → 2xx, with no business logic, downstream calls, or blocking DB writes beyond the durable store.
- A process crash after ack cannot lose the event; redelivery re-runs it safely.

## Deliverable

Produce a hardened webhook receiver consisting of:

1. **The route handler** implementing the verify → persist → enqueue → ack pipeline, with raw-body capture scoped to this route.
2. **The verification module**: constant-time HMAC check over raw bytes, dual-secret rotation support, and timestamp-window replay rejection.
3. **The background worker skeleton** that loads the stored raw event by ID, processes order-tolerantly, and routes poison events to a dead-letter queue.
4. **A checklist of what was hardened** - each Quality bar item marked verified, plus any provider-specific notes (header names, tolerance window, retry policy).

## Do NOT

- Do NOT parse JSON or run middleware before computing the HMAC over the raw body.
- Do NOT compare signatures with `==` or ordinary string equality - use a constant-time compare.
- Do NOT run business logic, call other services, or block on slow DB work inside the request; slow acks trigger sender timeouts and a retry storm.
- Do NOT return 2xx before the event is durably persisted or enqueued.
- Do NOT trust the payload's field order or arrival order to imply event sequence.
- Do NOT disable signature verification for any third-party webhook, even in staging or for local convenience.
- Do NOT use when the call is an internal, mutually-authenticated service-to-service request over mTLS - that channel may not need application-layer HMAC.
- Do NOT use this skill to design the dedup-key mechanism that makes downstream handlers idempotent (which event-ID column, where it lives, how effects key off it) - use idempotency-enforcer instead. This skill stores the raw event and acks; idempotency-enforcer owns deduplication semantics.
