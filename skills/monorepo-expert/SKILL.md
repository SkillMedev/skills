---
name: Monorepo Expert
description: Structures and scales JavaScript/TypeScript monorepos with Turborepo, Nx, and pnpm workspaces — layout rules, task pipelines, remote caching, and affected-only CI so build times stay flat as the repo grows. Use when someone asks "should we use a monorepo", "how do I set up Turborepo or Nx", "our monorepo CI takes 40 minutes", "how do I share code between apps", or "why is the cache never hitting". Do NOT use for authoring the CI workflow files themselves — use github-actions instead — and do NOT use for deciding microservice boundaries, which is about runtime architecture, not repo layout — use microservices instead.
---

# Monorepo Expert

A monorepo's promise is shared code with atomic cross-package changes; its failure mode is CI time growing linearly with repo size until every PR waits 40 minutes to test a one-line change. The costly mistake this skill prevents is running every task on every change — the entire discipline of monorepo tooling reduces to one rule: build and test only what the diff affected, and cache everything else.

## Operating procedure

### Step 1: Gather inputs

1. Package manager and tool already in use (pnpm/npm/yarn; Turborepo/Nx/none). Default recommendation for a fresh setup: pnpm + Turborepo for simplicity, Nx when you want generators, enforced boundaries, and plugin depth.
2. Number of apps and packages, and expected growth.
3. Current CI wall-clock time and what a PR actually runs today.
4. Whether builds are reproducible — any task reading undeclared env vars or the network.
5. Team count — boundaries matter more with more teams.

### Step 2: Lay out the repo with a one-way dependency rule

```text
apps/        deployable apps (web, api, mobile)
packages/    shared libraries (ui, config, utils)
```

- Apps consume packages; packages never import from apps. Enforce this mechanically (Nx tags or eslint import rules), not by convention — every un-enforced boundary is eventually violated.
- Each package has its own `package.json`, its own build output, and a clear public entry point; deep imports into another package's `src/` are forbidden.
- Hoist shared dev tooling (eslint config, tsconfig base) into a `config` package so there is exactly one source of truth.

### Step 3: Set the workspace invariants

- Workspaces (pnpm/npm/yarn) so internal packages resolve by name; reference internal deps as `workspace:*` (pnpm) to pin the local version.
- One lockfile at the root; per-package lockfiles mean per-package dependency drift.
- Pin the package manager with `packageManager` in root `package.json` — "works on my machine" cache misses are usually version skew.
- One TypeScript version repo-wide; use project references for incremental type-checking.

### Step 4: Declare the task pipeline honestly

```json
{
  "tasks": {
    "build": { "dependsOn": ["^build"], "outputs": ["dist/**"] },
    "test":  { "dependsOn": ["build"], "outputs": [] },
    "lint":  { "outputs": [] }
  }
}
```

`^build` means "build my dependencies first." Two honesty rules make or break caching:

- Declare all outputs. An undeclared output means cache restores produce missing files.
- Declare all inputs, including env vars (`env` / `globalEnv` in Turborepo). A task that reads an undeclared env var or the network will be cached when it shouldn't be — this produces the worst class of bug: stale artifacts that look fresh. If a task is genuinely non-deterministic, mark it uncacheable rather than lying about inputs.

### Step 5: Turn on remote caching — the single biggest speedup

Local caching helps one machine; remote caching (Vercel Remote Cache, Nx Cloud, or self-hosted) shares results across CI and every teammate, so a package built once is built once for the whole org. Expectations to verify after enabling: warm-PR CI should hit cache on 80%+ of tasks, and a PR touching one leaf package should rebuild only that package and its dependents. If cache hit rate is low, debug input hashing first (undeclared env vars, timestamps in generated files, absolute paths).

### Step 6: Make CI affected-only

Run only what the diff touched: `turbo run test --filter=...[origin/main]` or `nx affected -t test`. This is the decision rule that keeps PR CI roughly constant as the repo grows — a 200-package repo where PRs touch 3 packages should run ~3 packages' tasks, not 200. Keep one scheduled full-graph run (nightly or on main) as the safety net for anything affected-detection misses.

### Step 7: Handle the recurring edge cases

- Circular dependencies: detect with `madge` or the Nx graph; break the cycle by extracting the shared types into a new leaf package.
- Publishing packages externally: use Changesets for independent semver and changelogs.
- Docker builds: `turbo prune --scope=<app>` produces a minimal subtree so images don't carry the whole repo.

## Worked artifact: good/bad pipeline pair

Bad — caching silently broken and CI runs everything:

```json
{
  "tasks": {
    "build": { "dependsOn": ["^build"] },
    "test": {}
  }
}
```

No `outputs` (cache restores nothing), `test` doesn't depend on `build` (races or stale dist), API keys read from env but undeclared (cached test runs "pass" against old config), and CI runs `turbo run test` unfiltered.

Good:

```json
{
  "globalEnv": ["NODE_ENV"],
  "tasks": {
    "build": { "dependsOn": ["^build"], "outputs": ["dist/**"] },
    "test":  { "dependsOn": ["build"], "env": ["API_URL"], "outputs": ["coverage/**"] },
    "lint":  { "outputs": [] },
    "e2e":   { "dependsOn": ["build"], "cache": false }
  }
}
```

with CI running `turbo run lint test build --filter=...[origin/main]` on PRs and the full graph nightly.

Setup checklist to copy:

```
MONOREPO HEALTH — [FILL: repo]
[ ] pnpm workspaces + workspace:* internal refs, single root lockfile
[ ] packageManager pinned; single TS version + project references
[ ] apps/ -> packages/ one-way imports, enforced by [FILL: nx tags / eslint rule]
[ ] every cached task declares outputs AND env inputs
[ ] remote cache on ([FILL: provider]); warm-PR hit rate: [FILL]% (target 80%+)
[ ] PR CI is affected-only: [FILL: exact filter command]
[ ] full-graph run scheduled: [FILL: cadence]
[ ] circular-dep check in CI ([FILL: madge / nx])
```

## Deliverable

Produce the repo layout, the task pipeline JSON with honest inputs/outputs, the exact affected-only CI commands, the remote-cache configuration, and the filled health checklist with the measured cache hit rate.

## Do NOT

- Do not run the full task graph on every PR — that discards the entire point of the tooling.
- Do not cache tasks with undeclared inputs; a stale-but-green build is worse than a slow one.
- Do not allow apps to be imported by packages, or packages to deep-import each other's internals — enforce it with a linter, not a doc.
- Do not keep multiple lockfiles or multiple TypeScript versions; both produce unreproducible builds.
- Do not treat low cache hit rate as "caching doesn't work" — it is almost always an input-hashing bug (env vars, timestamps, absolute paths).
- Do not split the monorepo the moment CI gets slow; fix affected-only and remote caching first, since that is what they exist for.

## Quality bar

- A one-line change in a leaf package triggers builds/tests only for that package and its dependents.
- Warm-PR cache hit rate measured at 80%+ and stated as a number.
- Import boundaries fail the lint step when violated.
- A full-graph safety-net run exists on a schedule.
- Fresh clone to green build works with exactly the documented commands.

## Escalation

Workflow-file authoring and CI runner concerns go to github-actions; deploy pipelines to vercel-deploy-pipeline. If the question is how to split runtime services rather than repo code, use microservices. TypeScript config strictness questions pair with typescript-strict.
