---
name: Contract Test Writer
description: Writes consumer-driven contract tests at service and API boundaries — Pact consumer tests, provider verification, broker publishing, and can-i-deploy gates — so an incompatible change fails a build instead of breaking integrations in production. Use when someone asks "how do I stop the backend breaking my client", "set up Pact between these services", "a renamed field broke a consumer that unit tests passed", or two separately deployed services must stay in sync. Do NOT use for designing a new API contract from scratch or reviewing its design — use api-design instead; do NOT use for end-to-end user-journey tests — use e2e-scenario-author instead; do NOT use for calls inside a single deployable — write a unit test.
---

# Contract Test Writer

Pin the shape of a network boundary from the consumer's side so a provider change that breaks any deployed consumer fails the provider's build, not production. The costly failure this prevents is the silent break: both teams' unit tests pass, both deploys go green, and the integration dies at runtime because a field was renamed or a status code changed and nothing in either pipeline knew the other side existed.

## Operating procedure

The consumer-driven order is the point: the consumer declares what it needs first, and the provider verifies against that declaration. Reversing it (provider publishes a schema, consumers hope) is how unread fields calcify and real dependencies go untracked.

### Step 1: Gather inputs

1. The boundary: consumer name, provider name, and the calls in play. Confirm it is a real network boundary you do not control end-to-end (cross-service call, client-to-API). For an internal function call inside one deployable, stop — write a unit test instead.
2. The fields the consumer actually reads from each response — from the consumer's code, not the provider's docs.
3. Broker availability: Pact Broker or PactFlow URL and write credentials (default: recommend standing one up; pacts committed to a repo go stale silently).
4. The environments where consumer versions run (prod, staging), for can-i-deploy.

Label as a guess any field list not confirmed against consumer source.

### Step 2: Write the consumer test against a mock provider

Declare only the request shape and the exact response fields the consumer reads — never the provider's full schema. Over-specifying makes the provider brittle to harmless additions. The consumer-driven rule: a field enters the contract only when a consumer consumes it.

### Step 3: Match on type and shape, not literal values

Use matchers: `like()` for scalars, `eachLike()` for arrays, `term()`/regex for patterns. Pin a literal only when it is semantically part of the contract — an enum member, a specific status code.

Bad — pinned fixture values plus fields the consumer never reads; harmless data changes now break the build:

```javascript
.willRespondWith({
  status: 200,
  body: {
    id: 42, name: 'Alice Smith', email: 'alice@example.com',
    createdAt: '2021-03-04T10:00:00Z', internalScore: 0.87, region: 'us-east-1',
  },
})
```

Good — matchers for shape, literals only where meaning lives, only consumed fields:

```javascript
.willRespondWith({
  status: 200,
  body: {
    id: like(42),
    name: like('Alice Smith'),
    status: term({ matcher: 'active|suspended', generate: 'active' }), // enum: pinned
  },
})
```

### Step 4: Declare coarse provider states

For responses that depend on data, declare a coarse, reusable provider state ("user exists", "user is suspended") in the consumer test rather than encoding exact rows. Fine-grained states ("user 42 with 3 orders placed on Tuesday") multiply provider setup hooks and couple the contract to fixtures.

### Step 5: Publish the pact

Run the consumer test to emit the pact file. Publish to the broker tagged with the consumer version and git sha, plus the branch. The broker's matrix of verified consumer-provider version pairs is what makes Step 7 possible.

### Step 6: Verify in the provider's pipeline

In the provider's own test suite, replay the pact against the real provider: stand up the endpoint, implement a setup hook for each declared provider state, and verify recorded requests against expectations. A renamed field or changed status code must fail this build. Verification that runs anywhere other than the provider's merge-blocking pipeline is decoration.

### Step 7: Gate deploys with can-i-deploy

Run can-i-deploy against the consumer versions actually running in each target environment — not just the latest pact on main. Older consumer versions still deployed are still contracts in force; a provider change compatible with main but not with the version running in prod is still a production break.

## Deliverable

Produce: (A) consumer tests emitting a pact whose every field is traceable to a consumer read, (B) broker publishing wired with version, sha, and branch tags, (C) provider verification with state hooks running in the provider's merge-blocking CI, and (D) a can-i-deploy gate checking every environment's deployed consumer versions before release.

## Do NOT

- Do not assert exact field values that are not part of the contract — it produces false breaks on harmless data changes, and false breaks teach teams to ignore the gate.
- Do not include unread response fields just because the provider returns them — every extra field is a freedom the provider loses for nothing.
- Do not verify business correctness, performance, or auth flows here — keep logic in unit tests and critical journeys in a thin E2E layer; a contract test asserts shape compatibility only.
- Do not write contract tests for calls inside a single deployable — the compiler and unit tests already cover that boundary.
- Do not gate deploys against only the latest pact when older consumer versions are still running in an environment.
- Do not let the provider team write the "consumer" test from their own schema — that inverts consumer-driven and re-creates the original blindness.

## Quality bar

- The contract contains only fields the consumer consumes — nothing the provider happens to return.
- Matchers everywhere except deliberately pinned enums and status codes; zero coupling to fixture values.
- Provider verification runs in the provider's pipeline and a deliberately renamed field demonstrably fails it.
- Provider states are coarse and shared; can-i-deploy checks every deployed consumer version, not just main.
- Both teams can name where the pact lives and which build breaks when compatibility does.
