---
name: Flaky Test Detangler
description: Root-causes intermittently failing tests and eliminates the hidden dependency at its source instead of retrying around it. Use when a test passes locally but fails in CI, goes green on a CI re-run, fails roughly one run in ten, or is already tagged "flaky." Do NOT use when the task is to design the fake or stub that replaces a real dependency — use mock-stub-designer instead.
---
# Flaky Test Detangler

A flaky test is not random — it is a hidden dependency on something you did not control (order, timing, shared state, a real clock, or a real network). Your job is to name that dependency and remove it, never to add a retry.

## Workflow

1. **Reproduce before theorizing.** Never diagnose from a single failure. Run the suspect test 100x in isolation, then 100x inside the full suite. Failing only in the suite means cross-test contamination; failing alone means the test owns the flake.
2. **Randomize order and capture the seed.** Run with randomized order (Jest `--shuffle`, RSpec `--order random`, pytest-randomly) and record the failing seed plus the order so the failure is reproducible. This seed is your gating evidence — do not accept any fix until it makes the captured seed pass deterministically.
3. **Kill fixed-time waits.** The top cause is waiting on a sleep instead of a condition. Replace `setTimeout`/`sleep` with explicit waits on observable state: Playwright auto-waiting locators and `expect.poll`, Cypress retry-ability, awaitility on the JVM. Never assert immediately after firing an async action.
4. **Isolate shared mutable state.** Tests that pass alone but fail together share a singleton, a module-level cache, a seeded row, an unrolled-back transaction, an env var, or the system clock. Make each test set up and tear down its own world: wrap DB tests in a per-test transaction that rolls back, or truncate between tests.
5. **Freeze the clock and cut the network.** Pin time with fake timers (`jest.useFakeTimers`, sinon, freezegun) so midnight and DST cannot break a test. Replace live outbound HTTP with a controlled stub — a test hitting a real endpoint is flaky by definition. When the stub or fixture itself needs designing, hand that off to mock-stub-designer; here you only require that the dependency is no longer real.
6. **Remove order assumptions.** Never assume insertion order from a hash, set, or unordered query — sort before asserting.
7. **Quarantine only what you cannot yet fix.** Tag a confirmed-flaky test as skipped-but-still-running (non-blocking) with a linked ticket, an owner, and a deadline, so you keep collecting failure data. Quarantine is a holding cell, not a verdict.
8. **Stop at the root cause.** Close the case when you can name the dependency and the captured seed passes deterministically. If 500x of looping yields no repro and the test guards critical behavior, ship structured logging and the failing seed to CI artifacts instead of deleting it. Delete a test only when it asserts nothing meaningful or duplicates coverage.

## Worked example

The most common flake, and its fix:

Bad — passes whenever the save completes within 2 seconds, fails on a loaded CI runner:

```js
fireEvent.click(saveButton);
await new Promise((r) => setTimeout(r, 2000)); // "give it time to save"
expect(screen.getByText('Saved')).toBeInTheDocument();
```

Good — waits on the observable condition, fails only when saving is actually broken:

```js
fireEvent.click(saveButton);
await waitFor(() => expect(screen.getByText('Saved')).toBeInTheDocument());
```

The bad version encodes a hidden timing dependency ("the save always finishes in under 2s on my machine"). The good version removes the dependency instead of padding it — no sleep survives, and the test's pass/fail now tracks the behavior it names.

## Deliverable

Produce a fix PR plus a short root-cause note that names the hidden dependency in one sentence (order / timing / shared state / clock / network), records the captured failing seed and order, and shows the rerun evidence (e.g. 100/100 passes on the previously failing seed). If the test is quarantined instead of fixed, the note carries the ticket link, owner, and deadline.

## Quality bar

- You can name the controlled dependency in one sentence (order / timing / shared state / clock / network).
- The previously failing seed and shuffled order now pass deterministically across at least 100 reruns.
- The fix removed a hidden dependency; it added no new `sleep`, no new retry, and no widened timeout.
- Any quarantined test has a ticket, an owner, and a deadline — not an open-ended skip.

## Do NOT

- Do NOT add an auto-retry or rerun as the fix. CI auto-retry is a last-resort bandage that hides cost; track retry rate as a quality metric, never as the solution.
- Do NOT add or extend arbitrary `sleep`/`setTimeout` to "stabilize" a test — ban this in review.
- Do NOT diagnose or declare a fix from a single run without a captured failing seed.
- Do NOT delete a flaky test that guards real behavior just to make CI green.
- Do NOT design the replacement mock/stub/fixture here — that is mock-stub-designer's job.
