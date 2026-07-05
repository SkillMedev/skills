---
name: Tableau Best Practices
description: Builds Tableau dashboards that compute correct numbers at the right grain and stay fast - LOD expressions, filter-order pipeline, extract strategy, and mark-count control. Use when someone asks "why is my Tableau number different from SQL", "FIXED vs INCLUDE vs EXCLUDE", "my dashboard takes 30 seconds to load", "table calc or LOD", or is designing a workbook others will consume. Do NOT use for Power BI or DAX measures - use power-bi-dax instead; for narrative presentation of findings use data-story; for KPI selection and review cadence use kpi-scoreboard-and-cadence; for static table layout and formatting use data-table-design.
---

# Tableau Best Practices

Tableau's two failure modes are wrong numbers and slow dashboards, and both come from the same blind spot: not knowing at what grain a number is computed and when each filter fires. A dashboard that shows a plausible-but-wrong average per customer costs more than one that errors, because nobody checks it.

## Operating procedure

### Step 1: gather inputs

Before building, establish:

- The one question the dashboard answers, and the headline metric that answers it. If stakeholders list five questions, that is five dashboards or a scope fight - have it now.
- The grain of the source data (one row = ?) and the grain of every metric requested. "Average sales" is undefined until someone says per-order, per-customer, or per-region; label any grain you inferred as a guess.
- Data size and freshness need. Live connection only when data must be minutes-fresh and the source is fast; otherwise a Hyper extract, refreshed on schedule.
- Target screen and device - fixed-size dashboards sized to the actual display render faster and break less than automatic sizing.

### Step 2: pin the grain of every number

Tableau aggregates measures to the dimensions in the view. The same SUM([Sales]) is a different number on a region chart than on a customer chart. For every metric, write down the grain it should have; when it differs from the view grain, that is an LOD expression:

- FIXED computes at a stated grain regardless of view dimensions and dimension filters (context filters still apply): `{ FIXED [Customer] : SUM([Sales]) }`
- INCLUDE adds dimensions to the view grain, then aggregates up.
- EXCLUDE removes dimensions from the view grain.

The canonical case - average sales per customer, shown by region:

```
Bad:  AVG([Sales])
      // averages order lines, not customers; a customer with many
      // small orders drags the number down
Good: AVG({ FIXED [Customer] : SUM([Sales]) })
      // sums to customer grain first, then averages customers
```

Choose table calcs vs LODs by grain source: table calcs (RUNNING_SUM, RANK, WINDOW_AVG) operate on the rendered table **after** aggregation and depend on partitioning/addressing - use them for running totals, percent of total, rank. Use LODs when the grain must hold regardless of what is in the view.

### Step 3: place filters in the pipeline deliberately

Tableau applies filters in a fixed order: Extract → Data Source → Context → Dimension → Measure → Table Calc. Two consequences drive everything:

- FIXED LODs compute **before** dimension filters and **after** context filters. If a FIXED metric ignores a user's filter selection, either that is intended (state it in the tooltip) or the filter must be promoted to context.
- A slow workbook that scans too much data usually needs its big categorical filter promoted to a context filter, so everything downstream operates on the reduced set.

### Step 4: engineer for performance

Practitioner red lines - treat a dashboard over 5 seconds to load as broken:

- Extracts over live connections for anything large or slow; aggregate the extract to the visible grain when row-level detail is never shown.
- Keep marks per view in the low thousands; tens of thousands of marks is a scatterplot nobody can read and a render nobody will wait for. Aggregate before plotting.
- Limit a dashboard to 4-5 worksheets; each sheet is at least one query.
- Avoid blending when a join or relationship will do - blends compute client-side and choke on high-cardinality link fields.
- Avoid quick filters with hundreds of values ("Only relevant values" fires an extra query per filter); prefer a wildcard or parameter-driven filter.
- Hide unused fields; run Performance Recorder and read which query or render step actually dominates before optimizing anything.

### Step 5: design the view

- One clear question per dashboard; headline metric top-left, since that is where eyes land first.
- Chart choice is a decision, not a taste: time trend → line; category comparison → sorted bar; contribution to whole at one point in time → stacked bar or treemap, never pie beyond ~4 slices; relationship between two measures → scatter; single KPI vs target → big number with delta. A worked example: "revenue by month by segment" is a line chart with one line per segment - not a clustered bar per month, which makes trend reading a memory exercise.
- Consistent color encoding: one meaning per color across the whole workbook; restrained palette; color-blind-safe defaults.
- Tooltips carry context (grain, filters in effect, last refresh), not clutter.
- Parameters and dashboard actions for interactivity instead of many near-duplicate sheets.

### Step 6: make it maintainable

Name calculated fields for what they mean (`Sales per Customer (FIXED)`), comment any calc a reviewer would pause on, organize fields into folders, and document data source assumptions and the refresh schedule on the workbook or a hidden readme sheet.

## Deliverable

Produce a workbook plus a one-page spec containing: the question the dashboard answers, each metric with its declared grain and calc type (plain aggregate / LOD / table calc), the filter plan (which filters are context and why), the extract and refresh strategy, and a Performance Recorder result showing load under 5 seconds on the target data volume.

## Do NOT

- Do not ship AVG of a measure when the request was an average per entity - that is the FIXED-LOD case, and the plain AVG is silently wrong.
- Do not promote filters to context casually; context filters change what FIXED LODs see, altering numbers, not just speed.
- Do not blend two large sources on a high-cardinality field; restructure as a join or relationship.
- Do not build one mega-dashboard for five audiences; each extra question costs every user load time.
- Do not "optimize" before Performance Recorder shows where time goes; the slow part is usually one query, not the rendering.

## Quality bar

Ship only when: every displayed number has a stated grain and reconciles to a SQL spot-check at that grain; filter behavior on FIXED metrics is intentional and documented; load time is under 5 seconds on production-size data; each view's marks are in the low thousands; color has one meaning per hue workbook-wide; and a second analyst can find every input field from the folder structure alone.
