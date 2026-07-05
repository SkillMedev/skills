---
name: Dependency Risk Audit
description: Audits third-party dependencies for exploitable CVEs, abandonment, license exposure, and supply-chain hygiene, and delivers a ranked findings report with a remediation order. Use when someone asks "is this package safe to add", "audit our dependencies", "npm audit is screaming, what actually matters", "can we use this GPL library", or is preparing a security review or vendor questionnaire. Do NOT use for triaging vulnerabilities in first-party code or a full CVE queue — use vulnerability-triage instead; for reviewing the code you wrote for security flaws — use secure-code-review instead; for how secrets are stored and rotated — use secrets-hygiene instead; for assembling compliance evidence — use soc2-evidence-helper instead.
---

# Dependency Risk Audit

Third-party packages are the most common source of supply-chain compromise and known-vulnerability exploitation; treat dependency selection as a security decision, not a convenience one. The costly failure this skill prevents is on both ends: shipping a reachable critical CVE because the audit output was noise nobody read, and burning a sprint patching findings in code paths that can never execute.

## Operating procedure

### Step 1: gather inputs

Collect (label guesses as guesses):

1. Ecosystem and manifest/lockfile (package.json + lockfile, requirements/poetry, Gemfile.lock, go.mod).
2. Deployment context: server-side, client-side, CLI-only, or build-time only — this decides reachability.
3. License posture: proprietary/commercial code, or open source (decides copyleft tolerance).
4. Whether this is a point decision (one new package) or a full audit (whole tree).

### Step 2: run the native audit and triage for reachability

Run the ecosystem's native tool first: `npm audit`, `pip-audit`, `bundle audit`, `govulncheck` (which does reachability analysis natively — trust its "not called" verdicts). For each finding:

- Confirm exploitability in context — a CVE in a CLI-only code path of a server-side lib may not be reachable. A dev-only dependency (test runner, linter) is almost never exploitable in production, but IS in scope for install-time attacks.
- Check whether a fixed version exists and whether upgrading is a semver-compatible bump.
- Red line: any CVSS 7.0+ finding with no available fix is a blocking risk requiring a compensating control or replacement.
- Never dismiss a finding without a written reason attached to it. "Probably fine" is not a reason; "the vulnerable function is X, we never call X, verified by grep" is.

### Step 3: check maintenance health

A dependency with no CVEs today can become a liability tomorrow. For any new or high-traffic dependency:

- Last release and commit activity: no release in 24+ months on an active ecosystem is a yellow flag (in slow-moving ecosystems, judge by open-issue responsiveness instead).
- Open security issues versus closed, and whether reports get responses.
- A documented security policy or disclosure contact.
- Single-maintainer packages without a succession plan carry concentration risk — flag them; for anything on a critical path, require a fork-and-maintain fallback plan or an alternative identified in the report.

### Step 4: check supply-chain hygiene

Install-time attacks abuse the publication pipeline:

- Verify the package name exactly matches the intended project — typosquatting is common, and a one-character difference is the attack.
- Confirm the publisher identity on the registry matches the known author or organization.
- Prefer packages publishing provenance attestations (SLSA, npm provenance, sigstore) when available.
- Lockfile rules, non-negotiable: pin transitive dependencies in a committed lockfile; never allow floating ranges to resolve in production builds; CI must install from the lockfile with integrity checking (`npm ci`, never `npm install`; `pip install` from hashes or a compiled lock). A build that can resolve different versions on two days is unauditable.
- For new additions, prefer waiting until a fresh release is several days old — most hijacked-package incidents are caught within days of publication.

### Step 5: check license risk

Licenses create legal exposure, not just engineering risk. Flag any copyleft license (GPL, AGPL, EUPL) in a proprietary codebase — these require legal review before shipping; AGPL is the highest-severity flag for anything serving network requests. Acceptable defaults for most commercial projects: MIT, Apache-2.0, BSD-2, BSD-3, ISC. Check transitive licenses too — a permissive package with a copyleft dependency inherits the problem.

### Step 6: rank and report

Rank findings in this remediation order:

1. Exploitable CVE with an available fix — upgrade now.
2. Exploitable CVE with no fix — needs replacement or compensating control.
3. Abandoned package on a critical path.
4. License conflict.
5. Maintenance concern only.

Address (1) and (2) before any release. Defer (5) to a scheduled dependency-hygiene sprint. First-party code findings that surface along the way route to vulnerability-triage.

## Worked contrast: a finding writeup

Bad: "npm audit shows 23 vulnerabilities (3 critical). Ran npm audit fix. 14 remain. Probably fine, mostly dev deps."

Good:

```
FINDING: lodash 4.17.15 — prototype pollution (CVSS 7.4), fixed in 4.17.21
Reachability: HIGH — merge() called on request bodies in api/params.ts
Fix: semver-compatible bump; no API changes between 4.17.15 → 4.17.21
Rank: 1 (exploitable, fix available). Action: upgrade before release.

FINDING: minimist 1.2.5 (via dev-only mocha) — CVSS 5.6
Reachability: NONE at runtime (devDependency, not bundled)
Rank: 5. Action: bump in hygiene sprint; written reason recorded.
```

Every finding names reachability with evidence, the fix path, and its rank.

## Deliverable

Produce a findings report containing: each finding with package, version, CVE/flag, reachability verdict with evidence, fix path, and rank (1–5); the lockfile/CI hygiene verdict; the license flag list; and a remediation order stating what blocks release versus what defers to the hygiene sprint.

## Do NOT

- Do not forward raw audit output as the report — unranked noise trains the team to ignore all of it, including the real criticals.
- Do not dismiss findings without a written, evidence-backed reason.
- Do not treat CVSS score as reachability — a 9.8 in code you never call outranks nothing; a 6.5 in your request path blocks release.
- Do not exempt devDependencies from supply-chain checks — install-time attacks execute at install, regardless of runtime reachability.
- Do not run `npm install` in CI — it can rewrite resolution; `npm ci` enforces the lockfile.

## Escalation

License findings are risk flags, not legal advice — copyleft conflicts and anything contractual go to counsel before shipping. Evidence of active compromise (malicious postinstall, exfiltration) is an incident: stop the audit and escalate to incident response immediately.

## Quality bar

- Every finding carries a reachability verdict with evidence, not just a scanner severity.
- Every dismissal has a written reason.
- Rank 1 and 2 items are explicitly marked release-blocking.
- Lockfile integrity and CI install command were checked, not assumed.
- Transitive licenses were scanned, not just direct dependencies.
