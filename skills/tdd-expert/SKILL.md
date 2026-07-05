---
name: TDD Expert
description: Drive development with strict Red-Green-Refactor discipline - write one failing test, write the minimum code to pass it, refactor on green - including test-list planning and a worked kata cycle. Use when someone asks "write this test-first", "do TDD on this feature", "help me practice red-green-refactor", "write a failing test for this bug", or wants tests to drive a new module's design. Do NOT use for adding tests to existing untested legacy code before a refactor - use characterization-test-writer instead - or for designing shared fixtures and factories - use test-data-builder instead.
---

# TDD Expert

Test-driven development produces code where every line exists because a test demanded it - which means every line is covered, the design is testable by construction, and refactoring is safe. The costly mistake this skill prevents is writing production code ahead of the tests: untested branches accumulate, the tests become an afterthought that mirrors the implementation, and the safety net has holes exactly where the bugs are.

## Operating procedure

Order is the whole method - each step is only safe because the previous one finished.

### Step 1: Gather inputs and write the test list

Before any test, collect: the behavior being built (one sentence), the interface it will be called through, and the known edge cases. Then write the **test list**: a plain bullet list of every behavior and edge case you can think of - happy path, empty input, boundaries, error cases. This is planning, not test code; add to it whenever a new case occurs to you mid-cycle instead of breaking the current cycle to chase it. If requirements are ambiguous, write the ambiguity into the list as a question and pick a defensible default, labeled as a guess.

### Step 2: Red

Pick ONE item from the list - the simplest one that teaches you something. Write one failing test that describes the desired behavior through the public interface. Run it and **watch it fail** with the failure you expected. A test you never saw fail proves nothing: it may be testing nothing, or passing for the wrong reason.

### Step 3: Green

Write the minimum code to pass - ugly, hardcoded, whatever. **Minimum means minimum**: hardcoding the expected return value is correct at this stage; the next test will force the generalization. Never write production code without a failing test that requires it, and never write ahead of the current test.

### Step 4: Refactor

Only with all tests green: remove duplication, improve names, extract structure. Do not add behavior - behavior changes require going back to Red. Run the tests after every refactoring move.

### Step 5: Repeat until the list is empty

Cross off the item, pick the next. When the list is empty and no new cases occur to you, the feature is done - by definition, with full coverage.

## Worked cycle - leading-zeros kata (`wordCount`)

Item from the test list: "empty string has zero words."

Red - write the failing test:

```javascript
test('empty string has zero words', () => {
  expect(wordCount('')).toBe(0)
})
// FAIL: wordCount is not defined  ← watched it fail
```

Green - minimum code, hardcoded is correct:

```javascript
function wordCount(s) { return 0 }
// PASS
```

Next Red - force generalization with the next list item:

```javascript
test('single word counts as one', () => {
  expect(wordCount('hello')).toBe(1)
})
// FAIL: expected 1, received 0
```

Green - now the hardcode must die:

```javascript
function wordCount(s) { return s === '' ? 0 : s.split(' ').length }
// both PASS
```

Refactor - on green, clean up (handle multiple spaces when the *test list* demands it, not before):

```javascript
function wordCount(s) {
  const words = s.trim().split(/\s+/).filter(Boolean)
  return words.length
}
// all PASS - only after a "multiple spaces between words" test went red first
```

That is one full turn of the crank: fail for the expected reason, pass with the least code, refactor with the net up.

## Thresholds

- One logical assertion per test where possible; multiple asserts are acceptable only when they verify one behavior (e.g., several fields of one returned object).
- Unit test budget: each test under 100ms, the whole unit suite under 10 seconds locally - slower suites stop getting run on every Green, which breaks the loop.
- No I/O (network, disk, real clock) in unit tests unless the I/O is the behavior under test.
- Cycle length: if you have been in Red for more than about 10 minutes, the step is too big - delete the test, pick a smaller item from the list.
- Test names read like specifications: "returns zero for empty cart," not "test1".

## When TDD applies

- Strongest: new features with clear requirements, complex business logic, API contract development, and bug fixes - for a bug, first write a test that reproduces it, watch it fail, then fix.
- Weaker: exploratory spikes (write them without tests, then throw the spike away and TDD the real version), UI layout changes, simple CRUD with no logic.
- Legacy code with no tests: do not start here. Pin current behavior first with characterization-test-writer, then TDD the change inside that net. If you cannot write a test for the code at all, the design is wrong - fix the seam before the feature.

## Deliverable

Produce the test list, then the code and tests grown one Red-Green-Refactor cycle at a time, with the final suite green and each test readable as a one-line specification of behavior.

## Do NOT

- Do not write production code without a failing test that demands it - that code is unverified by construction.
- Do not write several tests ahead; batching hides which test forced which code and stalls the feedback loop.
- Do not refactor while red - you cannot tell whether a break came from the cleanup or the unfinished feature.
- Do not skip watching the test fail; a never-failed test may pass vacuously.
- Do not test implementation details (private methods, call counts on internals) - test behavior through the public interface, or every refactor breaks the suite.
- Do not "improve" the code during Green beyond what passes the test; generalization belongs to the next Red.

## Quality bar

- Every production line traceable to a test that failed without it.
- Test list fully crossed off, including error and boundary cases.
- Suite meets the runtime budget and passes deterministically (no order dependence, no shared mutable state).
- Each test asserts one behavior and names it as a specification.
- No test knows anything a caller of the public interface would not know.
