---
name: Dead Code Eliminator
description: Proves code is unreachable with converging evidence, then removes it and its tests, fixtures, and flags in reversible slices without breaking dynamic callers. Use when deleting suspected dead code, cleaning up after a migration or feature retirement, trimming a bloated module, or before estimating work on unfamiliar code. Do NOT use when you want to survey and rank an area's debt without committing to deletion — use find-tech-debt instead.
---
# Dead Code Eliminator

Delete code only after converging evidence proves it dead, then remove it in reversible slices that include its tests, fixtures, config, and flags.

## Workflow

1. **Scope one cluster.** Pick a single symbol, file, or feature to prove dead. Do not batch unrelated removals — a regression must point to one change.
2. **Gather static evidence.** Run the language's dead-code detector (ts-prune or knip for TS/JS, vulture for Python, `deadcode` for Go) and a dependency grapher (madge) or IDE "find usages". Record what each reports unreachable.
3. **Gather runtime evidence.** Check production coverage or profiling data over a representative window — at least 30 days for user-facing paths, 90+ days for code tied to monthly or quarterly jobs — and telemetry: was this endpoint, job, or branch hit this quarter? A symbol is a deletion candidate only when static analysis AND runtime evidence agree. One signal alone produces false positives that take down prod.
4. **Hunt the dynamic callers static tools miss.** Grep the symbol name as a *string* (reflection, string-keyed dispatch, serialization). Check DI containers, registries, ORM hooks, cron/queue workers, and feature-flag config. Search infra repos and other services. Public API surface and library exports are reachable by definition unless you control every consumer.
5. **Classify the kind of dead, because each is removed differently.** Unreachable (no path calls it) → delete. Unused (callable, never called) → delete. Redundant (duplicates a live path) → repoint callers first. Dormant-behind-a-flag (flag off for a cycle) → retire the flag first, then delete its branch.
6. **Soft-delete risky removals first.** Replace the suspect path with log-and-throw, or gate it behind a kill flag while keeping the code, for one full business cycle — 30 days minimum, 90 for anything touching monthly or quarterly processes. If nothing fires the log, proceed. For low-risk leaf code, skip to step 7.
7. **Delete in a focused PR.** Remove the code AND its tests, fixtures, config, flags, and now-orphaned imports. Keep the diff under roughly 400 changed lines so it is reviewable in one sitting; a bigger cluster gets split into multiple PRs. Let CI and the type-checker catch references the search missed. No formatting or refactors in the same diff.

## Deliverable

Produce a deletion PR (one per cluster) containing the removed code plus its entire footprint — tests, fixtures, config, flags, orphaned imports — and a PR description that cites the two converging evidence signals, names which of the four kinds of dead the code is, and, for soft-deleted paths, records the observation window with zero hits.

## Quality bar

- Every deletion cites at least two independent converging signals (static + runtime).
- The PR removes the dead symbol's entire footprint, leaving no orphaned test, fixture, or flag.
- The removal names which of the four kinds of dead it is.
- Reverting the PR cleanly restores the prior behavior.

## Do NOT

- Treat "I can't find a caller" as proof of death — absence of a static caller is not absence of a dynamic one.
- Delete code you only suspect is dead, public API without consumer proof, or code added in the last few weeks (it may be ahead of its caller).
- Skip the string-grep and DI/flag/registry check for anything invoked indirectly.
- Fold formatting or refactors into a deletion PR.
- Batch many clusters into one PR.

When evidence is ambiguous, mark the symbol deprecated and instrument it rather than guessing.
