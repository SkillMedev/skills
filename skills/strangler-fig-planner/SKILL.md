---
name: Strangler Fig Planner
description: Produces an incremental migration plan that runs a legacy and a new system side by side behind a routing seam, slicing and sequencing whole capabilities so the old system stays live until its last route is cut. Use when planning to replace or rebuild a large, business-critical system that must keep serving traffic throughout. Do NOT use when extracting a single service from a still-living monolith — use monolith-decomposer instead; for generic non-migration implementation planning, use the plan skill instead.
---
# Strangler Fig Planner
Plan a route-by-route replacement of a live system: install a routing seam, migrate one vertical capability at a time, and delete the legacy trunk only after each slice has soaked at full traffic.

## Workflow
1. **Install the routing seam first.** Find or insert a place to redirect traffic per slice: a reverse proxy (NGINX, Envoy), an API gateway, a facade service, or a feature-flag check in the legacy entry point. The seam must route at the granularity you intend to migrate — per endpoint, per URL prefix, per message type. Verify you can flip one route to the new system and back without a deploy, in under a minute. If you cannot route incrementally, you cannot strangle incrementally; building the seam is step zero, before any slice.
2. **Slice by business capability, not by layer.** Cut vertically — a whole capability (checkout, user profile, billing export) end to end, never 'the data layer' or 'all the controllers'. Pick the first slice for low risk and high learning: modest traffic (roughly 1-5% of total volume is a good first target), few upstream dependencies, clear ownership, observable behavior. Do not start with the crown-jewel transaction. Size each slice to ship in days to a few weeks; if an estimate exceeds 6 weeks, cut the slice smaller.
3. **Sequence by dependency and data gravity.** Map which slices read and write shared data. Migrate leaf capabilities before the tables everyone touches. Where new and old share a database, decide per slice: dual-write with reconciliation, change-data-capture, or new-system-owns-the-table with a read replica for legacy. Order steps so each one leaves the system fully working — never a state that only resolves after three more PRs.
4. **De-risk every cutover.** Per slice: shadow traffic (send to both, compare outputs, serve old) to validate — hold shadowing until the output mismatch rate is below ~0.1% or every mismatch is explained — then canary a small percent and ramp on a schedule like 1% → 5% → 25% → 100%, watching the comparison metric at each step. Define rollback as flipping the route, not reverting code. Add production metrics that compare old vs new outputs. Keep the kill switch until the slice has run at 100 percent through a full business cycle (month-end, peak) — typically 30 days minimum.
5. **Define done and delete the trunk.** A slice is done only when the legacy path has served zero traffic for a defined soak period and the dead legacy code is deleted, not left dormant. Track remaining legacy surface explicitly so the project ends rather than stalling at 80 percent forever.

## Worked example

Migrating a monolithic order-management system:

**Bad slicing:** "Phase 1: migrate the persistence layer to the new Postgres schema. Phase 2: port all API controllers. Phase 3: move the frontend." Every phase touches every capability, nothing is independently shippable, and the system is broken between phases — there is no route to flip and no rollback short of reverting months of work.

**Good slicing:** "Slice 1: order-status lookup (read-only, ~2% of traffic, no writes) — proves the seam, the deploy pipeline, and the comparison metrics. Slice 2: shipping-label generation (writes to its own table, one upstream consumer). Slice 3: order creation (dual-write with nightly reconciliation until slice 4). Slice 4: new system owns the orders table; legacy reads via replica. Each slice: shadow until mismatches < 0.1%, canary 1% → 5% → 25% → 100%, soak 30 days at full traffic, delete legacy path." Each step is a whole capability, independently rollback-able by flipping a route, and the plan names what gets deleted and when.

## Quality bar
A plan is ready when:
- A concrete routing seam exists or is the first deliverable, and you can name the unit it routes on.
- Every slice is one vertical capability, shippable in weeks, with a named first slice chosen for low risk.
- Each step in the sequence leaves the system fully working on its own.
- Every cutover has a shadow/canary/ramp path and a route-flip rollback with a comparison metric.
- 'Done' includes deletion of dead legacy code and an explicit count of remaining legacy surface.

## Do NOT
- Do not begin slicing before the seam can flip a route without a deploy.
- Do not slice horizontally by technical layer.
- Do not start with the highest-value or most-coupled capability.
- Do not define rollback as reverting code; define it as flipping the route.
- Do not declare a slice done while legacy code remains dormant or legacy still serves traffic.
- Do not impose this ceremony on a system small enough to rewrite in under a few weeks, or one with no stable seam and no way to add one — a careful in-place rewrite is cheaper there.
