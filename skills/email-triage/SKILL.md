---
name: Email Triage
description: Triages a batch of emails into delete, do-now, delegate, or defer decisions using the 2-minute rule, returning drafted replies, delegation forwards, and dated tasks. Use when someone says "help me get through my inbox", "triage these emails", "I have 200 unread messages, what needs a reply", or "turn this pile of email into a to-do list". Do NOT use for designing the ongoing system that keeps an inbox at zero — filters, folder setup, unsubscribe habits, daily routines — use inbox-zero instead.
---

# Email Triage

Process, don't browse. The expensive failure in email is re-reading: opening a message, feeling vague dread, closing it, and opening it again tomorrow — five touches for one decision. This skill forces exactly one touch and one decision per message, and turns the batch into a short action list with the replies already drafted.

## Inputs to collect

Before triaging, confirm — and label guesses as guesses:

1. The batch: which messages (a pasted list, a date range, "everything unread"). Default: everything unread.
2. VIP list: names whose messages surface first (boss, key clients, family). Default: infer from sender frequency and title, flagged as a guess.
3. Delegation targets: who can own what kinds of asks. Default: none — nothing gets delegated without a named owner.
4. Task system: where deferred items land (todo app, calendar, plain list). Default: a plain dated list in the output.
5. Reply register: default tone. Match the sender — brief to brief, formal to formal.

## Operating procedure

Order matters: time-sensitive items are found first because a triage pass over 200 messages can take an hour, and a same-day deadline buried at position 180 must not wait that long.

### Step 1: Sweep for urgent and VIP

Scan the whole batch once for VIP senders and time-sensitive content (deadlines within 48 hours, meeting changes today or tomorrow, anything blocking another person). Pull these to the top and decide them first.

### Step 2: Decide every remaining message, top to bottom

One decision per message, in this priority order. Spend no more than 30 seconds deciding — a message that needs more thought than that is, by definition, Defer.

1. **Delete / archive** — no action, no future reference value. Most email is this. Be ruthless; search recovers mistakes.
2. **Do now** — a reply would take under 2 minutes. Draft it immediately. Deferring a 1-minute reply costs more than sending it (the 2-minute rule).
3. **Delegate** — someone else owns it. Draft the forward with a clear ask, a deadline, and enough context that the owner never has to ask "what do you need from me?" Track it as a waiting-for item.
4. **Defer** — real work required. Convert it to a task phrased as a next action with a concrete verb and a suggested due date, then archive the email. The action lives in the task list, not the inbox.
5. **Reference** — no action, but genuinely needed later (confirmation numbers, contracts). File it. This bucket should be small; when in doubt, archive and trust search.

Never leave a message in limbo. "I'll look at it later" is not one of the five decisions.

### Step 3: Assemble the triage report

Return the batch as a single report (template below). Include the archived/deleted count so nothing feels silently lost.

### Step 4: Recommend batch windows

If the user triages reactively all day, prescribe 2–3 fixed processing windows daily (for example 9:00, 13:00, 16:30) with notifications off between them. Continuous checking shatters focus and re-fills the decision queue one message at a time. Recurring structural problems — 40 newsletters, no filters, inbox as to-do list — are a system problem: route to inbox-zero.

## Worked artifact: triage report template

```
EMAIL TRIAGE — [FILL: date] — [FILL: N] messages processed

URGENT / VIP (decided first)
1. [FILL: sender — subject] → [decision] — [why it was urgent]

REPLY NOW (drafts ready to send)
1. To [FILL: sender] re: [subject]
   Draft: "[FILL: 1-3 sentence reply in sender's register]"

DELEGATE
1. Forward to [FILL: owner] re: [subject]
   Ask: "[FILL: specific ask + deadline + context]"
   Waiting-for: [owner] since [date]

TASKS (deferred — add to task system)
1. [FILL: verb-first next action] — due [FILL: date] — from [sender/subject]

REFERENCE (filed)
1. [FILL: item — where filed]

ARCHIVED/DELETED: [FILL: count] messages, no action needed.
```

Example filled rows: "Reply now → To Priya re: offsite dates — Draft: 'Both weeks work for me; slight preference for the 14th. Booking travel once you confirm.'" and "Task → Draft Q3 budget response for Sam — due Thursday — from Sam/Q3 planning thread."

## Deliverable

Produce a triage report containing: urgent/VIP items with decisions, ready-to-send reply drafts, delegation forwards with named owners and deadlines, deferred tasks phrased as verb-first next actions with due dates, a short reference list, and the archived/deleted count.

## Do NOT

- Do not summarize the inbox without deciding it — a summary still leaves every message untouched.
- Do not let a "Do now" balloon past 2 minutes; convert it to Defer and keep moving. Processing and doing are separate modes.
- Do not delegate without a named owner, a specific ask, and a deadline — a bare forward is deferred confusion.
- Do not file more than a handful of messages as Reference; an overgrown reference bucket is an archive with extra steps.
- Do not skip the deleted count. Trust in the triage depends on knowing nothing vanished silently.

## Quality bar

- Every message in the batch has exactly one of the five decisions — zero limbo items.
- Every draft reply matches the sender's register and is sendable without edits.
- Every deferred task starts with a concrete verb and carries a due date.
- Every delegated item names an owner and appears in the waiting-for list.
- Urgent and VIP items appear at the top, decided first.
