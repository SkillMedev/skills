---
name: Data Quality Framework
description: Designs layered data quality checks across completeness, validity, consistency, uniqueness, timeliness, and accuracy, with severity tiers, freshness SLAs, and anomaly baselines wired into dbt and CI. Use when someone asks "how do I stop bad data reaching dashboards", "set up dbt tests for this model", "our pipeline loaded duplicate rows again", "what data quality checks should this table have", or "how fresh does this source need to be". Do NOT use for detecting distribution drift in ML features and predictions — use data-drift-monitor instead; for one-off exploration and profiling of a new dataset — use eda-playbook instead; for infrastructure and application telemetry — use observability-stack instead; for removing personal data from datasets — use pii-scrubber instead.
---

# Data Quality Framework

Bad data costs most when it is discovered by the consumer — an executive quoting a dashboard built on a half-loaded table, or a model trained on duplicated rows. The costly mistake this skill prevents is the all-or-nothing test suite: either no checks, or hundreds of untiered checks that page someone nightly until they are all silenced. The output is a small, tiered check suite where every failure has a defined severity and action.

## Operating procedure

Severity tiering (Step 3) comes before implementation because a check without a defined response is future alert fatigue.

### Step 1: Gather inputs

1. The tables in scope, their primary keys, and who consumes them. Default scope: start with the 3-5 tables feeding the most-viewed dashboards or production models, not the whole warehouse.
2. Load cadence and expected arrival time per source.
3. Business rules that define a valid row (status enums, amount ranges, referential links).
4. Where checks will run: dbt is the default assumption below; the structure ports to any framework.
5. An owner per dataset. If none exists, assigning one is Step 1, not an afterthought — an unowned failing test is noise by definition.

### Step 2: Map checks to the six dimensions

1. Completeness: required fields are populated; expected rows arrive.
2. Validity: values conform to types, ranges, and formats.
3. Consistency: values agree across tables and over time.
4. Uniqueness: keys are not duplicated.
5. Timeliness: data lands within the expected SLA.
6. Accuracy: values reflect the real-world entity — hardest to test automatically; approximate with reconciliation against a trusted system (e.g. warehouse revenue vs the billing system, alert beyond 1% divergence).

Every table gets, at minimum: unique + not_null on the primary key, freshness on its source, and one row-count anomaly check. Add business-rule checks only where a failure has a named consequence.

### Step 3: Tier severities with concrete red lines

- ERROR (block the pipeline): primary key nulls or duplicates — the red line is zero, not "low"; referential integrity breaks; row count deviating more than ±50% from the trailing 4-week same-weekday average; source freshness beyond the error SLA.
- WARN (alert, do not block): null rate on a non-key column jumping more than 5 percentage points day-over-day or drifting more than 3 standard deviations from its 28-day baseline; row count deviating ±30%; accepted-values violations under 0.5% of rows.
- INFO (dashboard only): slow-moving distribution changes worth a look during review.

The asymmetry is deliberate: key integrity failures corrupt every downstream join, so they block; distribution wobbles usually have benign causes, so they warn.

### Step 4: Implement in dbt

Generic tests in schema YAML:

```yaml
models:
  - name: orders
    columns:
      - name: order_id
        tests:
          - unique
          - not_null
      - name: status
        tests:
          - accepted_values:
              values: ['pending', 'shipped', 'delivered', 'cancelled']
      - name: customer_id
        tests:
          - relationships:
              to: ref('customers')
              field: customer_id
```

Singular tests (SQL that returns failing rows) for business rules:

```sql
-- tests/assert_positive_amount.sql
select order_id from {{ ref('orders') }} where amount <= 0
```

Use `dbt_utils` and `dbt_expectations` for tests like `expect_column_values_to_be_between`, freshness checks, and row-count anomalies rather than hand-rolling them.

### Step 5: Set freshness SLAs

```yaml
sources:
  - name: raw
    tables:
      - name: events
        freshness:
          warn_after: {count: 6, period: hour}
          error_after: {count: 12, period: hour}
        loaded_at_field: ingested_at
```

Derive the numbers from consumption, not habit: warn at roughly 2x the normal load interval, error at the point a consumer makes a wrong decision on stale data. A daily-refreshed executive dashboard tolerates 24 hours; a fraud feature does not tolerate 15 minutes.

### Step 6: Add anomaly baselines

Static thresholds miss slow rot. Track per table, trended daily against a rolling 28-day baseline: row counts per load, null rate per column, and distribution summaries (mean, p50, p95) on key numeric columns. Alert when a metric deviates more than 3 standard deviations from its recent history; compare same-weekday to avoid weekend false positives.

### Step 7: Operationalize

- Run tests in CI on every pull request against a sample; full suites on schedule after each load, failing the pipeline on ERRORs.
- Route alerts to a channel with the failing table, the check, and example failing rows — an alert that requires a query to understand gets ignored.
- Maintain a dashboard of pass rates per source; report quality SLAs to stakeholders.
- Treat recurring failures as incidents with root-cause analysis, not noise to silence. A test muted more than twice is either mis-tiered or exposing a real upstream defect.

## Worked artifact: check-spec template

```
TABLE: [FILL: schema.table]        OWNER: [FILL: team/person]
CONSUMERS: [FILL: dashboards/models]
Check                          Dimension      Severity   Threshold                    Action on failure
PK unique + not_null           Uniqueness     ERROR      0 violations                 Block load; page owner
Source freshness               Timeliness     WARN/ERROR [FILL]h / [FILL]h            Alert; block at error
Row count vs 4wk baseline      Completeness   WARN/ERROR ±30% warn / ±50% error       Alert; block at error
status accepted_values         Validity       WARN       <0.5% of rows                Alert; ticket if recurring
[FILL: business rule]          Consistency    [FILL]     [FILL]                       [FILL]
Reconciliation vs [FILL: sot]  Accuracy       WARN       >1% divergence               Alert finance/owner
```

## Deliverable

Produce a data quality specification containing: the scoped tables with owners, the filled check-spec per table with severities and thresholds, the dbt YAML and singular tests implementing them, freshness SLAs justified by consumption, and the alert routing with defined actions per severity.

## Do NOT

- Do not ship untiered checks; a suite where everything pages is a suite where nothing does.
- Do not set freshness SLAs by habit; derive warn/error from when consumers make decisions.
- Do not tolerate any duplicate or null primary keys "temporarily" — key corruption multiplies through every join.
- Do not silence a recurring failure; mute-count above two means re-tier it or fix the source.
- Do not start with the whole warehouse; instrument the tables behind the top dashboards and models first.

## Quality bar

- Every check has a dimension, severity, numeric threshold, and named action.
- Primary-key and referential checks exist on every in-scope table and block on failure.
- Anomaly checks use rolling baselines (28-day, same-weekday) rather than fixed counts.
- Every dataset has an owner who receives its alerts.
- The suite runs in CI and post-load, and pass rates are visible to stakeholders.
