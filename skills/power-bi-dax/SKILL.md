---
name: Power BI DAX
description: Writes correct, fast DAX measures with proper filter context, time intelligence on a marked date table, and VertiPaq-friendly patterns, diagnosing slow visuals down to the storage-vs-formula engine split. Use when someone asks "why does my DAX measure return the wrong total", "write a YTD or year-over-year measure", "my Power BI report is slow", "CALCULATE isn't doing what I expect", or "measure vs calculated column". Do NOT use for Tableau calculated fields and dashboard design - use tableau-best-practices instead; for writing warehouse SQL and turning query results into findings - use sql-to-insights instead; for Excel or Google Sheets financial models - use spreadsheet-model-builder instead.
---

# Power BI DAX

DAX fails quietly: a measure that returns plausible numbers under one slicer combination and wrong ones under another, or a report that takes 20 seconds because one measure iterates a fact table row by row. This skill produces measures that are correct under every filter context and fast in VertiPaq, and gives a procedure for diagnosing the ones that are not.

## Operating procedure

### Step 1: Gather inputs

1. The model schema: fact and dimension tables, relationships and their directions, and row counts on the facts.
2. Whether a dedicated Date table exists, is marked as a date table, and covers a contiguous range - time intelligence silently misbehaves without all three.
3. The business definition of each measure in one sentence, including what it should do under totals and slicers ("margin % weighted by revenue, respecting the category slicer").
4. Performance symptoms if any: which visual, which page, how slow. Default target: any single visual renders in under 1 second; anything over 3 seconds is a defect to diagnose, not a fact of life.

### Step 2: Choose measure vs calculated column

Prefer measures. They evaluate in the report's filter context and do not bloat the model. Use calculated columns only when you need a row-level value for slicing or relationships. Every calculated column is materialized and compressed into the model; every aggregation belongs in a measure.

### Step 3: Write the measure with context in mind

Two contexts exist: filter context (slicers, rows, columns, CALCULATE) and row context (calculated columns, iterators like SUMX). `CALCULATE` is the only function that turns row context into filter context via context transition. A measure referenced inside SUMX is wrapped in an implicit CALCULATE - that context transition per row is both a correctness feature and a performance trap.

Base measure:

```dax
Total Sales = SUM ( Sales[Amount] )
```

Use variables for clarity and speed - evaluated once and reused:

```dax
Margin % =
VAR Revenue = [Total Sales]
VAR Cost = SUM ( Sales[Cost] )
RETURN DIVIDE ( Revenue - Cost, Revenue )
```

Always use `DIVIDE` instead of `/` to avoid divide-by-zero errors.

### Step 4: Time intelligence on a marked date table

```dax
Sales YTD =
CALCULATE ( [Total Sales], DATESYTD ( 'Date'[Date] ) )

Sales PY =
CALCULATE ( [Total Sales], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )

Sales YoY % =
DIVIDE ( [Total Sales] - [Sales PY], [Sales PY] )
```

If a time-intelligence measure returns blanks or wrong periods, check the three date-table conditions from Step 1 before touching the DAX - that is the cause in the large majority of cases.

### Step 5: Control filter context deliberately

```dax
% of Total =
DIVIDE (
    [Total Sales],
    CALCULATE ( [Total Sales], ALLSELECTED ( Product[Category] ) )
)
```

- `ALL` removes all filters from a column or table.
- `ALLSELECTED` respects outer slicers but ignores inner row/column grouping.
- `KEEPFILTERS` intersects rather than overrides a filter inside CALCULATE.
- `REMOVEFILTERS` is the modern alias for clearing filters.

Pick by asking: should the denominator move when the user slices? ALLSELECTED if yes, ALL if never.

### Step 6: Diagnose performance

Run Performance Analyzer, then copy the slow query into DAX Studio with server timings on. Read the split: the storage engine (VertiPaq scans) should dominate. When the formula engine consumes more than ~50% of query time, the measure is doing row-by-row work the storage engine cannot compress - rewrite it. Model-side levers, in order of payoff:

- Reduce cardinality of columns used in relationships; split datetime into separate date and time columns. A column with over ~1 million distinct values is a compression red flag.
- Prefer integer keys over strings.
- Avoid bidirectional relationships unless genuinely required; they multiply scan work and create ambiguity.
- Avoid iterators over large fact tables (10M+ rows) that reference measures per row; restate the logic as column arithmetic inside one SUMX, or pre-aggregate.
- Use `SELECTEDVALUE` instead of `HASONEVALUE` + `VALUES` patterns.

## Worked artifact: bad/good measure pair

Business ask: sales for red products, respecting all other slicers.

Bad - iterates the entire fact table in the formula engine and forces a context transition per row:

```dax
Red Sales (Bad) =
CALCULATE (
    [Total Sales],
    FILTER ( Sales, RELATED ( Product[Color] ) = "Red" )
)
```

Good - a column predicate VertiPaq resolves as a compressed scan:

```dax
Red Sales (Good) =
CALCULATE ( [Total Sales], KEEPFILTERS ( Product[Color] = "Red" ) )
```

On a 50M-row fact table the bad version commonly runs seconds in the formula engine; the good version runs tens of milliseconds in the storage engine. Same numbers, two orders of magnitude apart. (`KEEPFILTERS` preserves an existing user filter on Color; drop it only if the measure must override the slicer.)

Second pair - weighted math. Bad: a calculated column `Sales[Margin %]` per row, then averaged in the visual - wrong weighting (simple average of row percentages) and model bloat. Good:

```dax
Weighted Margin =
SUMX ( Sales, Sales[Qty] * ( Sales[Price] - Sales[Cost] ) )
```

Row-by-row math belongs in an iterator inside a measure, using column arithmetic rather than nested measure references.

## Deliverable

Produce a measure set where each measure has: the DAX, a one-sentence business definition, the expected behavior under totals and slicers, and - for any measure flagged slow - the DAX Studio storage/formula engine split before and after the rewrite.

## Do NOT

- Do not use calculated columns for aggregations that belong in measures; the model pays in size and refresh time forever.
- Do not filter whole tables inside CALCULATE when a column predicate works; `FILTER ( Sales, ... )` forces formula-engine iteration.
- Do not average percentage columns in visuals; compute ratios as measures so weighting is correct at every level.
- Do not build time intelligence without a marked, contiguous Date table; the failure is silent wrong numbers, not an error.
- Do not reference measures inside iterators over large tables without checking the timing split; implicit context transition per row is the classic hidden cost.

## Quality bar

- Every measure verified at the total level and under at least two slicer combinations, not just the detail rows.
- No division uses `/`; all use DIVIDE.
- Date table is marked, contiguous, and every time-intelligence measure filters through it.
- No visual over 1 second without a documented diagnosis; formula engine share under ~50% on the measures that matter.
- Model has no unexplained bidirectional relationships and no string keys on large relationships.
