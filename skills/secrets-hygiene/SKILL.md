---
name: Secrets Hygiene
description: Guides detection, emergency rotation, and prevention of leaked secrets and credentials across source code, git history, CI pipelines, logs, and infrastructure config. Use when someone says "I think I committed an API key", "a secret leaked", "scan the repo for credentials", "how should we store secrets", or is setting up secrets management for an application, CI/CD, or Kubernetes. Do NOT use for detecting or redacting personal data like names, emails, or SSNs — use pii-scrubber instead; for routine non-secret environment configuration on Vercel, use vercel-env-management.
---

# Secrets Hygiene

A leaked secret is a live incident until rotated. Treat any suspected exposure as confirmed until proven otherwise — the cost of an unnecessary rotation is always lower than the cost of a breach. The costly mistake this skill prevents is the instinctive wrong move: deleting the commit, force-pushing, and considering it handled while the key stays valid in every fork, clone, and scraper cache.

## Operating procedure

The order is deliberate: rotation comes before investigation because every minute of analysis is a minute the credential works for an attacker.

### Step 1: Gather inputs

- What leaked (or might have): credential type, issuing system (cloud console, API portal, identity provider), and the privilege it carries.
- Where and when: repo and commit, CI log, chat message, Docker image; how long it has been exposed. If unknown, assume the worst plausible window and label it a guess.
- Whether the repo is public, has forks, or has ever been public.
- Who can execute the rotation and whether a rotation runbook exists for this credential.

For prevention-mode engagements (no active leak), skip to Step 4.

### Step 2: Respond to the suspected leak — in order, without delay

1. Rotate the credential immediately through the issuing system. Do not wait to confirm exposure first. Target: rotation started within 1 hour of discovery for any production credential.
2. Revoke active sessions and tokens derived from the compromised credential — a rotated key with live derived sessions is not contained.
3. Scan audit logs for the credential's usage over the prior 90 days to assess blast radius. Any access that cannot be attributed to a legitimate caller escalates this from a leak to a breach investigation.
4. If the secret was ever committed to git, treat the full history as compromised even after deletion — history is persistent and forks may exist. History rewriting is cleanup, never containment; only rotation contains.

### Step 3: Verify containment

Rotation is complete only when all three hold: the old credential is revoked (not just superseded), the new credential is deployed and the service is healthy, and audit logs show zero post-revocation use of the old value.

### Step 4: Detect — scan code, history, and infrastructure

Run a secret scanner across the full git history, not just HEAD. Recommended tools: trufflehog (history-aware), gitleaks, or detect-secrets. Configure patterns for the stack's credential formats: AWS AKIA prefixes, GCP service account JSON keys, Stripe sk_live_ prefixes, PEM headers.

Also scan the places scanners miss by default: CI/CD pipeline logs (they frequently echo env vars), Terraform state files, Docker image layers, and Kubernetes configmaps or Secrets stored in plaintext.

### Step 5: Fix storage — the right store per runtime

- Local development: a .env file listed in .gitignore, plus a committed .env.example with placeholder values. Never commit real values.
- CI/CD: the platform's native secret store (GitHub Actions encrypted secrets, GitLab CI variables). Never interpolate secrets into log output.
- Application runtime: a dedicated secrets manager (AWS Secrets Manager, HashiCorp Vault, GCP Secret Manager). Fetch at runtime, not at build time — build-time injection bakes secrets into artifacts. Prefer short-lived credentials with automatic rotation over long-lived API keys.
- Kubernetes: an external secrets operator syncing from a secrets manager, rather than storing values in etcd via native Secrets objects.

### Step 6: Install prevention controls

- A pre-commit hook running a secret scanner before every commit (pre-commit framework with detect-secrets or gitleaks).
- The same scanner in CI as a required check that blocks merge on findings.
- Minimum IAM permissions per credential — a leaked read-only key is less damaging than a leaked admin key.
- Rotation cadence: no credential lives longer than 90 days without automated rotation; prefer short-lived tokens (minutes to hours) wherever the platform supports them; rotate any static secret a departing person had access to during offboarding.

## Worked artifact: secret handling, bad vs good

Bad — key in the repo, token in the logs:

```javascript
// config.js — committed to source control
const stripeKey = "sk_live_REPLACE-WITH-REAL-KEY";
logger.info("inbound request", { headers: req.headers }); // Authorization header now sits in logs
```

Good — fetched at runtime, allowlisted logging:

```javascript
// nothing secret in the repo, the image, or the logs
const stripeKey = await secretsManager.get("payments/stripe-api-key");
logger.info("inbound request", { path: req.path, userId: req.user.id }); // allowlisted fields only
```

Rotation checklist template — copy and fill during an incident:

```
SECRET ROTATION RECORD
  Credential:              [FILL: name + issuing system]
  Privilege scope:         [FILL: what it can access]
  Exposure window:         [FILL: first exposure -> revocation, or "unknown, assumed N days"]
  Rotated at:              [FILL: timestamp]   Old value revoked: yes / no
  Derived sessions revoked: yes / no
  90-day audit review:     clean / suspicious access found -> escalate to incident
  Root cause:              [FILL: how it leaked]
  Prevention added:        [FILL: pre-commit hook / CI check / scope reduction]
```

## Deliverable

Produce either an incident record (the filled rotation checklist with containment verified) or a secrets posture report: scan results across history and infrastructure, the storage pattern per runtime with gaps named, and the prevention controls installed or missing — each gap with an owner.

## Do NOT

- Do not base64-encode a secret and consider it safe — encoding is not encryption.
- Do not store secrets in env files committed to source control, even on non-production branches.
- Do not log request headers or POST bodies at INFO level — auth tokens and API keys appear there routinely.
- Do not delete the commit or rewrite history and call the incident closed; the only containment is rotation plus revocation.
- Do not rotate the key but leave derived sessions and tokens alive.
- Do not grant a new credential the same broad scope the leaked one had — re-scope to minimum during rotation, when attention is highest.

## Quality bar

- Any suspected leak was rotated first and investigated second.
- Containment shows all three proofs: old value revoked, new value live and healthy, no post-revocation use in logs.
- Scans covered full git history plus CI logs, Terraform state, image layers, and cluster config — not just HEAD.
- Every stored secret maps to the correct store for its runtime, and no credential exceeds the 90-day rotation ceiling.
- Prevention includes both the pre-commit hook and the blocking CI check; one without the other leaks.

## Escalation

If the 90-day audit review shows unattributed use of the leaked credential, this is a breach, not a leak — escalate to incident response (sev-triage) and involve legal or compliance before any external communication, since disclosure obligations may apply. Personal-data exposure routes to pii-scrubber; code-level credential handling flaws found along the way route to secure-code-review.
