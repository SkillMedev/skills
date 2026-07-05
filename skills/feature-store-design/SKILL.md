---
name: Feature Store Design
description: Designs a reusable, leakage-safe feature store - entity and naming contracts, point-in-time correct training joins, feature versioning, online/offline consistency with staleness SLOs, and governance. Use when someone asks "should we build a feature store", "how do I share features across models", "our model trains great but serves garbage", "how do I version a feature definition", or is diagnosing training-serving skew. Do NOT use for designing the feature transformations themselves - use ml-feature-engineering instead; for monitoring feature and prediction distributions after deployment - use data-drift-monitor instead; for general relational schema design - use database-schema instead.
---

# Feature Store Design

Feature stores exist to solve three problems at once: reuse across models, point-in-time correctness during training, and consistency between training and serving. A design that solves only one is incomplete - and the failure mode is expensive: silent label leakage that inflates offline metrics, or training-serving skew that makes a model score 0.85 offline and behave randomly in production.

## Operating procedure

### Step 1: Decide whether a feature store is warranted

Collect: number of models in production or planned, how many features they would share, and whether any model serves online. Rule of thumb: below 2-3 models sharing features, a feature store is premature infrastructure - a documented feature pipeline with a catalog (see ml-feature-engineering) does the job. Build or adopt one when features are shared across 3+ models, when online serving needs the same values training used, or when point-in-time bugs have already burned the team. Label the decision and its inputs; revisit when the model count doubles.

### Step 2: Fix entity and naming contracts

Consistent naming is load-bearing - it is the API contract for every downstream model.

- Entity format: <entity-type>_id (e.g., user_id, item_id, session_id).
- Feature format: <entity-type>__<source>__<transformation>__<window> (e.g., user__payments__sum__30d). The name alone should tell a reader what the value is without opening the code.
- No abbreviations that are not defined in a project glossary.
- Each feature has exactly one owning team or owner string in metadata; unowned features are undeleteable and untrustable.

### Step 3: Enforce point-in-time correctness

The most common feature store bug is silent label leakage from future data.

- Every feature retrieval accepts an as-of timestamp parameter. No API path may return "latest" to a training job.
- Offline training joins use point-in-time correct (as-of) lookups - never a naive left join on entity ID, which grabs current values for historical labels.
- Audit any feature derived from a slowly-changing dimension for the correct SCD type; an SCD1 (overwrite) source silently rewrites history and cannot produce correct training data for attributes that change.
- Document the event-time vs processing-time distinction for every feature source, and build training joins on event time plus a realistic availability delay - a feature computed in a nightly batch was not available at 2pm the same day, even though its event time says it was.

### Step 4: Version features and plan backfills

Changing a feature definition without a new version is a breaking change.

- Increment the feature version when transformation logic changes, the source table changes, or the window size changes.
- The old version stays readable during any transition; consumers migrate on their schedule, not the producer's.
- Backfills are idempotent, with the strategy (full vs incremental) documented before running.
- Store the transformation code reference (git commit or DAG run ID) alongside feature values, so any training set is reproducible from its lineage.

### Step 5: Split online and offline stores with explicit SLOs

The two stores have different consistency requirements - design them separately, then guard the seam.

- Offline store: append-only, partitioned by entity and date, optimized for bulk training retrieval.
- Online store: low-latency key-value lookup serving the latest value only. Typical latency budget: p99 under 10-25 ms for a feature vector fetch inside a real-time scoring path.
- Dual-write or materialization pipelines carry a lag monitor; divergence beyond the acceptable threshold triggers an alert. Set a staleness SLO per feature (e.g., 15-minute max lag for the online store), driven by how fast the underlying behavior changes - a 30-day spend aggregate tolerates hours; a session-based fraud feature does not.
- Run a consistency check on a sample: fetch the same entity from both stores and compare. Sustained mismatch above ~0.5-1% of sampled reads is training-serving skew in the making - treat it as an incident, not noise.
- Strongly prefer one transformation definition compiled to both batch and streaming paths; independently reimplemented logic in two languages is where skew is born.

### Step 6: Govern and make discoverable

A feature store no one can discover is a feature store no one reuses.

- Every feature carries: description, entity, dtype, source table, owner, staleness SLO, and a freshness check.
- Deprecation process: mark deprecated, notify owners of dependent models, hard-delete after 90 days.
- Track feature usage by model to identify orphaned features (candidates for deletion) and high-value shared features (candidates for extra monitoring and SLO investment).

## Worked artifact: the join that leaks vs the join that does not

Bad - naive join fetches current feature values for historical labels:

```sql
select l.user_id, l.label, f.user__payments__sum__30d
from labels l
left join features_latest f on f.user_id = l.user_id;  -- future data leaks in
```

Good - as-of join returns the newest value at or before each label's timestamp:

```sql
select l.user_id, l.label, f.user__payments__sum__30d
from labels l
asof join features f
  match_condition (f.feature_ts <= l.label_ts)
  on f.user_id = l.user_id;
```

(On engines without ASOF JOIN, implement with a windowed subquery selecting the max feature_ts at or before label_ts.) The bad version inflates offline metrics because the 30-day payment sum already includes activity after the label event; the good version reproduces what the model would actually have known.

Feature registration template:

```
FEATURE: [FILL: entity__source__transformation__window]
Owner: [FILL]        Entity: [FILL]        Dtype: [FILL]
Description: [FILL: one sentence a stranger understands]
Source table: [FILL]     Event-time field: [FILL]   Availability delay: [FILL]
Version: [FILL]          Code ref: [FILL: commit/DAG run]
Online: yes/no           Staleness SLO: [FILL]      Freshness check: [FILL]
```

## Deliverable

Produce a feature store design document containing: the build/adopt decision with its inputs, the naming and entity contract, the point-in-time retrieval rules with the as-of join pattern, the versioning and backfill policy, online/offline SLOs with lag and consistency monitors, and the governance metadata schema with the deprecation process.

## Do NOT

- Do not build a feature store for one model; it is infrastructure whose payoff is reuse.
- Do not allow any training path to read "latest" values; every offline read is as-of a timestamp.
- Do not change a feature's logic, source, or window in place; version it and keep the old version readable.
- Do not implement the transformation twice for batch and streaming; duplicated logic is the root cause of most training-serving skew.
- Do not skip the availability delay: event time alone overstates what was knowable at prediction time when features land in batches.

## Quality bar

- Every feature name parses as entity__source__transformation__window and carries complete metadata with an owner.
- Training retrieval is provably point-in-time correct (spot-check: no feature value's timestamp exceeds its label's timestamp minus the availability delay).
- Online store meets its latency budget and every online feature has a staleness SLO with a live lag monitor.
- Offline/online consistency is sampled continuously and mismatch stays under the agreed threshold.
- Version lineage lets any historical training set be reconstructed from code refs.
