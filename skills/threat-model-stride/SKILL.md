---
name: Threat Model STRIDE
description: Applies STRIDE threat modeling to a feature or system design and produces a prioritized threat table with concrete mitigations ranked by exploitability and impact. Use when someone asks "threat model this feature", "what could go wrong with this design", "is this integration safe to build", or is reviewing auth flows, new data stores, webhook receivers, file uploads, or external integrations before the code is written. Do NOT use for prioritizing scanner or pentest findings on already-shipped code — use vulnerability-triage instead; for line-by-line security review of written code, use secure-code-review.
---

# Threat Model STRIDE

A threat found at design review costs a paragraph in a doc; the same threat found in production costs an incident, a credential rotation, and sometimes a disclosure. STRIDE catches whole classes of flaws — spoofed callers, tamperable payloads, privilege leaks — before there is code to review. Apply it to any feature touching auth, data storage, external calls, or privilege changes; skip it and the same flaws surface later in secure-code-review or, worse, in vulnerability-triage.

## Operating procedure

Follow the steps in order. Threats enumerated before trust boundaries are explicit come out vague and unactionable, and mitigations written before scoring waste effort on Low findings.

### Step 1: Gather inputs

Collect these before modeling. Where the user cannot answer, use the default and label the assumption a guess.

- Feature description in 3-5 sentences: what it does, who calls it, what it stores.
- Actors: users, internal services, external APIs, admin roles. Default: anonymous internet user, authenticated user, one backend service.
- Data assets and a sensitivity tier for each: credentials > financial > PII > internal > public.
- Deployment context: internet-facing or internal-only; behind a WAF or VPN or not.
- Existing controls: auth mechanism (session, JWT, mTLS), rate limiting, audit logging.
- Compliance regime in scope (SOC 2, PCI, HIPAA, GDPR), if any.

### Step 2: Map the data flow in prose

Establish a minimal data-flow diagram in prose: name every actor, every trust boundary (browser/server, service/DB, internal/external network), and every data asset with its sensitivity tier. Do not proceed until boundaries are explicit — vague scope produces vague threats.

### Step 3: Apply STRIDE per boundary-crossing component

For each component that crosses a trust boundary, evaluate all six categories. State "no credible threat" explicitly rather than skipping a category.

- Spoofing: can an attacker impersonate a user, service, or token? Check auth mechanisms at every entry point.
- Tampering: can inputs, stored records, or in-flight data be modified without detection? Check integrity controls and audit logs.
- Repudiation: can an actor deny an action? Check whether sensitive operations are logged with actor identity and timestamp.
- Information Disclosure: can data leak to unauthorized parties? Check access controls, error messages, logs, and caches.
- Denial of Service: can an attacker exhaust resources or degrade availability? Check rate limiting, queue depth limits, and timeouts.
- Elevation of Privilege: can an actor gain permissions beyond their role? Check authorization at every privilege boundary, not just at entry.

### Step 4: Score with DREAD-light

Score each threat on two axes only.

- Exploitability: 3 = triggerable by an anonymous internet user with public tooling; 2 = requires an authenticated low-privilege account or nontrivial setup; 1 = requires insider access, physical access, or chaining other flaws.
- Impact: 3 = credential theft, remote code execution, regulated-data exposure, or full account takeover; 2 = single-user data exposure or degraded service; 1 = marginal information or brief nuisance.

Severity = the sum: 6 is Critical, 5 is High, 4 is Medium, 3 or below is Low. Drop Low findings from the table unless they chain into a higher-severity path — a padded table trains reviewers to skim.

### Step 5: Recommend one concrete control per Critical and High

- Prefer existing platform primitives (JWT validation middleware, ORM parameterization, IAM roles) over custom code — custom security code is new attack surface.
- Specify the control, not the category: "add HMAC-SHA256 signature on the webhook payload, verified before parsing", not "add integrity check".
- Flag any mitigation requiring a schema change or new dependency — those need separate review.

### Step 6: Produce the threat table

One row per threat: STRIDE category, affected component, attack scenario in one sentence, severity with the two scores, recommended control, owner team. The whole table must be reviewable in under ten minutes by a non-security engineer.

## Worked example: expiring share links for uploaded invoices

Feature: users upload invoice PDFs; the app generates share links that expire after 7 days; recipients need no account. Trust boundaries: browser/API, API/object storage, anonymous internet/share endpoint.

| STRIDE | Component | Attack scenario | Severity | Control | Owner |
|---|---|---|---|---|---|
| Spoofing | Share endpoint | Attacker enumerates link tokens to read other users' invoices | Critical (E3+I3) | 128-bit CSPRNG tokens, constant-time comparison, rate limit 10 req/min per IP | Platform |
| Elevation of Privilege | Storage access | A link grants access to the whole bucket prefix, not one object | Critical (E3+I3) | Scope the signed credential to a single object key; deny listing | Platform |
| Information Disclosure | Expired links | Storage URL exposed in the redirect outlives the 7-day expiry | High (E3+I2) | Proxy bytes through the API, or signed URLs with TTL at or below link expiry | Platform |
| Tampering | Upload handler | Uploaded "PDF" is HTML/JS served inline — stored XSS on the share domain | High (E2+I3) | Validate magic bytes; serve with Content-Disposition attachment from a sandboxed domain | App |
| Denial of Service | Upload handler | Unbounded upload size exhausts storage and memory | Medium (E2+I2) | 10 MB size cap, per-user daily quota, streaming writes | App |
| Repudiation | Share creation | User denies having shared an invoice externally | Medium (E2+I2) | Log actor ID, timestamp, and link scope on creation | App |

## Deliverable

Produce a threat model containing: the prose data-flow summary with named trust boundaries; the threat table with STRIDE category, component, one-sentence scenario, severity with both scores, concrete control, and owner per row; and a flagged list of mitigations that require schema changes or new dependencies for separate review.

## Do NOT

- Do not enumerate threats before trust boundaries are explicit — every vague threat traces back to a vague boundary.
- Do not recommend a category instead of a control; a finding nobody can turn into a ticket does not get fixed.
- Do not custom-build what a platform primitive already provides.
- Do not pad the table with Low findings; include a Low only when it chains into a higher-severity path.
- Do not treat a STRIDE pass on a design as clearance for the implementation — pair with secure-code-review once code exists, because STRIDE cannot see injection bugs or missing ownership checks.

## Quality bar

- Every trust boundary is named before any threat is listed.
- All six STRIDE categories are evaluated for every boundary-crossing component; absences are stated, not skipped.
- Every Critical and High row names one control specific enough to write a ticket from.
- Severity comes from the exploitability and impact bands, not gut feel.
- The full table reads in under ten minutes for a non-security engineer.

## Escalation

Route implementation review of written code to secure-code-review, prioritization of already-discovered vulnerabilities to vulnerability-triage, and any credential exposure found during modeling to secrets-hygiene. For regulated data (PCI, HIPAA), bring in a qualified security engineer or assessor — this skill structures the analysis; it does not replace a formal assessment.
