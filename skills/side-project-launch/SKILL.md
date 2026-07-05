---
name: Side Project Launch
description: Takes a side project from idea to shipped launch with behavior-based validation, a ruthlessly cut MVP, a hard timebox, and explicit kill criteria so nights-and-weekends effort never runs open-loop for months. Use when someone asks "how do I launch my side project", "should I keep building this", "how do I validate before building", "I've been building for months and haven't shipped", or "how do I know when to quit". Do NOT use for the Product Hunt assets and mechanics specifically - use product-hunt-launch instead - for orchestrating a multi-channel company launch calendar - use launch-plan-sequencer instead - or for running a launch day in real time - use launch-day-runbook instead.
---

# Side Project Launch

The biggest side-project risk is not failure - it is spending six months of evenings building something nobody wants, with no deadline forcing the question. A side project runs on scarce hours, so the discipline is different from a startup's: validate with behavior before building, cut the MVP to one flow, timebox hard, and write the kill criteria before launch, when judgment is still honest.

## Operating procedure

Validation precedes building, and the timebox precedes scoping - scope must be cut to fit the date, never the reverse.

### Step 1: Gather inputs

1. The idea, as a one-liner: "I help [audience] do [job] better by [approach]." Force the sentence; vagueness here compounds everywhere.
2. Weekly hours honestly available (default assumption: 5-10 for a side project).
3. Skills on hand and preferred tools - boring and familiar wins.
4. The success motive: revenue, portfolio, learning, or fun. The kill criteria in Step 7 depend on it.
5. Where the audience already gathers.

### Step 2: Validate before building

Talk to 5-10 potential users. Ask about their current behavior, never about your idea:

- How do you handle this today?
- What is frustrating about that?
- Have you paid for or actively sought a solution?

Evidence of real pain is people already spending money, time, or duct-tape effort on the problem. A landing page with a waitlist or a small pre-sale is a stronger signal than any number of compliments - compliments are free, behavior is not. Passing bar: several interviewees describe active workarounds or spend, or a simple landing page converts visitors to signups at a few percent. If nobody exhibits the pain, stop here - that is the cheapest possible kill.

### Step 3: Define a ruthless MVP

List every imagined feature, then cut to the one core flow that delivers the promise once. Ask: what is the smallest thing that lets a user get the main value one time?

- Cut accounts, settings, admin, and edge cases from v1 wherever possible.
- Manual is fine: do things that do not scale - a concierge MVP where you personally perform the service beats a month of automation code.

### Step 4: Set the timebox

Pick a launch date 2-6 weeks out and scope to fit it. The deadline forces the hard cuts that an open-ended project never makes. Track in a simple kanban (To do / Doing / Done). At the halfway mark, apply the timebox rule: if less than half the scope is done, cut scope again - never move the date first. A slipped side-project date slips forever.

### Step 5: Build lean

- Boring, familiar tools; no-code where it is faster.
- No premature optimization, no scaling work - you do not have a scaling problem, you have a "does anyone want this" problem.
- Ship something embarrassing but working.

### Step 6: Launch

- Build the audience early - waitlist, build-in-public posts, a small community presence - so launch day is not day one of marketing.
- Prepare three assets: a clear landing page, a short demo, and the honest story of why you built it.
- Launch where your users already gather: relevant communities, forums, newsletters, and Product Hunt (for the PH assets and mechanics, use product-hunt-launch). Respect each community's norms - a launch that reads as spam burns the channel permanently.
- Personally invite every person from the Step 2 interviews.

### Step 7: Measure and decide - with pre-written kill criteria

Pick one north-star metric (signups, activations, or revenue) and ignore vanity metrics. Watch whether users reach the core value (activation), not just visit.

Write the kill criteria before launch, while honesty is cheap:

```
KILL / CONTINUE - decided before launch, reviewed at week 6
Continue if:  [FILL: e.g., 20+ activated users, or 5 paying, or retention week-over-week]
Kill if:      north-star below [FILL] by [FILL: date, default 6 weeks post-launch]
              AND no week-over-week growth for 3 straight weeks
Pivot if:     usage exists but concentrated on an unexpected use case - keep the
              audience, re-run Step 2 on the new job.
```

At the review date, judge against the numbers, not the sunk cost. Double down on pull, kill the absence of it, or pivot while keeping the audience. A clean kill at week six is a success of the process: it returned your evenings.

### Step 8: Early growth loops (if continuing)

- Talk to every early user; fix the single top friction point.
- Ask happy users for referrals and testimonials.
- Iterate weekly on the one biggest drop-off point - one fix per week beats five half-fixes.

## Worked artifact: timeboxed plan

```
SIDE PROJECT PLAN - [FILL: name]
One-liner:     I help [FILL: audience] do [FILL: job] better by [FILL: approach].
Hours/week:    [FILL]      Launch date: [FILL: 2-6 weeks out]
Validation:    [FILL: 5-10 interviews done? waitlist/pre-sale signal?]
MVP (one flow): [FILL: the single core flow]
Cut list:      accounts / settings / [FILL: everything else deferred]
North star:    [FILL: one metric]
Halfway check: [FILL: date] - if <50% done, cut scope, keep the date
Kill criteria: [FILL: from Step 7, written before launch]
Review date:   [FILL: launch + 6 weeks]
```

## Deliverable

Deliver: the one-line value statement, a validation interview script with a pass/fail bar, the cut-down MVP feature list with the explicit cut list, the timeboxed plan above filled in, a launch checklist naming the communities and the personal invites, the single north-star metric, and the pre-written kill/continue/pivot criteria with a review date.

## Do NOT

- Do not ask interviewees whether they like the idea - ask what they do today; opinions about hypotheticals are noise.
- Do not treat compliments as validation; only behavior (signups, pre-sales, workaround spend) counts.
- Do not move the launch date when behind - cut scope. The date is the only forcing function a side project has.
- Do not automate what a concierge MVP can do by hand for the first ten users.
- Do not launch cold into a community you have never participated in; it reads as spam and burns the channel.
- Do not write kill criteria after launch - post-launch criteria bend to sunk cost every time.

## Quality bar

- The one-liner names a specific audience, job, and approach - no "everyone" audiences.
- Validation cites behavior evidence, and guesses are labeled as guesses.
- The MVP is one flow; the cut list is longer than the build list.
- The timebox is 2-6 weeks with a halfway scope check on the calendar.
- Kill criteria are numeric, dated, and written before launch.
