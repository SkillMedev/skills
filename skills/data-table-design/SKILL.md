---
name: Data Table Design
description: Designs presentation tables that read correctly at a glance — numeric alignment, consistent precision, deliberate sort order, unit-labeled headers, and well-placed totals — and delivers a before/after redesign of the reader's table. Use when someone asks "make this table more readable", "how many decimals should I show", "should totals go at the top or bottom", "this report table is a mess", or is preparing tabular data for an exec deck or published report. Do NOT use for designing database tables, keys, and normalization — use database-schema instead; for narrative structure around the numbers — use data-story instead; for assembling the full multi-section document — use formatted-report-writer instead.
---

# Data Table Design

A table is not a spreadsheet dump. Every formatting choice either helps or hinders the reader's ability to compare, rank, and interpret — and a misaligned or falsely precise table gets numbers misread in the meetings that matter. The output of this skill is a table a reader can extract the right conclusion from in under ten seconds.

## Operating procedure

### Step 1: Gather inputs

1. The table as it stands (or the raw data), and where it will live: slide, printed report, dashboard, or scrolling web page — the medium changes the density rules in Step 5.
2. The reader's single most likely question. Every subsequent decision serves it. If the owner cannot name the question, that is the first problem to fix.
3. The action the reader takes from the table. Columns that inform no comparison or action are candidates for deletion.
4. Units and the true precision of the data — what the collection method actually supports, not what the export prints.

### Step 2: Set alignment

- Right-align all numeric columns without exception — alignment makes magnitude visible at a glance.
- Left-align text columns.
- Center column headers only when the column is narrow and centering does not visually disconnect the header from its values.
- Never mix alignments within a single column.
- Use tabular (fixed-width) figures where the medium allows; proportional digits break vertical comparison.

### Step 3: Fix precision

Consistency within a column matters more than absolute precision:

- Currency: two decimal places for unit prices; zero for large aggregates (1,240,000 not 1,240,000.00).
- Percentages: one decimal place unless the context is scientific.
- Large numbers: use K, M, B suffixes with the unit noted in the header rather than printing eight digits.
- If a column mixes scales (most values in thousands, one in millions), flag the outlier with a footnote rather than changing the column format.
- Never imply false precision — round to the significant figures the data actually supports. Survey-based or estimated figures rarely support more than two significant figures; showing four communicates a certainty that does not exist.

### Step 4: Choose sort order

Default to the order that answers the reader's most likely question:

- Rankings: descending by the primary metric.
- Time series: chronological.
- Categories with no natural order: alphabetical, or grouped by a meaningful variable.
- Never present data in database row order unless that order is meaningful.

### Step 5: Place totals and control density

- Totals belong at the bottom, separated by a visible rule, bold row, or shading.
- Averages and totals never share an unlabeled row — they are different statistics.
- A percentage column gets a total only when it sums to 100% by design.
- Include only columns the reader needs to act on or compare; every extra column adds load. Beyond 8 columns, consider a chart or two smaller tables.
- Freeze or repeat the label column when a table scrolls horizontally.
- Zebra striping is acceptable above ~15 rows; skip it for short tables. On slides, cap at roughly 10 rows and 6 columns — beyond that the audience reads instead of listening.

### Step 6: Write headers

Every header names the metric and the unit. "Revenue" is incomplete; "Revenue (USD K)" is correct. Avoid abbreviations unless universal in the reader's context.

## Worked artifact: before/after

Bad — raw export pasted into the deck:

```
name       revenue        growth      region   id
acme co    1240300.5034   0.0521      west     10442
Beta LLC   87200.25       0.31        east     10318
zenith     15800000.00    -0.002      west     10559
delta inc  445100.9       0.0899      south    10201
```

Failure modes: database row order, mixed capitalization, an ID column no reader acts on, raw floats implying cent-level certainty on estimates, growth as unreadable decimals, no units, left-aligned numbers, the $15.8M outlier formatted identically to $87K.

Good — designed for "who is driving revenue, and is it growing?":

```
Company     Revenue (USD K)   YoY Growth (%)   Region
Zenith               15,800             -0.2   West
Acme Co               1,240              5.2   West
Delta Inc               445              9.0   South
Beta LLC                 87             31.0   East
------------------------------------------------
Total                17,572              —
```

Sorted descending by the question's metric, numbers right-aligned in thousands with the unit in the header, growth in percent with one decimal, ID column deleted, total at bottom behind a rule, and no total on the growth column because a sum of growth rates is meaningless. The reader sees in three seconds that Zenith dominates revenue but is flat, while the growth is coming from the small accounts.

## Deliverable

Produce a redesigned table plus a short change log: the reader question it is optimized for, each formatting change made and the rule behind it, and any columns deleted with the reason.

## Do NOT

- Do not left-align or center numbers; magnitude comparison depends on right alignment.
- Do not print export-level precision; false precision misleads more politely than a wrong number, but it misleads.
- Do not ship database row order; unsorted tables force the reader to do the analysis themselves.
- Do not total percentage or ratio columns unless they sum to 100% by design.
- Do not keep a column because the query returned it; keep it because the reader compares or acts on it.

## Quality bar

- A cold reader answers the intended question within ten seconds.
- Alignment, precision, and units are consistent within every column, with units in headers.
- Sort order is deliberate and stated; totals sit at the bottom behind a separator.
- No column fails the "does the reader act on this?" test.
- Precision does not exceed what the data collection actually supports.
