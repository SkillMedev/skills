---
name: Clinical Summary
description: Produces structured, faithful clinical summaries - either a patient-record summary with strict chronology, medication reconciliation, and expanded abbreviations, or a clinical-study summary with effect sizes, absolute risks, harms, and limitations. Use when someone asks "summarize this patient chart", "turn these notes into a discharge-style summary", "summarize this RCT for journal club", or "what did this trial actually show". This is clinical documentation support, not medical advice or diagnosis. Do NOT use for explaining lab results to a patient - use bloodwork-explainer instead; for synthesizing many studies into one evidence review, use systematic-review.
---

# Clinical Summary

A clinical summary gets acted on by people who trust it, so the costly failure is not vagueness - it is confident distortion: a scrambled timeline, a dropped medication, an overstated trial result. Faithfulness to the source is paramount; never state more certainty than the documents support, and never fill a gap with a plausible guess. This skill produces two artifact types with one discipline: patient-record summaries and clinical-study summaries.

**Boundary, stated up front:** this is clinical documentation support - organizing and restating what the source material says. It is not medical advice, not diagnosis, and not treatment recommendation. Never suggest a diagnosis the records do not contain, never recommend starting, stopping, or dosing a medication, and always defer care decisions to the treating clinician.

## Inputs to collect

1. **Mode** - patient-record summary or study summary. If the source is a chart, notes, or discharge paperwork, it is Path A; if a paper or trial report, Path B.
2. **Audience** - clinician, care coordinator, reviewer, or the patient's own file. Default: clinician-facing.
3. **Source documents** - everything available. Note explicitly which document types are missing (no med list, no imaging reports), because gaps change the summary's reliability.
4. **Purpose** - handoff, chart review, journal club, appraisal. Purpose sets length and emphasis.

Label every inference as an inference ("dose change appears to follow the elevated creatinine on [date], though no note states the reason").

## Path A: patient-record summary

### Step 1: Build the chronology first

Order every event by absolute date (convert "3 days ago" and "last admission" to real dates using the document's own date). Present chronologically, never in the order documents happened to arrive. Flag contradictions between documents - do not silently resolve them.

### Step 2: Reconcile medications

Produce a reconciliation table, one row per drug:

```
| Medication (generic) | Dose | Route | Frequency | Indication | Status | Source/date |
|---|---|---|---|---|---|---|
| [FILL] | [FILL] | [FILL] | [FILL] | [FILL] | [FILL: continued / changed / held / discontinued - with stated reason or "reason not documented"] | [FILL] |
```

Rules: generic names (brand in parentheses on first mention); every status change carries its documented reason or an explicit "reason not documented"; discrepancies between lists (admission vs discharge) are flagged as discrepancies, never merged.

### Step 3: Expand abbreviations

Expand every clinical abbreviation on first use: "HTN (hypertension)", "CABG (coronary artery bypass graft)". Treat error-prone shorthand with extra care - "QD", "U", "IU", "MS", trailing zeros ("5.0 mg") are classic misread sources; write the unambiguous form ("daily", "units", "5 mg").

### Step 4: Assemble

Sections in order: patient context (as documented), problem list, chronology of events, medication reconciliation table, pending items and follow-ups, and explicit gaps ("no records between [date] and [date]").

**Bad:** "Patient was started on a blood thinner after the procedure and improved."
**Good:** "Following the [FILL: procedure, date], apixaban (Eliquis) 5 mg by mouth twice daily was started for atrial fibrillation per the [date] discharge summary. The cardiology note of [date] describes symptoms as improved; no objective follow-up measurement is documented."

## Path B: clinical-study summary

### Step 1: Capture the study identity

Title, authors, journal, year, registration ID (e.g., NCT number), funding source, and declared conflicts of interest. Funding and conflicts matter for appraisal - record them even when the user did not ask.

### Step 2: Summarize the methodology

- **Design**: RCT, cohort, case-control, single-arm. Randomized? Blinded?
- **Population**: inclusion/exclusion criteria, sample size, key demographics.
- **Intervention and comparator**: dose, duration, placebo vs active control.
- **Endpoints**: distinguish the pre-specified primary outcome from secondary and exploratory ones; note the follow-up period.

### Step 3: Report outcomes faithfully

- Primary outcome first, with effect size and confidence interval - not just a p-value.
- Report both relative and absolute effects; relative risk reduction misleads without absolute numbers and NNT/NNH.
- Include adverse events and dropouts with the same prominence as benefits - a summary that omits harms is incomplete.
- State plainly whether the primary endpoint was met; never let positive secondary endpoints obscure a failed primary.

### Step 4: Appraise limitations and significance

List specific threats to validity: small sample, short follow-up, surrogate endpoints, selective reporting, high attrition, lack of blinding, narrow population, industry funding - specific, not boilerplate. Then separate statistical from clinical significance: a statistically significant change may be too small to matter to a patient. Close with a one-paragraph bottom line stating clinical significance and a confidence level, preserving the authors' stated conclusions while noting where the evidence is weaker than their framing.

## Deliverable

Produce either (A) a patient-record summary containing problem list, dated chronology, medication reconciliation table, pending items, and documented gaps, or (B) a study summary containing identity, methods, primary-first results with CIs and absolute effects, harms, limitations, and a bottom-line paragraph with confidence level.

## Do NOT

- Do not diagnose, recommend treatment, or adjust doses - that is the treating clinician's job, and a summary that does it invites harm.
- Do not smooth over contradictions or fill undocumented gaps; a flagged gap is useful, an invented fact is dangerous.
- Do not use relative dates or unexpanded abbreviations - both are top sources of clinical misreading.
- Do not extrapolate a study beyond its studied population, dose, or duration.
- Do not report an association as causation, or a surrogate endpoint as a hard outcome.

## Quality bar

Every date absolute; every abbreviation expanded on first use; every medication row complete or explicitly marked incomplete; harms as prominent as benefits; every inference labeled; the not-medical-advice boundary stated in the deliverable itself.

## Escalation

Any care decision goes to the treating clinician. Patient-facing lab explanation routes to bloodwork-explainer; multi-study evidence synthesis routes to systematic-review; broad academic source surveys route to literature-review.
