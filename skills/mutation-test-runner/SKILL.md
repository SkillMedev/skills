---
name: Mutation Test Runner
description: Runs mutation testing on already-covered code and turns each surviving mutant into a specific missing assertion, exposing tests that execute code but verify nothing. Use when line coverage is high yet a bug slipped through, when reviewing a critical module (pricing, permissions, auth, state machines) before release, or when you are asked to run Stryker, PIT, mutmut, or cosmic-ray. Do NOT use when the goal is to find which code paths are untested at all — use coverage-gap-finder instead.
---
# Mutation Test Runner

Prove whether covered code is actually verified: deliberately break a line, and check that a test notices. A surviving mutant is proof that the line is executed but unasserted.

## Workflow

1. Pick the target. Scope to high-risk logic where a silent bug is expensive — pricing, permissions, auth, state machines, financial math. Skip generated code, trivial DTOs, and glue; they have nothing worth mutating.
2. Pick the runner: Stryker (JS/TS/C#), PIT (JVM), mutmut or cosmic-ray (Python). Configure it to run against the target's existing test suite.
3. Run scoped, not global. In CI, mutate only changed files (Stryker incremental / diff mode); reserve a full sweep for a nightly job. Enable coverage analysis so only tests touching a mutated line rerun, and parallelize across cores.
4. Read each surviving mutant as a named missing assertion, not a vague gap:
   - `<` mutated to `<=` survives -> the boundary case is unasserted; add an assertion at the exact edge value.
   - A removed function call survives -> no test checks that side effect; assert the side effect.
   - A negated condition survives -> the two branches are indistinguishable to the suite; assert the differing outcome of each branch.
   - A return value replaced with a constant survives -> the return is unchecked; assert it.
5. Triage equivalent mutants. Some mutants cannot change observable behavior (a change in dead code, `i++` vs `++i` in isolation). Distinguishing them is undecidable in general — mark them ignored in the tool's config rather than chasing 100%.
6. Set a meaningful floor on the targeted modules only (e.g. 70-80% mutation score), where score = killed / (total non-equivalent mutants). Never set one global threshold.

## Quality bar

- Every reported survivor maps to one concrete, named assertion a developer can write — no "add more tests."
- Mutation runs are scoped (diff/incremental in CI, full sweep nightly); no PR blocks on a full-repo run.
- Equivalent mutants are explicitly ignored, not silently inflating or deflating the score.
- The score is presented as a quality signal for already-covered code, never as a coverage substitute.

## Do NOT

- Do NOT block every PR on a full-repo mutation run — it reruns the suite per mutant and will stall the pipeline.
- Do NOT chase a 100% mutation score; equivalent mutants make it unreachable and the marginal mutants are noise.
- Do NOT run mutation testing on generated code, DTOs, or glue — spend the compute where a silent failure hurts.
- Do NOT use a single global threshold; floors belong on critical modules.
- Do NOT treat mutation testing as a substitute for deciding what to test. It measures assertion strength on existing code; a missing feature has no code to mutate. To find untested paths and inputs, use coverage-gap-finder.
