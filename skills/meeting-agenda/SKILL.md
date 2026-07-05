---
name: Meeting Agenda Builder
description: Builds focused, time-boxed meeting agendas where every item has an owner, a timebox, and a named outcome, status updates are pushed to async, and the agenda ships 24 hours before the meeting. Use when someone asks "write an agenda for my planning meeting", "this meeting keeps running over", "how do I make this decision meeting productive", or "our recurring sync feels aimless". Do NOT use for recurring manager-report one-on-ones — use 1on1-agenda instead — or for turning meeting notes into action items — use meeting-notes-to-actions.
---

# Meeting Agenda Builder

An agenda is a contract for everyone's time, and the rule is absolute: no agenda, no meeting. A 60-minute meeting with 8 people spends a full person-day of work; running it on vibes wastes that person-day and, worse, trains attendees that meetings are where attention goes to die. This skill turns "let's get together" into a tight, outcome-driven agenda — or into the discovery that no meeting was needed.

## Inputs to collect

1. **Purpose** — decide, align, brainstorm, or inform. Exactly one. If the answer is "inform," stop: that is a written update, not a meeting; reclaim the hour.
2. **Attendees** — required vs. optional, with each required person's role. Anyone without a role gets cut from required. Meetings above 8 people rarely decide anything; if the list is bigger, most of them are audience — make them optional and send notes.
3. **Time budget** — total minutes. Default to 25 or 50 minutes (not 30/60) to allow transit between back-to-back calls.
4. **Decision owner** — the single person accountable for the outcome. A decision meeting without a named decider produces a discussion, not a decision.
5. **Candidate topics** — everything people want covered, raw.

## Operating procedure

Order matters: items are classified before they are timeboxed, because status items must leave the agenda before the time math is done.

### Step 1: Classify every candidate item

Sort each topic into decision, discussion, or status. Status items — anything that only informs — leave the meeting and ship as a written pre-read or async update. This is the decision-vs-status separation, and it routinely halves the meeting. Recurring syncs that "feel aimless" are almost always status meetings wearing a decision meeting's calendar slot.

### Step 2: Give every surviving item three properties

Each agenda item gets an owner (who drives those minutes), a timebox, and an outcome phrased as a verifiable artifact.

**Bad:** "Discuss the roadmap — 20 min"

**Good:** "[20 min] Decide the top 3 Q3 roadmap bets — Ana — outcome: ranked list of 3, with owner per bet"

"Discuss X" cannot fail and therefore cannot succeed; a ranked list of three either exists at minute 20 or it doesn't.

### Step 3: Do the time math

Sum the timeboxes. The total must be at most the meeting duration minus a 5-minute buffer. Something always runs over; a plan with zero slack guarantees the last item — usually the decision — gets crushed. If items don't fit, cut items rather than shaving every timebox to fiction. Order items with the most important decision first, not last: energy and attendance decay through the meeting.

### Step 4: Attach pre-reads

Pre-reads replace presentations: context ships ahead so the meeting is for discussion, not narration. Each pre-read lists a link, why it matters, and an honest reading estimate (most people budget 5–10 minutes; a "quick 30-minute read" will not be read). Reserve the first 3–5 minutes for silent reading if the group reliably arrives unprepared — it beats pretending.

### Step 5: Reserve the closing block

The last 5 minutes are fixed: restate decisions made, name owners and due dates for every action, list open questions. Skipping this block is how a meeting full of agreement produces zero motion.

### Step 6: Ship it 24 hours ahead

Circulate the agenda at least 24 hours before the meeting. No agenda by then means cancel or decline — enforcing this once does more for meeting culture than any template. After the meeting, send a 3-line recap (decisions, actions with owner + date, open questions) within 24 hours; pair with meeting-notes-to-actions for extracting actions from raw notes.

## Worked artifact: agenda template

```
Meeting: [FILL: name]
Purpose: [FILL: decide / align / brainstorm — one verb, one sentence]
Decision owner: [FILL]
Duration: [FILL: 25 or 50] min   (timeboxes sum to duration - 5)
Required: [FILL: name — role in this meeting, each]
Optional: [FILL: gets notes]

Desired outcomes:
- [FILL: verifiable artifact, e.g. "ranked list of 3 Q3 bets with owners"]

Pre-reads (before the meeting):
- [FILL: link] — [why it matters] (~[FILL] min)

Agenda:
1. [03 min] Context & goal restated — [FILL: owner]
2. [[FILL] min] [FILL: item] — [owner] — outcome: [decision/list/draft]
3. [[FILL] min] [FILL: item] — [owner] — outcome: [FILL]
4. [05 min] Decisions, owners & due dates — [decision owner]
```

## Deliverable

Produce a circulated agenda containing: a one-verb purpose, a named decision owner, a required list where every attendee has a role, timeboxed items each with owner and verifiable outcome summing to duration minus 5, pre-reads with reading estimates, and a fixed closing block — shipped at least 24 hours before the meeting.

## Do NOT

- Do not hold an "inform" meeting; write it down instead.
- Do not let a status update survive classification because its owner likes airtime — that is the mechanism by which syncs go aimless.
- Do not write "discuss X" as an outcome; if the item cannot fail, it cannot succeed.
- Do not timebox to exactly the duration; the missing 5-minute buffer is why the decision gets 90 seconds.
- Do not put the big decision last. Decaying attention should fall on the small items.
- Do not use this structure for 1:1s — the rolling personal agenda in 1on1-agenda serves that meeting.

## Quality bar

- Purpose is a single verb; if "inform," no meeting exists.
- Every required attendee has a stated role; headcount for a decision meeting is 8 or fewer.
- Every item has owner + timebox + verifiable outcome; the sum leaves a 5-minute buffer.
- Pre-reads carry honest minute estimates.
- The last 5 minutes are reserved for decisions, owners, and due dates.
- The agenda shipped 24 hours ahead — or the meeting was cancelled, which also passes.
