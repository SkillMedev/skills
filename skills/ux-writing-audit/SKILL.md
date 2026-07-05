---
name: UX Writing Audit
description: Audits an existing corpus of product copy — buttons, labels, errors, empty states, toasts, tooltips — against clarity, consistency, voice, and actionability heuristics, and delivers a scored findings table with rewrites plus a reusable glossary. Use when someone asks "audit our product copy", "why does our UI text feel inconsistent", "review these error messages", "is it log in or sign in", or before a redesign or localization push. Do NOT use for writing new error messages from scratch — use error-message-writer instead; for first-run and activation flows, use onboarding-copy.
---

# UX Writing Audit

Copy debt is invisible until it isn't: users stall on a button they don't trust, support tickets pile up around one confusing error, and translators quote double because the source text contradicts itself. An audit finds where the words fail and fixes them systematically — not one string at a time, but as a corpus with shared vocabulary and voice. The output is rewrites the team can ship plus a glossary that stops the debt from regrowing.

## Inputs to collect

1. **The strings, with context** — every string in scope, each tagged with its surface (button, field label, error, toast, modal title, empty state, tooltip) and the moment it appears. You cannot audit copy in isolation from the moment it appears. If the team has no string inventory, extracting one is Step 1, not a blocker.
2. **Voice definition** — the product's voice in three adjectives (e.g., "plain, confident, warm"). If none exists, draft one from the best existing copy and label it a proposal.
3. **Locked strings** — legal, regulated, or contractual wording. These get flagged, never rewritten.
4. **Scope priority** — default: errors and empty states first; that is where copy debt concentrates.

## Operating procedure

### Step 1: Inventory

Collect every string into a single list with surface and context. Number them — findings reference string IDs.

### Step 2: Audit each string through the four lenses

**Clarity**
- Can a first-time user understand it without prior knowledge?
- Remove jargon, internal terms, and feature codenames.
- Prefer concrete nouns and verbs over abstractions ("Delete project", not "Manage").
- One idea per sentence. Cut filler: "simply", "just", "please".

**Consistency**
- Build a mini glossary: one term per concept. The classic collisions to resolve:

```
| Concept | Pick ONE | Reject the rest |
|---|---|---|
| Authentication | [FILL: log in / sign in] | the other one |
| Ending a session | [FILL: log out / sign out] | the other one |
| Destruction | [FILL: delete / remove] | using both interchangeably (delete = permanent, remove = detach, if you need both, define both) |
| Cancellation | [FILL: cancel / discard] | mixing them |
| Confirmation | [FILL: save / done / apply] | using all three |
```

- Match capitalization style (sentence case vs title case) everywhere.
- Standardize number, date, and unit formats.
- Reuse phrasing for parallel actions across the product.

**Voice and tone**
- Hold every string against the voice definition.
- Modulate tone by moment: celebratory on success, calm and blame-free on error.
- Never blame the user. "We couldn't save your changes" beats "You entered invalid data."

**Actionability**
- Every error states what happened AND what to do next.
- Buttons name the action they perform ("Save changes") — never "OK" or "Submit".
- Empty states tell the user how to fill them.

### Step 3: Apply the error message formula

Errors get the three-part structure:

1. **What happened** — plain, specific.
2. **Why** (only if it helps) — brief.
3. **What to do** — an actionable next step or recovery path.

### Step 4: Score and tally

Rate each string **Pass / Revise / Rewrite**. Tally by surface to show the team where the copy debt concentrates — typically errors and empty states.

## Findings table template

```
| ID | Surface | Original | Lens violated | Score | Proposed rewrite |
|---|---|---|---|---|---|
| [FILL] | [FILL: button/error/empty state/...] | [FILL] | [FILL: clarity/consistency/voice/actionability] | [FILL: Pass/Revise/Rewrite] | [FILL] |
```

## Good/bad microcopy pairs

Error:
- **Bad:** "Error 403."
- **Good:** "You don't have permission to edit this file. Ask the owner for access."

Button:
- **Bad:** "Submit"
- **Good:** "Create account"

Empty state:
- **Bad:** "No data."
- **Good:** "No reports yet. Create your first report to see it here."

Destructive confirmation:
- **Bad:** "Are you sure? OK / Cancel"
- **Good:** "Delete 'Q3 Plan'? This can't be undone. Delete project / Keep project"

Blame-free failure:
- **Bad:** "Invalid input. You must enter a valid date."
- **Good:** "We couldn't read that date. Use the format MM/DD/YYYY."

## Deliverable

Produce: the scored findings table (original, surface, lens, score, rewrite), the tally by surface showing where debt concentrates, the glossary of resolved term collisions, and a short voice-and-tone reference the team can reuse going forward.

## Do NOT

- Do not lengthen copy for its own sake — shorter is usually better; a rewrite that doubles the word count needs to earn every word.
- Do not rewrite legal or regulated wording — preserve it verbatim and flag it.
- Do not audit strings stripped of context; "Cancel" is fine on a dialog and a disaster on a subscription page.
- Do not fix strings one-by-one without the glossary — you will resolve "log in vs sign in" three different ways in one audit.
- Do not break localization: avoid idioms, concatenated sentence fragments, and strings that embed grammar assumptions ("You have 1 new message(s)").

## Quality bar

Every string in scope has a score; every Revise/Rewrite has a proposed rewrite that passes all four lenses; every error rewrite follows the three-part formula; the glossary resolves every term collision found; no rewrite touches a locked string; the tally makes the debt distribution visible at a glance.

## Escalation

For drafting brand-new error messages and taxonomies from scratch, route to error-message-writer. For first-run experience and activation copy, route to onboarding-copy. For push and notification copy specifically, pair with push-notification-copy. If the audit reveals structural confusion no rewrite can fix (users can't find the action at all), that is a design problem — route to design-critique.
