---
name: Secure Code Review
description: Reviews code for the security flaw classes that cause the most breaches — broken authorization and IDOR, injection, SSRF, mass assignment, and unsafe deserialization — and returns a short, focused findings list with concrete fixes. Use when someone asks "review this code for security issues", "is this endpoint safe", "check this PR for vulnerabilities", or is shipping auth logic, new API endpoints, file handling, or anything that touches untrusted input. Do NOT use for general code quality, style, or maintainability review — use code-review-checklist instead; for prioritizing findings a scanner already produced, use vulnerability-triage; for design-stage analysis before code exists, use threat-model-stride.
---

# Secure Code Review

Secure code review is not a checklist audit — it is a focused search for the flaw categories that actually cause breaches. A 40-finding report gets skimmed and ignored; a 5-finding report where every item is exploitable gets fixed. The costly mistake this skill prevents is spending review time evenly across the diff while the one missing ownership check ships to production.

## Operating procedure

Work the steps in order: scope first, then the highest-breach-rate categories, then filtering, so scrutiny lands where risk lives.

### Step 1: Gather inputs

- The code under review: diff, PR, or file set.
- Entry points: which routes, handlers, jobs, or consumers accept untrusted input (request params, headers, cookies, body, queue messages, webhooks).
- Stack facts: framework, ORM or query layer, template engine, auth model (session, JWT, API key).
- Deployment context: internet-facing or internal, and whether the service runs in a cloud environment with an instance metadata endpoint.
- What changed and why, in one sentence — reviews without intent context miss logic flaws.

If entry points are unclear, trace them from the router or handler registry before reviewing anything, and label inferred entry points as guesses.

### Step 2: Budget scrutiny by risk

Spend time proportional to risk: auth logic and untrusted-input handling get the deepest read; rendering, styling, and config get a skim. Any function reachable from untrusted input is in scope; pure internal refactors of trusted data are not.

### Step 3: Authorization — the highest-value check

Broken authorization (OWASP A01) is the most common high-severity finding. For every action that reads or mutates a resource:

- Confirm the code checks that the authenticated user owns or has permission for the specific resource ID — not merely that the user is logged in.
- Hunt IDOR: resource IDs from request parameters used without ownership verification.
- Verify privileged actions enforce role checks at the function level, not only at the route level — middleware-only checks are bypassed by internal calls.
- Check that authorization logic is centralized; duplicated per-endpoint checks drift and create gaps.

Bad — authenticated but not authorized:

```python
@app.get("/invoices/<invoice_id>")
def get_invoice(invoice_id):
    user = require_login()                    # proves identity only
    return db.invoices.find(id=invoice_id)   # any logged-in user reads any invoice
```

Good — ownership enforced in the query, uniform response:

```python
@app.get("/invoices/<invoice_id>")
def get_invoice(invoice_id):
    user = require_login()
    invoice = db.invoices.find(id=invoice_id, owner_id=user.id)
    if invoice is None:
        abort(404)   # same response for missing and forbidden — no existence oracle
    return invoice
```

### Step 4: Injection surfaces

For any code that constructs queries, commands, or markup from user input:

- SQL: parameterized queries or a safe ORM API throughout — no string concatenation into query bodies, even for identifiers or ORDER BY clauses (map user sort keys through an allowlist).
- Shell: flag any exec/spawn call that includes user input. The correct fix is to avoid shell execution; if unavoidable, use argument arrays, never string interpolation.
- HTML/templates: output encoding for the correct context (HTML body, attribute, JavaScript, URL). Rich-text user content must pass through a sanitization library with an allowlist, never a blocklist.
- Path traversal: normalize any user-supplied filename or path and confirm it resolves inside the expected root directory before use.

### Step 5: Server-side request forgery

Any outbound HTTP request with a URL derived from user input is an SSRF candidate:

- Confirm the target is validated against an allowlist of permitted hosts and schemes before the request fires.
- Cloud-hosted services: verify requests to instance metadata endpoints (169.254.169.254, fd00:ec2::254) are blocked.
- Redirects: confirm the HTTP client does not follow redirects automatically, or that each redirect destination is re-validated against the allowlist.

### Step 6: Mass assignment and deserialization

- Mass assignment: model or ORM binding must use an explicit allowlist of permitted fields, not a deny-list. Any field controlling permissions, ownership, or internal state must be excluded from user-supplied binding.
- Deserialization: flag any deserialization of user-supplied data via native object serialization (Java ObjectInputStream, Python pickle, PHP unserialize, Ruby Marshal) — almost always unsafe with untrusted input. Prefer JSON or protobuf with explicit schema validation.

### Step 7: Filter — raise vs skip

Raise: anything letting an attacker access data or perform actions beyond their authorization, execute code, or exfiltrate secrets. Skip: style issues, missing logging (unless the gap breaks audit requirements), and theoretical issues with no realistic attack path in the current deployment. Target roughly seven findings or fewer, ordered by severity; a longer list means the filter was not applied.

### Step 8: Write the findings

Each finding: file and line, flaw class, one-sentence attack scenario ("an authenticated user can read any tenant's invoices by iterating IDs"), severity (Critical for unauthenticated data access or code execution; High for authenticated cross-user access; Medium for issues needing chaining or non-default config), and the concrete fix — the specific code change, not "sanitize input".

## Deliverable

Produce a findings report containing: the reviewed scope and entry points, at most about seven findings each with file:line, flaw class, one-sentence attack scenario, severity, and a specific fix, plus an explicit "checked, clean" list of the categories reviewed with nothing to raise.

## Do NOT

- Do not pad the report with style or theoretical findings — every low-value item lowers the odds the real ones get fixed.
- Do not accept middleware-only or route-only permission checks as proof of authorization; internal call paths bypass them.
- Do not pass string-built ORDER BY or identifier interpolation as safe "because it's not a value" — it is still injectable.
- Do not accept a blocklist sanitizer for HTML or an "encode everything as HTML" approach; encoding must match the output context.
- Do not confirm authentication and stop; nearly every IDOR sits behind a valid login.
- Do not review only the diff when the change touches auth — read the callers, because authorization bugs live at call sites.

## Quality bar

- Every finding has a realistic attack scenario an engineer can reproduce, not a rule citation.
- Every finding names the exact fix; none say "validate input" without saying how and where.
- Authorization was checked object-by-object for every resource access in scope.
- All six categories (authz, SQL, shell, HTML/path, SSRF, mass assignment/deserialization) were covered or explicitly marked out of scope.
- The report is short enough to be read whole — about seven findings or fewer.

## Escalation

Route general readability and maintainability review to code-review-checklist, prioritization of scanner output to vulnerability-triage, design-stage questions to threat-model-stride, and any hardcoded credential found mid-review to secrets-hygiene immediately — a live secret in code outranks every other finding.
