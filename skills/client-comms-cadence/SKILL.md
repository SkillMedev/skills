---
name: client-comms-cadence
description: Design an agency's client communication operating system — a channel contract per client tier, the weekly pulse / monthly report / quarterly QBR ladder, same-business-day response SLAs, the no-surprises rule for bad news, and internal escalation triggers. Use when an agency owner says "clients keep asking what we're working on", "every client pings us on a different channel at all hours", "we got fired and never saw it coming", or "my team is drowning in client Slack messages". Do NOT use for planning the content of the monthly report itself — use agency-monthly-report instead.
---

# Client Comms Cadence

Agencies rarely get fired for bad work; they get fired for silence. The client who "never hears from you" mentally cancels the retainer months before procurement does, and the costly mistake this skill prevents is reactive communication — answering whatever arrives, on whatever channel, whenever — which reads to the client as chaos and burns the team out. A communication cadence is a contract: the client knows exactly when they will hear from you, so they stop wondering whether the retainer is working.

Work the example agency throughout: Northbeam Digital, an 8-person marketing agency with 11 retainer clients averaging $6,500/month and a 62 percent gross margin target.

## Operating procedure

Set tiers first, then channels, then the ladder — the cadence a client gets is a function of what they pay, and per retainer-economics-calculator, account manager communication time is delivery cost, so over-communicating with a small retainer destroys its margin.

### Step 1: Tier the client book

Split clients into two or three tiers by retainer size and strategic value. Northbeam uses two: Tier A at $7,000/month and up (5 clients: Crestline SaaS, Verra Health, Pillar Home, Onyx Fitness, Bluepine Outdoors), Tier B below (6 clients). Tiering is internal; clients see their own contract, never the tier list.

Check the load while tiering: one account manager can properly run roughly 8-10 retainer accounts on this cadence — fewer if the book skews Tier A, since the weekly call alone adds 30 minutes per client per week. An AM carrying 14 accounts will skip pulses under load, and skipped pulses are the exact silence this system exists to prevent.

### Step 2: Write the channel contract per tier

One primary channel per client, agreed in writing at onboarding (route onboarding itself to client-onboarding-system). The contract names: the channel, who answers, the response SLA, and the escalation path for genuine emergencies.

Budget the cost of the contract before signing it: communication and account management should run about 10-15 percent of a retainer's delivered hours. Past 20 percent, the comms load is eating the work the client is actually paying for — at Northbeam's 62 percent gross margin target, a $4,000/month Tier B client getting Tier A treatment goes underwater on AM time alone.

- Tier A: shared Slack channel, weekly 30-minute call, monthly report call, quarterly QBR.
- Tier B: email as primary, biweekly 20-minute call, monthly report by email with a call on request, quarterly QBR.
- Response SLA for both tiers: same business day for any message received before 3pm, next business morning otherwise. Same business day is the practitioner standard for retainer work — under an hour is a pager, not an agency, and pricing does not support it.
- Emergencies (site down, ad account suspended, public brand issue): phone the account manager, 2-hour response, any day.

### Step 3: Install the cadence ladder

Three rungs, each answering a different client question:

1. Weekly pulse — "are they working?" A 5-bullet async update, sent the same weekday every week: what shipped, what is in progress, what is blocked and by whom, one number, what is next. Ten minutes to write, under 200 words to read — if it takes longer to produce or reads like a report, it will be skipped under load. Never skip it in a bad week; a skipped pulse in a bad week is exactly the silence that kills accounts.
2. Monthly report — "is it working?" Results against goals, owned by agency-monthly-report. Same delivery date every month (Northbeam: fifth business day).
3. Quarterly QBR — "should we continue and expand?" Owned by agency-qbr-upsell.

Each rung has one named owner. At Northbeam, the account manager owns the pulse and the report draft; a founder joins every QBR.

### Step 4: Enforce the no-surprises rule

Bad news travels first and fast: the client hears about a miss, an overspend, a delay, or a mistake from the agency before they could discover it themselves, within one business day of the agency knowing. Deliver it with the three-part frame — what happened, what the impact is in their numbers, what is already being done. A client told early about a problem sees a professional; a client who finds it in the monthly report sees a cover-up. Never let the monthly report be the first mention of a problem.

### Step 5: Set internal escalation triggers

The account manager escalates to the owner, same day, when any of these fires. These same signals feed the early-warning ranking in client-churn-save — escalating here is what makes a save possible there.

- Client response rate drops: two consecutive pulses with no reply or reaction from a client who used to engage.
- A standing call is skipped or cancelled twice in a row by the client side.
- "Budget review," "procurement," "re-evaluating vendors," or a competitor's name appears in any message.
- The champion (day-to-day contact) goes quiet, changes roles, or leaves.
- Any missed SLA on the agency side, or any bad news that has not yet been delivered to the client.

## Inputs to collect

- Client list with retainer sizes (from retainer-economics-calculator).
- Current channels in actual use per client, and where the sprawl is worst.
- AM hours actually spent on communication per client; if untracked, label the estimate a guess.
- Any contractual communication promises already made.

## Worked artifact: channel contract template

Send at onboarding, per client:

```
COMMUNICATION AGREEMENT — [FILL: client name] x Northbeam Digital

Primary channel:      [FILL: shared Slack channel / email]
Who answers:          [FILL: AM name], backed by [FILL: backup name]
Response time:        Same business day (received before 3pm); next morning otherwise
Emergencies:          Call [FILL: AM phone]. 2-hour response, any day.
                      Emergency = site down, ad account suspended, public brand issue.

What you'll hear from us:
  Weekly (every [FILL: weekday]):  5-bullet progress pulse
  Monthly (5th business day):      results report [FILL: + 30-min call, Tier A]
  Quarterly:                       business review (60 min, [FILL: founder name] attends)

Our commitment: if anything goes wrong, you hear it from us first —
within one business day of us knowing.
```

## Deliverable

A one-page comms operating system: the tier list with each client assigned, the channel contract per tier ready to send, the cadence ladder with a named owner and fixed day for each rung, and the five internal escalation triggers posted where the delivery team works.

## Do NOT

- Do not promise an SLA the team cannot staff — a broken same-day promise is worse than an honest next-day one.
- Do not let every client pick a bespoke channel; channel sprawl is how messages get missed and SLAs silently break.
- Do not give Tier B clients Tier A cadence out of guilt — AM time is delivery cost, and unpriced communication is margin leak.
- Do not sit on bad news until the monthly report; the no-surprises rule exists because discovered problems end retainers and disclosed problems build trust.
- Do not treat a skipped client call as a scheduling accident twice in a row — it is a churn signal; escalate per client-churn-save.

## Quality bar

- Every client has exactly one primary channel, one named AM, and a written SLA.
- Every rung of the ladder has an owner and a fixed calendar day.
- The escalation triggers are specific and observable (countable events, not "seems unhappy").
- The weekly pulse takes 10 minutes or less to produce, or it will be skipped under load.

## Escalation and neighbors

Install after retainer-economics-calculator (tiers follow margin and retainer size). The monthly rung is produced by agency-monthly-report; the quarterly rung by agency-qbr-upsell; fired triggers route to client-churn-save. New-client setup, including the channel contract signing, belongs to client-onboarding-system.
