---
name: SQL to Insights
description: Turns SQL query results into a decision-ready business narrative - headline finding, drivers, recommendation - plus the right chart choice for the data shape. Use when someone asks "what does this data actually mean", "summarize these query results for the exec team", "what chart should I use for this", or pastes a result set and wants the so-what for a non-technical audience. Do NOT use to diagnose or speed up the query itself - use sql-query-optimizer instead; do NOT use to build a full multi-part narrative presentation around an analysis - use data-story instead; do NOT use for open-ended exploration of an unfamiliar dataset - use eda-playbook instead.
---

# SQL to Insights

Turn rows and columns into something a decision-maker can act on. The costly failure mode is a "summary" that restates the numbers the stakeholder can already see, buries the one finding that matters under six that do not, or implies causation the data cannot support - all of which erode trust in every future readout.

## Operating procedure

Work the steps in order: the decision question must be stated before interpretation, and the honesty screens must run before the narrative is written, because a caveat discovered after the headline ships is a retraction.

### Step 1: Gather inputs

Collect these before interpreting anything. Label anything assumed as a guess.

1. The result set (and ideally the query), including row count and date range.
2. The audience: exec, functional lead, or analyst peer. Default: business stakeholder with no SQL.
3. The decision this data informs. If the requester cannot name one, propose the most likely one and label it a guess.
4. The comparison baseline: prior period, budget/target, cohort, or benchmark. A number with no baseline is not a finding. Default: prior period.
5. Known data caveats: incomplete current period, recent tracking changes, backfills.

### Step 2: State the question behind the query

Write one sentence: "This data informs the decision to X." Everything in the narrative must serve that sentence; cut anything that does not.

### Step 3: Read the result, not just the schema

Identify three things: the headline number, the trend or comparison that gives it meaning, and the outliers or concentrations driving it. Compute the drivers, do not eyeball them: rank segments by contribution to the change, not by size.

### Step 4: Run the honesty screens

Apply these thresholds before writing a word:

- **Sample size**: call out any segment with n < 30, or any percentage computed on a base under ~100 - a 40% change on 12 events is noise until proven otherwise.
- **Concentration**: if the top 3 accounts/segments/SKUs explain more than half of a change, say "concentrated and fragile" explicitly; it changes the recommendation.
- **Data quality**: a single-period spike more than ~3x the trailing average, exact duplicates, or suspiciously round numbers are more likely pipeline artifacts than real changes. Flag as "verify before acting."
- **Noise floor**: do not narrate a movement smaller than the metric's normal period-to-period wobble; call it flat.
- **Causation**: never claim causation from a correlation; write "associated with" and name what evidence would be needed to establish cause.
- **Fit**: if the query cannot answer the stated question, say so and specify the query that would.

### Step 5: Write the narrative

Use this structure, in this order:

- **Headline** (1 sentence, bolded): the single most important finding, with the number in it.
- **Context** (1-2 sentences): the trend or comparison that makes it meaningful.
- **Drivers** (bullets): what is pushing the number - segments, periods, outliers - ranked by contribution.
- **Recommendation** (1-2 sentences): what to do or look at next, tied to the decision from Step 2.

### Step 6: Recommend the visualization

Match chart to intent:

- Trend over time → line chart.
- Part-to-whole → stacked bar or, sparingly, a single donut.
- Comparison across categories → horizontal bar, sorted by value.
- Correlation → scatter.
- Distribution → histogram or box plot.
- Single KPI vs target → big number with a delta.

Avoid: 3D charts, dual axes unless truly unavoidable, pie charts with more than 4 slices.

## Worked example

**Bad:** "Revenue was $1.24M in June versus $1.05M in May, an 18% increase. Enterprise revenue was $610K, self-serve was $420K, and mid-market was $210K. The number of accounts was 342."

That is a restatement: no baseline meaning, no drivers ranked, no fragility flag, no action.

**Good:**

> **Revenue grew 18% MoM, driven almost entirely by the Enterprise tier.**
> Self-serve was flat. Three accounts explain 60% of the lift, so the
> growth is concentrated and fragile. Recommend a chart of revenue-by-tier over
> the last 6 months, and a watchlist for those three accounts.

## Deliverable

Produce a narrative block (headline, context, drivers, recommendation) sized for the audience, one visualization recommendation with the chart type and axes named, and an explicit caveat line listing any honesty-screen flags (sample size, concentration, data-quality suspicion). If the data cannot answer the question, the deliverable is the corrected question and the query that would answer it.

## Do NOT

- Do NOT lead with methodology or the query; the stakeholder reads the first sentence and maybe the second.
- Do NOT average averages or report percentages without their denominators - both silently misweight segments.
- Do NOT present a spike as a finding before ruling out the data-quality explanations in Step 4.
- Do NOT recommend a chart by habit; a KPI-vs-target result needs a big number with a delta, not a line chart.
- Do NOT soften a bad number with hedging; state it plainly and put the judgment in the recommendation.

## Quality bar

A finished readout passes only when: the headline contains the number and would survive being the only sentence read; every driver bullet is ranked by contribution with its share stated; every honesty screen was run and every triggered flag appears in the output; the recommendation names a specific next action or next query, not "monitor closely"; and nothing in the narrative merely restates a cell the reader can see in the table.
