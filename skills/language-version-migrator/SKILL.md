---
name: Language Version Migrator
description: Ports a codebase across a breaking language or runtime version with compatibility shims, batched automated transforms, dual-runtime CI, and old-vs-new output diffing, ending in a flag-flip cutover with a rollback rule. Use when someone says "migrate us from Python 2 to 3", "we're jumping Node majors", "move to the next Java LTS", or any runtime upgrade where source must change to keep compiling or behaving correctly. Do NOT use for an application framework's major version (React, Rails, Spring) - use framework-upgrader instead; for proving a database schema or data migration is safe, use migration-safety-checker; and skip it entirely when the runtime change is purely operational (base image, CI matrix, deploy target) with no syntax or semantic breaks.
---
# Language Version Migrator

Port code across a breaking language or runtime version by making it run on both versions first, then flipping the interpreter. The failure this skill prevents is the big-bang migration branch: a months-long fork that cannot merge, cannot ship, and cannot roll back, where the team discovers the semantic breaks (encoding, division, ordering) in production. Dual compatibility turns cutover into a config flip.

## Operating procedure

Run the steps in order. Dependencies gate the interpreter, tests gate the transforms, and the transforms gate the cutover - skipping ahead is how migrations stall.

### Step 1: Gather inputs

Collect these before planning. Label any estimate as a guess and refine as the inventory lands.

1. Source and target versions, and whether intermediate stops are required (Java 8 to 21 usually staged through 11 and 17; Python 2 to 3 is direct).
2. Codebase size: LOC and module count. This sets the batch count.
3. Test coverage per module. Any module under roughly 60 percent line coverage on migration-affected paths needs characterization tests first (pair with characterization-test-writer).
4. Dependency count and lockfile state. Every dependency needs a compatibility verdict.
5. Deployment shape: one deployable or many services (many services migrate independently - sequence leaf services first).
6. Deadline and any code-freeze windows.

### Step 2: Inventory the breaking surface

Run the official analyzers - 2to3, python-modernize, pyupgrade for Python; jdeps and the JDK migration guide for Java; Node's deprecation list plus the linter's target-version rule for Node. Produce a categorized list: mechanical (renames, removed syntax), semantic (Python 3 str/bytes split and integer division; Java module system and removed javax APIs; Node ESM/CommonJS and removed globals), and dependency-blocked. The mechanical count sizes the codemod work; the semantic count sizes the hand-review work.

### Step 3: Sequence dependencies before the interpreter

Resolve the dependency graph for the target runtime first: find compatible versions, upgrade them on the OLD runtime where possible, and flag any library with no compatible release as a separate tracked blocker with an owner. Flip the interpreter only once the tree supports it.

### Step 4: Pin behavior where tests are thin

Add characterization tests on the old runtime first so behavior preservation is provable after the move. For data-affecting changes (encoding, numeric, locale), capture real-sample outputs now to diff against later.

### Step 5: Make the code dual-compatible

Land changes that pass on BOTH runtimes while still on the old interpreter: future imports or six for Python, conditional polyfills, multi-release JARs for Java. Ship these incrementally to main. This is what turns cutover into a flag flip, not a rewrite.

### Step 6: Batch the transforms - automate the mechanical, hand-carve the semantic

Run transforms (2to3, pyupgrade, jscodeshift, OpenRewrite recipes) under these batch rules:

- One module or 10-20 files per batch, whichever keeps the PR under about 500 changed lines - beyond that, review quality collapses and codemod mistakes slip through.
- One transform category per commit (print statements in one, import renames in another). A reviewer must be able to verify a commit by pattern-matching one kind of change.
- Every batch merges to main behind dual compatibility and is revertible with a single git revert. No migration branch lives longer than two weeks unmerged.

Read and fix semantic changes by hand in separate commits - encoding boundaries, division, hash/ordering stability, default charset, timezone handling - because codemods cannot see intent. Treat every I/O and serialization boundary as suspect.

### Step 7: Run both runtimes in CI throughout

Keep the suite green on old and new until cutover. For data-affecting changes, gate on actual output comparison old-vs-new on real samples, not just pass/fail.

### Step 8: Cut over with an explicit rollback rule

Flip the runtime in CI and deploy config, confirm green, and hold to these rules:

- Keep the old runtime deployable for at least one full release cycle after cutover.
- Rollback trigger: any P0/P1 regression attributable to the migration within 48 hours of cutover reverts the interpreter flip (a config change), never a code revert of the migration commits.
- Delete the compatibility shims only after two consecutive green weeks (or one release cycle, whichever is longer) on the new runtime, in a final dedicated pass so they do not ossify.

## Worked artifact: migration plan skeleton

Copy this, fill the fields, and keep it updated per batch.

```
MIGRATION PLAN - [FILL: repo] - [FILL: from-version] -> [FILL: to-version]

INVENTORY
  Mechanical changes (codemod-able):   [FILL: count] across [FILL: n] modules
  Semantic changes (hand review):      [FILL: count] - list: [FILL]
  Dependency blockers:                 [FILL: lib -> status/owner]
  Modules under 60% coverage:          [FILL] -> characterization tests first

BATCH SCHEDULE (one row per PR, <=500 changed lines each)
  #  Module/files          Transform category      Owner   Merged?
  1  [FILL]                [FILL: e.g. imports]    [FILL]  [ ]
  2  [FILL]                [FILL]                  [FILL]  [ ]

VERIFICATION
  Dual-runtime CI green since:         [FILL: date/commit]
  Output-diff samples (encoding/num):  [FILL: dataset + location]

CUTOVER
  Flip date:                           [FILL]
  Rollback trigger:                    P0/P1 within 48h -> revert runtime config
  Old runtime deployable until:        [FILL: date, >= 1 release cycle]
  Shim deletion PR after:              [FILL: 2 green weeks]
```

## Deliverable

Produce a filled migration plan (skeleton above) containing the categorized breaking-surface inventory, the dependency compatibility verdicts with blockers and owners, the batch schedule with one transform category per PR, the dual-runtime CI status, and the cutover date with its rollback trigger and shim-deletion date.

## Quality bar

- A single codebase passes its full suite on both the old and new runtime before the interpreter is flipped.
- Every automated transform lands in its own reviewable sub-500-line commit, separate from hand-written semantic fixes.
- Encoding, numeric, locale, and serialization changes are validated by diffing real outputs, not by green tests alone.
- Each dependency is either confirmed compatible with the target runtime or recorded as a tracked blocker with an owner.
- The plan names its rollback trigger and the date the old runtime stops being deployable.

## Do NOT

- Do NOT blind-merge codemod output - review every hunk; transforms miss dynamic usage and rewrite intent wrong.
- Do NOT flip the interpreter before the dependency tree and shims are in place; a big-bang cutover is the failure mode this skill exists to avoid.
- Do NOT let a migration branch live unmerged past two weeks - merge dual-compatible batches to main continuously.
- Do NOT trust pass/fail tests for str/bytes, division, charset, ordering, or timezone changes - diff actual outputs.
- Do NOT roll back by reverting migration commits after cutover; revert the runtime config flip instead.
- Do NOT apply the full codemod ceremony when no source changes are required; an operational-only runtime bump is a config/infra task.
- Do NOT use this for framework major-version upgrades - that is framework-upgrader's job; for schema/data migration safety, use migration-safety-checker.
