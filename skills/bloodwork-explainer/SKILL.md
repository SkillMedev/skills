---
name: Bloodwork Explainer
description: Translates common lab panels — CBC, CMP, lipid panel, thyroid, hormones, HbA1c — into plain language, flags values against the lab's own reference ranges, and produces a prioritized question list to bring to a physician. Use when someone asks "what does my bloodwork mean", "is my ALT high", "explain my lipid panel", or "what should I ask my doctor about these results". General wellness education, not medical advice — it flags and explains, never diagnoses or prescribes. Do NOT use to summarize a medical chart or clinical document — use clinical-summary instead; for diet changes motivated by results, hand off to nutrition-planner.
---

# Bloodwork Explainer

Most people do one of two harmful things with lab results: self-diagnose from a single out-of-range value, or file the report away without understanding what it flagged. This skill does neither. It converts a lab report into plain-language explanations, honest flags, and a question list that makes the next doctor visit dramatically more productive. It never diagnoses, never recommends treatment, and never overrides a physician.

## Operating procedure

### Step 1: Gather inputs

Collect before interpreting anything. Missing context is the main source of bad interpretation.

1. The results themselves, including the reference range printed next to each value. Reference ranges are lab-specific — different labs use different assays and populations, so never substitute a generic range from memory for the one on the report. If the user gives values without ranges, ask for the ranges; label any range you supply yourself as a generic approximation, not the lab's.
2. Age and sex (many ranges differ by both).
3. Current medications and supplements (statins, biotin, creatine, and many others move markers).
4. Fasting status at the draw (glucose and triglycerides are meaningless to compare against fasting ranges otherwise).
5. Prior results for the same markers, if available — a trend beats a snapshot.
6. Recent context: intense exercise within 48 hours (raises ALT/AST and creatinine), acute illness, heavy alcohol.
7. Any symptoms the user is worried about.

### Step 2: Screen for urgent flags first

Before explaining anything, scan for values the lab marked as critical, and for symptom red flags. If any are present, lead with the escalation guidance below — do not bury it under education.

### Step 3: Organize by panel and explain in plain language

Work panel by panel. For each marker: what it measures, whether the value sits inside the lab's range, and the common non-alarming reasons it moves.

- **CBC**: hemoglobin (oxygen-carrying capacity; low suggests anemia), hematocrit (percent of blood that is red cells), WBC count (immune activity; high can indicate infection or inflammation), platelets (clotting). A single out-of-range value in isolation rarely means much — trend and clinical context matter.
- **CMP**: ALT and AST (liver stress, heavy alcohol, or recent intense exercise), ALP (bile ducts and bone), creatinine and BUN (waste clearance), eGFR (estimated kidney filtration), electrolytes (sodium, potassium, chloride, bicarbonate), calcium. Fasting glucose: 70–99 mg/dL is the typical normal band; 100–125 is the pre-diabetic range; note that the lab's own range governs.
- **Lipid panel**: total cholesterol alone is a poor predictor. LDL is the primary target (under 100 mg/dL is the common optimal reference for most adults, lower for high-risk individuals — the target is a physician decision). HDL under 40 mg/dL is a risk factor. Triglycerides under 150 mg/dL is the normal reference. Suggest asking the doctor about ApoB when cardiovascular risk is the concern.
- **Thyroid and hormones**: TSH is the standard screen; abnormal values prompt free T4 and T3 follow-up. Testosterone (total and free) is relevant to fatigue, libido, and body-composition symptoms in both sexes. HbA1c reflects roughly 3 months of average glucose. Vitamin D (25-OH): the optimal range is debated; 30–60 ng/mL is a common clinical target.

### Step 4: Flag, don't diagnose

For each out-of-range value, state three things only: the value versus the lab's range, the direction and rough magnitude (barely over vs several-fold), and the plain-language possibilities a doctor would consider — explicitly framed as questions for the doctor, not conclusions.

**Bad:** "Your ALT is 62 (range 7–56), so you have fatty liver disease. Cut out sugar and it will resolve."

**Good:** "Your ALT is 62 against this lab's range of 7–56 — mildly elevated, about 10% over the top of the range. Mild ALT elevations are common and have many causes, including recent intense exercise, alcohol, some medications, and liver conditions a doctor would want to rule out. Worth asking: could my workout two days before the draw explain this, and should we retest in a few weeks?"

The Bad version diagnoses and treats. The Good version flags, contextualizes, and hands the decision to a physician.

### Step 5: Build the doctor question list

Produce 5–8 prioritized questions. Always include the evergreen five: What is the trend versus my previous results? Which out-of-range values need action now versus monitoring? Are any of my medications or supplements affecting these? What lifestyle changes would move the most important markers? When should I retest? Then add marker-specific questions from Step 4.

## Deliverable

Produce a results walkthrough containing: each panel explained in plain language, every out-of-range value flagged with the lab's own range and a non-diagnostic context note, a trend note where prior results exist, and the prioritized question list for the physician visit.

## Do NOT

- Do not diagnose, name a probable condition, or recommend starting/stopping any medication or supplement — that converts education into practicing medicine.
- Do not apply a generic reference range when the lab printed its own; assay differences make ranges non-transferable.
- Do not interpret a fasting-dependent marker without confirming fasting status.
- Do not reassure away a lab-flagged critical value or a symptom red flag with "it's probably nothing."
- Do not treat "in range" as "optimal" or "out of range" as "sick" — reference ranges are statistical, covering the middle 95% of a tested population that includes undiagnosed sick people. Optimal ranges for longevity sometimes differ from standard lab ranges; that distinction belongs in the doctor questions.

## Quality bar

Before delivering: every explained value is compared against the lab's printed range, not a remembered one; every out-of-range flag includes at least one benign and one see-your-doctor possibility, phrased as questions; the question list is prioritized and specific to this report; the escalation section appears whenever a critical value or red-flag symptom exists; and nothing in the output could be read as a diagnosis or treatment plan.

## Escalation

This is general wellness education, not medical advice — all results should be reviewed with a licensed physician. Advise urgent or emergency care, not a routine appointment, when any of these accompany the results: chest pain or pressure, shortness of breath, fainting, sudden severe headache, blood in stool or urine, unexplained weight loss, fever with a very high or very low WBC count, or any value the lab flagged as critical. For repeated fasting glucose at or above 126 mg/dL or a markedly abnormal thyroid or hemoglobin value, recommend prompt (days, not months) physician follow-up. Route adjacent jobs out: clinical-summary for condensing medical records, nutrition-planner for diet changes, longevity-protocol for building the broader health routine the results might motivate.
