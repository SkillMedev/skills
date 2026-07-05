---
name: Mock Stub Designer
description: Designs the minimal set of test doubles for a unit or integration test and decides, per dependency, whether to stub, fake, spy, mock, or exercise the real thing. Use when someone asks "should I mock this", "how do I test code that calls Stripe or S3 or the clock", "why do our tests pass while production is broken", or when a test touches an HTTP API, payment or email/SMS SDK, database, filesystem, system clock, or randomness. Do NOT use for fabricating valid domain fixture data (a User with defaults, an Order with line items) - use test-data-builder instead; for driving a red-green implementation loop, use tdd-expert.
---
# Mock Stub Designer

A test double exists to make a test fast, deterministic, and focused - never to mock everything in sight. Over-mocking yields tautological tests that pass against a broken system because they only verify the mocks. This skill decides, per dependency, the weakest double that still isolates the boundary, so the test exercises real behavior everywhere else.

## Operating procedure

### Step 1: Gather inputs

Collect before designing anything:

1. The unit under test and the behavior being verified (one sentence).
2. Every dependency the code touches, direct and transitive-at-the-boundary: HTTP/third-party APIs, payment/email/SMS SDKs, database, filesystem, clock, randomness, queues.
3. Language and test stack (this picks the wire-interception tool: nock or msw for JS, WireMock for JVM, VCR-style cassettes for Ruby/Python, responses/respx for Python).
4. Whether a sandbox environment exists for the critical integration backstop.

If the dependency list is unknown, trace the code first - guessing the boundary list is how internal collaborators end up mocked.

### Step 2: Classify each dependency

For each dependency: external boundary you do not own (third-party HTTP, payment/email/SMS SDK, clock, randomness, filesystem, network) versus your own code (domain objects, pure functions, cheap deterministic collaborators). Double only the boundaries you do not own or cannot control. Use the real thing for your own domain objects - mocking them turns the test into a tautology.

### Step 3: Pick the weakest double that works

Apply this decision table top to bottom; stop at the first row that fits.

| Double | What it is | Use when | Red flag |
|---|---|---|---|
| Real | The actual collaborator | Your own code: domain objects, pure functions, fast deterministic classes | Mocking these tests nothing |
| Stub | Canned answers to queries, no assertions | The dependency only supplies data the test needs | Asserting on stub calls - that makes it a mock |
| Fake | Working lightweight implementation (in-memory repository, SQLite) | Many tests need a behaving dependency with state | The fake drifting from the real contract - pin it with a shared contract test |
| Spy | Records calls on the real object | Verify a side effect happened without replacing behavior | Asserting internal call sequences |
| Mock | Pre-programmed interaction expectations | The interaction IS the contract: charge exactly once, emit the audit event, send exactly one email | Any interaction assertion where the call is an implementation detail |

Thresholds: if a single test needs more than 3 doubles, the design is too coupled - report that as the finding rather than papering over it. A fake earns its maintenance cost when 3 or more tests reuse it; below that, stub inline. Allow at most one interaction assertion per test, on the one call that is the contract.

### Step 4: Intercept HTTP at the wire, not your client

Use nock, msw, WireMock, or VCR-style cassettes so serialization and URL building are still exercised. Record real responses once, replay them, and refresh cassettes on a schedule (a weekly CI job, or immediately when the provider announces a contract change) so they do not drift from the real API.

### Step 5: Inject or freeze time and randomness

Never call the wall clock or RNG directly in code that must be deterministic; inject a clock and a random source, or freeze them (fake timers, freezegun). Real-clock tests are flaky near boundaries and untestable for time logic.

### Step 6: Keep one real integration test as a backstop

For each critical integration (payments, primary datastore), retain at least one test against a real sandbox dependency so the doubles cannot lie forever. Run it in CI even if it is slower and quarantined from the fast suite.

## Worked artifact: good vs bad double design

Scenario: `OrderService.place(order)` charges a payment gateway over HTTPS and saves via `OrderRepository`.

Bad - tautological, refactor-hostile:

```python
def test_place_order_bad():
    order = Mock()                      # mocks own domain object
    order.total.return_value = 100
    repo = Mock()
    gateway = Mock()                    # mocks own client wrapper, skips the wire
    svc = OrderService(repo, gateway)
    svc.place(order)
    gateway.charge.assert_called_once_with(100)
    repo.save.assert_called_once()      # asserts internal call choreography
    assert repo.save.call_args[0][0].status == order.status  # verifies the mock, not the system
```

Every assertion checks the mocks' own wiring. Serialization, URL building, and Order's real total logic are untested; renaming an internal method breaks the test while a broken system still passes it.

Good - real domain, fake state, wire-level stub, one interaction assertion:

```python
def test_place_order_good(responses_mock):
    order = build_order(items=[item(price=100)])   # real domain object (test-data-builder)
    repo = InMemoryOrderRepository()               # fake, reused across the suite
    responses_mock.post(                           # wire-level stub: URL + JSON exercised
        "https://api.pay.example/v1/charges",
        json={"id": "ch_1", "status": "succeeded"}, status=200,
    )
    svc = OrderService(repo, HttpPaymentGateway(clock=frozen_clock))
    svc.place(order)
    assert repo.get(order.id).status == OrderStatus.PLACED   # observable outcome
    assert len(responses_mock.calls) == 1                    # the ONE contract interaction: charged exactly once
```

## Deliverable

Produce a double design for the test: a table of every dependency with its classification (own code vs boundary), the chosen double type with the one-line justification, the wire-interception tool for HTTP, the clock/randomness injection point, and the named real-sandbox backstop test for each critical integration.

## Quality bar

- Every double covers a boundary the team does not own; nothing internal and deterministic is mocked.
- HTTP is stubbed at the wire layer, not at a hand-mocked client wrapper.
- Time and randomness are injected or frozen, never read live in tested logic.
- A single fake implementation is reused across tests (and contract-tested against the real thing), not re-stubbed per test.
- Interaction assertions appear at most once per test, only where the interaction is the contract.
- No test carries more than 3 doubles without an accompanying design-smell finding.

## Do NOT

- Do NOT mock the unit under test's own domain objects, pure functions, or cheap deterministic collaborators - use them for real.
- Do NOT assert call counts or argument order for harmless internal calls; that breaks on every refactor while catching nothing.
- Do NOT mock three layers deep. If you must, the design is too coupled - report that as the finding instead of papering over it with doubles.
- Do NOT skip the wire layer by mocking your HTTP client; you lose coverage of serialization and URL building.
- Do NOT let cassettes and fakes drift - refresh recordings on a schedule and pin fakes with contract tests.
- Do NOT use this skill for fabricating valid domain or fixture data (a User with defaults, an Order with line items) - use test-data-builder. This skill decides whether and how to fake a dependency, not how to construct domain values.
