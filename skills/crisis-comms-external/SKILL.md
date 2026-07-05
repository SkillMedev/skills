---
name: crisis-comms-external
description: Drafts and sequences external communications during an incident or crisis - a first statement within the hour, acknowledge-what-you-know-without-speculating discipline, a single-spokesperson rule, channel sequencing from status page to email to social, and a legal-review escalation boundary, with severity-tiered statement templates. Use when a user says "we have an outage and customers are asking questions", "we had a data breach, what do we say publicly", "draft a public statement about this incident", "the story is blowing up on social, how do we respond", or "when do we need legal to review this statement". Do NOT use for routine technical status posts during normal-severity incidents - use status-page-update instead - for the internal writeup after resolution - use postmortem-writer instead - or for calming one angry customer - use refund-deescalation instead.
---

# Crisis Comms External

In a crisis, silence is a statement - and it says "we don't know" or "we're hiding something," both of which customers and journalists will fill in for you. The costly mistake this skill prevents is the double failure that defines botched crisis comms: waiting hours for complete information before saying anything, then over-speaking with speculation that later proves wrong and must be retracted. The discipline is the opposite pairing - speak fast, but say only what is verified.

## Operating procedure

Steps 1-3 happen in the first hour, in parallel where possible. The clock starts when the organization becomes aware customers are affected, not when the root cause is found.

### Step 1: Classify severity and open the log

Classify immediately, because severity drives everything downstream - template, channels, legal involvement:

- **SEV-A - Trust events:** data breach, security incident, safety issue, anything touching personal data or money. Legal review is mandatory before any external statement (see Step 6).
- **SEV-B - Broad service failure:** major outage or degradation affecting a large share of customers, visible enough to draw press or social attention.
- **SEV-C - Contained failure:** limited outage, single-feature breakage, small blast radius. Usually handled entirely by status-page-update; this skill applies only if it escalates or draws public attention.

Open a comms log: every statement, channel, timestamp, and approver, recorded as it happens. The postmortem and any regulatory response will need it.

### Step 2: Establish the single voice

Name one spokesperson - all external words flow through this person or carry their explicit approval. For SEV-A this is an executive; for SEV-B the comms lead or support lead. Simultaneously instruct the rest of the company: **no employee replies about the incident on social media, community forums, or to press - not even to be helpful.** Well-meaning engineers speculating on social media create the contradictions journalists quote. Every inbound press query gets routed to the spokesperson; support agents get a holding line (Step 4's statement, verbatim) and instructions not to improvise beyond it.

### Step 3: Draft the first statement - out within 60 minutes

The first statement must ship within one hour of awareness. It will feel uncomfortably early; that is correct. Speed is achievable because the first statement never explains - it acknowledges. Build it from exactly four parts:

1. **Acknowledge** - what customers are experiencing, plainly ("Some customers cannot log in").
2. **What we know** - only verified facts, however few.
3. **What we're doing** - investigating, engaged, escalated.
4. **When we'll update next** - a specific commitment ("next update by 3:00pm ET"), and honor it even if the update is "still investigating."

The acknowledge/don't-speculate discipline, stated as rules: never state a cause before it is confirmed ("a database issue" said at minute 40 and wrong at hour 3 is a retraction); never estimate recovery time before engineering commits to one; never minimize ("brief," "minor," "small number of users") while impact is still being measured - minimizing language that proves wrong reads as deception, not error; never say "we take security seriously" - show, don't claim.

### Step 4: Sequence the channels - status page → email → social

Order matters because each channel has a different correction cost and audience:

1. **Status page first, always.** It is the canonical record, it is where affected users already look, and every later message links to it. It updates cheaply as facts evolve. Post here within the 60-minute window.
2. **Email second** - when the incident is SEV-A, or SEV-B lasting beyond roughly 4 hours, or when customers must act (reset a password, expect a delay). Email cannot be edited after sending, so it ships only content already stable on the status page. Segment to affected customers where possible.
3. **Social last** - only if the incident is already being discussed publicly or press coverage exists. Social posts point to the status page rather than carrying details, because threads fragment and screenshots outlive corrections. If nobody is talking about it on social, do not start the conversation there.

Update cadence once engaged: SEV-A and SEV-B get an update at least every 60 minutes while active - even a no-news update - because a stale status page reads as abandonment. Routine technical updates within this cadence are status-page-update's job; this skill owns the wording whenever the message carries trust risk.

### Step 5: Close the loop

When resolved: a resolution statement on every channel that carried the incident (same sequence), containing what happened in plain language at whatever level of cause is confirmed, what customers need to do (or explicitly "no action needed"), and - for SEV-A/major SEV-B - a commitment to a fuller account with a date. That fuller account is postmortem-writer's job; link the eventual public postmortem from the status page. Individually affected, angry, or high-value customers get 1:1 handling via refund-deescalation, not another broadcast.

### Step 6: Know the legal-review boundary

Legal review is **mandatory before sending** when any of these hold: personal data may be involved (breach-notification laws carry deadlines and prescribed content); anyone was or could be harmed physically or financially; a regulated domain is touched (health, financial, children's data); litigation is plausible or has been threatened; the statement would admit fault or accept liability. In these cases the spokesperson still owns the voice, but legal clears the words - and the one-hour clock does not excuse skipping it: get legal on the incident call in parallel at minute 5, not sequentially at minute 55. For pure availability incidents with no data or safety dimension, legal review is not required and waiting for it is a self-inflicted delay.

## Inputs to collect

- What is verified right now, and what is suspected-but-unconfirmed - kept in separate lists; the suspected list is labeled "unconfirmed, internal only" and never leaves the building.
- Severity classification per Step 1 (default: if data exposure is even possible, treat as SEV-A until ruled out).
- Blast radius: which customers, how many, since when (default: "being measured" - say that rather than guessing).
- Spokesperson name and approver chain.
- Channels available: status page URL, email segmentation ability, social accounts.
- Whether legal counsel is engaged (mandatory for SEV-A).

## Concrete thresholds

- First statement: within 60 minutes of awareness. Four parts, none skipped.
- Update cadence: every 60 minutes minimum while SEV-A/SEV-B is active; every commitment to "next update by X" honored.
- Email triggers: SEV-A always; SEV-B beyond ~4 hours; any required customer action.
- Social triggers: existing public discussion or press only.
- Legal review: mandatory for the five Step 6 conditions; engaged by minute 5, in parallel.
- Spokespersons: exactly 1.

## Statement templates by severity

```
FIRST STATEMENT - SEV-B (outage/degradation) - status page
We're aware that [FILL: plain-language symptom, e.g. "some customers are
unable to log in"] starting around [FILL: time + timezone]. Our engineering
team is actively investigating. We don't yet know the cause and will not
speculate - we'll share verified updates here. Next update by [FILL: time,
≤60 min out].

FIRST STATEMENT - SEV-A (data/security, LEGAL-CLEARED) - status page + email
We are investigating a security incident that may have affected [FILL:
scoped description of data/customers, only as verified]. Here is what we
know: [FILL: verified facts only]. Here is what we are doing: [FILL:
containment steps, external experts engaged if true]. What you should do
now: [FILL: concrete customer action, or "no action is required at this
time"]. We will update you by [FILL: time]. We know your trust depends on
how we handle this, and we will share what we learn as we verify it.

RESOLUTION STATEMENT - any severity
The incident affecting [FILL: symptom] is resolved as of [FILL: time +
timezone]. What happened: [FILL: plain-language cause at confirmed level of
detail]. Impact: [FILL: honest scope - who, what, how long]. What you need
to do: [FILL: action or "no action needed"]. [SEV-A/major SEV-B: We will
publish a full account of the incident and the changes we're making by
[FILL: date].] We're sorry for the disruption this caused.
```

## Deliverable

A severity-classified incident comms package: the cleared first statement out within the hour, the channel sequence with owners and triggers, the update-cadence commitments, the spokesperson and no-freelancing instruction distributed internally, the running comms log, and the resolution statement with its postmortem commitment.

## Do NOT

- **Do NOT wait for complete information before the first statement.** The vacuum gets filled by speculation you don't control; acknowledgment requires no answers.
- **Do NOT speculate on cause or recovery time.** Every retracted guess costs more trust than an honest "we don't know yet."
- **Do NOT minimize while impact is unmeasured.** "Brief disruption for a small number of users" that turns out to be six hours for 40 percent of customers converts an outage into a credibility crisis.
- **Do NOT let multiple voices speak.** Contradiction between an engineer's social reply and the official statement is the quote that leads the story.
- **Do NOT blast social media about an incident nobody is discussing there.** It informs unaffected people that something is wrong without reaching the affected ones.
- **Do NOT skip legal on data or safety incidents to save time.** Breach-notification statutes have deadlines and required content; a fast wrong statement can create legal exposure the incident itself did not.
- **Do NOT miss a promised update time.** A blown "next update by 3pm" teaches readers the statements can't be trusted on anything.

## Quality bar

- First statement shipped inside 60 minutes and contains all four parts - acknowledge, known facts, actions, next-update time.
- Zero unverified claims in any external statement; the suspected-but-unconfirmed list never appeared externally.
- Channel order followed (status page before email before social) and every channel that opened the incident carried the resolution.
- One spokesperson; comms log complete with timestamps and approvers.
- SEV-A statements carry a legal clearance record.
- Resolution statement includes honest scope and, for SEV-A/major SEV-B, a dated postmortem commitment.

## Escalation and neighbors

This skill is not legal advice: breach notification, regulatory disclosure, and liability language require counsel, full stop. Route routine technical status posts to status-page-update, the public after-action account to postmortem-writer, and individual angry-customer conversations to refund-deescalation. If press interest outlasts the incident, sustained media-relations work and coverage tracking route to media-monitor.
