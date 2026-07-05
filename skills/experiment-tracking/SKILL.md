---
name: Experiment Tracking
description: Sets up disciplined ML experiment tracking — run logging schemas, artifact versioning, naming conventions, and reproducibility standards — and produces the run-record template a team actually follows. Use when someone asks "how should we track our ML experiments", "why can't we reproduce this result", "how do I set up MLflow or W&B for the team", or is bootstrapping a new ML project. Do NOT use for analyzing product A/B tests — use ab-test-analyzer instead; for monitoring deployed models use data-drift-monitor; for judging whether a trained model is good enough to ship use model-evaluation-report.
---

# Experiment Tracking

An experiment no one can reproduce is a result no one can trust. Disciplined tracking is not overhead — it is the minimum viable scientific practice for ML, and the alternative is a promoted model whose provenance nobody can reconstruct when it misbehaves in production. This skill installs the logging schema, naming discipline, and promotion workflow that make every run auditable.

## Operating procedure

Follow the steps in order: the run schema (Step 2) must exist before naming conventions (Step 3) matter, and reproducibility standards (Step 4) are unenforceable until the schema captures seeds and environments.

### Step 1: Gather inputs

Collect these before touching tooling. Where the answer is unknown, record the default and label it a guess.

1. Team size and collaboration need. Default: solo or small team.
2. Existing tooling, if any (MLflow, Weights and Biases, homegrown spreadsheets).
3. Where artifacts live today (local disk, S3, GCS) — shared object storage is required for teams.
4. Model iteration style: iterative training with epochs (deep learning) or single-fit (trees, linear models). This decides whether per-epoch curves are logged.
5. The promotion decision this tracking must serve: what metric, on what test set, decides whether a model ships.

Tooling default: MLflow for self-hosted setups; Weights and Biases when the team needs collaboration features. Both satisfy every requirement below. Use the tracking server, not local file logging, in any team setting, and point artifact storage at a shared location (S3 or GCS) from day one.

### Step 2: Define the run logging schema

Inconsistent logging is as bad as no logging. Every run logs all of the following — automate the scaffold, because a run that requires manual logging steps will be logged inconsistently.

- Hyperparameters: every value, including defaults. Do not rely on code to reconstruct them.
- Dataset: name, version or content hash, split sizes, and any sampling applied.
- Code: git commit SHA. Fail the run if the working tree is dirty unless explicitly overridden.
- Environment: Python version and key library versions (framework, numpy, pandas at minimum). A Docker image SHA or conda lock file is the gold standard.
- Metrics: train, validation, and test values; per-epoch curves for iterative models.
- Random seeds for Python random, numpy, the framework RNG, and data shuffling.
- Artifacts: model checkpoint path, preprocessor path, and evaluation report path.

### Step 3: Impose naming and organization

Naming is the index — garbage names make the tracker useless.

- Run name format: project/hypothesis/variant, for example churn/feature-selection/drop-low-variance.
- Never reuse a run name. Each run is immutable once logged; a "fixed" run is a new run.
- Group runs into experiments by hypothesis, never by date or author.
- Tag every run with dataset version, model family, and outcome: promoted, rejected, or inconclusive.

### Step 4: Enforce the reproducibility standard

A run is reproducible only if re-executing it lands within metric tolerance. Set the tolerance explicitly — 0.5 percent relative difference on the primary metric is a reasonable default for seeded runs; anything looser than 2 percent means the run is effectively non-deterministic and the causes (unseeded ops, nondeterministic GPU kernels, unstable data ordering) must be found.

- Dataset splits must be deterministic. Prefer hash-based splitting (hash a stable ID into buckets) over random splits, because it survives dataset growth.
- Spot-check reproducibility by re-running the current top experiment from scratch at least quarterly. If it fails tolerance, treat it as an incident, not an inconvenience.

### Step 5: Wire the comparison and promotion workflow

Tracking only has value if it drives decisions.

- Define the promotion criterion before any experiment starts — metric, test set, and threshold. Deciding after seeing results is p-hacking with extra steps.
- Compare runs only on the same held-out test set. Changing the test set resets every comparison to zero.
- Record why each run was rejected. Negative results are data; a tracker full of unexplained dead runs teaches nothing.
- Link every promoted model artifact back to the exact run that produced it, so production incidents trace to a commit, a dataset hash, and a seed in one click.

## Worked artifact: run record template

Copy this into the tracker's run description or a RUNS.md. Replace FILL fields; the tags block feeds the tracker's tag API.

```
RUN: [FILL: project]/[FILL: hypothesis]/[FILL: variant]
COMMIT: [FILL: git SHA]          DIRTY TREE: no (required)
DATASET: [FILL: name] @ [FILL: version or hash]   SPLITS: [FILL: train/val/test sizes]
SEEDS: python=[FILL] numpy=[FILL] framework=[FILL] shuffle=[FILL]
ENV: python [FILL], [FILL: framework+version], image/lock: [FILL]
HYPERPARAMS: [FILL: full dict, defaults included]
METRICS: train=[FILL] val=[FILL] test=[FILL]  (primary metric: [FILL])
ARTIFACTS: checkpoint=[FILL] preprocessor=[FILL] eval-report=[FILL]
TAGS: dataset=[FILL] model-family=[FILL] outcome=[promoted|rejected|inconclusive]
REJECTION REASON (if rejected): [FILL: one sentence]
```

Bad run name: "final_model_v2_new_ACTUALLY_FINAL" — no hypothesis, no lineage, invites reuse.
Good run name: "churn/feature-selection/drop-low-variance" — the tracker becomes browsable by question asked.

## Deliverable

Produce a tracking setup document containing: the chosen tool and storage configuration, the run logging schema (the list in Step 2 as a checklist), the naming convention with one filled example, the reproducibility tolerance and quarterly spot-check owner, and the promotion criterion written down before the next experiment starts.

## Do NOT

- Do not log only the hyperparameters that changed — defaults drift between library versions and silently change results.
- Do not allow dirty-working-tree runs by default; the commit SHA becomes a lie.
- Do not organize experiments by date or person; hypotheses are the unit of learning.
- Do not choose the promotion metric after seeing the leaderboard.
- Do not store artifacts on a laptop; the tracker outlives the machine.

## Quality bar

A finished setup passes when: a new teammate can reproduce the top run to within stated tolerance using only tracker metadata; every run in the last month carries all Step 2 fields and an outcome tag; the promotion criterion predates the runs it judges; and the promoted production model links to exactly one run.

For evaluating whether a tracked model should ship, hand off to model-evaluation-report. For monitoring it after deployment, use data-drift-monitor.
