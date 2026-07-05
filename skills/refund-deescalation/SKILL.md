---
name: Refund and De-escalation
description: Handles angry customers and refund requests with a validate-own-act de-escalation sequence and a refund decision matrix that weighs refund cost against customer LTV and chargeback risk. Use when someone asks "this customer is furious, what do I say", "should I approve this refund", "how do I deny a refund without losing the customer", or a conversation is emotionally charged. Do NOT use for routine, non-charged ticket responses - use support-ticket-reply instead; for diagnosing systemic patterns behind falling satisfaction scores, use csat-root-cause.
---

# Refund and De-escalation

An angry customer is not attacking the agent - they are in pain and looking for someone to fix it. De-escalation is not about calming the customer down; it is about giving them a reason to trust the company again. The costly mistake this prevents is the two-sided failure: refunding reflexively (training customers to escalate for money) or denying rigidly (converting a $49 refund request into a chargeback, a public review, and a lost account worth far more).

## Operating procedure

### Step 1: Gather inputs

Before responding, establish:

1. The exact thing that went wrong, in the customer's words.
2. The refund amount requested versus the customer's LTV (tenure × monthly value, or total historical spend). Label an LTV estimate a guess if the data is thin.
3. Whether the failure was the company's fault, ambiguous, or the customer's misunderstanding.
4. Contact history on this issue - how many touches so far.
5. Payment method and dispute exposure: a card payment the customer can charge back changes the math (a chargeback typically costs the refund amount plus a $15-25 fee plus a hit to the dispute ratio).

### Step 2: De-escalate before deciding anything

Run the three-step sequence. Emotion must be handled before money - a refund granted to an unheard customer still churns.

1. Validate without agreeing: acknowledge the specific frustration ("Having a charge appear that you did not expect is genuinely alarming"). Never say "I understand how you feel" - it is hollow. Name the exact thing that went wrong.
2. Take ownership: use "I will" and "we will", never "the system" or "the team". Customers want a person responsible.
3. State the next action in 10 words or fewer. Vague promises ("we will look into it") increase anger; specific commitments ("I am issuing the refund now - you will see it in 3-5 business days") reduce it.

### Step 3: Decide the refund with the matrix

Apply in order; the first matching row wins.

| Situation | Decision |
|---|---|
| Company clearly at fault | Full refund, first reply, no negotiation - speed is the recovery. |
| Refund amount < ~5% of customer LTV | Approve even when ambiguous; the goodwill is cheaper than the risk. |
| Ambiguous fault, card payment, customer mentions "dispute"/"chargeback"/"my bank" | Refund or credit - a lost chargeback costs the refund + fee + dispute-ratio damage, so denial has negative expected value. |
| Customer misunderstanding, low LTV, policy clear | Deny once, clearly, with the best available alternative. |
| Repeat refund-seeker (2+ refunds in 6 months) | Deny or partial; note the pattern for account review. Serial refunds are a churn signal, not a service opportunity. |

### Step 4: Communicate the decision

Approved: state the amount, the method, and the timeline in the first sentence - never make the customer hunt for the confirmation. "I have processed a full refund of [amount] to your [payment method] - it will appear within [timeframe]."

Denied: give the specific policy reason, offer the best available alternative (account credit, partial refund, service extension), and close with a genuine acknowledgment that the outcome is disappointing. State the denial exactly once - repeating it reads as rubbing it in.

Partial or goodwill: frame it as a choice the company is making, not a compromise extracted. "As a goodwill gesture I am applying a [amount] credit to your account" lands; "unfortunately I can only offer..." does not.

### Step 5: Close and log

End with a specific check: "Is there anything else I can help you with today?" If the customer is satisfied, note the resolution and the tone shift in the ticket for QA - positive recovery moments are strong training material. If the same failure keeps producing these tickets, route the pattern to csat-root-cause.

## De-escalation script skeleton

Copy and fill; each bracket is one deliberate choice, not boilerplate.

```
[VALIDATE - name the exact failure, no hedging]
"[FILL: the specific thing that went wrong] - that should not have happened,
and I can see why it's frustrating."

[OWN - first person, no system-blaming]
"I'm taking this over personally and here is exactly what I'm doing:
[FILL: one concrete action]."

[ACT - the decision, 10 words or fewer for the commitment]
Approved: "I have processed a [FILL: full/partial] refund of [FILL: amount]
to your [FILL: method] - it will appear within [FILL: timeframe]."
Denied:   "Because [FILL: specific policy reason], I can't refund this -
but I can [FILL: best alternative]. I know that's not the answer you
wanted, and I'm sorry about that."

[CLOSE]
"Is there anything else I can help you with today?"
```

## Language guardrails

- Never say "calm down" - it escalates every time.
- Never say "that is our policy" as a standalone explanation - it sounds dismissive; give the reason behind the policy.
- Never make promises that cannot be kept just to end the conversation; that creates a second, worse complaint.
- Say "I am sorry this happened", never "I am sorry you feel that way" - the latter implies the customer's reaction is the problem.

## Escalation threshold

Escalate to a senior agent or manager immediately when any of these hold: threatening or abusive language, an explicit legal or regulator threat, a request for a specific senior person, or more than three contacts on the same issue. Hand off with a full context summary so the customer never has to repeat themselves - forced repetition is itself an escalation trigger. Refunds involving regulated products, disputed contracts, or legal claims go to management and, where relevant, counsel; this skill is not legal advice.

## Deliverable

Produce a send-ready response containing: the validation naming the exact failure, an ownership statement in first person, the refund decision with amount/method/timeline (or the single denial with its reason and alternative), and the closing check - plus a one-line ticket note recording the decision, the matrix row applied, and the customer's ending temperature.

## Do NOT

- Do not lead with the refund decision before validating - an unheard customer stays angry even when they win.
- Do not deny a small refund to a high-LTV customer on principle; winning the $30 argument and losing the $3,000 account is the canonical failure.
- Do not ignore chargeback signals; once "my bank" enters the conversation, denial usually costs more than approval.
- Do not repeat the denial; once, with the reason and the alternative.
- Do not treat serial refunders as service failures - flag the pattern instead of funding it.

## Quality bar

The reply names the specific failure (no generic apology), commits to one action with a timeframe, applies exactly one matrix row (noted in the ticket), stays within the language guardrails, and - read cold - sounds like a person taking responsibility rather than a policy dispenser.
