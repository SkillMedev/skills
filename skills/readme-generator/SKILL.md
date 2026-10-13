---
name: readme-generator
description: Write or overhaul a project README from the repository itself - audience-first structure, a quickstart where every command is verified by running it or tracing it to CI, a configuration table built from the env vars the code reads, real troubleshooting, and contributing and license sections. Use when someone asks "write a README for this repo", "our README is out of date", "improve the getting-started section", or "new contributors can't get this running". Do NOT use for end-user help-center articles - use help-documentation instead - or for on-call procedures - use runbook-writer instead.
metadata:
  title: "README Generator"
---

# README Generator

A README's main job is to get a stranger from "what is this?" to a working result before they give up. The costly mistake this skill prevents is the plausible README written from memory or a template: its quickstart says `npm install && npm start` when the repo uses pnpm, needs Node 20, and will not boot without three unmentioned environment variables. Every newcomer pays the same 30 minutes or leaves. A first command that fails teaches readers not to trust the rest.

## Operating procedure

### Step 1: Identify the audience and the job

Decide who lands on this README and what they came to do. That choice drives everything else:

- **Library or package users** want install, a minimal usage snippet, and a link to the API reference.
- **Application operators** want to run, deploy, and configure it.
- **Contributors** want to clone, build, test, and understand the layout.

Put the primary audience's path at the top; move the rest lower, or into `CONTRIBUTING.md`.

### Step 2: Extract facts from the repo, not from assumptions

Read these sources, roughly in order of trust:

1. **CI workflows** (`.github/workflows/*`, `.gitlab-ci.yml`, and similar). CI is the closest proof of which commands build and test the project, on which runtimes.
2. **Manifests and task runners**: `package.json` scripts plus `packageManager` and `engines`, the lockfile type (`pnpm-lock.yaml`, `yarn.lock`, `package-lock.json`, `bun.lock` (Bun ≥1.2) or `bun.lockb`), `pyproject.toml`, `Cargo.toml`, `go.mod`, `Makefile`, `justfile`, and `Taskfile.yml`.
3. **Runtime pins**: `.nvmrc`, `.node-version`, `.python-version`, `.tool-versions`, and `rust-toolchain.toml`.
4. **Configuration**: `.env.example` and similar files, and a code search for how variables are read (`process.env.`, `os.environ`, `os.Getenv`, `env::var`). The code search catches variables the example file forgot.
5. **Services**: `docker-compose.yml` and `Dockerfile` show which databases, queues, and ports the app expects.
6. **Existing docs**: the current README, `docs/`, `CONTRIBUTING.md`, and `LICENSE` (name the license from it, never guess).

Record each fact with its source. Anything unsourced is a question for the user, not a sentence in the README.

### Step 3: Verify every command

For each command you intend to publish, either run it in a clean checkout or trace it to a CI step or script definition that runs it. Mark each command:

- **Verified (ran):** you ran it and saw it succeed.
- **Verified (CI):** CI runs this exact command.
- **Unverified:** neither of the above, so it must be flagged to the user in the deliverable.

Pay special attention to:

- **Implicit prerequisites**: a running Postgres, a seeded database, a generated client (`prisma generate`), or a build step required before `dev` works.
- **Ports and URLs**: the URL you publish must match the port the app binds.

### Step 4: Write in this order

1. **Name and one-sentence description**: what it is and who it is for, in plain words, within the first three lines.
2. **Short status line or badges**, only if they are true and maintained (a CI badge for a workflow that exists, a package version). Use three badges at most.
3. **Quickstart**: prerequisites with versions, then the shortest verified path to a visible result, ending with how to know it worked ("open http://localhost:3000 and you should see the sign-in page").
4. **Usage**: the two or three most common tasks, with real, runnable examples.
5. **Configuration**: a table of every environment variable or config key. Give each one Required or Optional, its default, and what it does. Group them by feature if there are more than about 10. Never paste real secret values; use obviously fake placeholders.
6. **Project structure** (for applications and monorepos): a short annotated tree of the top-level directories only.
7. **Development**: how to run tests, lint, and typecheck, using the same commands CI uses.
8. **Troubleshooting**: the three to five failures a newcomer actually hits. Take them from issues, from error handling in the code, and from what broke while you ran the quickstart. Write each as the symptom (an exact error string where possible), then the cause, then the fix.
9. **Contributing, license, and support**: link to `CONTRIBUTING.md` if it exists, name the license from the `LICENSE` file, and say where to ask questions.

### Step 5: Run the stranger test

Read the draft as a newcomer. At each step: do I know what to type, what I should see, and what to do if I don't? Hidden prerequisites, undefined terms, and "configure as needed" fail.

## Worked example: fixing a quickstart

Before (written from memory):

```
## Getting started
npm install
npm start
```

Repo facts found:

- `pnpm-lock.yaml` exists, and `package.json` has `"packageManager": "pnpm@9.12.0"`.
- `.nvmrc` pins 20.
- `package.json` has no `start` script; `dev` runs `next dev`.
- `docker-compose.yml` defines a `db` service (Postgres 16, port 5432).
- CI runs `pnpm install --frozen-lockfile`, then `pnpm db:migrate` (a `package.json` script) against a Postgres service.
- `.env.example` lists `DATABASE_URL` and `AUTH_SECRET`, and `lib/email.ts` reads `RESEND_API_KEY`, which `.env.example` does not list but which is only used when sending email.

After:

````markdown
## Quickstart

Prerequisites: Node 20 (see `.nvmrc`), pnpm 9 (`corepack enable`), and
Docker for the local database.

```bash
git clone https://github.com/acme/ledgerly && cd ledgerly
cp .env.example .env            # then set AUTH_SECRET (see Configuration)
docker compose up -d db         # Postgres on localhost:5432
pnpm install
pnpm db:migrate
pnpm dev
```

Open http://localhost:3000. You should see the sign-in page.

## Configuration

| Variable         | Required | Default | Purpose                                         |
|------------------|----------|---------|-------------------------------------------------|
| `DATABASE_URL`   | Yes      | (none)  | Postgres connection string                      |
| `AUTH_SECRET`    | Yes      | (none)  | Session signing key: `openssl rand -base64 32`  |
| `RESEND_API_KEY` | No       | (none)  | Enables outbound email; without it, email is skipped |
````

Flagged to the user: `RESEND_API_KEY` is missing from `.env.example` (suggest adding it there), and the "email is skipped" behavior was inferred from `lib/email.ts` and should be confirmed.

## Thresholds

- From clone to visible result: at most about 6 commands and under 5 minutes, excluding dependency download time. If it takes more, the README should say so and explain why, or the setup should be scripted.
- The one-sentence description appears within the first 3 lines.
- Every environment variable read by the code appears in the configuration table, or is explicitly listed as internal.
- At most 3 badges, all backed by real CI or registry data.
- A README longer than about 300 lines should move reference material into `docs/` and link to it.

## Deliverable

Provide three things:

1. **The complete README.md**, ready to commit.
2. **A verification table** listing each published command and its state: Verified (ran), Verified (CI), or Unverified.
3. **Repo gaps you discovered**: variables missing from `.env.example`, scripts that do not work, and a missing `LICENSE` file. Present these as suggested fixes for the user to decide on; do not silently work around them in the prose.

## Do NOT

- Do not publish a command you have not verified or traced to CI without flagging it.
- Do not guess the package manager, runtime version, port, or license; each one has a file that answers it.
- Do not document aspirational features or roadmap items as if they exist.
- Do not delete accurate, project-specific content from an existing README just to fit the template. Restructure it instead.

## Quality bar

- A stranger following only the README reaches the stated result on the first try.
- Every command is verified or flagged, and every configuration key traces to code or to an example file.
- Troubleshooting entries quote real error messages and give a concrete fix.

For help-center articles use help-documentation; for on-call procedures use runbook-writer; if the quickstart fails because a local environment is broken, diagnose it with env-doctor before documenting a workaround.
