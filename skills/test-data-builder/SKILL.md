---
name: Test Data Builder
description: Builds realistic domain fixtures, factories, and edge-case datasets with the builder pattern and valid defaults, so each test owns exactly the data it asserts on. Use when someone asks "how should I set up test data", "my fixtures broke half the suite", "make a factory for this model", "why is this test flaky only in CI", or a test needs domain objects, seed data, or boundary inputs. Do NOT use when a test needs to fake a network call, database client, clock, or third-party SDK — use mock-stub-designer instead; do NOT use for designing the assertions or test cases themselves — use tdd-expert instead.
---

# Test Data Builder

Produce per-test domain data that is realistic, deterministic, and isolated. The costly failure this prevents is the shared fixture file that becomes an append-only god object: one test tweaks a record, three unrelated tests start flaking, and nobody can delete a fixture row because nobody knows who depends on it. Builders with valid defaults make every test self-describing and every failure reproducible.

## Operating procedure

Order matters: defaults must be valid before overrides mean anything, and determinism must be in place before you add realistic randomness.

### Step 1: Gather inputs

Collect before writing a factory. Defaults in parentheses; label guesses as guesses.

1. The domain object(s) under test and their required fields.
2. The stack's factory library (FactoryBot for Ruby, Fishery or test-data-bot for TypeScript, factory_boy for Python, Bogus/AutoFixture for .NET; default: hand-rolled builder if none exists).
3. Whether tests touch a real database, and the isolation mechanism available (per-test transaction rollback preferred).
4. The invariants a "valid" object must satisfy — validations, foreign keys, non-null constraints.

### Step 2: Build per test, never share a global fixture

Construct the objects each test needs inside that test via a factory or builder (FactoryBot, Fishery, test-data-bot, Mother objects). Do not load one static fixture file for the suite — shared mutable fixtures silently couple tests, and the coupling only surfaces as ordering-dependent flakes.

### Step 3: Expose a builder with valid defaults; override only what the test cares about

Write `aUser().withRole('admin').build()` so the override is the test's documentation and the reader ignores the irrelevant fields. Defaults must always produce a valid, persistable object: when a new required field is added to the schema, set it once in the factory and zero unrelated tests break.

Bad — the reader cannot tell which of nine fields the test is actually about, and the next required field breaks this test and forty like it:

```typescript
const user = new User({
  id: 42, name: 'Test User', email: 'test@test.com',
  role: 'admin', locale: 'en', createdAt: new Date(),
  emailVerified: true, plan: 'free', avatarUrl: null,
})
expect(canDeleteWorkspace(user)).toBe(true)
```

Good — one override, and it is the point of the test:

```typescript
// user-builder.ts — valid defaults live in exactly one place
export const aUser = () => {
  let attrs: UserAttrs = {
    id: seq(), name: faker.person.fullName(),
    email: faker.internet.email(), role: 'member',
    locale: 'en', createdAt: fixedNow, emailVerified: true,
    plan: 'free', avatarUrl: null,
  }
  return {
    withRole: (role: Role) => { attrs = { ...attrs, role }; return api },
    unverified: () => { attrs = { ...attrs, emailVerified: false }; return api },
    build: () => new User(attrs),
  }
  // (api = the object above; shown flat for brevity)
}

// the test
const admin = aUser().withRole('admin').build()
expect(canDeleteWorkspace(admin)).toBe(true)
```

### Step 4: Make data realistic but deterministic

Use Faker (Faker, faker.js, Bogus) for names, emails, and addresses so fixtures resemble production and surface encoding and length bugs that `"test"` never would. Seed the faker with a fixed seed in CI so a failing run reproduces exactly. The rule: randomness may vary the shape of inputs; it must never decide whether an assertion passes.

### Step 5: Build the inputs that break code

Deliberately construct: empty strings, unicode and emoji, very long strings, leading/trailing whitespace, nulls, zero, negative numbers, max integers, timezone-boundary dates, and duplicate keys. For ranges, always build the boundary value and the just-over-boundary value — off-by-one bugs live exactly there. Name each edge-case test after the boundary it exercises. For input spaces too large to enumerate, reach for property-based generators (Hypothesis, fast-check) instead of hand-picking.

### Step 6: Keep persistence cheap and isolated

Build in memory by default; only persist (`create` vs `build`) when the test needs a real row. Wrap DB-touching tests in a transaction rolled back per test so factories never leak state between tests. Do not rely on sequences or global counters surviving across tests — the suite must pass in any order and in parallel.

## Deliverable

Produce, for each domain object under test: a builder or factory whose bare defaults yield a valid persistable object, override methods named for intent (`unverified()`, `withRole()`), a seeded-faker configuration for CI, and an edge-case dataset covering the boundaries in Step 5 with each case named for its boundary.

## Do NOT

- Do not build elaborate object graphs a test never asserts on — every extra field is a false signal about what the test checks.
- Do not wrap a one-field value object in a builder; a literal is clearer than ceremony.
- Do not let a builder reach across the boundary to fake network, DB clients, clocks, or third-party SDKs — that is mock-stub-designer's job, and mixing the two makes builders untestable.
- Do not seed randomness from the wall clock or leave it unseeded in CI — an unreproducible flake costs more than the realism buys.
- Do not "fix" a broken shared fixture by adding another record to it; migrate the affected tests to builders instead.

## Quality bar

- Every factory's defaults alone produce a valid, persistable object.
- A reader can tell what a test asserts from its overrides without opening the factory file.
- The suite passes identically on repeated runs and in any order; no test depends on another's data.
- Edge-case tests name the specific boundary they exercise.
- Adding a required field to the schema touches exactly one factory, zero tests.
