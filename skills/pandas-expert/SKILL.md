---
name: Pandas Expert
description: Writes correct, vectorized pandas for cleaning, joining, reshaping, and aggregating tabular data, with validated joins and deliberate dtype and missing-data handling. Use when someone asks "why did my merge duplicate rows", "how do I clean this CSV in pandas", "my groupby numbers look wrong", "this apply is too slow", or is transforming a DataFrame for analysis or a pipeline. Do NOT use for first-pass profiling of an unfamiliar dataset — use eda-playbook instead; for data too large for one machine use spark-jobs; for answering business questions directly in SQL use sql-to-insights.
---

# Pandas Expert

Most pandas bugs are silent: a join that fans out rows, a dtype that upcast to object, a NaN that vanished from a sum. The output looks plausible and is wrong. This skill produces transformations that validate themselves — every join asserted, every coercion counted, every aggregate reconciled — so errors surface at the line that caused them instead of in a dashboard three weeks later.

## Operating procedure

Follow the steps in order. Types must be fixed before cleaning, cleaning before joining, joining before aggregating — each later step silently produces wrong numbers if an earlier one was skipped.

### Step 1: gather inputs

Before writing any transformation, establish:

- The grain of each table: what one row represents. If the user cannot state it, derive it with `df.duplicated(subset=candidate_keys).sum()` and label the result a guess until confirmed.
- The expected output: shape, grain, and one or two spot-check numbers the user already trusts (last month's total, a known customer's count).
- Whether the source can be mutated. Default to no: work on a copy, never mutate the caller's DataFrame.
- Approximate size. Pandas wants roughly 3-5x the on-disk dataset size in free RAM for comfortable transformation work. Above a few GB in memory, reach for chunked reads (`pd.read_csv(..., chunksize=...)`), pyarrow-backed dtypes, or hand off to spark-jobs.

### Step 2: inspect before touching

Run `df.info()`, `df.describe()`, `df.isna().sum()`, and `df.nunique()` first. You are looking for: object columns that should be numeric or datetime, null counts, and cardinality. This is a two-minute sanity pass, not a full audit — for a genuinely unfamiliar dataset, run eda-playbook first.

### Step 3: fix dtypes early

- Parse dates with `pd.to_datetime`; pass `format=` when known (it is faster and catches malformed rows), `format="mixed"` only when formats genuinely vary.
- Coerce numerics with `pd.to_numeric(col, errors="coerce")`, then **count and report** what failed: `col.isna().sum()`. Silent coercion is how "N/A" strings become invisible revenue holes.
- Cast low-cardinality strings to `category` when distinct values are under roughly 50% of row count — it cuts memory and speeds groupbys.
- Downcast large numeric columns (`pd.to_numeric(col, downcast="integer")`) when memory is tight.

### Step 4: handle missing data deliberately

Choose drop, fill, or flag — and state which and why in a comment. If more than ~5% of a key column is null, stop and ask whether the nulls are structural (a pipeline branch) before imputing; that determination changes the right treatment entirely.

### Step 5: join with validation, never on faith

- Always state `how=` and `on=` explicitly.
- Always pass `validate=` ("one_to_one", "many_to_one", "many_to_many") so pandas raises on unexpected key duplication instead of silently fanning out.
- After the join, assert the row count: a left join must preserve `len(left)`.
- Check match rate with `indicator=True` when a low match rate would be a data problem.

### Step 6: reshape and aggregate

- `pivot_table` for long→wide, `melt` for wide→long.
- `groupby().agg()` with **named aggregations** — `agg(revenue=("amount", "sum"))` — so output columns are self-describing.
- Remember `sum()` skips NaN silently; if a NaN-contaminated group should show as missing, use `min_count=1`.

### Step 7: verify the output

Reconcile at least one aggregate against the pre-transformation source (total rows, total revenue). Show the transformation step by step with a comment on each step explaining intent, not mechanics.

## Worked example

Messy orders joined to a customer table with a duplicate key — the validation catches it:

```python
import pandas as pd

orders = pd.DataFrame({
    "order_id": [1001, 1002, 1003, 1004, 1005],
    "customer_id": ["C01", "C02", "C01", "C03", "C02"],
    "amount": ["120.50", "80.00", "N/A", "45.25", "200.00"],
})
customers = pd.DataFrame({
    "customer_id": ["C01", "C02", "C03", "C03"],   # C03 duplicated upstream
    "region": ["West", "East", "South", "South-dup"],
})

# Coerce and count failures instead of letting them vanish
orders["amount"] = pd.to_numeric(orders["amount"], errors="coerce")
print(orders["amount"].isna().sum(), "amounts failed to parse")

# validate= raises instead of silently duplicating order 1004
try:
    orders.merge(customers, on="customer_id", how="left", validate="many_to_one")
except pd.errors.MergeError as e:
    print("MergeError:", str(e).splitlines()[0])

customers = customers.drop_duplicates("customer_id", keep="first")
merged = orders.merge(customers, on="customer_id", how="left", validate="many_to_one")
assert len(merged) == len(orders)

summary = (
    merged.groupby("region", as_index=False)
    .agg(orders=("order_id", "count"), revenue=("amount", "sum"))
    .sort_values("revenue", ascending=False)
)
print(summary.to_string(index=False))
```

Output:

```
1 amounts failed to parse
MergeError: Merge keys are not unique in right dataset; not a many-to-one merge
region  orders  revenue
  East       2   280.00
  West       2   120.50
 South       1    45.25
```

Read it: the "N/A" string was counted, not swallowed; the duplicate C03 row raised at the merge line instead of inflating South's revenue; West shows 2 orders but only 120.50 revenue because the failed parse became NaN and `sum` skipped it — a fact the failure count already disclosed.

## Performance rules

- Avoid `apply` over rows when a vectorized op, `np.where`, or `np.select` exists — vectorized versions are commonly 10-100x faster.
- Avoid chained indexing (`df[a][b] = ...`); use `.loc[rows, cols]` for all assignment.
- Watch for silent upcasts to `object` dtype after concatenation or fillna — they destroy both speed and memory.
- Prefer `merge` over row-wise lookups; prefer `groupby().transform` over apply-per-group when the output is row-aligned.

## Deliverable

Produce a runnable, commented transformation script (or notebook cells) containing: the dtype-fixing block with coercion-failure counts, every join carrying `how=`, `on=`, `validate=`, and a row-count assertion, named aggregations, and a final reconciliation check against a number the user already trusts.

## Do NOT

- Do not merge without `validate=` — fan-out from duplicated keys is the single most common source of inflated metrics.
- Do not use `errors="coerce"` without counting what was coerced; a clean-looking column can hide hundreds of destroyed values.
- Do not use `apply` with a lambda for arithmetic a vectorized expression can do.
- Do not mutate the input DataFrame in place inside a function; copy when in doubt.
- Do not trust `sum()` on a column with NaN to mean "complete total" — it means "total of what parsed".
- Do not profile an unknown dataset with ad-hoc snippets; run eda-playbook and come back with its decision log.

## Quality bar

A finished transformation ships only when: every join is validated and row-count-asserted; every type coercion reports its failure count; at least one output aggregate reconciles to a trusted source number; no chained indexing or row-wise `apply` remains where a vectorized form exists; and a reader can follow the intent from comments without running the code.
