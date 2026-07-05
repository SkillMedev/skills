---
name: Monolith Decomposer
description: Finds and validates one bounded-context seam to extract from a monolith - gated on coupling-graph, co-change, data-ownership, and transaction-boundary evidence - and sequences an incremental strangler-fig extraction plan. Use when someone asks "where should we cut this monolith", "is this module ready to extract as a service", "plan the extraction of billing from the app", or is evaluating whether a candidate seam is clean. Do NOT use for designing the target service architecture, service communication, or greenfield service boundaries - use microservices instead; do NOT use to document the implicit business rules inside the code being moved - use business-rule-extractor instead. This skill decides where to cut and in what order.
---
# Monolith Decomposer

Extract one bounded-context seam at a time, only after data ownership and coupling evidence prove the cut is clean. Cut along the wrong seam and you get a distributed monolith: the latency, failure modes, and operational cost of microservices with the coupling of a monolith - the single most expensive architecture mistake a team can make.

## Operating procedure

Evidence gates come before design, and design before sequencing, because a seam that fails the data-ownership veto in Step 3 makes every downstream step wasted work.

### Step 1: Gather inputs and confirm the motive

Collect: the candidate context (if any), the driver (independent scaling, deploy cadence, team ownership - the only three valid ones), team size, and current deploy pain. If the driver is slow deploys, flaky tests, or unclear modules, stop: modularize in-process first. A well-modularized monolith beats a badly-cut set of services on nearly every axis. Label an unverified driver as a guess and verify it before proceeding.

### Step 2: Pick a candidate bounded context

Name one cluster of behavior with high internal cohesion and a thin, stable interface to the rest - a domain concept (DDD bounded context / aggregate), not an org-chart team or a technical layer. Extract exactly one per effort.

### Step 3: Run the seam-finding procedure - three artifacts, then the veto

Do not prescribe a cut without all three:

1. **Coupling graph.** Run madge / dependency-cruiser (or the language equivalent) scoped to the candidate. Count inbound call sites and shared symbols. Practitioner heuristic: a good seam has inbound call sites countable on one hand routing through few entry points and a shared vocabulary of a dozen types or fewer; dozens of scattered inbound edges means the seam is wrong or needs in-process consolidation first.
2. **Co-change report.** From history: `git log --format= --name-only | sort | uniq -c | sort -rn`, then check which files outside the candidate change in the same commits as files inside it. Files that always change together belong together - high cross-boundary co-change is a seam smell. Working thresholds: cross-boundary co-change in more than ~25% of the candidate's commits is a smell to resolve before extracting; under ~10% the seam reads clean (the artifact below shows both readings).
3. **Transaction boundaries.** List every transaction the candidate participates in and every table it reads/writes, with who else touches each.

**The data-ownership veto:** a service that does not own its data is not a service. If the candidate and the monolith both need write access to the same tables inside one transaction, the seam is wrong or not ready - stop and return to Step 2.

### Step 4: Plan the data split

Decide which tables move, how foreign keys across the new boundary break (typically: replace with soft references validated asynchronously), and how cross-boundary joins are replaced (API composition or a local read model). Specify the migration mechanism - dual-write or CDC - plus a reconciliation check (row counts and checksums on both sides) before and after cutover. Never dual-write without reconciliation.

### Step 5: Design the anti-corruption layer

Put a translation facade at the boundary so the extracted service speaks its own clean domain language and the monolith keeps its own. This lets either side fix its model without a coordinated big-bang change.

### Step 6: Plan distributed failure modes per operation

Each former local call is now a network call that times out, retries, and partially fails. For every operation crossing the seam, decide whether it can be eventually consistent; add idempotency (pair with idempotency-enforcer), timeouts, circuit breakers (circuit-breaker-builder), and a trace that follows the request across the boundary. If any operation truly needs cross-service ACID, the seam is in the wrong place - return to Step 2.

### Step 7: Sequence strangler-style, sized in weeks not months

Each increment must be independently shippable and reversible, ideally 1-3 weeks of work; an increment that cannot ship alone is two increments:

1. Create the seam in-process - a clear interface/module inside the monolith - and prove every caller routes through it (grep for bypasses; fail CI on new ones).
2. Move the implementation behind a network call with the old path behind a flag; run shadow/parallel traffic and compare outputs.
3. Migrate data with dual-write or CDC; reconcile.
4. Cut over via the flag; keep the old path warm.
5. Delete the in-monolith code only after the service has served real production traffic through a full business cycle (including month-end or peak, whichever stresses it).

## Worked artifact: seam evidence summary

```
SEAM CANDIDATE: Billing (invoices, payments, dunning)

Coupling graph (dependency-cruiser):
  Inbound call sites: 4  (OrderService.checkout, AdminController x2, cron/dunning)
  Shared types crossing boundary: 6 (Invoice, Payment, Money, CustomerRef, TaxLine, Currency)
  Verdict: PASS - few entry points, small vocabulary

Co-change (last 12 months):
  billing/* co-changes with orders/checkout.rb in 31% of billing commits  <- investigate
  billing/* co-changes with anything else in <5% of commits
  Verdict: CONDITIONAL - decouple checkout's inline tax call first

Data ownership:
  invoices, payments, dunning_events: written ONLY by billing        PASS
  orders.balance_cents: written by billing AND orders in one txn     VETO
  Resolution: move balance to billing-owned ledger; orders reads via API/event

Cross-seam operations: checkout->createInvoice (eventually consistent OK, idempotency
key = order_id), refund (sync, timeout 2s + circuit breaker). No cross-service ACID. PASS
```

## Deliverable

Produce an extraction plan containing: the named bounded context, the three evidence artifacts with pass/veto verdicts, the table-by-table data split with migration mechanism and reconciliation checks, the anti-corruption layer sketch, a per-operation consistency and failure-mode table, and the staged strangler sequence with a rollback path per stage.

## Do NOT

- Do NOT cut along the org chart or a technical layer; cut along a bounded context.
- Do NOT propose a seam from intuition - produce the coupling graph, co-change report, and table-ownership map first.
- Do NOT extract a service that shares write access to tables with the monolith inside one transaction.
- Do NOT do a big-bang cut; never delete the in-monolith path before the service has served real traffic through a full cycle.
- Do NOT dual-write or use CDC without a reconciliation check on both sides.
- Do NOT extract to fix slow deploys, flaky tests, or unclear modules - modularize in-process first; only independent scaling, deployment, or team ownership justify the network boundary.
- Do NOT extract more than one context per effort.

## Quality bar

A seam is ready to extract only when all hold: it maps to a named bounded context; inbound call sites are few and pass through one interface with a small shared vocabulary; the candidate exclusively owns the tables it writes; a data-migration plan with reconciliation exists; every cross-seam operation has a defined consistency model with none requiring cross-service ACID; and the extraction is staged in-process seam → network-behind-flag → cutover → delayed delete, each stage reversible.

## Escalation

Target-state service design and inter-service communication patterns belong to microservices. Pinning the legacy behavior before moving it belongs to characterization-test-writer; documenting its business rules to business-rule-extractor; database migration safety to migration-safety-checker.
