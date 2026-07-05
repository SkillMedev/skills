---
name: GitHub Actions
description: Designs and hardens GitHub Actions CI/CD - pipelines that hit a sub-10-minute PR feedback budget through caching and parallelism, with least-privilege security and safe deploy gates. Use when someone asks "why is my CI so slow", "how do I cache node_modules properly", "set up a deploy workflow", "is pull_request_target safe here", or wants a workflow written or reviewed. Do NOT use for provisioning the cloud infrastructure the pipeline deploys to - use terraform-expert instead; do NOT use for Vercel-specific deploy flows - use vercel-deploy-pipeline instead; for secret storage policy beyond CI, use secrets-hygiene.
---

# GitHub Actions

CI that takes 25 minutes gets ignored, and a workflow with a write-token running untrusted code gets owned. This skill builds pipelines against two hard constraints at once - a speed budget developers will actually wait for, and a security posture that assumes PR authors are hostile - because retrofitting either into a sprawling workflow file is far more painful than starting right.

## Operating procedure

### Step 1: Gather inputs

- Stack and package manager (locks the caching strategy).
- Current wall-clock time of the slowest required check, and the test-suite runtime.
- Monorepo or single package; deploy targets and who may approve prod.
- Any use of forked PRs or `pull_request_target` today (security review trigger).

### Step 2: Set the budgets

- PR feedback (all required checks) under 10 minutes wall-clock; under 5 is the mark of a healthy repo. Anything over 15 minutes changes developer behavior - people batch commits and stop running CI-first.
- No single job over 10 minutes; split or parallelize past that.
- Dependency-install step under 60 seconds on a warm cache; if `npm ci` runs 3+ minutes every build, caching is broken.
- Cache hit rate above 90% on PR builds. Check the cache step's output over a week of runs; a low rate means the key is too specific or the cache is thrashing the 10GB-per-repo eviction limit.

### Step 3: Structure jobs for parallelism

- Run independent jobs (lint, typecheck, unit, build) in parallel; use `needs` only for real data dependencies, not for tidiness - every unnecessary `needs` serializes the graph.
- Shard test suites over ~5 minutes across matrix jobs.
- Use concurrency groups to cancel superseded runs on the same branch.
- Monorepo: trigger by changed paths (path filters or affected-detection like Turborepo/Nx) so a docs change does not run the mobile build.

### Step 4: Caching

- Use the built-in cache in `setup-node`/`setup-python` for dependencies - the biggest, easiest win.
- For custom caches, key on the lockfile hash with a `restore-keys` prefix fallback, so a lockfile change still restores the nearest cache instead of starting cold: key `os-node-${{ hashFiles('**/package-lock.json') }}`, restore-key `os-node-`.
- Cache build outputs (Turborepo/Nx remote cache) to skip unchanged work entirely.
- Never cache things that are faster to recreate than to download (small artifacts), and never cache secrets or credentials into a cache readable by PR builds.

### Step 5: Security hardening

- Pin third-party actions to a full commit SHA, not a mutable tag - tags can be moved to malicious code.
- Set least-privilege `permissions:` at the workflow level (default `contents: read`) and widen per-job only where needed.
- Never echo secrets; pass via the `secrets` context, not interpolated into `run` args where they hit logs and shell history.
- Treat `pull_request_target` as loaded: it runs with a write token against untrusted fork code. Never check out and execute the PR's code under it; if you must, split into an unprivileged `pull_request` job plus a privileged job that consumes only validated artifacts.
- Forked PRs get no secrets - design required checks to pass without them.

### Step 6: Deploys

- Gate prod behind environments with required reviewers and protection rules.
- Use OIDC to assume short-lived cloud roles instead of long-lived cloud keys stored as secrets.
- Gate deploys on green tests via `needs`; never deploy from a workflow that skipped checks.

### Step 7: Keep it maintainable

- Extract repetition into reusable workflows and composite actions once the same steps appear a third time.
- Fail loudly: no `|| true`, no `continue-on-error` on required checks.
- Quarantine flaky tests to a non-blocking job and fix them; do not blanket-retry the suite to green.

## Worked artifact: workflow skeleton

Copy, replace the [FILL] fields, delete jobs you do not need.

```yaml
name: CI
on:
  pull_request:
  push: { branches: [main] }

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 10          # hard stop; budget is well under this
    steps:
      - uses: actions/checkout@[FILL: full commit SHA]
      - uses: actions/setup-node@[FILL: full commit SHA]
        with: { node-version: [FILL], cache: 'npm' }
      - run: npm ci
      - run: npm test -- --shard=${{ matrix.shard }}/[FILL: total shards]
    strategy:
      fail-fast: false
      matrix:
        shard: [FILL: e.g. 1, 2, 3]

  lint:
    runs-on: ubuntu-latest
    timeout-minutes: 5
    steps:
      - uses: actions/checkout@[FILL: full commit SHA]
      - uses: actions/setup-node@[FILL: full commit SHA]
        with: { node-version: [FILL], cache: 'npm' }
      - run: npm ci
      - run: npm run lint && npm run typecheck

  deploy:
    if: github.ref == 'refs/heads/main'
    needs: [test, lint]
    runs-on: ubuntu-latest
    environment: production        # required reviewers configured in repo settings
    permissions:
      contents: read
      id-token: write              # OIDC - no long-lived cloud keys
    steps:
      - uses: actions/checkout@[FILL: full commit SHA]
      - uses: [FILL: cloud OIDC auth action]@[FILL: full commit SHA]
        with: { role-to-assume: [FILL] }
      - run: [FILL: deploy command]
```

Matrix testing across versions/OS follows the same pattern - `matrix: { node: [FILL versions], os: [ubuntu-latest, macos-latest] }` with `fail-fast: false` when every combination should report.

## Deliverable

Produce the workflow file(s) plus a one-page pipeline report: measured PR feedback time vs the 10-minute budget, cache hit rate, the job dependency graph, and a security checklist result (SHA pinning, permissions, `pull_request_target` audit, OIDC).

## Do NOT

- Do not pin actions to `@v4`-style tags in anything with secrets or write permissions - pin the SHA.
- Do not run untrusted PR code under `pull_request_target` or with elevated permissions.
- Do not serialize jobs with `needs` for readability; each false dependency adds minutes.
- Do not blanket-retry flaky tests - a retried-green suite hides real races.
- Do not store long-lived cloud keys when OIDC is available.
- Do not let a job run unbounded - always set `timeout-minutes`, or a hung job burns 6 hours of runner time.

## Quality bar

- PR feedback under 10 minutes; every job has a timeout; cache hit rate above 90%.
- Every third-party action SHA-pinned; workflow-level permissions read-only; secrets never appear in logs.
- Prod deploys gated by environment protection and green checks, authenticated via OIDC.
- Forked-PR runs pass without secrets; monorepo paths filter correctly; superseded runs auto-cancel.
