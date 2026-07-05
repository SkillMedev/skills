---
name: Model Card Writer
description: Produces a complete model card — intended use, training data provenance, evaluation results with slices, limitations, and usage guidance — for any model shared beyond its team or affecting people. Use when someone asks "write a model card", "document this model before release", "what should our model documentation include", or is preparing a model for deployment, handoff, or external publication. Do NOT use for producing the underlying evaluation numbers — use model-evaluation-report instead; for ongoing production monitoring plans use data-drift-monitor.
---

# Model Card Writer

A model card is the contract between the team that built a model and the people who will use or be affected by it. It documents not just what the model does but what it must not be used for — and the cost of skipping it is a model quietly repurposed for a use case it was never validated on, discovered only after harm. This skill produces a card a consumer can act on, not a compliance checkbox.

## Operating procedure

Write sections in the order below; intended use (Step 2) constrains everything else, and limitations (Step 5) cannot be written honestly until evaluation results (Step 4) are in front of you.

### Step 1: Gather inputs

Collect before writing; label anything unverified as a guess and chase it before publishing.

1. Model name, version, and owning team or contact.
2. Architecture family in plain language (gradient boosted trees, transformer, logistic regression).
3. The evaluation report — if none exists, stop and produce one via model-evaluation-report first; a card without real numbers is fiction.
4. Training data sources, collection method, time range, and geographic/demographic scope.
5. Who consumes the model: internal team, other teams, external users, or the public. External sharing raises the honesty bar on every section.

### Step 2: Model overview and intended use

- Model name, version, release identifier, architecture family, owning team.
- Intended use cases as specific, bounded statements: "ranks support tickets by predicted resolution time for internal triage" — not "helps with support".
- Out-of-scope uses as an explicit list. This is the highest-leverage section of the card: every use case not validated in evaluation belongs here by default.

### Step 3: Training data

Data documentation is as important as model documentation.

- Dataset names and versions; collection method and time range; geographic and demographic scope.
- Known gaps and underrepresented populations, stated plainly.
- Preprocessing steps that affect interpretation (filtering, deduplication, label heuristics).
- Never include PII or specifics that would allow reconstruction of training records.

### Step 4: Evaluation results

Pull from the evaluation report; report failures alongside wins.

- Primary metric with confidence interval, plus baseline comparisons (majority class, prior model, human performance if measured).
- Slice-level results for demographic and domain subgroups, with sample sizes.
- Known failure modes with estimated frequency.
- A description of the evaluation dataset, explicitly distinct from training data.

### Step 5: Limitations and risks

This section requires more honesty than most teams are comfortable with — write it anyway.

- Performance degradation conditions: distribution shifts, edge cases, adversarial inputs.
- Misuse potential and the mitigations in place.
- Fairness: which groups may be disadvantaged and what was done about it.
- Uncertainty: what the team does not know about production behavior. An empty limitations section signals a card written by marketing, not engineering.

### Step 6: Usage guidance

Give the consumer enough to use the model responsibly without asking the team.

- Recommended confidence threshold or decision rules at the intended operating point.
- Human-in-the-loop requirements for high-stakes decisions.
- Monitoring requirements: which signals indicate retraining is needed (hand the specifics to data-drift-monitor).
- Version policy: how long this version is maintained and what triggers the next one.

### Step 7: Publish and maintain

- Store the card with the model artifact, not in a separate documentation system that will drift.
- Review and update at every version bump. A card untouched for over 12 months is stale and must be flagged as such wherever it is served.

## Worked artifact: filled intended-use section

```
## Intended Use

MODEL: ticket-triage-ranker v2.1        OWNER: support-ml@acme (Support ML team)
TYPE: gradient boosted trees over ticket text embeddings + account features

INTENDED USE CASES
- Rank incoming English-language support tickets by predicted time-to-resolution
  so agents work the longest-running risks first. Internal triage only.
- Batch re-prioritization of open queues up to 4x daily.

OUT-OF-SCOPE USES (not validated; do not deploy for these)
- Any customer-facing display of predicted resolution time.
- Non-English tickets (training data 97% English; measured PR-AUC drops
  from 0.61 to 0.38 on the Spanish slice, n = 412).
- Automated ticket closure or SLA commitments — the model ranks, humans decide.
- Agent performance evaluation. Predictions correlate with ticket difficulty,
  not agent skill; using them for reviews would penalize agents assigned
  hard queues.
```

Bad intended-use line: "This model helps prioritize support work." (Unbounded — permits every misuse above.)
Good: the block above — each permitted use is bounded, each exclusion cites the evidence or the mechanism of harm.

## Deliverable

Produce a model card containing seven sections: overview and intended use with an explicit out-of-scope list, training data provenance with known gaps, evaluation results with slices and baselines, limitations and risks, usage guidance with thresholds and monitoring triggers, version policy, and a maintenance commitment — stored alongside the model artifact.

## Do NOT

- Do not write the card before the evaluation exists; numbers first, narrative second.
- Do not leave out-of-scope uses implicit — unlisted uses will happen, and the card is the only artifact that could have prevented them.
- Do not copy aggregate metrics without slices; the groups the model fails are exactly what consumers need to know.
- Do not include training-record specifics or PII under the banner of transparency.
- Do not publish a card with an empty or boilerplate limitations section; it destroys the credibility of every other section.

## Quality bar

The card ships only when: a reader outside the team could decide whether their use case is in scope without contacting the authors; every claim in the evaluation section traces to the evaluation report; the out-of-scope list covers every plausible adjacent misuse; limitations name at least one thing the team genuinely does not know; and the card lives next to the artifact it describes.

## Escalation

Model cards for models affecting credit, employment, housing, healthcare, or other regulated decisions may carry legal disclosure obligations — involve counsel or the compliance function before external publication; this skill is documentation practice, not legal advice.
