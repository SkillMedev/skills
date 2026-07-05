---
name: Coverage Gap Finder
description: Produces a risk-ranked list of untested critical paths and branches from a real branch-coverage report crossed with git churn, naming the specific missing cases and the smallest test that buys the most safety. Use when someone says "we have 85% coverage but I don't trust it", "what should we test next", "audit the suite before this release", or "which coverage gaps actually matter". Do NOT use to prove existing assertions are strong — covered-but-unasserted lines are mutation-test-runner's job; do NOT use to pin the current behavior of legacy code before a refactor — use characterization-test-writer instead. This skill decides what to test and in what order, not how well a test asserts or how to lock down legacy.
---
# Coverage Gap Finder

Find the untested paths that would actually hurt, ranked by risk — never chase a line-coverage percentage. Coverage is necessary but not sufficient: a line can execute under test while nothing asserts its output, and a percentage says nothing about whether the uncovered 15% is dead config or the payment splitter. The costly mistake this skill prevents is a team burning a sprint raising 85% to 95% while the branch that corrupts money stays dark.

## Operating procedure

### Step 1: Gather inputs

- The coverage report — and whether it includes branch coverage. If it is line-only, regenerate before anything else (Step 2). No report means no prescription.
- Repo access for churn analysis, or at minimum the list of recently active files.
- The domain's crown jewels: where money moves, auth decisions happen, data is deleted or migrated. If the user cannot name them, propose a list and label it a guess.
- Release timeline, which sets how deep the ranked list must go.

### Step 2: Generate a real branch-coverage report

Turn on branch/condition coverage — Istanbul/nyc branches, coverage.py `--branch`, JaCoCo, SimpleCov branch mode — and read the uncovered-branch report. Branch beats line because a line with an if/else can be 100% line-covered while one arm never runs; compound conditions (`a && b`) additionally need condition coverage to confirm each operand was exercised both ways. Do not prescribe from line coverage.

### Step 3: Risk-weight every gap

Risk = blast radius × change frequency × ambiguity.

- Blast radius: top tier is money movement, auth/authorization, data deletion, migrations; middle tier is user-visible correctness; bottom tier is logging, formatting, static config.
- Change frequency: cross uncovered files against git churn — `git log --format= --name-only | sort | uniq -c | sort -rn` — because frequently and recently changed files with low coverage are the highest-leverage targets.
- Ambiguity: complex conditionals, date/time math, currency rounding, and concurrency carry more latent-bug risk per branch than straight-line code.

A static config file at 0% is fine; a payment splitter at 0% is an incident waiting.

### Step 4: Enumerate missing cases by reasoning, not by reading the gutter

For each high-risk path, name the specific cases: empty input, null, boundary values (0, -1, max), error/exception paths, timeouts, concurrent access. Catch blocks and early returns are the most commonly uncovered branches and the ones that fail in production. Coverage tools rarely flag a missing state *combination* — you must name it yourself.

### Step 5: Pick the smallest test per path and order the list

Choose the test type deliberately — unit for branch logic, integration for cross-boundary behavior — and order so the highest-risk, lowest-cost test comes first. Recommend a branch-coverage floor on the critical modules in CI (e.g. 90% on payments/, enforced per-module), never a global percentage target.

## Worked artifact: ranked gap plan (contrast pair)

Bad:

```
- payments.py is at 62%, add more tests
- utils.py is at 40%, add more tests
```

Good:

```
1. RefundProcessor.split_refund — uncovered else-branch at partial-refund path
   Risk: money movement x 14 commits last quarter x currency rounding
   Missing cases: refund > remaining balance; zero-amount refund; concurrent double-refund
   Test: unit, table-driven over the three cases. WRITE THIS FIRST — one test kills
   the highest-blast-radius uncovered branch in the repo.
2. AuthMiddleware.check_scopes — condition coverage shows `is_admin || has_scope`
   never evaluated with is_admin=false, has_scope=true
   Risk: authorization x 6 commits x compound conditional
   Missing case: scoped non-admin token on admin route. Test: integration (route-level).
3. migrations/0042 rollback path — 0% coverage, runs once but irreversibly
   Missing case: rollback after partial apply. Test: integration against throwaway DB.
Skipped as non-gaps: generated client (0%), __repr__ methods, log formatters.
CI floor recommendation: branches>=90% on payments/ and auth/; no global target.
```

## Deliverable

Produce a ranked gap plan: for each entry the path, why it is risky (blast radius, churn count, ambiguity), the specific untested cases, the test type, and the single test to write first — plus a named non-gap list and a per-module CI branch-coverage floor recommendation.

## Do NOT

- Do not rank by line-coverage percentage or demand 100% — the last 10% usually tests the least risky code.
- Do not treat high coverage as safety; coverage is necessary, not sufficient — covered-but-unasserted lines are coverage theater, and proving assertion strength is mutation-test-runner's job.
- Do not flag generated code, trivial getters, framework glue, or one-line pass-throughs as gaps.
- Do not stop short of the risk weighting — an unranked gap list is just the coverage report restated.
- Do not recommend a global coverage percentage as the fix; recommend per-module floors on critical code.
- Stop when remaining gaps are low-risk and the cost of the test exceeds the cost of the bug.

## Quality bar

- Every recommendation cites an actual uncovered branch from the report or a named missing case — no "add more tests here."
- The ranking is justified by blast radius and churn, not by which file has the lowest percentage.
- The top recommendation is the cheapest test with the biggest risk reduction, and the plan says why.
- The plan names what was deliberately excluded, so nobody re-litigates the generated code.

## Escalation

Hand covered-but-suspect assertions to mutation-test-runner. If a high-risk gap sits in legacy code about to be refactored, pin it with characterization-test-writer before touching it. Flaky results contaminating the report belong to flaky-test-detangler.
