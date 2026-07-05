---
name: User Flow Mapper
description: Maps a feature's complete user flow in text notation — happy path first, then decision branches, error and empty states with recovery, edge cases, and re-engagement — and flags the top risks before design or build. Use when someone asks "map the user flow for onboarding", "what error states are we missing", "diagram the signup flow", or is scoping any multi-step feature. Do NOT use for screen-level interaction and component behavior specs — use prototype-spec instead; for cross-channel customer stages spanning weeks or months, use lifecycle-journey-map.
---

# User Flow Mapper

Map a feature's full flow before design or build, surfacing the error and empty states teams usually discover in week three of implementation — when each one costs a redesign instead of a diagram edit. The output is a text-notation map any teammate can read in a ticket, not a diagram tool artifact that rots.

## Operating procedure

Order matters: the happy path is the spine everything else hangs from, so branches and errors drawn before it exists attach to nothing.

### Step 1: Frame the flow

Collect three things (ask for whatever is missing; defaults below are guesses to confirm):

- **Entry points** — every way a user arrives: deep link, nav, notification, search. Default guess: primary nav only.
- **Goal** — the single outcome that means success. One goal per map; two goals means two maps.
- **Actors and pre-conditions** — who, with what permissions and prior state (logged in? verified? first-time?).

### Step 2: Map the happy path first

List the minimal sequence from entry to goal when everything works. Use verb-led step names: "Enter email", "Verify code", "Set password". Keep it linear — no branches yet. **Red line: a happy path longer than 7 steps needs a consolidation pass before continuing**; either merge steps or split the feature, because every step past seven compounds drop-off and multiplies the error surface you are about to map.

### Step 3: Branch the decision points

At each step ask: what can the user choose, and what can the system decide here? Draw a branch for each. Common branch types:

- Validation outcomes (valid / invalid input)
- Permission checks (allowed / denied)
- Existence checks (new / returning, exists / not found)

**Red line: more than 3 decision points before the user's first value moment is a friction flag** — note it for the design team even if the flow is otherwise correct.

### Step 4: Enumerate error states

For every step that can fail, define three things — an error without all three is unmapped:

- **Trigger** — what causes it.
- **Message** — what the user sees (blame-free, actionable).
- **Recovery** — the path back to the happy path. A dead-end error is a defect in the map.

Cover at minimum: network failure, timeout, server error, invalid input, and permission denial.

### Step 5: Run the edge-case checklist

Walk every step against this list:

- Empty states (no data yet) and overflow states (too much data) — **every list or collection in the flow gets an explicit empty branch**, no exceptions.
- Slow connection / partial load.
- Interrupted flow (user leaves and returns mid-flow).
- Concurrent edits or stale data.
- First-time vs. power user.
- Boundary inputs: zero, maximum length, special characters, time zones.
- Accessibility paths: keyboard-only and screen-reader traversal.

### Step 6: Map re-engagement and exits

A flow doesn't end at the goal:

- **Success exit** — what's next: confirmation, next action, or dead end?
- **Abandonment** — if the user drops, what brings them back (email, push, saved progress)?
- **Loops** — can they repeat the flow easily?

### Step 7: Notate and flag risks

Write the map in the text notation below, then flag the top three risks where the flow is most likely to break for real users.

## Notation rules

Text notation so the map lives in tickets, specs, and diffs:

- Numbered lines are happy-path steps; verb-led names. Sub-numbering (2a, 2b) marks branches.
- `? condition ->` marks a decision; list every outcome, happy branch first.
- `! ERROR:` marks an error state; every `!` line must end with `-> recovery step`.
- `[EMPTY]` marks an empty-state branch on any list/collection step.
- `~>` marks a re-engagement or abandonment path (the dashed line of the visual grammar).
- If rendering visually instead: rectangle = screen/step, diamond = decision, rounded = system process, red = error state, dashed = re-engagement path. Keep the numbering either way so specs and tickets can reference steps.

## Worked example: password reset

```
FLOW: Password reset
Entry: "Forgot password?" link on login; support-email deep link
Goal: user signs in with a new password
Actors: existing account holder, logged out, may be unverified

1. Request reset (enter email)
   ? email exists -> 2
   ? email not found -> show same confirmation as success (no account enumeration) -> END
   ! ERROR: network failure - "Couldn't send. Check connection." -> retry 1
2. Open reset email, tap link
   ? link valid -> 3
   ? link expired (>30 min) - "Link expired." -> offer resend -> 1
   ~> no email action within 24h: send reminder email once
3. Set new password
   ? meets policy -> 4
   ? too weak - inline rule hints, blame-free -> retry 3
   ! ERROR: token consumed by earlier attempt - "Link already used." -> 1
4. Confirm + auto sign-in
   ! ERROR: server error - "Saved, but sign-in failed. Log in manually." -> login
   Success exit: land on last-visited page, toast "Password updated"
   Loop: settings page offers change-password without email round-trip

TOP RISKS
1. Step 2 email deliverability — highest drop-off; monitor send-to-click rate.
2. Expired-link path (2) — most common support ticket; resend must be one tap.
3. Step 3 policy rejections — show rules before first attempt, not after failure.
```

Happy path is 4 steps (under the 7-step red line), one decision before value, every error has a recovery.

## Deliverable

Produce a text-notation flow map covering: framing (entry points, goal, actors), the numbered happy path, all decision branches, every error state with trigger/message/recovery, the edge-case audit results, re-engagement paths, and the top three flagged risks.

## Do NOT

- Do not map branches before the happy path exists; they attach to nothing.
- Do not write an error without a recovery path — dead ends are the defect this skill exists to catch.
- Do not skip empty states on collections because "there will always be data"; there is never data on day one.
- Do not blame the user in error messages ("Invalid input" → "That email needs an @").
- Do not accept a happy path over 7 steps without a consolidation pass, or two goals in one map.
- Do not stop at the goal; a flow with no success exit or abandonment path is half a map.

## Quality bar

- Every step verb-led and numbered; every decision lists all outcomes, happy branch first.
- The five minimum failure modes (network, timeout, server error, invalid input, permission denial) each appear or are explicitly ruled out.
- Every `!` error line ends in a recovery; every collection has an `[EMPTY]` branch.
- Red lines checked: happy path ≤7 steps, ≤3 decisions before first value — violations flagged, not hidden.
- Top three risks named with a reason each.

When the map is approved and screens need interaction-level specification, hand off to prototype-spec.
