---
name: Playwright Testing
description: Writes and reviews end-to-end Playwright tests that survive UI refactors and never fail on timing - resilient locators, web-first assertions, isolated state, and a flake policy with quarantine rules. Use when someone asks "write a Playwright test for this flow", "why is this e2e test flaky", "how do I wait for this element", "what selector should I use", or is setting up a Playwright suite and CI config. Do NOT use for consumer/provider contract tests of API boundaries - use contract-test-writer instead; for turning product requirements into an e2e scenario inventory before any code - use e2e-scenario-author instead; for systematically diagnosing an already-flaky suite - use flaky-test-detangler instead; for designing reusable test fixtures and factories - use test-data-builder instead.
---

# Playwright Testing

A test should fail only when the app is broken - never because of timing, test order, or a CSS refactor. The costly failure mode this skill prevents is the suite the team stops trusting: once engineers rerun red builds by reflex, the suite catches nothing and its entire cost is waste.

## Operating procedure

### Step 1: gather inputs

Before writing a test, collect (label guesses as guesses):

1. The user behavior under test, phrased as intent ("user can reset password"), not implementation.
2. What is in scope: the backend too, or frontend only (decides whether to mock the network).
3. How test state is created and destroyed (API seeding, DB reset, per-test user).
4. Whether the app renders accessible roles/labels (decides locator strategy).

### Step 2: choose locators in strict priority order

1. `getByRole` with accessible name - survives any markup refactor that keeps the UI accessible.
2. `getByLabel` / `getByPlaceholder` for form fields.
3. `getByText` for non-interactive content.
4. `data-testid` only when semantics genuinely cannot identify the element - and then add the attribute to the app rather than falling through to CSS.
5. CSS/XPath tied to structure: never in new code.

Bad:

```ts
await page.locator('div.form-wrap > div:nth-child(3) button.btn-primary').click();
await page.waitForTimeout(3000);
expect(await page.locator('.msg').count()).toBe(1);
```

Breaks on any layout change, sleeps a fixed 3 s (too slow when the app is fast, flaky when it is slow), and asserts a non-retrying snapshot.

Good:

```ts
await page.getByRole('button', { name: 'Reset password' }).click();
await expect(page.getByRole('alert')).toHaveText(/check your email/i);
```

Locators encode user-visible semantics; the web-first assertion auto-retries until the alert renders or the timeout hits.

### Step 3: eliminate timing from the test

- Never `waitForTimeout` with a magic number - zero tolerance, it is the single largest flake source.
- Rely on auto-waiting actions and web-first assertions (`await expect(locator).toBeVisible()`), which retry until true.
- Wait for the condition you care about: `page.waitForResponse` for a request, `expect(page).toHaveURL` for navigation, a locator state for rendering.
- Keep the default expect timeout at 5 s and test timeout at 30 s. A test that needs more than 30 s is doing too much - split it.

### Step 4: isolate state

- One behavior per test, arrange-act-assert.
- Seed and reset data so no test depends on execution order; each test must pass alone and under `--fully-parallel`.
- Use fixtures for shared setup (auth via storage state, base URL) instead of repeating login flows - a UI login in every test adds seconds and a flake surface per test.
- Mock the network where the backend is out of scope; hit the real backend only in tests whose purpose is integration.

### Step 5: configure CI for evidence, not retries-as-a-cure

- `retries: 2` in CI, `0` locally; `trace: 'on-first-retry'` so every flake produces a trace.
- Headed locally to debug, headless in CI.
- Run new or modified tests 10 times before merging (`--repeat-each=10`); all 10 must pass.

## Flake policy (red lines)

- A test that fails then passes on retry with no code change is flaky by definition - file it, do not shrug.
- Quarantine any test that flakes more than twice in 30 CI runs (>~1% flake rate); a quarantined test is skipped from merge-blocking runs and gets an owner and a deadline. Hand systematic diagnosis to flaky-test-detangler.
- Suite-level red line: if more than 2% of CI runs need a retry to go green, stop adding tests and fix stability first - trust erodes faster than coverage accrues.

## Deliverable

Produce the test file(s) plus the config deltas: role-based locators throughout, zero fixed sleeps, fixtures for auth/setup, CI retry and trace settings, and a one-line intent-named title per test.

## Do NOT

- Do not use `waitForTimeout` - it is either wasted seconds or a flake, never correct.
- Do not chain assertions on `.count()` or other non-retrying snapshots; use web-first `expect` matchers.
- Do not share mutable state between tests or depend on run order - parallelism will eventually expose it at the worst time.
- Do not name tests after implementation ("clicks #submit-btn") - name the user intent so failures read as broken behavior.
- Do not treat CI retries as the fix for flakiness; they are instrumentation for capturing traces, not a cure.

## Quality bar

- Every locator is role/label/text-based or a justified `data-testid`; zero structural CSS/XPath.
- Zero fixed sleeps anywhere in the diff.
- Each test passes in isolation and repeated 10 times.
- Failure output alone (title + assertion + trace) tells a reader what user behavior broke.
- CI config captures a trace on first retry.
