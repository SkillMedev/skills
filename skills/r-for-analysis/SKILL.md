---
name: R for Analysis
description: Writes idiomatic tidyverse R for data analysis - dplyr wrangling pipelines, tidyr reshaping, explicit joins, layered ggplot2 visualization, and broom-tidied statistical models - with reproducibility practices baked in. Use when someone asks "write this analysis in R", "how do I pivot this data frame", "fit a regression per group in R", or wants messy base-R scripts converted to clean pipe-based tidyverse code. Do NOT use for Python-based dataframe work - use pandas-expert instead; for interpreting results for stakeholders use sql-to-insights.
---

# R for Analysis

Produce R analysis code that another analyst can re-run a year later and get the same answer. The costly mistake this prevents: interactive one-off scripts with silent type inference, implicit grouping, and unseeded randomness - code that works once on one laptop and never again.

## Inputs to collect

Before writing code, establish:

1. The data source and its shape - file, database, or in-memory; roughly how many rows (millions of rows change the tool choice: consider data.table or duckdb backends past ~10M rows).
2. The analytical question in one sentence. If the user cannot state it, help them state it first - code without a question produces plots without a point.
3. The output target - exploratory notebook, Quarto report, or a table/figure for a deck. This decides how much polish the code needs.

Label any assumption about column meanings as a guess and confirm against a `glimpse()` of the real data.

## Operating procedure

Work in this order - each step protects the ones after it.

### Step 1: Reproducible setup

```r
library(tidyverse)
library(lubridate)
library(broom)
set.seed(42)  # any stochastic operation must be seeded
```

### Step 2: Read with explicit types

Never trust type inference on data that matters:

```r
sales <- read_csv("sales.csv", col_types = cols(
  date = col_date(),
  amount = col_double(),
  region = col_character()
))
```

Immediately `glimpse(sales)` and check row count against expectation. Standardize names with `janitor::clean_names()`.

### Step 3: Validate before analyzing

Encode assumptions as executable checks, not comments:

```r
stopifnot(
  nrow(sales) > 0,
  !any(is.na(sales$date)),
  all(sales$amount >= 0 | is.na(sales$amount))
)
```

A check that fails loudly today beats a wrong number in a deck next week.

### Step 4: Wrangle with one-verb-per-line pipelines

```r
summary_tbl <- sales %>%
  filter(amount > 0) %>%
  mutate(month = floor_date(date, "month")) %>%
  group_by(region, month) %>%
  summarise(total = sum(amount), n = n(), .groups = "drop") %>%
  arrange(region, month)
```

Rules that prevent the classic silent errors:

- Always pass `.groups = "drop"` to `summarise()` - silently grouped output corrupts every downstream step.
- Joins take an explicit `by`; check `nrow()` before and after every join for unexpected row multiplication.
- Prefer `case_when()` over nested `ifelse()`; keep raw data immutable and create new objects per transformation stage.

### Step 5: Reshape only at the boundary

Keep data long for analysis; pivot wide only for presentation:

```r
wide <- summary_tbl %>% pivot_wider(names_from = region, values_from = total)
```

### Step 6: Visualize in layers

```r
ggplot(summary_tbl, aes(month, total, color = region)) +
  geom_line(linewidth = 1) +
  scale_y_continuous(labels = scales::comma) +
  labs(title = "Monthly sales by region", x = NULL, y = "Total") +
  theme_minimal()
```

Map variables inside `aes()`, set constants outside it. Every axis gets human-readable labels and formatted scales before anyone else sees the plot.

### Step 7: Model and tidy the output

```r
model <- lm(total ~ month + region, data = summary_tbl)
tidy(model, conf.int = TRUE)
glance(model)
```

For group-wise models, nest and map rather than looping:

```r
models <- sales %>%
  group_by(region) %>%
  nest() %>%
  mutate(fit = map(data, ~ lm(amount ~ date, data = .x)),
         tidied = map(fit, tidy)) %>%
  unnest(tidied)
```

Report coefficients with confidence intervals, never bare point estimates. Say "associated with", not "causes", unless the design supports causal claims - route causal questions to causal-inference.

### Step 8: Package for reproduction

Deliver as a Quarto or R Markdown document that runs top to bottom in a fresh session. If it only runs after manual steps, it is not done.

## Good / bad

Bad: `df2 <- df[df$amt > 0 & !is.na(df$amt),]; agg <- aggregate(amt ~ reg, df2, sum)` - base-R index gymnastics, no types checked, unreadable in review.

Good: the Step 4 pipeline - each verb does one thing, grouping is explicit and dropped, and the intent reads top to bottom.

## Deliverable

Produce a runnable script or Quarto document containing: seeded setup, typed data import, `stopifnot()` validation block, pipelined transformations, labeled publication-ready plots, tidied model output with confidence intervals, and a one-paragraph plain-language answer to the analytical question.

## Do NOT

- Do not rely on `read_csv()` type inference for anything that feeds a decision - a column silently read as character has ruined many aggregates.
- Do not leave `summarise()` output grouped; `.groups = "drop"` every time.
- Do not join without checking row counts - a many-to-many join inflates sums silently.
- Do not report model point estimates without intervals, or imply causation from observational fits.
- Do not mix analysis and presentation shapes - keep data long until the final table.

## Quality bar

The analysis ships only when: it runs top to bottom in a fresh R session with no manual steps; every stochastic step is seeded; every assumption about the data is a `stopifnot()` check; every plot has labeled axes and formatted scales; and the answer to the original question appears in plain language, not just in a table.
