---
name: Microservices Design
description: Designs microservice architectures — service boundaries from business capabilities and team ownership, sync vs async communication choices, saga and outbox patterns for consistency, and resilience defaults — producing a boundary map with an extraction verdict per candidate service. Use when someone asks "should this be its own service", "how do I split my monolith's domains", "sync or event-driven between these services", "how do I handle transactions across services", or is reviewing a distributed architecture. Do NOT use for planning the step-by-step migration off an existing monolith — use monolith-decomposer or strangler-fig-planner for the migration process; this skill owns the target architecture. Do NOT use for designing an individual REST contract — use api-design instead.
---

# Microservices Design

Microservices trade local complexity for distributed complexity: every boundary you draw buys independent deployment and ownership at the price of network latency, partial failure, and eventual consistency. The costly mistake this skill prevents is the distributed monolith — a system with all the operational cost of microservices and none of the independence, usually created by splitting along technical layers or by letting two services share a database.

## Operating procedure

### Step 1: Gather inputs

1. Team topology: how many teams, and who owns what today. Label guesses as guesses.
2. The bounded contexts of the domain (orders, inventory, billing…), and which change together.
3. Scaling asymmetries: which module needs 10x the resources of the rest.
4. Deploy friction evidence: how often does one team's change block another's release.
5. Operational maturity: CI/CD, observability with distributed tracing, on-call. Without these, more services means more outages.

### Step 2: Apply the extraction criteria — team ownership first, size never

Start from a modular monolith and extract only when a criterion is met. In priority order:

1. Team ownership boundary: a distinct team needs to deploy and be on call for this capability without coordinating releases. This is the strongest reason — services exist to give teams autonomy. One team owning six services is usually five services too many; a service split across two teams is a coordination bug.
2. Independent scaling: the module needs materially different resources (a 10x difference, not 20%).
3. Isolation: a failure or security domain that must be blast-radius-limited (payment processing, untrusted-code execution).
4. Technology mismatch: the capability genuinely requires a different runtime.

"It's getting big" is not a criterion. Lines of code never justify a network boundary; team cognitive load does.

### Step 3: Draw boundaries on business capabilities

- Align services to bounded contexts (Order, Inventory, Billing), never technical layers (API service, DB service, "utils service").
- Each service owns its data exclusively; no other service touches its database. Shared DB = distributed monolith, full stop.
- High cohesion inside, loose coupling across. Litmus test: if two services always change in the same PR-pair, they are one service — merge them.
- Duplicate small amounts of data across services rather than creating a chatty synchronous dependency; a copied `customer_name` is cheaper than a runtime call on every request.

### Step 4: Choose communication per edge

- Synchronous (REST/gRPC) only where the caller cannot proceed without the answer (auth check, inventory reservation at checkout). Every sync edge couples your availability: two services at 99.9% in series give 99.8%.
- Asynchronous (events/queues) for everything else — workflows, notifications, read-model updates. Prefer async by default for resilience.
- Contracts are explicit and versioned (OpenAPI, protobuf, AsyncAPI); backward-compatible changes only, additive schema evolution, tolerant readers. Never break an existing event consumer.

```text
Order Service --(OrderPlaced event)--> Inventory Service
                                   --> Notification Service
```

- Rule of thumb: if a request fans out synchronously to more than 2 downstream services, redesign — either the boundary is wrong or the read belongs in a CQRS read model fed by events.

### Step 5: Design consistency deliberately

- Distributed transactions are off the table; use the Saga pattern — a sequence of local transactions with compensating actions for rollback (e.g. `CancelReservation` compensates `ReserveStock`). Every step must have a written compensation before the saga ships.
- Use the Outbox pattern to publish events atomically with the local DB write; a bare "write then publish" loses events on crash.
- Make every consumer idempotent and deduplicate by event id — at-least-once delivery is the reality of every broker.
- Embrace eventual consistency and design UIs to tolerate it (optimistic "order received" states, not spinners waiting on five services).

### Step 6: Apply the resilience defaults on every remote call

- Timeout on every call — no exceptions; 1–5s for user-facing sync calls, never "infinite".
- Circuit breakers on dependencies so a failing service isn't hammered into a longer outage.
- Retries with exponential backoff and jitter, for transient errors only, and only on idempotent operations.
- Bulkheads: separate connection/thread pools per dependency so one slow dependency can't exhaust the caller.
- Propagate a correlation/trace id across every hop; without distributed tracing, debugging n services is guesswork.
- Externalize service discovery and config; never hardcode endpoints.

## Worked artifact: boundary decision — good/bad pair

Bad: an e-commerce team of 8 engineers splits into `api-gateway`, `user-service`, `product-service`, `order-service`, `payment-service`, `email-service`, `search-service`, `admin-service` — eight services, one team, one shared Postgres. Result: every feature touches four repos, releases are lockstep, one DB migration blocks everyone. Distributed monolith.

Good: the same team ships a modular monolith with enforced module boundaries (orders, catalog, identity) plus exactly one extracted service: `payment-service`, because it is a PCI isolation domain with its own database and an async `PaymentCaptured` event. When the company later adds a dedicated search team, `search-service` is extracted along that ownership line, fed by `ProductUpdated` events.

Extraction verdict template:

```
CANDIDATE: [FILL: capability]
Owning team (exactly one): [FILL]           -> no single team? STOP, fix org first
Criterion met: ownership / scaling(10x?) / isolation / tech  [FILL + evidence]
Data owned exclusively: [FILL: tables/entities moving with it]
Edges: sync -> [FILL: callers + why sync]   async -> [FILL: events in/out]
Consistency: sagas touching it: [FILL], compensations written: yes/no
Verdict: EXTRACT / KEEP AS MODULE / MERGE WITH [FILL]
```

## Deliverable

Produce a boundary map: the list of services (and modules deliberately kept in the monolith), each with owning team, exclusive data, sync and async edges, saga/compensation notes, and an extraction verdict with the criterion that justified it.

## Do NOT

- Do not split by technical layer — an "API layer service" and "data layer service" couple every change across both.
- Do not share a database between services; it silently welds their schemas and deploys together.
- Do not extract because a module is large; size is a refactoring problem, not a network-boundary problem.
- Do not default to synchronous calls — each one multiplies unavailability and latency down the chain.
- Do not ship a saga without written compensations for every step.
- Do not give one small team many services; the operational load per service is roughly constant and it lands on them.
- Do not start microservices without tracing, centralized logging, and per-service CI/CD already working.

## Quality bar

- Every service maps to a business capability and exactly one owning team.
- No shared databases anywhere on the map; every arrow is labeled sync or async with a reason.
- Every sync chain is ≤ 2 hops deep from the entry point.
- Every cross-service write path names its saga and compensations; every consumer is idempotent by event id.
- Timeouts, retries-with-jitter, and circuit breakers are stated defaults, not aspirations.

## Escalation

For the migration sequencing off an existing monolith, use monolith-decomposer and strangler-fig-planner — this skill decides the target, those decide the path. Individual API contracts go to api-design or graphql-schema; event pipeline design to kafka-pipelines; circuit-breaker implementation detail to circuit-breaker-builder; idempotent consumers to idempotency-enforcer.
