---
name: Demo Script
description: Builds a product demo script around the buyer's discovered pain — a 60-second situation recap opening, three to four before/after "moments" mapped to the buyer's metric, and a close that ends before the slot does. Use when someone asks "help me script this demo", "how should I structure tomorrow's product walkthrough", "turn these discovery notes into a demo plan", or before any live or recorded demo. Do NOT use for staging and directing the demo environment, data, and rehearsal — use product-demo-director instead. Do NOT use when discovery hasn't happened yet — run discovery-call-prep first, because a demo without known pain is a feature tour.
---

# Demo Script

A feature tour is not a demo. Buyers do not care about features — they care about outcomes, and the job of a demo is to make the buyer see themselves solving a specific problem they described in discovery. The costly failure this skill prevents is the tour that fills the whole slot, impresses the presenter, and leaves the buyer with no reason to take a next step.

## Operating procedure

The order is fixed: inputs gate everything (no discovery, no demo), moments are selected before the open is written (the recap promises what the moments deliver), and pacing is set last.

### Step 1: Gather inputs — the three-fact gate

Pull the discovery notes and extract:

1. The primary pain the buyer named, in their words.
2. The current workaround they use today.
3. The metric or outcome they care about improving.

Also collect: who is attending (roles), slot length (default 30 minutes), and whether the demo is live or recorded. If any of the three facts is missing, the demo should not happen yet — run discovery-call-prep first. If a fact is inferred rather than heard, label it a guess and open the demo by confirming it.

### Step 2: Select three to four moments

A moment is a before-and-after narrative, not a feature: the painful thing the buyer's team does today, how it works in the product, and what changes for the person doing it. For each moment, script four beats:

1. Name the role affected ("your ops analyst who owns this today").
2. Show or describe the painful current state briefly.
3. Show the product solving it — the shortest path, not the fullest one.
4. Connect back to the metric or outcome the buyer named.

Selection rules: every moment must trace to something the buyer said; three moments is the default, four is the ceiling; hold one strong moment in reserve for the Q&A or follow-up rather than spending everything.

### Step 3: Script the open — 60-second situation recap

Open with a recap, not a product login. Formula: "Based on what you shared, your team spends [their number] on [their pain], and the biggest frustration is [their words]. Today I'll show you specifically how that looks different." This proves the rep listened and frames every screen that follows.

Bad open: "Great to see everyone! Let me share my screen — so this is our dashboard. On the left you've got navigation, and up top you can configure your workspace settings..."

Good open: "Last week you told me your ops team burns about eight hours every Friday reconciling the warehouse feed by hand, and month-end is the worst of it. I'm going to show you three specific things today, and the first one is that Friday reconciliation going from eight hours to about twenty minutes. Sound like the right place to start?"

The bad open spends the highest-attention seconds of the call on navigation chrome. The good open replays the buyer's own number, promises a concrete outcome, and gets a micro-commitment before a single screen is shared.

### Step 4: Script the close — end before the slot does

Close the demo with content remaining, not with "and there's so much more." Ending weak is a tour; ending strong is: "What would be most useful to dig into next?" followed by proposing the specific next step with a date. The reserved moment from Step 2 is the answer when the buyer says "what else can it do?"

### Step 5: Set pacing

A 30-minute slot gets 20 minutes of content and 10 minutes of conversation — scale proportionally (a 60-minute slot: 40/20). If the script's content fills the whole slot, cut a moment. Engaged buyers ask questions; a demo with no time for them was mis-planned, and a demo that runs to the final minute went wrong even if it felt smooth.

## Beat structure (copy-paste skeleton)

```
DEMO SCRIPT — [FILL: account] / [FILL: attendees + roles] / [FILL: slot length] min

GATE (from discovery)
  Pain (their words):   [FILL]
  Current workaround:   [FILL]
  Metric they named:    [FILL]

OPEN (60 sec — recap, no login)
  "Based on what you shared, [FILL: pain + their number]. Today I'll show you
   how [FILL: promised outcome]. Sound like the right place to start?"

MOMENT 1 — [FILL: name of the outcome, not the feature]
  Role affected:        [FILL]
  Today (before):       [FILL: 1-2 sentences]
  In product (after):   [FILL: shortest path to shown outcome]
  Tie to metric:        "...which is what gets you [FILL: their metric]."

MOMENT 2 — [FILL]                      MOMENT 3 — [FILL]
  (same four beats)                      (same four beats)

RESERVE MOMENT (do not show unless asked): [FILL]

CLOSE (with time remaining)
  "What would be most useful to dig into next?"
  Proposed next step:   [FILL: specific step + date]

PACING: [FILL: content minutes] content / [FILL: conversation minutes] conversation
SKIP LIST: admin panel (unless an admin asked), integrations other than [FILL: theirs]
```

## Deliverable

Produce a demo script containing the three discovery facts, a 60-second recap open, three to four moments each with role, before, after, and metric tie-back, a reserved moment, a closing question with a dated next step, and the content/conversation time split.

## Do NOT

- Do not open with a login screen or navigation tour — the first 60 seconds carry the most attention and must be spent on the buyer's situation.
- Do not show the admin panel unless the buyer is an admin who asked, and do not show every integration — show the one that connects to their stack.
- Do not apologize for missing features mid-demo; if a gap comes up, acknowledge it cleanly and move on — apologies anchor the buyer on the gap.
- Do not end on "and there's so much more" — it converts a strong close into a weak trailer.
- Do not demo to an audience whose roles are unknown; a moment aimed at nobody in the room lands on nobody.

## Quality bar

Ship the script only when: every moment traces to a quoted or noted buyer statement; each moment has all four beats; the open contains the buyer's own number or words; one moment is held in reserve; the close names a specific next step with a date; and scripted content fills no more than two-thirds of the slot.

## Escape hatch

For an async or recorded demo, the same structure applies but tighter: the specific use case must appear in the first 15 seconds or the viewer stops watching, and chapter markers should map one-to-one to the moments. For staging environments, demo data, and rehearsal direction, pair with product-demo-director. If objections dominate the conversation after the demo, route to objection-handler.
