---
name: Vercel Deploy Pipeline
description: Ship a Next.js app to Vercel the production way: promote a tested preview to production, instant-rollback a bad release, build once with --prebuilt, run a staged/canary rollout with Rolling Releases (GA), and wire the deploy into CI. Use when someone says "deploy to Vercel", "promote preview to production", "vercel promote", "roll back the deploy", "vercel rollback", "instant rollback", "canary release on Vercel", "rolling release", "--prebuilt build", "vercel deploy in GitHub Actions", or "set up CI for my Vercel project". Do NOT use to manage env vars or pull .env — use vercel-env-management instead; do NOT use to tune runtime/cold-start/ISR performance — use next-on-vercel-perf or vercel-edge-and-isr instead.
---

# Vercel Deploy Pipeline

You own the *ship* step of a curated "Next.js app on Vercel" workflow. This is not
the official Vercel CLI plugin re-stated — it sequences the platform into one
opinionated path: build a preview, prove it, promote the **same artifact** to
production, and keep a one-command escape hatch. The governing rule is **promote,
don't rebuild**: a deployment is immutable, so the bytes you tested in preview are
the exact bytes you put in front of users. Rebuilding to "release" reintroduces
every risk you just verified.

Trigger eagerly on anything about deploying a Next.js app to Vercel, promoting
preview to production, rolling back, building with `--prebuilt`, canary / staged
rollouts, or wiring `vercel` into CI. Hand off the adjacent concerns:
**vercel-env-management** owns env vars and `vercel env pull`;
**vercel-ai-gateway** owns model routing; **vercel-edge-and-isr** owns ISR / Cache
Components and runtime placement; **vercel-firewall-and-botid** owns WAF/BotID;
**next-on-vercel-perf** owns Fluid Compute cold-start and bundle tuning.

## Platform facts this skill assumes (current as of 2026)

- **Runtime:** Node.js 24 LTS is the default (18 is deprecated). Default function
  `maxDuration` is 300s on every plan. Default to **Fluid Compute** (full Node.js,
  instance reuse for fewer cold starts) — Edge Functions are deprecated; do not
  reach for them.
- **Config:** prefer `vercel.ts` (`import { ... } from '@vercel/config'`);
  `vercel.json` still works and is fine for CI-only settings.
- **Deployments are immutable.** Promotion and rollback re-point the production
  alias at an existing deployment — they never rebuild. That is the whole reason
  rollback is instant.
- **Rolling Releases is GA (Jun 2025):** staged/canary traffic shifting to a new
  production deployment, automatic (time-based) or manual-approval stages.

## The pipeline (run in order)

### 1. Build a preview deployment

Every change goes through a preview first. From a feature branch:

```bash
vercel deploy            # preview build + deploy; prints a unique preview URL
```

Git-connected projects already do this on every push/PR — let the Git integration
build previews and reserve the CLI for promotion, rollback, and CI. Preview URLs
are immutable and shareable; this URL is the artifact you will promote.

### 2. Prove the preview before it is allowed near production

Do not promote on vibes. Inspect, hit real routes, and check error logs on the
**preview** deployment:

```bash
vercel inspect <preview-url>                                  # status, build, aliases
vercel curl /api/health --deployment <preview-url>            # bypasses deploy protection
vercel logs --deployment <preview-url> --level error --limit 50
```

If anything is red here, fix and redeploy a new preview. Promotion should be
boring.

### 3. Promote the *same* deployment to production

This is the heart of the pipeline. Re-point the production alias at the preview you
just verified — no rebuild:

```bash
vercel promote <preview-url> --yes        # --yes skips the confirm prompt (CI-safe)
vercel promote status                     # confirm the promotion landed
```

`vercel deploy --prod` is the *build-and-release-in-one* alternative; use it for the
trivial path (small project, trunk-based, no separate verification gate). For
anything with a review gate, **build once in preview, then `vercel promote`** so the
released bytes equal the tested bytes.

### 4. Keep the instant-rollback escape hatch ready

Because deployments are immutable, rollback is an alias re-point, not a revert
commit — it restores service in seconds while you debug at leisure:

```bash
vercel rollback                                   # roll back to the previous production deployment
vercel rollback <good-deployment-url-or-id>       # or target a specific known-good one
vercel rollback status                            # confirm the alias moved
```

When you do not know *which* deployment broke, binary-search instead of guessing:

```bash
vercel bisect --good <good-url> --bad <bad-url>                 # interactive
vercel bisect --good <good-url> --bad <bad-url> --run ./test.sh # exit 0=good, non-0=bad, 125=skip
```

Roll back **first**, find root cause **second**. A green production beats a clever
diagnosis.

### 5. Build once, deploy with --prebuilt (decouple build from deploy)

When CI already runs `next build`, do not pay for the build twice. Build locally /
in CI, then upload the prebuilt output so Vercel skips its build step:

```bash
vercel pull --yes --environment=production   # fetch project settings + env into .vercel/
vercel build --prod                          # produces .vercel/output (Build Output API)
vercel deploy --prebuilt --prod              # upload + deploy that output, no remote build
```

Match the `vercel build` target to the deploy target: `--prod` on both for
production, neither for preview. `--prebuilt` is what makes CI deploys fast and
makes the artifact you tested the artifact you ship.

### 6. Stage the rollout with Rolling Releases (canary)

For high-traffic or risky changes, shift production traffic gradually instead of a
100% cutover. Configure stages once per project (CLI shown; the dashboard and REST
API do the same):

```bash
# Automatic, time-based: 10% for 5m, 50% for 10m, then 100%
vercel rolling-release configure --cfg '{"enabled":true,"advancementType":"automatic","stages":[{"targetPercentage":10,"duration":5},{"targetPercentage":50,"duration":10},{"targetPercentage":100}]}'

# Or manual approval between stages (you gate each advance)
vercel rolling-release configure --enable --advancement-type=manual-approval --stage=10
```

Then deploy to production and **start** the rolling release so the new deployment
becomes a **canary** — it takes the first stage's traffic share while the previous
production serves the rest:

```bash
vercel deploy --prod                              # deploy the new production candidate
vercel rolling-release start --dpl <canary-url>   # begin the staged rollout for that deployment
vercel rolling-release fetch                      # current stage + traffic split
```

Advance, complete, or abort by traffic-watching the canary. Every command targets
the specific deployment with `--dpl`:

```bash
vercel rolling-release approve --dpl <canary-url> --currentStageIndex 0   # advance one stage (manual-approval)
vercel rolling-release complete --dpl <canary-url>                        # promote the canary to 100% production
vercel rolling-release abort --dpl <canary-url>                           # instant rollback: all traffic back to old prod
```

A rolling release is just a *gated* promotion — same immutable-deployment model,
same instant-undo. Aborting is step 4 applied to a canary. Watch the canary's error
rate (`vercel rolling-release fetch` plus `vercel logs --environment production --level error --since 5m`) before advancing.

### 7. Wire it into CI

Encode steps 1-5 as a workflow so humans never hand-run a production deploy.
Pattern: **PR → preview deploy + checks; merge to main → prebuilt production
deploy**. See the runnable GitHub Actions template below. Store `VERCEL_TOKEN`,
`VERCEL_ORG_ID`, `VERCEL_PROJECT_ID` as CI secrets;
**runtime app env vars belong in Vercel and are pulled by `vercel pull` — manage
those with vercel-env-management, not in the workflow file.**

## Quality bar

A deploy pipeline is A+ only when all hold:

- **Promote, don't rebuild.** Production runs a deployment that was first built and
  verified as a preview (`vercel promote` / `--prebuilt`), not a fresh `--prod` build
  whose bytes nobody has seen.
- **Every promotion is gated** by an inspect + real-route check + error-log scan on
  the preview (step 2). No green checks, no promote.
- **Rollback is one command and rehearsed.** The team knows `vercel rollback` and has
  run it at least once on purpose, not for the first time during an incident.
- **CI deploys are non-interactive:** `--yes` on prompts, target flags
  (`--prod`/none) consistent between `vercel build` and `vercel deploy`, secrets in the CI
  store.
- **Risky changes ship as a canary** (Rolling Releases) with a watched first stage,
  not a 100% cutover.
- **No stale runtime claims.** Functions default to Node.js 24 + Fluid Compute;
  nothing in the pipeline pins Node 18 or routes new code onto Edge Functions.

## Do NOT

- Do NOT run a fresh `vercel deploy --prod` to "release" a change you already built and
  tested in preview — promote the existing deployment so released bytes equal tested
  bytes.
- Do NOT debug a broken production before rolling back. Run `vercel rollback` to
  restore service, *then* investigate.
- Do NOT mismatch build/deploy targets: `vercel build` (preview) + `vercel deploy --prebuilt --prod`
  ships a preview build to production. Keep `--prod` on both or neither.
- Do NOT bake runtime app secrets into the CI workflow or `vercel.json` — they live in
  Vercel and arrive via `vercel pull`. That is vercel-env-management's job.
- Do NOT target deprecated Edge Functions or pin Node 18 for new functions; default
  to Fluid Compute on Node.js 24.
- Do NOT hand-promote production from a laptop once CI exists — the workflow is the
  source of truth; manual `promote`/`rollback` is the break-glass path.
- Do NOT treat a rolling release as fire-and-forget: watch the canary's error rate
  before `approve`/`complete`, and `abort` the moment it regresses.

## Artifact: preflight gate script

Self-contained Bash. Save as `promote-gate.sh`, run as
`./promote-gate.sh <preview-url>`. It runs the step-2 checks and only prints the
exact `vercel promote` command when the preview passes — so a human (or CI) never
promotes an unproven deployment. Needs the Vercel CLI logged in (`vercel login`) and
a linked project. No other dependencies.

```bash
#!/usr/bin/env bash
# Gate a preview deployment before promotion. Usage: ./promote-gate.sh <preview-url> [health-path]
set -euo pipefail

URL="${1:?usage: ./promote-gate.sh <preview-url> [health-path]}"
HEALTH="${2:-/api/health}"

echo "==> Inspecting $URL"
vercel inspect "$URL" >/dev/null || { echo "FAIL: deployment not inspectable"; exit 1; }

echo "==> Hitting $HEALTH (deploy-protection bypassed)"
if ! vercel curl "$HEALTH" --deployment "$URL" >/dev/null 2>&1; then
  echo "FAIL: health route $HEALTH did not respond 2xx"; exit 1
fi

echo "==> Scanning error logs"
ERRS="$(vercel logs --deployment "$URL" --level error --limit 50 2>/dev/null | grep -c . || true)"
if [ "${ERRS:-0}" -gt 0 ]; then
  echo "FAIL: $ERRS error log line(s) on the preview — fix before promoting"; exit 1
fi

echo "==> PASS. Promote the SAME deployment (no rebuild) with:"
echo "    vercel promote $URL --yes && vercel promote status"
```

### Worked example output

```
$ ./promote-gate.sh https://acme-7gk2qz.vercel.app
==> Inspecting https://acme-7gk2qz.vercel.app
==> Hitting /api/health (deploy-protection bypassed)
==> Scanning error logs
==> PASS. Promote the SAME deployment (no rebuild) with:
    vercel promote https://acme-7gk2qz.vercel.app --yes && vercel promote status
```

Read it: the preview was inspectable, its health route returned 2xx, and there were
no error-level logs — so the gate emits the promote command for the *exact* URL it
verified. A failing health check or any error line exits non-zero and prints no
promote command, so CI stops and nothing reaches production.

## Template: CI deploy workflow (GitHub Actions)

Drop in `.github/workflows/deploy.yml`. PRs get a verified preview; merges to `main`
get a **prebuilt** production deploy (build once, deploy that output). Set
`VERCEL_TOKEN`, `VERCEL_ORG_ID`, `VERCEL_PROJECT_ID` as repo secrets. Runtime app env vars
are NOT here — they live in Vercel and are fetched by `vercel pull` (see
vercel-env-management).

```yaml
name: Deploy
on:
  push:
    branches: [main]
  pull_request:

env:
  VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
  VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}

jobs:
  preview:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 24 }          # Node.js 24 LTS — matches the runtime default
      - run: npm i -g vercel@latest
      - run: vercel pull --yes --environment=preview --token=${{ secrets.VERCEL_TOKEN }}
      - run: vercel build --token=${{ secrets.VERCEL_TOKEN }}
      - name: Deploy preview (prebuilt)
        run: |
          URL=$(vercel deploy --prebuilt --token=${{ secrets.VERCEL_TOKEN }})
          echo "Preview: $URL"
          # gate before this preview is allowed to be promoted by the prod job
          vercel inspect "$URL" --token=${{ secrets.VERCEL_TOKEN }}

  production:
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 24 }
      - run: npm i -g vercel@latest
      - run: vercel pull --yes --environment=production --token=${{ secrets.VERCEL_TOKEN }}
      - run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}     # build once
      - name: Deploy production (prebuilt, same bytes)
        run: vercel deploy --prebuilt --prod --token=${{ secrets.VERCEL_TOKEN }}
      # Rollback is intentionally a manual break-glass step:
      #   vercel rollback --token=$VERCEL_TOKEN && vercel rollback status
```

Fill in the secrets, commit, and the pipeline runs steps 1-5 on every change. Add a
manual-approval environment around the `production` job, or swap the prod deploy for a
Rolling Release (step 6), when a change needs a canary instead of a cutover.
