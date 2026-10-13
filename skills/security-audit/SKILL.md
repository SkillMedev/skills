---
name: security-audit
description: Runs a whole-application security audit - threat-model-lite scoping, dependencies and supply chain, secrets, authentication and authorization, input handling, transport and headers, logging, and infrastructure config - and delivers a severity-ranked findings report with reproduction steps and concrete fixes. Use when someone asks "audit our app's security", "are we ready for a pentest or SOC 2", "what are our biggest security holes", or is preparing for launch, an enterprise security questionnaire, or taking over an inherited codebase. Do NOT use for hunting flaw classes in a single diff or PR - use secure-code-review instead; for prioritizing an existing scanner or pentest report, use vulnerability-triage; for design-stage analysis before code exists, use threat-model-stride.
metadata:
  title: "Security Audit"
---

# Security Audit

A security audit asks, for a whole system: where would a motivated attacker get in first, and what would it cost? It is broader than code review - many real breaches start from a leaked key in git history, an admin panel without MFA, an unpatched edge dependency, or a public bucket, none of which appear in a diff. The costly mistake this skill prevents is auditing only the code you were shown, producing a long list of medium-severity lint, and missing the one system-level hole that is trivially exploitable from the internet.

## Operating procedure

Scope before scanning. A scan with no map of assets and entry points produces noise ranked by tool output instead of by attacker value.

### Step 1: Confirm authorization and rules of engagement

- Get written confirmation of what you may touch: repositories, environments, cloud accounts, live endpoints.
- Default to read-only analysis and non-destructive testing in non-production. No exploitation, fuzzing, or load against production without a named owner's approval.
- Record exclusions so the report does not imply coverage it lacks.

### Step 2: Threat-model-lite scoping (time-box to about 10% of the audit)

One page, not a full STRIDE exercise:

- Assets: what an attacker wants - customer PII, payment data, credentials, tenant data, admin capability, compute.
- Actors: anonymous internet user, authenticated user, user of another tenant, insider or contractor, compromised dependency.
- Entry points: public routes, APIs, webhooks, file uploads, admin panels, CI/CD, cloud consoles, third-party integrations.
- Trust boundaries: where data crosses from untrusted to trusted - browser to API, API to database, webhook provider to handler, CI to production.

Rank entry points by exposure x asset value; the top three get the deepest review in every later step. If the design itself looks unsound, flag it for threat-model-stride.

### Step 3: Dependencies and supply chain

- Run the ecosystem's audit tool or a cross-ecosystem scanner (for example osv-scanner) against every lockfile; a missing or uncommitted lockfile is itself a finding.
- Check reachability: is the vulnerable code called, and is the service internet-facing? Only reachable or edge-exposed issues rate above Medium; a reachable CISA KEV entry is at least High.
- CI: third-party actions or images pinned by mutable tag instead of SHA or digest, untrusted pull requests reaching secrets, over-scoped CI tokens.
- Hand deep package-by-package analysis to dependency-risk-audit.

### Step 4: Secrets

- Scan the full git history (gitleaks or trufflehog), not just the tree, plus CI logs, image layers, and client bundles. A later deleting commit does not remove a secret.
- A live credential is Critical and pauses the audit: tell the owner now and route to secrets-hygiene. Rotation, not history rewriting, is the fix.
- Runtime secrets come from a secrets manager; no server key sits in a client-exposed variable (NEXT_PUBLIC_, VITE_).

### Step 5: Authentication and authorization

- Authentication: password hashing with argon2id, scrypt, or bcrypt (never fast hashes); login, reset, and MFA endpoints rate limited; reset tokens single-use and short-lived; session IDs regenerated on login; MFA enforced for every admin and cloud-console account.
- Authorization: build a roles x sensitive-actions matrix and test it on the top entry points with two users in two tenants. Cross-tenant reads are the highest-value test in multi-tenant systems.
- Row-level security and IAM policies must be enabled and tested, not just declared.
- Check OAuth or OIDC integrations against oauth-flow; for line-level flaw hunting in specific handlers, apply secure-code-review rather than re-deriving its checklist.

### Step 6: Input handling

Sample the top entry points for injection (SQL, NoSQL, shell, template), SSRF on user-supplied URLs, path traversal, unrestricted file upload (type, size, served from the app origin), unsafe deserialization, and mass assignment. Webhook handlers must verify signatures over the raw body and reject replays.

### Step 7: Transport and headers

Check every public hostname, not just the main app:

- TLS 1.2 minimum; plain HTTP only redirects.
- Strict-Transport-Security with max-age of at least 31536000 and includeSubDomains once all subdomains serve HTTPS.
- Content-Security-Policy without 'unsafe-inline' or 'unsafe-eval' for scripts, and frame-ancestors (or X-Frame-Options) against clickjacking.
- Session cookies Secure, HttpOnly, SameSite=Lax or Strict.
- CORS never reflects arbitrary Origins with Access-Control-Allow-Credentials: true.

### Step 8: Logging and detection

- Logins, failures, MFA and permission changes, admin actions, and exports are logged with actor, target, time, and source IP.
- Logs hold no passwords, tokens, card numbers, or session cookies.
- Someone is alerted on anomalies (failed-login bursts, new admin grants); unread logs are not detection.

### Step 9: Infrastructure and configuration

- Buckets and databases not publicly reachable.
- IAM least privilege: no wildcard admin on workloads, no long-lived keys where workload identity exists.
- Containers non-root where possible; debug endpoints and stack traces off in production.
- Backups encrypted, with a tested restore. Deep IaC review goes to terraform-expert.

### Step 10: Reproduce, rank, and write up

Reproduce every High and Critical in a safe environment and record exact steps; mark the rest Suspected. Rank by severity then exploitation effort, grouping shared root causes.

## Worked example

A B2B SaaS on Next.js and Postgres with Stripe webhooks; scoping ranks the public API, admin route, and webhook handler as top three. Report excerpt:

```
F-01  CRITICAL  Live Stripe secret key in git history
      Where: commit 4e1a9c2, config/stripe.ts (removed in a later commit)
      Repro: git show 4e1a9c2:config/stripe.ts -> sk_live_ prefix present; key
             still authenticates against the provider API (read-only call).
      Fix:   Rotate the key in the Stripe dashboard now; load from the platform
             secret store; add gitleaks as a pre-commit hook and CI gate.

F-02  HIGH      Cross-tenant invoice read via sequential IDs
      Where: GET /api/invoices/[id]
      Repro: As user A (tenant 1) request an invoice ID belonging to tenant 2
             -> 200 with full invoice body.
      Fix:   Scope the query by tenant_id from the session; return 404 for
             not-owned; add a two-tenant regression test.

```

## Thresholds

- Critical: unauthenticated remote code execution, unauthenticated access to sensitive data, or a live production credential exposed. Fix or mitigate within 24 hours.
- High: authenticated cross-user or cross-tenant access, privilege escalation, a reachable KEV-listed vulnerability. Fix within 7 days.
- Medium: requires chaining, non-default config, or significant attacker position. Fix within 30 days.
- Low: defense-in-depth gaps with no demonstrated path. Fix within 90 days or accept in writing.
- Coverage minimum: all eight areas (Steps 3-9 plus scoping) addressed or explicitly marked out of scope; access matrix tested on at least the top three entry points with two tenants.
- More than ~25 findings: consolidate by root cause.

## Deliverable

A report with: scope, rules of engagement, and exclusions; the threat-model-lite map; a plain-language summary of the top three risks; a findings table (ID, severity, area, location, verified or suspected); per-finding reproduction, impact, and specific fix; a "checked, clean" list per area; and a remediation order with owners and SLA dates.

## Do NOT

- Do not test production destructively or without written authorization.
- Do not audit only the code - secrets in history, cloud config, CI, and admin accounts are where system-level breaches start.
- Do not rank by raw scanner score; rank by reachability and asset value.
- Do not list a finding without reproduction steps or a clear Suspected label.
- Do not write "sanitize input" or "follow best practices" as a fix; name the change and where it goes.
- Do not paste live secrets into the report; reference commit and path, and redact the value.

## Quality bar

- An engineer can reproduce every High and Critical finding from the report alone.
- Every finding has a concrete fix and an owner-assignable remediation.
- The top three risks would survive scrutiny from an external pentester as the right top three.
- Clean areas are listed, so the reader knows what was checked rather than guessing.
- Verified and suspected findings are clearly separated.

## Escalation

Use secure-code-review for line-level review of specific handlers, threat-model-stride when the design itself needs analysis, dependency-risk-audit for package-by-package supply-chain work, secrets-hygiene for any leaked credential, vulnerability-triage for prioritizing external scanner output, oauth-flow for fixing OAuth or OIDC integrations, terraform-expert for infrastructure-as-code remediation, and sev-triage if you find evidence of active exploitation - stop auditing and start incident response.
