---
name: Async Communication
description: Converts sync meetings and vague pings into structured asynchronous messages - TL;DR up front, an explicit ask with owner and deadline, and a default-action line that keeps work moving if nobody replies - plus team response norms per channel. Use when someone asks "how do I say this without a meeting", "rewrite this message so people actually respond", "help my remote team stop living on calls", or "draft an async decision doc". Do NOT use for the recurring exec status update itself - use eng-status-rollup instead - for extracting actions from a meeting that already happened - use meeting-notes-to-actions instead - or for designing the meeting you do keep - use meeting-agenda instead.
---

# Async Communication

Async lets distributed teams move without living on calendars, but the trade is real: it demands writing clear enough that the reader can act without a reply loop. The costly failure is the dead thread - a message with no explicit ask, no deadline, and no default, which stalls the work while everyone assumes someone else will respond. Every rule below exists to prevent that thread.

## Operating procedure

### Step 1: gather inputs

- What the sender actually wants from the reader - a decision, a review, an FYI, or an action. If they cannot name it in one sentence, that is the first fix.
- Who must act, who merely needs to know, and the real deadline.
- The team's time-zone spread and existing channel conventions, if any.
- The message or meeting being replaced, when rewriting.

Label assumptions as assumptions ("assuming Legal doesn't need to sign off on this").

### Step 2: decide sync vs async

Default to async when any of these holds: the topic is informational (status, FYI, announcement); the decision benefits from reflection time; participants span time zones; or a written record matters later.

Reserve sync for exactly four cases: high-emotion conversations, fast-moving brainstorms, relationship building, and genuine deadlock after at least one async attempt. "This is faster to explain live" is usually a sign the sender hasn't done the thinking yet - writing it forces the thinking.

### Step 3: structure the message

Every non-trivial message follows this pattern so the reader can act without a reply loop:

```
TL;DR: [FILL: one line - what you want from the reader]

Context: [FILL: why this is coming up - 2-3 sentences, max]

Details: [FILL: the substance; link out rather than inline anything long]

Decision/Action needed:
- [FILL: specific ask] - owner: [FILL: name] - by: [FILL: date]

Default: [FILL: what happens if no one responds by the deadline]
```

The default line is the secret weapon: "If I don't hear back by Friday EOD, I'll proceed with option B." It converts silence from a blocker into a signal, keeps work moving, and respects everyone's time. A message that needs a response but has no default line is a hostage to its slowest reader.

### Step 4: apply the writing rules

- Front-load the ask; the reader knows what is wanted from line one.
- Be self-contained: link or restate context so zero back-and-forth is needed to start.
- Set explicit deadlines. "Soon" and "when you get a chance" guarantee never.
- Pick the channel by artifact life: chat for quick and ephemeral, docs for decisions that need a record, issues/tickets for trackable work. A decision made in chat is a decision that will be re-litigated.
- Over-communicate tone. Without faces and voices, neutral reads as cold - add one line of warmth and assume good intent when reading.
- Length check: if the ask takes more than one screen to state, split the decision doc from the notification message that links to it.

### Step 5: replace the meeting, not just the invite

Map each recurring meeting to its async form:

- Status meeting → written standup: each person posts done / doing / blocked on a fixed daily deadline.
- Decision meeting → decision doc: options, a recommendation, comments requested by a named date, then the decider decides. Escalate to a live call only if the thread deadlocks after one full round.
- Brainstorm → seeded idea doc open for 24 hours, then a short sync to converge only if needed.
- 1:1 → rolling shared doc for topics, keeping occasional live time for the human conversation - do not fully async the relationship.

### Step 6: set team response norms

Norms only work when agreed, written, and visible - not assumed. Defaults to propose: chat gets a same-working-day response; email and doc comments get 24 hours; anything urgent enough to need under an hour is a phone call, not a ping. Working-hours badges and Do-Not-Disturb are respected without justification. Threads, not new channel messages, keep topics navigable.

## Worked contrast

Bad:

```
hey, do you have a sec to hop on a quick call about the vendor thing?
```

The reader must reply just to learn the topic, the urgency, and whether they are the right person - three round trips before substance, likely across time zones.

Good:

```
TL;DR: Need your yes/no on switching to Vendor B by Thu EOD.

Context: Vendor A raised renewal pricing 40%. B matches our current cost
and passed the security review last month.

Details: comparison doc - [link]. Migration effort is ~2 days, owned by my team.

Decision needed:
- Approve switch to Vendor B - owner: you - by: Thu EOD

Default: no response by Thu EOD = I proceed with Vendor B before the
Friday renewal deadline.
```

Same request, zero round trips: the reader can decide from the message alone, and silence has a defined meaning.

## Deliverable

Produce one or more of: a rewritten async message in the five-part pattern (TL;DR, context, details, ask with owner and date, default); a meeting-to-async conversion plan naming the replacement format per meeting; or a one-page team response-norms agreement with per-channel windows.

## Do NOT

- Do not send an ask without a deadline and a default - that message starts the dead thread this skill exists to prevent.
- Do not "quick call?" something expressible in two written sentences; each unnecessary sync call taxes every attendee's calendar and time zone.
- Do not post walls of text with no TL;DR - readers bounce, then answer only the last question they saw.
- Do not mark everything urgent. Urgency inflation destroys the signal for the message that genuinely is.
- Do not make decisions in chat; move them to a doc, or accept re-litigating them monthly.
- Do not async conflict or emotionally loaded feedback - tone loss turns a hard conversation into a worse one. That is one of the four legitimate sync cases.

## Quality bar

- The ask is in the first line, and a stranger to the project could act on the message without asking a clarifying question.
- Every request carries an owner, a date, and a default action on silence.
- The channel matches the artifact's lifespan (ephemeral → chat, decision → doc, work → ticket).
- Tone includes at least one deliberate line of warmth.
- Any proposed norms are written as a shareable agreement, not implied.
