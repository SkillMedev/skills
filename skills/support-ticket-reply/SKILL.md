---
name: Support Ticket Reply
description: Drafts empathetic, accurate, action-first support replies structured to resolve the ticket in one touch - resolution first, context second, one unambiguous next step. Use when someone asks "help me reply to this customer", "draft a response to this ticket", "how do I tell a customer X", or is composing responses to customer issues, complaints, or feature requests. Do NOT use for emotionally charged conversations or refund decisions - use refund-deescalation instead; for building a reusable library of canned responses, use support-macro-library; for diagnosing why satisfaction scores are falling, use csat-root-cause.
---

# Support Ticket Reply

Every support reply has one job: give the customer exactly what they need to move forward without writing back. The costly mistake this prevents is the partial answer - a reply that addresses two of the customer's three questions guarantees a follow-up, doubles handle time, and damages CSAT more than a slow-but-complete reply would have. Lead with the resolution, follow with context, close with one clear next step.

## Operating procedure

### Step 1: Gather inputs

Before drafting, establish:

1. The full ticket text, including every distinct question or request in it (count them - this number drives Step 4).
2. Ticket severity: is the customer blocked, degraded, or merely curious? Default to "degraded" if unclear and say so.
3. What is actually known: is the resolution confirmed, in progress, or unknown? Never draft "will" statements from guesses - label uncertain facts as unconfirmed and write around them.
4. The customer's emotional temperature. If the ticket is angry, threatens to cancel, or disputes a charge, stop - route to refund-deescalation.

### Step 2: Draft in the fixed structure

Always this order:

1. Acknowledge the specific issue in one sentence - name it precisely, never paraphrase vaguely.
2. State the resolution or the concrete next action immediately.
3. Explain the "why" only if it reduces future confusion or builds trust; cap it at two sentences.
4. Close with a single, unambiguous next step: a link, a yes/no question, or a confirmation request.
5. Sign off warmly but briefly.

### Step 3: Calibrate tone to severity

Match urgency to impact: a blocked customer gets short sentences and a timeline in the first two lines; a curious one can get a friendlier, fuller answer. Use plain language - no jargon, no product-internal terms. For a frustrated customer, name the frustration before the fix: "That should not have happened, and I am sorry it did." Never use hollow openers like "Great question!" or "I totally understand your frustration" without immediately proving it with specifics.

Commit to times, not vibes: if investigation is needed, give an explicit follow-up window ("by Thursday 2pm your time"), and set it at something the team can beat. A follow-up promise without a time is a second ticket waiting to happen.

### Step 4: Run the one-touch check before sending

- Does the reply answer every question in the ticket? If the customer asked three things, address all three, numbered.
- If a full answer needs information not yet available, say what is being done to get it and give a time window.
- Is there exactly one next step, and is it something the customer can do in under a minute?
- Would this reply make sense to someone who has not read internal notes? Policy must be translated into what it means for this customer, never pasted verbatim.

## Worked contrast pair

Ticket: "I was charged twice this month, my export to CSV is broken, and does your API support webhooks?"

Bad:

```
Hi! Great question! I totally understand your frustration. I've looked into
your account and it seems there may have been a billing issue. Our team is
aware of some export problems and we should have a fix soon. Let me know if
there's anything else I can help with!
```

Why it fails: hollow opener, answers zero of three questions concretely, hedges with "may" and "should" on knowable facts, no timeline, no next step.

Good:

```
Hi Dana - three things, answered in order:

1. Double charge: confirmed. I've refunded the duplicate $49 to your card;
   it will appear within 3-5 business days.
2. CSV export: this is a known bug in exports over 10,000 rows. The fix
   ships Thursday; until then, filtering to under 10,000 rows exports fine.
3. Webhooks: yes - docs here: [link].

Once the refund lands, reply here to confirm and I'll close this out.
```

Why it works: names all three issues, resolution first on each, one concrete workaround, one clear next step.

## Deliverable

Produce a send-ready reply containing: a specific one-sentence acknowledgment, the resolution or next action up front, numbered answers matching every question in the ticket, any commitment tied to an explicit time, and exactly one closing next step.

## Do NOT

- Do not open with "I" - start with the customer or the action; the reply is about them.
- Do not paste policy text verbatim; translate it into what it means for this customer, or it reads as a brush-off.
- Do not hedge with "should" or "might" when the answer is known - use "will" or "is"; hedging on known facts reads as incompetence.
- Do not apologize more than once; repeated apologies shift the reply's center of gravity from resolution to performance.
- Do not answer a subset of the questions - the unanswered one is the follow-up ticket.
- Do not handle refund disputes or visibly angry customers with this structure alone; that is refund-deescalation territory.

## Quality bar

Before sending: every question in the ticket has a numbered answer; the resolution appears in the first two sentences; every promise has a time attached; there is exactly one next step; and no sentence exists solely to sound nice. If the same reply is being written for the third time this week, turn it into a macro via support-macro-library.
