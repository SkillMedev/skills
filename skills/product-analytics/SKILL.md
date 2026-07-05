---
name: Product Analytics
description: Stands up product analytics from decision questions backward — a north-star metric with an input-metric tree, an object-action event taxonomy with a governed tracking plan, and the priority analyses to build first: activation funnels, cohort retention curves, and feature adoption correlated with retention. Use when someone asks "what events should we track", "set up our analytics taxonomy", "where do users drop off before first value", or "what's our activation moment". Do NOT use for growth-accounting decompositions of MAU changes — use growth-accounting instead — or for deep funnel diagnosis on existing data — use funnel-analysis instead.
---

# Product Analytics

You cannot improve what you do not measure, but most teams measure the wrong
things badly. A messy event taxonomy is technical debt that misleads every
decision built on it. This skill sets up analytics that actually inform product
work.

## Start With Questions, Not Events

Before instrumenting anything, write the decisions analytics must inform:

- Where do users drop off before first value?
- Which features correlate with retention?
- What does an activated user do that a churned one did not?

Instrument backward from these questions. Tracking everything "just in case"
produces noise nobody queries.

## Event Taxonomy

A disciplined naming convention is the foundation. Get it wrong and you spend a
year cleaning data.

- Use a consistent structure: object-action (e.g. `project_created`,
  `invite_sent`). Pick one convention and enforce it.
- Define properties deliberately: each event carries the context you will
  segment by (plan, source, role).
- Maintain a tracking plan — a single source of truth listing every event, its
  properties, and its owner. This is the contract between product, eng, and
  data.
- Size it: an early-stage tracking plan should be roughly 20–50 events, not
  hundreds. If the plan exceeds ~100 events before product-market fit, it is
  tracking implementation details, not decisions.
- Govern changes: new events go through review so the taxonomy does not rot.

## The North Star and Inputs

- Pick one north-star metric that captures delivered value (e.g. weekly active
  teams, queries run, documents shipped) — not a vanity count.
- Decompose it into input metrics you can actually move (acquisition,
  activation, engagement, retention).

## Core Analyses

### Funnels
Map the path to first value and measure conversion at each step. The biggest
drop-off is your highest-leverage fix. Watch time-to-convert, not just rate:
for a self-serve product, most activation that will ever happen happens in the
first session or first day — a median time-to-first-value beyond a day is
itself the finding.

### Cohort Retention
Group users by signup period and track retention over time. A healthy product
shows a retention curve that flattens (a "smile" for the best products); a
curve decaying to zero means no product-market fit yet.

How to read the curve:

- Judge where it flattens, not the month-1 number — the asymptote is the
  product's real retained base.
- Consumer apps: roughly 40% day-1 / 20% day-7 / 10% day-30 is a good curve;
  well under that (say ~25/10/5) means the leak comes before the habit forms.
  A consumer curve flattening above ~20% is a strong signal.
- B2B SaaS: retention is measured in weeks/months, not days; expect the curve
  to flatten within the first 4–8 weeks, and monthly logo retention below ~95%
  (churn above ~5%/month) is a red flag for anything sold to businesses.
- Compare newer cohorts against older ones — cohorts flattening higher over
  time is the clearest evidence the product is improving.

### Activation
Define the activation event — the action that predicts long-term retention
(the "aha moment"). Find it by comparing what retained users did early versus
churned users: take users still active at week 4+, contrast their first 1–7
days of behavior against churned users' first 1–7 days, and look for the
early action with the biggest retention gap. Then optimize the funnel to it.

### Feature Adoption
Track breadth (how many use a feature) and depth (how often). Correlate
adoption with retention to prioritize the roadmap.

## Avoid Vanity Metrics

- Total signups, total pageviews, cumulative anything — they only go up and
  inform nothing.
- Prefer rates and cohorts over totals; prefer leading indicators over lagging
  ones.

## Tooling and Hygiene

- Choose one analytics tool as the source of truth; pipe to a warehouse for
  deep analysis.
- Validate instrumentation regularly — silent tracking bugs corrupt every
  downstream decision.
- Document metric definitions so "active user" means the same thing to everyone.

## Quality bar

- Every event in the tracking plan traces back to a named decision question;
  none exist "just in case".
- The taxonomy follows one object-action convention with zero exceptions, and
  the plan lists properties and an owner per event.
- The north star measures delivered value (a rate or active count, never
  cumulative) and decomposes into movable input metrics.
- The retention read states where the curve flattens and against which
  benchmark, not just a month-1 percentage.
- The activation event is backed by a retained-vs-churned comparison, not
  picked by intuition.

## Deliverable

Produce an analytics plan: the decision questions, a north-star metric with
input-metric tree, an event tracking plan with naming convention, and the
priority analyses (activation funnel, cohort retention) to build first.
