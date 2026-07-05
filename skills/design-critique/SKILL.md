---
name: Design Critique
description: Runs a structured design critique of a screen, flow, or component across three dimensions - jobs-to-be-done, clarity, and delight - and delivers a severity-ranked critique table with one prioritized recommendation. Use when someone asks "critique this design", "review this mockup before we build it", "this flow feels off but I can't say why", "which design debt matters", or a new design or redesign is up for review. Do NOT use for verifying a built implementation against its spec - use design-qa-checklist instead; for planning moderated usability sessions, use usability-test-plan.
---

# Design Critique

A vague reaction ("I don't like it") wastes a design review; the team leaves with hurt feelings and no direction, then ships the same problems. A rigorous critique anchors every note to a dimension, a severity, and a concrete fix - so a designer or PM walks out knowing exactly what to change and in what order. This skill runs pre-build, on mockups and prototypes, where a fix costs minutes instead of sprints.

## When to use

- A new design or redesign is up for review.
- A flow feels "off" but no one can articulate why.
- You need to prioritize design debt against shipping goals.

## Inputs to collect

Gather three things first. If any is missing, ask for it before critiquing - a critique without them degenerates into taste.

1. The primary **job** the user is hiring this design to do. If the requester cannot state it in one sentence, help them draft one and label it a guess.
2. The **context** of use: device, urgency, frequency, emotional state. Default assumption if unknown: mobile, first-time, mildly impatient - the least forgiving case.
3. The **success metric** the team cares about. Default: task completion rate for the primary job.

Also useful but optional: the previous version (for redesigns) and any known constraints (brand, legal, engineering). Note constraints so the critique does not recommend the impossible.

## The three dimensions

Work them in this order - a delightful screen that fails the job is a decorated failure.

### 1. Jobs-to-be-done (does it work?)

- Restate the user's job in one sentence.
- Trace the shortest path from intent to completion. Count the steps.
- Flag any step that does not advance the job.
- Check failure paths: what happens when input is wrong, empty, or slow?

### 2. Clarity (can they understand it?)

- Read the screen top to bottom in 5 seconds. What is the one obvious action?
- Verify visual hierarchy matches task priority (size, weight, color, position).
- Check labels: are they written in the user's language, not the system's?
- Test for ambiguity - could any control be misread as something else?

### 3. Delight (do they enjoy it?)

- Identify one moment that could feel effortless or rewarding.
- Check motion, microcopy, and empty states for personality without noise.
- Ensure delight never costs clarity or speed.

## Output format

Produce a critique table. For each finding:

```
| Dimension | Severity | Observation | Suggested fix |
|-----------|----------|-------------|---------------|
| [FILL: JTBD / Clarity / Delight] | [FILL: Blocker / Major / Minor] | [FILL: what you observed, neutrally] | [FILL: concrete change] |
```

Severity scale:

- **Blocker** - breaks the job, ship-stopping.
- **Major** - measurable friction, fix this cycle.
- **Minor** - polish, backlog it.

### Worked example

Critiquing a checkout screen (job: "pay and get out in under a minute"; metric: checkout completion rate):

| Dimension | Severity | Observation | Suggested fix |
|-----------|----------|-------------|---------------|
| JTBD | Blocker | Payment fails silently on card decline; user sees a spinner, then the same form with no message. | Show an inline decline message with a retry path; the failure path is part of the job. |
| Clarity | Major | "Continue" and "Apply" buttons have identical styling; three test users tapped Apply expecting to pay. | Make "Continue" the sole primary button; demote "Apply" to a text link inside the promo field. |
| Delight | Minor | Order confirmation is a plain toast. | Add the delivery date to the confirmation - reassurance is the rewarding moment here. |

**Bad note:** "The buttons feel cluttered and the flow is kind of confusing." (No dimension, no severity, no observation separable from opinion, no fix.)

**Good note:** "Clarity, Major: the promo-code field sits between the card form and the pay button, interrupting the payment path for the ~90% of users without a code. Fix: collapse it behind an 'Add promo code' link." 

## Rules of engagement

- Lead with what works before what doesn't.
- Separate observation from prescription - describe the problem, then propose.
- Never give more than 3 Blockers per critique; if there are more, the design needs a reset, not a critique - say so explicitly and recommend returning to the job definition.
- Tie every Major and Blocker to the success metric or the job. A finding you cannot tie to either is a Minor or a personal preference; label it honestly.

## Deliverable

Produce a critique containing: the restated job, context, and metric; two or three things that work and should be kept; the severity-ranked critique table; and a closing single prioritized recommendation - "If you change one thing before shipping, change X." This forces focus and gives the team a clear next step.

## Do NOT

- Do not critique without the job, context, and metric - you will produce taste, not analysis.
- Do not blend observation with prescription in one breath; the team must be able to accept the problem while rejecting your fix.
- Do not inflate severity to be heard - a critique with six Blockers teaches the team to ignore Blockers.
- Do not redesign the screen in the critique; propose the smallest fix per finding and let the designer design.
- Do not skip the failure paths - most "the flow feels off" complaints live in error, empty, and slow states.

## Quality bar

Before delivering: every finding has all four columns filled; every Blocker and Major traces to the job or the metric; at most 3 Blockers; strengths listed before problems; the closing recommendation names exactly one change. If the critique could apply to any generic screen, it is not done.

## Escalation

For checking a built implementation against redlines and specs, route to design-qa-checklist. For accessibility findings that surface during critique, run accessibility-audit rather than folding them in as Minors. For copy-level problems across many surfaces, pair with ux-writing-audit.
