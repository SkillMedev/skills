---
name: API Versioning Strategist
description: Produces an API version scheme (date-pinned header or URI), a breaking-vs-additive change policy, and a published deprecation/sunset timeline with translation shims. Use when removing or renaming a field or endpoint, tightening validation, cutting a "v2", binding a partner integration to a version, or planning how long an old version lives. Do NOT use when designing the resource shape, URLs, status codes, or pagination of a new endpoint — use REST API Design instead; this skill owns only the version scheme and deprecation path layered on top of that contract.
---
# API Versioning Strategist

Pick a version scheme, classify every change as breaking or additive, and ship a deprecation path that never silently breaks a live integration.

## Workflow

1. **Classify the change first.** Decide whether it is breaking or additive before choosing any mechanism.
   - Breaking: removing/renaming a field or endpoint; adding a required request field; tightening validation; changing a field's type or an enum's semantics; changing error codes or status codes clients branch on.
   - Additive (ship freely within the current version): adding optional request fields; adding response fields; adding endpoints; adding optional enum values clients are told to tolerate.
   - Mandate a tolerant-reader contract: clients MUST ignore unknown fields. Document this so additive changes stay non-breaking.
2. **Choose the scheme to match release cadence.**
   - Frequent, granular evolution → date-pinned versions: clients send a version header (a pinned release identifier), the account is bound to a default, and each release is a small documented set of breaking changes — ideally 1–5 per release, never dozens. Scales without parallel rewrites.
   - Coarse, infrequent revisions → URI versioning (`/v1/`): simplest and most visible, but biases toward big-bang jumps. Pick only when breaking releases happen roughly once a year or less.
   - Avoid letting a "v2" become a years-long parallel fork of "v1".
3. **Translate at the edge, never fork logic.** Keep one current internal model. Apply ordered version-transform shims that up-convert old requests and down-convert responses to a pinned client's contract. Each shim is small, independently testable, and removable when its version sunsets. Forking business logic per version is forbidden.
4. **Pin every integration, never float.** Bind each client to a fixed version at onboarding so it never moves underneath them. Upgrading is an explicit, opt-in action the client takes after reading a changelog — never an automatic float to current.
5. **Deprecate on a published timeline.** Announce a concrete sunset date, not "soon." On responses served from a deprecated version, emit a `Deprecation` header and a `Sunset` header (HTTP-date), and link migration docs. Give a minimum of 6 months' notice for partner APIs and 12 months for broad public APIs; internal APIs with known consumers can move in 30–90 days.
6. **Track per-version usage.** Instrument which clients hit which version so you can reach the stragglers before removal and confirm a version is truly idle before deleting its shim. A practical removal gate: zero production traffic on the version for at least 30 consecutive days, plus direct confirmation from any client that was still calling it in the final 90 days.

## Deliverable

Produce a versioning policy document containing: the chosen scheme (date-pinned header or URI) with the cadence rationale, the breaking-vs-additive classification table your team applies to every change, the tolerant-reader clause for the client contract, the shim architecture note (where transforms live, how one is added and removed), the pinning rule for new integrations, and the deprecation runbook — notice periods, `Deprecation`/`Sunset` header emission, migration-doc link, and the usage-based removal gate. For an in-flight change, also deliver its classification, the target version, and its concrete sunset date.

## Quality bar

- Every change is explicitly classified breaking or additive, with a documented rationale for any new version.
- One internal model; all version differences live in removable edge shims, not branched handlers.
- Each integration is bound to a fixed version; nothing auto-upgrades.
- Every deprecation carries a concrete sunset date plus `Deprecation`/`Sunset` headers and a migration link.
- Per-version usage is measurable before any version is removed.

## Do NOT

- Do NOT silently change behavior within a published version — additive only.
- Do NOT cut a new version for an additive change; reserve versions for genuinely breaking ones.
- Do NOT fork business logic per version or sprinkle `if version == ...` through handlers.
- Do NOT float clients to the current version automatically or auto-migrate them.
- Do NOT announce deprecations without a concrete date, sunset headers, and a migration guide — "deprecated soon" trains clients to ignore you.
- Do NOT remove a version on the calendar date alone; verify the 30-day zero-traffic gate first, or the sunset becomes an outage.
- Do NOT impose this machinery on an internal API with a single deployable consumer — coordinate deploys instead. The full scheme is for public, partner, or independently deployed clients you cannot update in lockstep.
