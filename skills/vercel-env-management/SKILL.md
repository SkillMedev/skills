---
name: Vercel Env Management
description: Manage environment variables across Vercel environments and keep local .env in sync — vercel env pull/add/rm/ls, per-environment values (development/preview/production + custom), sensitive secrets, OIDC tokens for keyless cloud access, and the NEXT_PUBLIC build-time inlining gotcha. Use when someone says "sync my .env", "vercel env pull", "set a secret on Vercel", "my env var is undefined in production", "different API key per environment", "why is my NEXT_PUBLIC var stale", or "connect to AWS/GCP without storing keys". Do NOT use when the task is the deploy/promote/rollback flow — use vercel-deploy-pipeline; for routing AI provider keys through one gateway — use vercel-ai-gateway; for runtime/region/caching config (ISR, Fluid Compute) — use vercel-edge-and-isr; for blocking bots or WAF rules — use vercel-firewall-and-botid; for bundle/runtime speed tuning — use next-on-vercel-perf.
---

# Vercel Env Management

Vercel is the source of truth for environment variables; your local `.env*` files
are a *cache* of it, not the other way around. This skill keeps that cache honest:
pull the right values for the right environment, add and remove vars without
leaking secrets into git, and use OIDC so you never paste a long-lived cloud key
anywhere. Get this wrong and you ship a preview build talking to a production
database, or a `NEXT_PUBLIC_` key that is stale because it was baked in at build.

This is one step in the **vercel-platform** ship-a-Next.js-app workflow. It is
deliberately *not* a transcription of the `vercel env` man page — it sequences the
commands into the order a real project needs them and flags the three mistakes
that actually cost people an afternoon. Pair it with `vercel-deploy-pipeline`
(which consumes these vars at build) and `vercel-ai-gateway` (which replaces a pile
of per-provider keys with one).

Trigger eagerly: "sync my env", "pull env vars", "set a secret", "env var is
undefined", "different value per environment", "my `NEXT_PUBLIC` var won't update",
"keyless AWS/GCP access", "OIDC token".

## The model: three environments, one variable name

Vercel scopes every variable to one or more **environments**:

- **development** — used by `vercel dev` and pulled into your local `.env.local`.
- **preview** — every non-production deployment (every branch/PR). You can scope a
  preview value to a single git branch with `--git-branch`.
- **production** — the deployment behind your production domain.
- **custom environments** (e.g. `staging`) — named, long-lived targets you create
  in the dashboard; addressed by name everywhere a built-in environment is.

The same *name* (`DATABASE_URL`) holds a *different value* per environment. That is
the whole point: preview points at a branch database, production at the real one.

## Step 1 — Link the project (once)

Every `vercel env` command operates on the linked project. Link before anything
else; it writes `.vercel/` (which is gitignored by the CLI):

```bash
vercel link
```

## Step 2 — Pull the development env into a local file

`vercel env pull` writes the **development** variables to `.env.local` by default
(Next.js, Astro, SvelteKit, Nuxt all read `.env.local` locally):

```bash
vercel env pull                 # -> .env.local (development env)
vercel env pull .env.local      # explicit, same thing
vercel env pull --yes           # overwrite without the confirm prompt (CI)
```

To inspect what production or preview actually has — to debug an "it works locally
but not in prod" gap — pull that environment into a throwaway file and diff:

```bash
vercel env pull .env.production.local --environment=production
vercel pull --environment=preview --git-branch=my-feature  # branch-scoped preview
```

`vercel pull` (no `env`) also pulls project settings + env into `.vercel/`; use it
in CI before `vercel build`. For just the dotenv file locally, `vercel env pull` is
the one you want.

## Step 3 — Add a variable to the right environment(s)

Never hand-edit a value into `.env.local` and expect it to deploy — that file is a
local cache and is gitignored. Add it to Vercel:

```bash
vercel env add DATABASE_URL production          # prompts for the value
vercel env add DATABASE_URL preview             # a different value for preview
vercel env add DATABASE_URL development          # and for local `vercel dev`
```

Mark anything credential-shaped **sensitive** so its value can never be read back
out of the dashboard or API (write-only after creation):

```bash
vercel env add STRIPE_SECRET_KEY production --sensitive
```

Pipe a value in for scripting (avoids the interactive prompt), and `--force` to
overwrite an existing one:

```bash
printf '%s' "$VALUE" | vercel env add API_TOKEN production --sensitive
vercel env add API_TOKEN production --force      # replace existing value
```

Mind the budget: Vercel caps the combined size of all env vars at 64 KB per
deployment (Edge-runtime consumers are tighter, around 5 KB per variable). If you
are pushing against that, the fix is fewer/leaner vars — move big blobs of config
into Edge Config or a store, not into the environment.

After adding or changing a var, **re-pull** so your local cache matches:
`vercel env pull`.

## Step 4 — List and remove

```bash
vercel env ls                       # all vars, which environments each targets
vercel env ls production            # filter to one environment
vercel env rm OLD_TOKEN production   # remove from one environment
vercel env rm OLD_TOKEN --yes        # skip the confirm prompt
```

Removing a var does **not** affect already-built deployments — it takes effect on
the next build (see Step 6 for the redeploy rule).

## Step 5 — Keyless cloud access with OIDC (stop storing cloud keys)

Do not put a long-lived AWS/GCP/Azure key in an env var if you can avoid it. With
**Secure Backend Access** enabled, Vercel issues a short-lived `VERCEL_OIDC_TOKEN`
(present at build time and as a request header at runtime) that you exchange for
temporary cloud credentials. Nothing long-lived is stored, and tokens rotate
automatically. This is the recommended pattern for talking to a cloud provider
from a Vercel Function.

Pull the token for local dev like any other dev var. Note it is short-lived by
design — a locally pulled `VERCEL_OIDC_TOKEN` is valid for about 12 hours, so a
morning `vercel env pull` is part of the routine, not a one-time setup:

```bash
vercel link
vercel env pull        # brings down VERCEL_OIDC_TOKEN among the dev vars
```

Exchange it for AWS credentials with the first-party provider — no `AWS_ACCESS_KEY_ID`
in your env at all:

```ts
// app/api/report/route.ts
import { S3Client } from '@aws-sdk/client-s3';
import { awsCredentialsProvider } from '@vercel/oidc-aws-credentials-provider';

const s3 = new S3Client({
  region: process.env.AWS_REGION,
  credentials: awsCredentialsProvider({
    roleArn: process.env.AWS_ROLE_ARN, // the IAM role Vercel's OIDC may assume
  }),
});
```

(The AWS provider lives in `@vercel/oidc-aws-credentials-provider` —
`npm i @aws-sdk/client-s3 @vercel/oidc-aws-credentials-provider`. The older
`@vercel/functions/oidc` import has been moved out.)

For GCP, feed `getVercelOidcToken` to a workload-identity external account client:

```ts
import { getVercelOidcToken } from '@vercel/oidc';
import { ExternalAccountClient } from 'google-auth-library';

const authClient = ExternalAccountClient.fromJSON({
  type: 'external_account',
  audience: `//iam.googleapis.com/projects/${process.env.GCP_PROJECT_NUMBER}/locations/global/workloadIdentityPools/${process.env.GCP_WORKLOAD_IDENTITY_POOL_ID}/providers/${process.env.GCP_WORKLOAD_IDENTITY_POOL_PROVIDER_ID}`,
  subject_token_type: 'urn:ietf:params:oauth:token-type:jwt',
  token_url: 'https://sts.googleapis.com/v1/token',
  service_account_impersonation_url: `https://iamcredentials.googleapis.com/v1/projects/-/serviceAccounts/${process.env.GCP_SERVICE_ACCOUNT_EMAIL}:generateAccessToken`,
  subject_token_supplier: { getSubjectToken: getVercelOidcToken },
});
```

The cloud-side setup (an IAM role / workload-identity pool that trusts Vercel's
OIDC issuer with your team+project as the subject) is one-time and lives in your
cloud console, not here. Once it trusts Vercel, you delete the stored key.

Aside: for AI providers specifically, you usually do **not** need any of this — the
AI Gateway already accepts `VERCEL_OIDC_TOKEN` on Vercel and an `AI_GATEWAY_API_KEY`
locally, so one credential covers every model. That belongs to `vercel-ai-gateway`.

## Step 6 — The build-time inlining rule (NEXT_PUBLIC_*)

This is the single most common env surprise. **`NEXT_PUBLIC_`-prefixed variables
are inlined into the client bundle at build time** — their values are literally
string-replaced into the JavaScript when the build runs. Consequences:

- Changing a `NEXT_PUBLIC_` value in the Vercel dashboard does **nothing** to a
  deployment that already built. You must **redeploy** to bake in the new value.
- They are **public** — anything in a `NEXT_PUBLIC_` var ships to the browser. Never
  prefix a secret. (Marking it `--sensitive` does not make a `NEXT_PUBLIC_` var
  private; the prefix wins.)
- Server-only vars (no prefix) are read at **runtime** from
  `process.env` inside Functions/Middleware, so updating them + redeploying — or in
  some cases just a new deployment — picks up the change without a rebuild caveat.

When someone reports "I updated the env var but the site still shows the old
value," 90% of the time it is a `NEXT_PUBLIC_` var and the fix is **redeploy**, not
re-pull. Use the verifier below to catch leaks and stale-risk before they ship.

## Quality bar

An env setup is done right only when all hold:

- `.env.local` (and any pulled `.env.*.local`) is gitignored and was produced by
  `vercel env pull`, never hand-authored as the source of truth.
- Every variable that differs by stage is set in **each** environment it is needed
  in — a value present only in production silently becomes `undefined` in preview.
- Every credential-shaped var is `--sensitive`; no secret carries a `NEXT_PUBLIC_`
  prefix.
- Cloud access uses OIDC where the provider supports it; long-lived cloud keys are
  removed once the role trust is in place.
- After any dashboard change to a `NEXT_PUBLIC_` var, the app was **redeployed**
  (not just re-pulled) before anyone trusts the new value.

## Do NOT

- Do NOT commit `.env.local` / `.env.production.local`. They hold real secrets and
  are gitignored for a reason; pulling them is fine, committing them is a leak.
- Do NOT treat the local file as the source of truth. Edit on Vercel
  (`vercel env add`), then `vercel env pull` to refresh the cache.
- Do NOT put a secret behind `NEXT_PUBLIC_` — it is shipped to every browser, and
  `--sensitive` cannot save it.
- Do NOT expect a `NEXT_PUBLIC_` value change to take effect without a redeploy; it
  was inlined at build time.
- Do NOT set one shared value across all environments and assume preview is safe —
  a preview build with a production `DATABASE_URL` writes to prod.
- Do NOT paste long-lived AWS/GCP keys into env vars when OIDC + a trusted role
  does the same job with rotating short-lived credentials.
- Do NOT own the deploy/promote step here — that is `vercel-deploy-pipeline`.
- Do NOT configure runtime/region/ISR caching here (`vercel-edge-and-isr`), WAF /
  bot rules (`vercel-firewall-and-botid`), or bundle/runtime perf
  (`next-on-vercel-perf`) — this skill stops at the variables themselves.

## Verifier: lint your dotenv + Vercel env wiring

Self-contained Node script, no dependencies. Save as `env_check.mjs` and run from
your repo root with `node env_check.mjs`. It parses `.env.local`, flags secrets
that are publicly inlined, secrets that are not gitignored, and (optionally) any
variable your code reads via `process.env.X` that is missing from the pulled file.

```javascript
// Vercel env wiring linter. Run from repo root: node env_check.mjs
// Reads .env.local (or arg 1), checks it against your source for missing vars.
import { readFileSync, existsSync, readdirSync, statSync } from 'node:fs';
import { join, extname } from 'node:path';

const envFile = process.argv[2] || '.env.local';
const SECRETY = /(SECRET|TOKEN|KEY|PASSWORD|PRIVATE|DSN|CREDENTIAL)/i;

function parseDotenv(text) {
  const out = {};
  for (const line of text.split('\n')) {
    const m = line.match(/^\s*([A-Z0-9_]+)\s*=/i);
    if (m && !line.trimStart().startsWith('#')) out[m[1]] = true;
  }
  return out;
}

function gitignored(name) {
  if (!existsSync('.gitignore')) return false;
  const ig = readFileSync('.gitignore', 'utf8');
  return ig.split('\n').some((l) => l.trim() === name || l.trim() === '.env*.local' || l.trim() === '.env.local');
}

function walk(dir, acc = []) {
  for (const e of readdirSync(dir)) {
    if (e === 'node_modules' || e === '.next' || e === '.git' || e === '.vercel') continue;
    const p = join(dir, e);
    const st = statSync(p);
    if (st.isDirectory()) walk(p, acc);
    else if (['.ts', '.tsx', '.js', '.jsx', '.mjs'].includes(extname(p))) acc.push(p);
  }
  return acc;
}

const problems = [];
const notes = [];

if (!existsSync(envFile)) {
  console.error(`No ${envFile} found. Run: vercel link && vercel env pull`);
  process.exit(1);
}

const present = parseDotenv(readFileSync(envFile, 'utf8'));
const names = Object.keys(present);

// 1) Secrets that are publicly inlined.
for (const n of names) {
  if (n.startsWith('NEXT_PUBLIC_') && SECRETY.test(n)) {
    problems.push(`${n}: secret-shaped but NEXT_PUBLIC_ -> inlined into the browser bundle.`);
  }
}

// 2) The env file itself must be gitignored.
if (!gitignored(envFile)) {
  problems.push(`${envFile} is not gitignored. Add ".env*.local" to .gitignore before committing.`);
}

// 3) Vars the code reads but the pulled file is missing.
const referenced = new Set();
for (const file of walk(process.cwd())) {
  const src = readFileSync(file, 'utf8');
  for (const m of src.matchAll(/process\.env\.([A-Z0-9_]+)/g)) referenced.add(m[1]);
}
const SYSTEM = /^(NODE_ENV|VERCEL|VERCEL_|CI|npm_)/;
for (const r of referenced) {
  if (!present[r] && !SYSTEM.test(r)) {
    notes.push(`${r}: read via process.env but absent from ${envFile}. Set it: vercel env add ${r} <env>, then re-pull.`);
  }
}

console.log(`Checked ${names.length} vars in ${envFile}, ${referenced.size} process.env refs in source.`);
if (problems.length) {
  console.log('\nPROBLEMS (fix before deploy):');
  for (const p of problems) console.log('  x ' + p);
}
if (notes.length) {
  console.log('\nMISSING LOCALLY (set on Vercel + re-pull, or these are server-only prod vars):');
  for (const n of notes) console.log('  ? ' + n);
}
if (!problems.length && !notes.length) console.log('\nOK: no leaked secrets, file is gitignored, every referenced var is present.');
process.exit(problems.length ? 1 : 0);
```

### Worked example output

Given a `.env.local` that contains `NEXT_PUBLIC_STRIPE_KEY=sk_live_...` (a secret
wrong-prefixed) and source that reads `process.env.DATABASE_URL` (never pulled):

```
Checked 6 vars in .env.local, 4 process.env refs in source.

PROBLEMS (fix before deploy):
  x NEXT_PUBLIC_STRIPE_KEY: secret-shaped but NEXT_PUBLIC_ -> inlined into the browser bundle.

MISSING LOCALLY (set on Vercel + re-pull, or these are server-only prod vars):
  ? DATABASE_URL: read via process.env but absent from .env.local. Set it: vercel env add DATABASE_URL <env>, then re-pull.
```

Read it: rename `NEXT_PUBLIC_STRIPE_KEY` to `STRIPE_SECRET_KEY`, re-add it as
`--sensitive`, and read it only in a Function — never the client. Then
`vercel env add DATABASE_URL development` (and `preview`/`production`) and
`vercel env pull` so the local cache is complete. A clean run is the gate before
you hand off to `vercel-deploy-pipeline`.

## Template: env-inventory

Keep this table in the repo (README or `docs/env.md`) so every var's scope,
sensitivity, and inlining are documented. The verifier checks wiring; this checks
*intent*.

```
VERCEL ENV INVENTORY. [FILL: project name]

NAME                     DEV  PREVIEW  PROD  SENSITIVE  CLIENT?(NEXT_PUBLIC)  SOURCE
[FILL: DATABASE_URL]      y    y(branch) y     y          n                    Neon (Marketplace)
[FILL: STRIPE_SECRET_KEY] y    y        y     y          n                    Stripe dashboard
[FILL: NEXT_PUBLIC_URL]   y    y        y     n          y (redeploy on change) computed per env
[FILL: AWS_ROLE_ARN]      y    y        y     n          n                    OIDC role (no key stored)

RULES
  - SENSITIVE=y  -> added with `--sensitive`, write-only, never echoed.
  - CLIENT=y     -> NEXT_PUBLIC_, public + inlined at build; REDEPLOY to change.
  - Any cloud credential -> prefer OIDC role over a stored key.
  - .env*.local is gitignored; Vercel is the source of truth.
```
