---
name: Skill Tester
description: Empirically tests a skill with subagent scenarios to verify it fires on the right prompts, stays silent on the wrong ones, and performs its job when loaded. Use when validating a new or modified SKILL.md before publishing — "test this skill", "does this trigger correctly", "verify the skill works". Do NOT use to statically grade or rewrite skill quality (use skill-auditor) — this runs live behavioral tests.
---

# Skill Tester

Verifies empirically that a skill fires on the right prompts, stays silent on unrelated ones, and produces the behavior its body promises when loaded. Static review (skill-auditor) judges the text; this skill judges the behavior. A skill that reads perfectly and never fires is still broken.

## Inputs to collect

1. The SKILL.md under test, from source.
2. The neighbor skills whose territory borders it (for negative scenarios).
3. The intended user phrasing, if the author collected any — real phrasing makes the best positive scenarios.

## Operating procedure

### Step 1 — Write the scenario matrix

For each skill, write at minimum:

- **Three positive trigger scenarios** — prompts that must fire the skill. Paraphrase the description's WHEN clause in different words; do not copy it verbatim, or you test string matching instead of routing.
- **Two negative trigger scenarios** — adjacent tasks the skill must NOT hijack. The best negatives are the named neighbors from the description's NOT clause; the second-best are near-miss requests one topic over.
- **Two performance scenarios** — full realistic requests where you will judge the output against the skill's own quality bar and deliverable sections.

Example for a database-migration skill:
- Positive: "add a nullable column to a live orders table without downtime"
- Positive: "we need to backfill a new field across 40M rows"
- Negative: "design the schema for a new app" (neighbor: database-schema)
- Negative: "why is this query slow" (neighbor: sql-query-optimizer)

### Step 2 — Run trigger tests

Launch each scenario as a fresh subagent with the skill (and its neighbors, when testing collisions) available. Record for each: did the skill load, did anything else load instead, did the agent act on the skill's instructions. Run positives and negatives in the same batch so conditions match.

### Step 3 — Run performance tests

For the two performance scenarios, judge the output against the skill's own contract:

- Did the agent follow the operating procedure in order?
- Did it elicit the inputs the skill says to collect, instead of assuming them?
- Does the output contain the Deliverable the skill promises?
- Does the output violate anything in the skill's Do NOT section?
- If the skill contains a calculator or template, was it actually used, and does the calculator run and produce the documented output?

### Step 4 — Iterate to green

- Trigger failures → revise the description's WHEN clause; add the missing trigger terms or strengthen the NOT clause (hand to skill-description-writer if the fix is description-only).
- Performance failures → revise the body section for the failing behavior; a procedure the agent skipped is usually a procedure stated as a principle instead of a numbered step.
- Re-run the failing scenario after each revision. Do not publish until all scenarios pass.

## Pass criteria

- All positive scenarios fire the skill; all negative scenarios do not.
- No collision: neighbors stay silent on this skill's positives and vice versa.
- Both performance outputs satisfy the skill's own quality bar and include its deliverable.
- Any embedded calculator runs as documented; any template's FILL fields survive into the output.
- No frontmatter parsing errors.

## Deliverable

A test report: the scenario matrix with pass/fail per scenario, the observed failure behavior for each fail (what fired instead, which step was skipped), and the specific revision made or recommended for each failure.

## Do NOT

- Do not test with the description's own words as the positive prompt — that validates string overlap, not routing.
- Do not skip negative scenarios; a skill that fires on everything looks great in positive-only testing and is a defect in production.
- Do not judge performance by general output quality; judge against the skill's own quality bar and deliverable, or the test cannot fail meaningfully.
- Do not rewrite the skill wholesale from inside a test run — report, fix the specific failing clause, re-run. Structural rewrites go to skill-auditor.
