---
name: Characterization Test Writer
description: Writes pinning and characterization tests that lock in the current behavior of untested legacy code — bugs included — before a refactor, so any later behavior change trips an alarm. Use when someone says "I need a safety net before refactoring this", "this module has no tests and I have to change it", "capture what this function does today", or the correct behavior is simply whatever the code does now. Do NOT use for writing tests for new behavior built red-green — use tdd-expert instead; do NOT use to decide which code is worth testing or to rank coverage gaps — use coverage-gap-finder instead; do NOT use to verify existing assertions are strong — use mutation-test-runner instead.
---
# Characterization Test Writer

Pin what legacy code currently does — bugs included — so a refactor that changes behavior fails loudly instead of silently. The costly mistake this prevents is the "clean" refactor that quietly changes an output nobody noticed was load-bearing, discovered weeks later in production. A characterization test records reality; it never judges it.

## Operating procedure

Order matters: the seam must exist before you can capture output, output must be captured before you can stabilize it, and coverage is only measurable once tests run.

### Step 1: Gather inputs

- The exact function/module about to change, and the refactor's blast radius — which code paths will the change touch? Only those need pinning.
- The dependency list: database, clock, RNG, network, filesystem, globals, environment. Each is a nondeterminism source to control in Step 4.
- Any recorded production inputs or logs available for replay. Label assumptions about "typical inputs" as guesses.

### Step 2: Find the smallest seam

Untested legacy code reaches for databases, clocks, network, and globals, so it usually cannot be called in isolation. Introduce the minimum seam that lets you invoke it: extract a method, parameterize a constructor, inject the dependency, or subclass-and-override (the "subclass to sense" technique from Working Effectively with Legacy Code). Do not refactor the body before the net exists — seam-introduction edits must be mechanical and behavior-preserving (rename, extract, inject), nothing that changes logic.

### Step 3: Capture actual output, not expected

Write a deliberately failing assertion, run it, read the real value from the failure message, and paste it in:

```python
def test_characterize_shipping_for_oversize_order():
    result = calc_shipping(weight_kg=32, region="EU")
    assert result == "PLACEHOLDER"   # run -> failure says: AssertionError: 47.6 != PLACEHOLDER
```

becomes, after one run:

```python
def test_characterizes_oversize_eu_shipping():
    result = calc_shipping(weight_kg=32, region="EU")
    assert result == 47.6  # observed 47.6, not the 45.0 the spec claims — see pins_known_bug below
```

If the captured value looks wrong, pin it anyway and tag it (`pins_known_bug_` prefix or a comment) so the team decides its fate later. Fixing it now defeats the purpose: the refactor must be provably behavior-neutral first, and the bug fix becomes its own reviewable change afterward.

### Step 4: Control nondeterminism until the suite passes twice in a row

- Freeze time (freezegun, libfaketime, an injected Clock), seed RNGs, stub network and I/O, and snapshot database state.
- When output genuinely cannot be made value-deterministic (large reports, generated documents), use golden-master / approval testing (ApprovalTests, snapshot files): record the full output blob once, diff on every run, and scrub volatile fields (timestamps, IDs) in the comparison.
- The acceptance test for this step is literal: run the suite twice consecutively with zero flakes.

### Step 5: Cover every branch the refactor will touch

Run branch coverage and add cases until every branch in the refactor's blast radius is exercised — branch coverage, not line coverage, because an if/else can be 100% line-covered with one arm never taken. Use parameterized tests and replay of recorded production inputs to reach edge cases you would never invent (empty input, nulls, boundary values, error paths). Code outside the blast radius does not need pinning — this is a net for one refactor, not a full suite.

### Step 6: Make failures legible

Name each test for the behavior it pins (`characterizes_returns_zero_for_empty_cart`, not `test_method_3`) and keep each assertion narrow, so one regression breaks one narrowly-scoped test and the failure message names the changed behavior.

## Deliverable

Produce a characterization suite plus a one-paragraph coverage note: which paths in the blast radius are pinned, the twice-in-a-row green run confirmed, and a tagged list of pinned-but-suspect outputs (`pins_known_bug_*`) handed to the team for post-refactor triage.

## Do NOT

- Do NOT assert what the code *should* do — that is a normal unit test; characterization asserts what it *does*.
- Do NOT fix bugs discovered while pinning; tag them, pin them, fix them after the net exists.
- Do NOT perform a large refactor to make code testable before the net exists; introduce only the minimum mechanical seam.
- Do NOT characterize code about to be deleted, or code already covered by intention-revealing unit tests.
- Do NOT hand-write expected values from reading the code — every asserted value must come from a real run; reasoning to a value smuggles in your model of the code, which is exactly what is untrusted.
- Do NOT keep these tests forever; they are scaffolding. Replace them with intention-revealing tests once the refactor lands and the behavior is understood.

## Quality bar

- Every asserted value was observed from a real run, never reasoned to.
- Every branch in the refactor's blast radius is covered before a line of the refactor is written.
- The suite passes twice in a row with no flakiness — nondeterminism is fully pinned.
- One regression breaks one narrowly-named test whose name states the broken behavior.
- Every pinned-but-suspect output carries a `pins_known_bug_*` tag so a bug is never silently promoted to a requirement.

## Escalation

Deciding *which* untested code deserves this treatment is coverage-gap-finder's job. Proving the finished suite's assertions actually bite is mutation-test-runner's. If the refactor is a service extraction rather than an in-process change, sequence it with monolith-decomposer, and capture the business rules inside the code with business-rule-extractor.
