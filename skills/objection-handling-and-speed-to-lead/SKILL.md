---
name: objection-handling-and-speed-to-lead
description: Builds the speed-to-lead system, multi-touch follow-up cadence, and four-objection response framework that stop a gym from losing leads to slow contact or stalled consults. Use when a gym owner or coach says "my leads go cold before we ever talk to them", "prospects stall at the price and never sign", "we are slow and inconsistent at follow-up", or a consult stalls on price, time, "ask my spouse", or "let me think about it" — and when building a follow-up sequence, no-show rebooking, or drilling objection responses with the team. Do NOT use when the buyer is a B2B account and the objection is an incumbent competitor or organizational status quo — use objection-handler instead.
---

# Objection Handling and Speed to Lead

Two things lose gym leads: contacting them too slowly and folding at the first objection. This skill installs the speed-to-lead system, the multi-touch cadence, and the four-objection framework that fix both, scoped to gym and consumer-buyer prospects.

## Workflow

### Step 1: Contact every lead within minutes
Response time is the biggest lever in lead conversion. A lead is hottest the second they submit; minutes later they are browsing competitors.
1. Target first contact within 5 minutes during business hours, every time.
2. First touch is a call. No answer → text, then email — all three inside the first few minutes.
3. Assign an owner per lead with an alert or rotation so no lead waits. If the owner cannot answer, it routes to someone who can.
4. Run the calculator below to quantify what current response time costs in lost members, then set the 5-minute target against that number.

### Step 2: Run the multi-touch cadence by lead state
Most leads do not convert on the first touch. Persistence, not pressure, wins. Build one cadence per lead state and run it until they book, buy, or opt out.
1. **Unbooked** (submitted, not booked): aggressive first day (call, text, email), then daily for several days, then spaced out.
2. **No-show** (booked, did not attend): contact within minutes of the missed time — warm, non-judgmental — and rebook on that contact.
3. **Unsold** (consulted, did not buy): follow up over days leading with value and a clear path back, not "are you ready yet."
4. Track touches per lead so none falls through, and set an explicit end point with a final-offer touch.

Use the follow-up sequence template below for ready scripts per state.

### Step 3: Handle the four objections with one framework
Almost every gym objection is money, time, spouse, or "let me think about it." Run the same four moves on each: **acknowledge, reframe, isolate, close.**
1. **Acknowledge** the concern so they feel heard.
2. **Reframe** toward the cost of inaction or the real value.
3. **Isolate**: ask the question that confirms it is the only thing in the way.
4. **Close**: once isolated and answered, ask for the decision again.

The real concern behind each, and the move that works:
- **Money** — usually doubt it will work, not the dollars. Raise perceived likelihood (proof, guarantee); if it is truly cash flow, offer a payment plan. Isolate: "If the money worked, is this the right program for you?"
- **Time** — fear of one more thing they cannot keep up. Reframe the program as the thing that removes effort: done-for-you plans, short sessions, fits a real week. Close on a start date.
- **Spouse** — sometimes real, sometimes a soft no. Isolate: "If your partner is on board, are you in?" If yes, bring the partner onto a quick call. If they still hesitate, surface the other concern.
- **Think about it** — almost always one specific unanswered question. Surface it ("What specifically do you want to think through?"), answer it now while desire is high, then close.

### Step 4: Drill it and put it on the floor
Run the team through each objection until the responses are natural. A scripted response delivered warmly beats an improvised one. Keep the quick-reference cards below visible during consults and follow-up.

## Quality bar
- First-contact target is 5 minutes and is measured, not assumed.
- Every lead state (unbooked, no-show, unsold) has a written cadence with exact timing and channel, and touches are tracked per lead.
- Every objection response runs acknowledge → reframe → isolate → close, and isolates before closing.
- Later touches lead with value, and every cadence has a defined end point.

## Do NOT
- Do not discount price first — it confirms the price was inflated. Raise perceived likelihood of success before touching the number.
- Do not close before isolating; closing on a concern you have not confirmed is the only blocker just earns a new objection.
- Do not stop after one or two touches — most sales come after several. Run the full cadence.
- Do not nag ("are you ready yet"); lead with a useful tip or a matching result.
- Do not let response time slip past minutes during business hours; speed gets the contact, persistence gets the sale.

## Calculator

Self-contained Node script. Save the block as `speed_to_lead_impact.js` and run `node speed_to_lead_impact.js`. Contact-rate assumptions are illustrative; edit them to the gym's own data. No dependencies.

```javascript
// Speed-to-lead impact. ILLUSTRATIVE assumptions. Edit to your data.
// Run: node speed_to_lead_impact.js
const inputs = {
  monthlyLeads: 100,
  currentResponseMinutes: 120, // current average first-response time
  targetResponseMinutes: 5,    // the goal
  closeRateOfContacted: 0.30,  // share of contacted leads that join
  membershipLtv: 1558,         // contribution LTV per member
}

// Illustrative contact-rate curve by response time. Edit these buckets.
function contactRate(minutes) {
  if (minutes <= 5) return 0.90
  if (minutes <= 30) return 0.70
  if (minutes <= 60) return 0.55
  if (minutes <= 240) return 0.40
  return 0.25
}

function impact(i) {
  const cur = contactRate(i.currentResponseMinutes)
  const tgt = contactRate(i.targetResponseMinutes)
  const curMembers = i.monthlyLeads * cur * i.closeRateOfContacted
  const tgtMembers = i.monthlyLeads * tgt * i.closeRateOfContacted
  const extraMembers = tgtMembers - curMembers
  const extraValue = extraMembers * i.membershipLtv
  return { cur, tgt, curMembers, tgtMembers, extraMembers, extraValue }
}

const r = impact(inputs)
const pct = (n) => (n * 100).toFixed(0) + '%'
console.log('Current contact rate:  ', pct(r.cur), 'at', inputs.currentResponseMinutes, 'min')
console.log('Target contact rate:   ', pct(r.tgt), 'at', inputs.targetResponseMinutes, 'min')
console.log('Members now / month:   ', r.curMembers.toFixed(1))
console.log('Members faster / month:', r.tgtMembers.toFixed(1))
console.log('Extra members / month: ', r.extraMembers.toFixed(1))
console.log('Extra value / month:   ', '$' + Math.round(r.extraValue).toLocaleString('en-US'))
```

Example output with the illustrative numbers:

```
Current contact rate:   40% at 120 min
Target contact rate:    90% at 5 min
Members now / month:    12.0
Members faster / month: 27.0
Extra members / month:  15.0
Extra value / month:    $23,370
```

Cutting first response from two hours to five minutes lifts contact rate from 40 to 90 percent — on 100 leads, 15 extra members a month. Exact figures depend on the gym's real contact curve, but the shape holds: faster response, more contacted, more members. Replace the buckets with the gym's data to make it precise.

## Follow-up sequence (scripts per state)

Replace `FILL` fields.

```
UNBOOKED LEAD
  Min 0:  CALL. "Hi [name], it's [coach] from [gym]. You just asked about the
          [challenge]. Got two minutes?"
  Min 2:  TEXT. "Just tried you, [name]. Here's the link to grab your spot: [link].
          What's the best time to chat today?"
  Min 5:  EMAIL. Subject "Your spot in the [challenge]". Body: offer + booking link.
  Day 1-3: one call + one text daily.
  Day 4-10: every 2-3 days, lead with value (a tip, a result).

NO-SHOW
  +5 min: TEXT. "Missed you at [time], [name], everything ok? Let's grab another
          time, I held your spot."
  +1 hr:  CALL to rebook.
  Day 1:  EMAIL with the booking link and a short result story.

UNSOLD (consulted, didn't join)
  Day 1:  TEXT. "Great meeting you, [name]. The [result] is absolutely doable.
          Whenever you're ready, your spot's here: [link]."
  Day 3:  value touch (client transformation that matches their goal).
  Day 7:  CALL. "Checking in. What's the one thing still holding you back?"
  Day 14: final offer / deadline reminder.
```

## Objection cards (quick reference for the floor)

```
MONEY
  Acknowledge: "I hear you, it's an investment."
  Reframe:     "What's it costing you to stay where you are?"
  Isolate:     "If the money worked, is this the right program for you?"
  Close:       offer payment plan, then "Let's get you started."

TIME
  Acknowledge: "You're busy, that's exactly who this is built for."
  Reframe:     "We remove the guesswork, so it saves time, not adds it."
  Isolate:     "If it fit your schedule, would you start?"
  Close:       "Here's a session time that works around your week."

SPOUSE
  Acknowledge: "Makes sense to talk it over."
  Reframe:     "Your health is a win for both of you."
  Isolate:     "If your partner's on board, are you in?"
  Close:       "Let's get them on a quick call now so you can decide together."

THINK ABOUT IT
  Acknowledge: "Of course."
  Reframe:     "Usually 'think about it' means one specific question. What is it?"
  Isolate:     [answer that one thing]
  Close:       "Now that that's clear, let's get you going."
```

Give the team two or three variants per objection so it does not sound canned. The framework stays constant: acknowledge, reframe, isolate, close.
