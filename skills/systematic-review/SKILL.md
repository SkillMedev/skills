---
name: Systematic Review
description: Runs a PRISMA-compliant systematic review from PICO question and pre-registered protocol through search strings, two-stage screening, risk-of-bias appraisal, and GRADE-rated synthesis. Use when someone asks "run a systematic review", "build my search strategy and inclusion criteria", "screen these studies against a protocol", or "meta-analyze the evidence on X". Do NOT use for a thematic narrative review of a field without a registered protocol and exhaustive search — use literature-review instead. Do NOT use for synthesizing mixed non-study sources into decision themes — use research-synthesis instead.
---

# Systematic Review

A systematic review answers one focused question by finding, appraising, and synthesizing all relevant studies with a transparent, reproducible method. The costly failure it prevents is post-hoc bias: deciding what counts as evidence after seeing the results. Every eligibility decision is therefore locked in a protocol before the first search runs, and every excluded study leaves a logged reason. Follow PRISMA 2020 throughout.

## Operating procedure

Order is mandatory: the protocol must exist before the search, and the search before screening, or the review is not systematic — it is a literature review wearing a lab coat (route that job to literature-review).

### Step 1: Gather inputs and frame the PICO question

Collect from the user before starting:

- The research question, decomposed into **P**opulation, **I**ntervention/Exposure, **C**omparison, **O**utcome. If it has no clear PICO, refuse and help them write one — a question like "is telehealth good?" cannot be reviewed.
- Intended study designs (RCTs only? observational too?).
- Databases available to them (default: PubMed/MEDLINE, Embase, plus one field-specific database — a minimum of 3).
- Team size: two independent screeners are required; if the user is solo, flag this as a methodological limitation up front, not in the discussion section.
- Timeline and whether meta-analysis is intended.

Label any assumed input as a guess and confirm before Step 3.

### Step 2: Write and register the protocol

Document eligibility criteria, databases, full search strings, screening process, extraction fields, risk-of-bias tool, and analysis plan. Recommend PROSPERO registration (health topics) or OSF (other fields). Fill this criteria table — every row, every cell, before searching:

```
INCLUSION / EXCLUSION CRITERIA — [FILL: review title]

Domain         | INCLUDE                              | EXCLUDE (with justification)
---------------|--------------------------------------|------------------------------------------
Population     | [FILL: e.g. adults ≥18 with T2DM]    | [FILL: e.g. type 1, gestational — different pathology]
Intervention   | [FILL: e.g. telehealth coaching ≥8w] | [FILL: e.g. one-off SMS reminders — not coaching]
Comparison     | [FILL: e.g. usual care, waitlist]    | [FILL: e.g. head-to-head telehealth arms]
Outcome        | [FILL: e.g. HbA1c at ≥3 months]      | [FILL: e.g. satisfaction-only studies]
Study design   | [FILL: e.g. RCTs, cluster RCTs]      | [FILL: e.g. case reports, editorials, protocols]
Language       | [FILL — restricting is a limitation] | [FILL]
Setting        | [FILL]                               | [FILL]
```

Every exclusion needs a scientific justification, not a convenience one ("hard to obtain" is not a criterion).

### Step 3: Build the search strategy

Translate each PICO concept into controlled vocabulary (MeSH/Emtree) plus free-text synonyms. Combine synonyms within a concept with OR; combine concepts with AND. The Comparison concept is usually omitted from the string (it is enforced at screening). Worked example for "In adults with type 2 diabetes, does telehealth coaching versus usual care improve HbA1c?":

```
PubMed:
("diabetes mellitus, type 2"[MeSH] OR "type 2 diabetes"[tiab] OR T2DM[tiab])
AND
(telemedicine[MeSH] OR telehealth[tiab] OR "remote coaching"[tiab] OR mHealth[tiab])
AND
("glycated hemoglobin"[MeSH] OR HbA1c[tiab] OR "glycemic control"[tiab])
```

Record the exact query string, database, and run date for every search — reproducibility is the whole point. Supplement with reference-list snowballing and forward citation searches of included studies. If a search returns over ~5,000 hits, the question is too broad or the string too loose; tighten the population or outcome concept, never by silently adding filters not in the protocol.

### Step 4: Screen in two stages

1. Title/abstract screening against the criteria table.
2. Full-text screening of survivors, logging one primary exclusion reason per rejected paper.

Two independent screeners; pilot on 50–100 abstracts and proceed only when agreement reaches roughly 80% or better — below that, the criteria are ambiguous and must be sharpened first. Resolve conflicts by discussion or a third reviewer. Track every count for the PRISMA flow diagram: records identified, deduplicated, screened, excluded (with reasons), and included.

### Step 5: Extract data in duplicate

Use a piloted extraction form capturing: citation, design, sample size and characteristics, intervention details, comparator, outcomes, effect sizes with confidence intervals, funding source, and declared conflicts. Extract in duplicate; reconcile disagreements against the source paper.

### Step 6: Appraise risk of bias

Apply the validated tool matched to design: RoB 2 for RCTs, ROBINS-I for non-randomized studies, QUADAS-2 for diagnostic accuracy. Report domain-level judgments (randomization, deviations, missing data, measurement, selective reporting) — never a single opaque quality score.

### Step 7: Synthesize

- If studies are clinically and methodologically comparable, meta-analyze and report heterogeneity via I². Read I² with the Cochrane bands: 0–40% may not be important, 30–60% moderate, 50–90% substantial, 75–100% considerable. Substantial heterogeneity demands subgroup or sensitivity analysis, not a pooled estimate presented as settled.
- If not comparable, run a structured narrative synthesis grouped by outcome, never by study.
- Rate certainty per outcome with GRADE: high / moderate / low / very low, with the downgrade reasons stated (risk of bias, inconsistency, indirectness, imprecision, publication bias).

## Deliverable

Produce: the registered protocol, the criteria table, exact search strings with run dates, a PRISMA flow diagram with all counts, a summary-of-findings table with GRADE ratings, a risk-of-bias summary, and a plain-language conclusion stating what the evidence supports AND its limitations.

## Do NOT

- Do NOT change eligibility criteria after seeing results — that converts the review into cherry-picking with citations.
- Do NOT silently drop studies; every exclusion gets a logged reason.
- Do NOT confuse "no evidence of effect" with "evidence of no effect" — an empty field and a null result are different findings.
- Do NOT pool studies across high heterogeneity (I² above ~75%) without subgroup justification.
- Do NOT ignore publication bias when a few small positive studies dominate; use a funnel plot or Egger's test when 10+ studies allow it.

## Quality bar

- Another team could rerun the search from the recorded strings and reach the same included set.
- Every PRISMA flow count reconciles (identified − excluded = included at each stage).
- Every included study has domain-level risk-of-bias judgments and every outcome a GRADE rating.
- The conclusion never claims more certainty than the GRADE rating supports.

## Escalation

Evidence synthesis is not clinical guidance: route treatment decisions to a qualified clinician. For citation verification and formatting of the final reference list, use citation-tracker. If the user actually needs a fast thematic map of a field rather than exhaustive protocol-driven coverage, hand off to literature-review.
