---
name: Stripe Expert
description: Implements Stripe payments, subscriptions, and webhooks so billing state stays correct — Checkout Sessions, signature-verified idempotent webhook handlers, dunning, and SCA handling. Use when someone asks "add Stripe subscriptions to my app", "why is my webhook signature verification failing", "my database says subscribed but Stripe says canceled", "how do I avoid double-charging on retries", or "how should I handle failed payments". Do NOT use for choosing price points, tiers, or packaging — use saas-pricing instead; for general-purpose webhook receiver hardening beyond Stripe, use webhook-receiver-hardener; for idempotency patterns outside payments, use idempotency-enforcer.
---

# Stripe Expert

Billing bugs are the most expensive class of bug: an unverified webhook is an open door to fake "payment succeeded" events, a non-idempotent handler double-provisions on Stripe's at-least-once delivery, and state inferred from a client redirect grants free access to anyone who can type a URL. This skill wires Stripe so money moves correctly and your database never disagrees with Stripe about who has paid.

## Core principle

Stripe is the source of truth for billing state. Your database is a mirror, updated only by verified webhooks. Never grant access from a client-side success callback or redirect — the redirect can be spoofed, dropped, or replayed; the webhook cannot.

## Operating procedure

### Step 1: Gather inputs

1. Billing model — one-time purchase, flat subscription, per-seat, or usage-based? Default for SaaS: flat subscription via Checkout.
2. Existing user model — is there a `stripe_customer_id` column? One Stripe customer per user, created lazily on first purchase.
3. Access-gating point — the exact code path that checks "is this user paid?". It must read your mirrored subscription status, nowhere else.
4. Dunning policy — how many days of grace on `past_due` before access is cut? Default: 7 days with email notices.

### Step 2: Build the purchase flow (Checkout)

1. Create the Checkout Session server-side. Never trust amounts or price IDs from the client — accept only an internal plan identifier and resolve the Stripe price ID server-side.
2. Redirect the customer to the hosted page.
3. Treat the success page as UI only ("thanks, your access is activating"). Entitlement flips when the webhook lands, typically within seconds.

```ts
const session = await stripe.checkout.sessions.create({
  mode: 'subscription',
  line_items: [{ price: priceId, quantity: 1 }],   // resolved server-side
  success_url: `${base}/success?session_id={CHECKOUT_SESSION_ID}`,
  cancel_url: `${base}/pricing`,
  customer: stripeCustomerId,
}, { idempotencyKey: `checkout:${userId}:${planId}` });
```

Pass an `Idempotency-Key` on every create request (sessions, charges, customers) so a network retry cannot create two of anything.

### Step 3: Build the webhook handler against this checklist

Every item is mandatory; each one missing is a specific production incident.

- [ ] Signature verified with `stripe.webhooks.constructEvent(rawBody, sig, endpointSecret)` — reject with 400 on failure. Skipping this lets anyone POST fake payment events.
- [ ] Raw request body used for verification. Body-parsing middleware (e.g. `express.json()`) mutates bytes and breaks the signature — exempt the webhook route.
- [ ] Idempotent: store processed `event.id`s (unique-constrained table) and skip duplicates. Stripe delivers at-least-once; without this, a redelivery double-provisions.
- [ ] Returns 2xx within a few seconds; heavy work (emails, provisioning) goes to a queue. Stripe retries non-2xx responses for up to ~3 days with exponential backoff — a slow handler turns retries into a duplicate storm.
- [ ] Handles out-of-order delivery: apply the event's data (or re-fetch the object from the API) rather than assuming events arrive in sequence.
- [ ] Minimum event set handled: `checkout.session.completed`, `customer.subscription.updated`, `customer.subscription.deleted`, `invoice.payment_failed`.
- [ ] Unhandled event types return 200, not 500 — otherwise Stripe retries events you never intended to process.

### Step 4: Mirror subscription state

On each subscription event, upsert one row per user: `stripe_customer_id`, `stripe_subscription_id`, `status`, `current_period_end`, `price_id`. The access gate reads this row only. States to gate on: `active` and `trialing` get access; `past_due` gets the grace window from Step 1; `canceled` and `unpaid` do not.

### Step 5: Handle failure and lifecycle paths

- `invoice.payment_failed`: start dunning — notify the user, keep access through the grace window, cut on expiry or `subscription.deleted`.
- Declines: catch `StripeCardError` and surface a friendly message. Retry only transient errors (network, 5xx, rate limit); never auto-retry a card decline — that is card-testing behavior and triggers fraud flags.
- SCA/3DS: with Payment Intents, handle `requires_action` by completing authentication client-side. Checkout handles this for you — one more reason to prefer it.
- Plan changes: decide proration explicitly (`proration_behavior`) and communicate the resulting invoice line to the user before confirming.
- Trials: handle `customer.subscription.trial_will_end` (fires 3 days before) to warn the user before the first charge.

### Step 6: Test before going live

Run `stripe listen --forward-to localhost:PORT/webhook` and use test-mode keys. Verify with `stripe trigger`: successful checkout, duplicate event delivery (handler must no-op the second), payment failure, and cancellation. A webhook path that has never seen a replayed event in test will double-provision in production.

## Worked contrast

Bad — grants access from the redirect:

```ts
// success page handler
app.get('/success', async (req, res) => {
  await db.user.update({ where: { id: req.user.id }, data: { plan: 'pro' } }); // spoofable
  res.render('success');
});
```

Good — grants access from the verified, idempotent webhook:

```ts
app.post('/webhook', express.raw({ type: 'application/json' }), async (req, res) => {
  let event;
  try {
    event = stripe.webhooks.constructEvent(req.body, req.headers['stripe-signature'], endpointSecret);
  } catch { return res.status(400).send('bad signature'); }

  const inserted = await db.processedEvents.insertIgnoreDuplicate({ id: event.id });
  if (!inserted) return res.sendStatus(200); // already handled

  if (event.type === 'checkout.session.completed') {
    await queue.enqueue('activate-subscription', event.data.object);
  }
  res.sendStatus(200);
});
```

## Deliverable

Produce a working integration containing: the server-side Checkout Session endpoint with idempotency keys; a webhook handler passing every item on the Step 3 checklist; the mirrored subscription table and the single access-gate read path; the dunning policy wired to `invoice.payment_failed`; and a test log showing duplicate-delivery and failure events handled correctly via the Stripe CLI.

## Do NOT

- Do not gate access on the success redirect — it is spoofable and unreliable; only the verified webhook flips entitlement.
- Do not parse the webhook body before signature verification — verification fails on mutated bytes and you will "fix" it by disabling verification.
- Do not process an event ID twice — at-least-once delivery guarantees you will eventually receive duplicates.
- Do not trust price IDs or amounts from the client — a tampered request buys the enterprise plan at the hobby price.
- Do not auto-retry card declines or create more than one Stripe customer per user.
- Do not do slow work inline in the handler — return 2xx fast, queue the rest.

## Quality bar

- Signature verification on raw body, with a 400 path that is actually exercised in tests.
- Duplicate event delivery proven to no-op in a test run.
- Zero client-supplied amounts or price IDs reach the Stripe API.
- Access gating reads exactly one mirrored status field, and every subscription lifecycle state maps to a defined access decision.
- `stripe listen` test log covers success, duplicate, failure, and cancellation.

## Escalation

This skill covers integration mechanics, not pricing strategy — route tier and packaging questions to saas-pricing. Tax registration, revenue recognition, and regulated money-movement (marketplaces holding funds, Connect platform liability) warrant a payments-savvy accountant or lawyer, not just Stripe defaults.
