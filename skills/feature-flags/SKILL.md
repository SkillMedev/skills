---
name: Feature Flags
description: Designs feature-flag systems and lifecycle policy — flag taxonomy (release, ops/kill-switch, experiment, permission), percentage rollouts with stable bucketing, safe defaults, and mandatory removal deadlines so flags don't rot into dead code. Use when someone asks "how should I roll this out behind a flag", "kill switch vs feature flag", "how do I do a percentage rollout", "we have hundreds of stale flags", or is wiring up LaunchDarkly or a homegrown flag service. Do NOT use for designing the A/B test analysis itself — use ab-test-analyzer instead — and do NOT use for CI/CD pipeline gating — use github-actions instead.
---

# Feature Flags

Flags decouple deploy from release: code ships dark and turns on safely, in stages, with an instant off switch. The costly mistake this skill prevents is flag debt — every flag is a fork in your codebase that doubles the test matrix, and a flag without an owner and a removal deadline set at creation becomes permanent dead code that someone flips three years later during an incident with no idea what it does.

## Operating procedure

### Step 1: Gather inputs

Before creating any flag, collect:

1. Purpose — which of the four types below it is. If the answer is "two of them", that is two flags.
2. Blast radius if the new path misbehaves (cosmetic / degraded / data-corrupting / revenue-affecting).
3. Bucketing key — usually user id; sometimes account id so a whole team sees one variant.
4. Owner (a person, not a team) and the removal condition.
5. Safe default — the value served if the flag service is unreachable.

### Step 2: Classify the flag — lifecycle follows type

- Release flag: gates unfinished or risky features during rollout. Lifetime: days to weeks. Removal deadline set at creation, maximum 90 days out; most should die within 30 days of full rollout.
- Ops flag / kill switch: disables a feature or dependency during an incident. Long-lived by design, but the inventory must stay small (a service with 50 kill switches has 50 untested code paths) and each must be re-tested on a schedule — flip it in staging at least quarterly, because an untested kill switch fails exactly when you need it.
- Experiment flag: A/B test with metrics. Lifetime equals the experiment duration plus one decision week. When the experiment concludes, the losing branch and the flag are deleted in the same PR that records the result.
- Permission flag: gates by plan or entitlement. This is not a temporary flag — it is product configuration. Model it as entitlements data, not in the same system as release flags, or it will pollute every "stale flag" report forever.

Never mix purposes in one flag; lifecycle and ownership differ.

### Step 3: Implement evaluation safely

```ts
const ctx = { userId, plan, country };
if (flags.isEnabled('new-checkout', ctx)) {
  renderNewCheckout();
} else {
  renderOldCheckout();
}
```

- Evaluate with context (user, attributes) for targeting.
- Default to the safe value — usually "off" — when the flag service is unreachable. The flag SDK failing must never take the product down.
- Cache evaluations locally and stream updates; a per-request network call to the flag service puts the flag provider on your critical path.
- Flag checks must be side-effect free and fast (microseconds, from local cache).
- Evaluate on the server and pass the decision to the client to avoid UI flicker and variant mismatch.
- Log which variant served each request — you cannot debug or analyze an experiment without exposure logs.

### Step 4: Roll out in rings

Percentage rollout requires hashing a stable key (user id) into buckets so a user's experience is consistent across sessions and services. Standard ring progression, with a soak period at each stage sized to the blast radius:

1. Internal/employees — hours to a day.
2. 1% — watch error rate and the feature's core metric for at least one business cycle (usually 24h).
3. 10% — this is where load-related problems appear; soak 1–3 days.
4. 50% — the largest honest A/B comparison you will ever get; capture metrics here.
5. 100% — the feature is launched. Start the removal clock.

Advance only when error rate and latency for the flagged cohort are within noise of control. Roll back to the previous ring on any regression — that is the entire point of the flag.

### Step 5: Remove the flag

At 100% plus the soak period, delete the flag, the old branch, and its tests in one PR. The definition of done for a flagged feature includes the flag's removal, not just its rollout.

## Worked artifact: flag lifecycle record

Bad: `new_checkout_v2` — created by a departed engineer, no expiry, still evaluated on every request, nobody knows if any traffic hits the old branch.

Good — every flag gets this record at creation:

```
FLAG: checkout-address-autocomplete
TYPE: release
OWNER: [FILL: person, not team]
CREATED: [FILL: date]
REMOVE BY: [FILL: date, max 90 days out]  <- CI warns past this date
SAFE DEFAULT: off
BUCKETING KEY: userId
BLAST RADIUS: degraded (falls back to manual address entry)
ROLLOUT PLAN: internal 1d -> 1% 1d -> 10% 2d -> 50% 2d -> 100%
ROLLBACK TRIGGER: checkout completion rate drops > 0.5% vs control,
                  or address-service error rate > 1%
REMOVAL PR: [FILL: link when the flag dies]
```

Enforce the deadline mechanically: a CI check or weekly report that flags (sic) any release flag past its remove-by date, escalating to the owner's manager after 30 days overdue.

## Deliverable

Produce a flag design containing: the flag's type and lifecycle record (owner, remove-by date, safe default, bucketing key), the ring rollout plan with per-ring soak times and a numeric rollback trigger, and the removal PR criteria.

## Do NOT

- Do not create a flag without an owner and removal deadline — flag debt is the default outcome, not the exception.
- Do not reuse a release flag as a permanent kill switch; recreate it deliberately as an ops flag with its own testing schedule.
- Do not change experiment allocation mid-experiment or use non-sticky assignment — both invalidate the results.
- Do not nest flags into combinations (`flagA && !flagB`); the test matrix goes exponential and untested interactions ship.
- Do not bucket on session id or random-per-request — users flip between variants and support tickets become unreproducible.
- Do not let the flag service become a hard dependency; an outage at the provider must degrade to safe defaults, not errors.
- Do not use different bucketing keys across services — a user must see one variant everywhere.

## Quality bar

- Every flag has exactly one type, one owner, a safe default, and (for release/experiment flags) a remove-by date.
- Rollout plan names rings, soak durations, and a numeric rollback trigger.
- Exposure logging is in place before the first external ring.
- Kill switches have a scheduled re-test cadence.
- A stale-flag report exists and is empty or actively burning down.

## Escalation

For designing the experiment metrics and reading significance, use ab-test-analyzer. For gating deploys rather than features, use github-actions or vercel-deploy-pipeline. If a flag flip is the response to an active incident, pair with sev-triage.
