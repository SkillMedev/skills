---
name: Primary Research Planner
description: Designs a rigorous primary research protocol for collecting new data - research question, hypotheses, method selection, sampling with a power analysis, instrument design, a named control for every bias threat, and a pre-specified analysis plan. Use when someone asks "design a study to test this", "how many respondents do I need", "should this be a survey or interviews", or must produce defensible first-hand evidence rather than cite existing work. Do NOT use for synthesizing already-published sources - use deep-research or literature-review instead; for writing the discussion guide for a single expert conversation, use expert-interview instead; for detailed survey question wording and scale design, use survey-designer instead.
---

# Primary Research Planner

Use this when someone needs to collect new data, not review existing literature. The costly failure this skill prevents is the study that produces an answer nobody can trust: underpowered samples, leading instruments, and analysis decisions made after seeing the data - each one quietly converts research spend into confident noise. The goal is a plan that produces valid, defensible answers before a single respondent is contacted.

## Inputs to collect

1. **The decision** the research informs, and what answer would change it. Research with no decision attached gets descoped or declined.
2. **The vague goal as stated** - to be reframed into an answerable question.
3. **Timeline and budget**, since they constrain method and sample size.
4. **Access to the population**: existing panels, customer lists, recruiting budget.
5. **Prior evidence**: what is already known, so the study tests what is genuinely open. If an input is a guess, label it a guess.

## Operating procedure

The steps run in this order because each constrains the next: the question type dictates the method, the method dictates the sample, and the analysis plan must be locked before data arrives or it is not confirmatory.

### Step 1: Clarify the research question

Reframe the vague goal into a specific, answerable question. State whether the aim is **exploratory** (generate hypotheses) or **confirmatory** (test one) - these demand different designs, and pretending an exploratory study was confirmatory is the root of most overclaimed findings.

### Step 2: State hypotheses and operationalize variables

For confirmatory work, write a directional null and alternative hypothesis. Define the independent and dependent variables and exactly how each is measured. If a variable cannot be operationalized, the question is not yet researchable - go back to Step 1.

### Step 3: Choose the methodology by question type

- **Causal** ("does X change Y") → randomized experiment; quasi-experiment if randomization is infeasible (choose the design with causal-inference).
- **Descriptive / prevalence** ("how many, how often") → survey or observational study.
- **Mechanistic / why** ("why do users do X") → qualitative interviews or ethnography.
- **Mixed** → sequence qual and quant deliberately (qual to generate, quant to test), not by default.

If/then rule: if the deliverable is a number that will be compared across groups, the method must support statistical comparison - interviews cannot produce a prevalence estimate, however many are run.

### Step 4: Design the sample

- Define the target population and the sampling frame, and state the gap between them (the frame you can reach is never quite the population you want).
- Pick a sampling method - random, stratified, cluster, or purposive - and justify it against the question.
- Run a **power analysis** for quantitative work: state the minimum effect size worth detecting, alpha (0.05), and power (0.80), and compute required n. Rules of thumb when the user has no prior effect estimate: detecting a medium effect (Cohen's d ≈ 0.5) between two groups needs roughly 64 per group; a small effect (d ≈ 0.2) needs roughly 400 per group. Underpowered studies waste the budget and mislead - if the affordable n cannot detect the smallest effect that matters, say so and renegotiate scope rather than run it anyway.
- For qualitative work, plan 12-20 interviews per distinct segment and stop at saturation (two consecutive interviews yielding no new themes).

### Step 5: Design the instrument

- Prefer validated scales over inventing new ones - a homemade scale has unknown reliability.
- For surveys: no double-barreled, leading, or ambiguous items; balanced response scales; randomize option order where order effects are plausible. (Hand detailed wording work to survey-designer.)
- **Pilot the instrument on 5-10 people** from the target population and revise. A pilot that changes nothing was not a real pilot.

### Step 6: Mitigate bias - the core of the plan

Enumerate every threat and name a specific control for each. A threat without a control is an accepted risk and must be labeled as such:

- **Selection bias** → randomization, broad recruitment beyond the enthusiast fringe.
- **Response / social-desirability bias** → anonymity, neutral wording, indirect questioning for sensitive topics.
- **Confounding** → randomize; otherwise measure and adjust, and say which confounders remain unmeasured.
- **Researcher bias** → blinding where possible, pre-registration of the analysis.
- **Attrition bias** → track dropouts, compare them to completers, plan intention-to-treat analysis.

### Step 7: Pre-specify the analysis plan

Before any data is collected, state: the primary statistical test, how missing data is handled, and any subgroup analyses. Anything decided after seeing the data is exploratory - mark it as such in the readout, without exception.

### Step 8: Ethics and logistics

Address informed consent, data privacy and retention, IRB/ethics review where human subjects require it, timeline, and budget.

## Worked artifact: protocol skeleton

Copy and fill:

```
ONE-PAGE RESEARCH PROTOCOL
Decision this informs: [FILL]
Research question: [FILL]   Mode: [FILL: exploratory / confirmatory]
Hypotheses (confirmatory only): H0: [FILL]  H1: [FILL: directional]
Variables: IV = [FILL + how measured]  DV = [FILL + how measured]
Method: [FILL] - chosen because question type is [FILL: causal/descriptive/mechanistic]
Population: [FILL]   Frame: [FILL]   Gap between them: [FILL]
Sampling: [FILL: method + justification]
Power: effect size = [FILL], alpha = 0.05, power = 0.80 → n = [FILL]
Instrument: [FILL: validated scale or custom]  Pilot: [FILL: n=5-10, date]
Bias controls:
  Selection → [FILL]      Response → [FILL]
  Confounding → [FILL]    Researcher → [FILL]    Attrition → [FILL]
Primary analysis (pre-specified): [FILL]
Missing data handling: [FILL]   Subgroups (pre-specified): [FILL]
Ethics: consent [FILL], privacy [FILL], IRB [FILL: yes/no/why]
Timeline: [FILL]   Budget: [FILL]
BIGGEST VALIDITY RISK: [FILL - one sentence, stated plainly]
```

## Deliverable

Produce the one-page protocol above, fully filled: question, hypotheses, design, sample with power calculation, measures, a named control per bias threat, pre-specified analysis plan, and ethics - with the single biggest validity risk flagged explicitly at the bottom.

## Do NOT

- Do not run a confirmatory study without a pre-specified analysis plan - post-hoc analysis choices inflate false positives and cannot be undone by honesty later.
- Do not size the sample by budget alone and skip the power analysis; a study that cannot detect the effect it seeks is more misleading than no study.
- Do not use interviews to estimate prevalence or surveys to establish mechanism - method must match question type.
- Do not skip the pilot; the first 5-10 respondents always find an ambiguous item, and finding it after n=500 is unfixable.
- Do not present exploratory findings with confirmatory language ("we found that X causes Y") - label the mode.
- Do not leave a bias threat without either a control or an explicit "accepted risk" label.

## Quality bar

- The question is answerable, the mode (exploratory/confirmatory) is declared, and the method matches the question type.
- The power analysis shows its three inputs and the resulting n, or the qualitative saturation rule is stated.
- Every bias threat in Step 6 has a named control or an accepted-risk label.
- The analysis plan predates data collection and says so.
- The single biggest validity risk is stated in one plain sentence a stakeholder can weigh.

## Escalation

Studies involving human subjects, health data, or minors may legally require IRB/ethics approval - flag this and do not design around it. Route interview-guide construction to expert-interview, survey wording to survey-designer, causal design selection to causal-inference, and post-collection synthesis to interview-synthesis or research-synthesis.
