---
name: Stakeholder Update
description: Writes project stakeholder updates readable in under 60 seconds — honest RAG status, decisions made, explicit asks with owners and dates, next milestones — tiered to the audience. Use when someone asks "write my weekly project update", "how do I tell stakeholders we're at risk", "draft a status email for leadership", or "my updates get ignored, fix the format". Do NOT use for aggregating many teams' statuses into one engineering-wide report — use eng-status-rollup instead. For monthly investor letters with metrics and asks to VCs, use investor-update-writer; for customer-facing incident status, use status-page-update.
---

# Stakeholder Update

Stakeholders are busy and skim. A good update answers their questions before they ask them, in under 60 seconds of reading. The costly failure is the buried blocker: the ask that sat in paragraph four for three weeks, unread, until the project went red and the stakeholder's first question was "why am I hearing this now?"

## Inputs to collect

1. Audience tier (see below) — this decides length and vocabulary before anything else.
2. Honest status: on track, at risk, or off track — and the one thing driving that call.
3. Decisions made since the last update, with who made them.
4. Asks: what is needed, from whom, by when. If there are none, confirm that is really true.
5. Next 2–3 milestones with target dates.
6. Cadence and send time — same day, same time, every time.

## Audience tiering

One project, different updates. Write the core once, then tier it; sending the team-tier update to executives is how updates stop being read.

- **Executive tier** (sponsors, VPs): status line + one sentence of context + asks only. 60 seconds max, under 100 words. They act on exactly two things: the RAG signal and the asks. Cut decisions and milestones to links unless a decision needs their sign-off.
- **Peer tier** (adjacent leads, PMs, dependent teams): the full four-section format below, core under 150 words. They need decisions (which may affect them) and milestones (which they schedule against).
- **Team tier** (the working group): the four sections plus the Details block — metrics, links, technical context. This is the source document the other two tiers are cut from, never the other way around.

Rule: write for the peer tier by default, compress upward for executives, expand downward for the team. If one email goes to all tiers, structure it so the executive can stop after "Help needed" and lose nothing they act on.

## The four sections

Every update has exactly these, in this order:

1. **Status** — one-line health signal + a sentence of context.
2. **Decisions made** — what was decided since the last update, and by whom.
3. **Help needed** — explicit asks, each with an owner and a deadline. This is what gets you unblocked; make it the most scannable section.
4. **Next milestones** — what is coming and when.

## Skeleton

```
Subject: [FILL: project] Update — [FILL: date] — [FILL: On track / At risk / Off track]

Status: [FILL: Green / Amber / Red]
[FILL: one sentence of context — the single most important thing this period]

Decisions made:
- [FILL: decision] — [FILL: decider] — [FILL: date]

Help needed:
- [FILL: specific ask] — owner: [FILL: named person] — needed by: [FILL: date]

Next milestones:
- [FILL: milestone] — [FILL: target date]

Details (optional — team tier only):
[FILL: links, metrics, deeper context]
```

## The RAG status — the honesty rules

- **Green** — on track, no help needed.
- **Amber** — at risk; there is a specific, nameable thing that could derail it. Name it in the status sentence.
- **Red** — off track; a decision or resource is needed now, and the "Help needed" section says exactly which.

Sandbagging — reporting green when it is amber — destroys trust the moment reality hits, and one surprise red erases a year of credible greens. Flagging amber early is the mark of a strong owner, not a weak one. If the status has been amber for two consecutive updates with the same cause, escalate it as an explicit ask; amber is a flag, not a parking spot.

## Writing rules

- **Lead with the signal.** Status first, context second — bottom line up front, always.
- **Make asks unmissable.** Each ask names one person and one date. "We could use more support" is not an ask; "Need Priya's sign-off on the vendor contract by Friday the 14th" is.
- **Quantify when possible.** "70% complete, 2 of 3 integrations done" beats "good progress" — the second is unverifiable, the first is a claim someone can check.
- **Keep the core under 150 words** (under 100 for the executive tier). Push depth into Details or a linked doc.
- **Report outcomes, not activity.** "Aligned on scope with legal" — not "had 5 meetings with legal."
- **Be consistent.** Same format, same section order, same send time, so readers learn where to look — weekly for active projects, biweekly for slow-burn.

## Worked contrast

**Bad status line:** "Things are moving along, some challenges but the team is working hard." (No signal, no cause, unverifiable, and the reader learns nothing they can act on.)

**Good status line:** "Amber — API migration is 1 week behind because the vendor sandbox arrived late; recoverable if we get security review scheduled by the 20th (see ask below)." (Signal, cause, recovery condition, and a pointer to the ask.)

## Deliverable

Produce the tiered update: the filled skeleton at the correct tier, core under the word budget, at least the status line and every ask carrying a name and a date, sent on the standing schedule.

## Do NOT

- Do not bury a blocker below the fold — no one reads paragraph four, and an unread ask is an unmade ask.
- Do not list activity instead of outcomes — meetings held is effort, not progress.
- Do not surprise stakeholders with a red that was amber for weeks — the earlier flag was the cheap moment to ask for help.
- Do not send the same wall of text to every tier — executives who scroll past detail once will skip the email entirely next time.
- Do not write asks without a named owner and date — "leadership should be aware" produces awareness, not action.

## Quality bar

- Readable in under 60 seconds at the target tier; core within the word budget.
- The RAG status matches what the sender would say privately — no sandbagging.
- Every ask has one name and one date; every milestone has a target date.
- A reader of the last three updates would not be surprised by this one's status.

## Related skills

For rolling many teams' updates into one org-level report, use eng-status-rollup — it aggregates; this skill writes the single-project source it aggregates from. Investor letters have different anatomy (metrics, runway, asks to VCs) — use investor-update-writer. Customer-facing incident comms belong to status-page-update, and a one-off deep-dive summary for leadership is executive-summary territory.
