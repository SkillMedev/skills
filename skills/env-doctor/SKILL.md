---
name: env-doctor
description: Diagnose and fix a broken local development environment methodically - reproduce the failure, fingerprint runtime, package manager, OS and arch, check version files and PATH shadowing, lockfile drift, missing env vars (without printing secrets), port conflicts, stale caches, native-module and Apple Silicon arch mismatches, and Docker context - then apply the smallest fix and leave a reproducible setup note. Use when someone asks "it works on my teammate's machine but not mine", "npm install broke everything", "why is the wrong Node version running", "port 3000 is already in use", or "this native module fails to load". Do NOT use for bugs in application logic - use obra-superpowers-skills-systematic-debugging instead - or for planned dependency upgrades - use wshobson-dependency-upgrade instead.
metadata:
  title: "Environment Doctor"
---

# Environment Doctor

"Works on my machine" failures are almost always a difference in versions, paths, or state - not in code. The costly mistake this skill prevents is the shotgun fix: deleting `node_modules` and the lockfile, upgrading the runtime, reinstalling the toolchain, and reaching a state that happens to work but that nobody can explain or reproduce - while silently upgrading dozens of dependencies and leaking secrets into a pasted log along the way.

## Operating procedure

Diagnose before changing anything. Each step either finds the cause or rules out a whole class.

### Step 1: Reproduce and capture the exact failure

Run the failing command yourself and save the full output, not a summary. Note the first error, not the last - later errors are usually fallout. Ask: did this ever work on this machine? What changed since (OS update, new branch, `brew upgrade`, new terminal app)? If it works on CI or a teammate's machine, that machine is your reference.

### Step 2: Capture the environment fingerprint

```bash
uname -sm                      # OS and hardware arch
node -v; node -p process.arch  # runtime and the arch it runs as
npm -v; pnpm -v; yarn -v       # whichever is relevant
python3 -V; python3 -c "import platform; print(platform.machine())"
git rev-parse --short HEAD; git status --short | head
```

Compare against what the project declares: `.nvmrc`, `.node-version`, `.tool-versions`, `.python-version`, `engines` and `packageManager` in `package.json`, `requires-python` in `pyproject.toml`, `rust-toolchain.toml`, `go.mod`. A mismatch here is the cause more often than not.

### Step 3: Check which binary actually runs

```bash
which -a node python3 pnpm     # every match on PATH, in order
type node                      # alias/function/hashed path?
echo "$PATH" | tr ':' '\n'
```

Look for shadowing: a Homebrew or system binary ahead of the version manager's shim, a stale global install, a shell alias. Check that the version manager is initialized in the shell actually in use (zsh vs bash profile, IDE terminals that skip login profiles). Run `hash -r` (or open a new shell) after PATH changes.

### Step 4: Check dependency state against the lockfile

- One lockfile only. `package-lock.json` plus `pnpm-lock.yaml` means two package managers fought; use the one `packageManager` names (Corepack, if enabled, pins that manager's version; it does not block other managers).
- Install strictly from the lockfile: `npm ci`, `pnpm install --frozen-lockfile`, `yarn install --immutable` (Yarn 1: `--frozen-lockfile`), `uv sync --locked`, `poetry install` after `poetry check --lock`. If strict install fails, the manifest and lockfile have drifted - fix that on purpose, in its own commit.
- `pip check` or `npm ls` surface broken or conflicting installs. In Python, confirm the active interpreter is the project venv: `python -c "import sys; print(sys.prefix)"`.

### Step 5: Check environment variables without printing secrets

Compare required keys, never values:

```bash
comm -23 <(grep -oE '^[A-Za-z_][A-Za-z0-9_]*' .env.example | sort -u) \
         <(grep -oE '^[A-Za-z_][A-Za-z0-9_]*' .env | sort -u)
```

Output is the list of missing keys. To test presence in the live shell, print `set`/`MISSING` per name, or a value's length - never the value. Also check for a variable set in the shell that overrides `.env` (many loaders do not overwrite existing variables), and for the wrong env file being loaded (`.env.local` vs `.env.development`).

### Step 6: Check ports, processes, and services

```bash
lsof -nP -iTCP:3000 -sTCP:LISTEN     # macOS/Linux
ss -ltnp | grep :5432                # Linux
netstat -ano | findstr :3000         # Windows
```

A zombie dev server or a second Postgres (Homebrew plus Docker) on the same port causes "address in use" or, worse, silent connections to the wrong database. Confirm dependent services are up and reachable at the host and port the app is configured for.

### Step 7: Check caches and build artifacts

Stale output survives version changes: `.next`, `dist`, `build`, `node_modules/.cache`, `.turbo`, `__pycache__`, `.pytest_cache`, `target/`, Xcode DerivedData. Delete build artifacts first - they are always safe to regenerate. Reinstall dependencies from the lockfile second. Package-manager caches (`npm cache verify`, `pnpm store prune`) last.

### Step 8: Check native modules and architecture

Native addons are compiled for one runtime ABI and one CPU arch. Signs: `NODE_MODULE_VERSION` mismatch, `wrong ELF class`, `incompatible architecture (have 'x86_64', need 'arm64')`, `mach-o file, but is an incompatible architecture`. On Apple Silicon, a terminal running under Rosetta reports `uname -m` as `x86_64` and installs x64 binaries. Fix the runtime/arch first, then rebuild (`npm rebuild`, or a clean install).

### Step 9: Check Docker context

```bash
docker context show; docker context ls
echo "${DOCKER_HOST:-unset}"
docker version --format '{{.Server.Os}}/{{.Server.Arch}}'
```

Colima, OrbStack, Docker Desktop, and remote contexts each have their own images, volumes, and networks - "my container vanished" is often a switched context. On Apple Silicon, amd64-only images need `--platform linux/amd64` and run emulated. Inside containers, `localhost` is the container; use the service name or `host.docker.internal` (on Linux Docker Engine this needs `--add-host=host.docker.internal:host-gateway`).

### Step 10: Apply the minimal fix and leave a setup note

Change one thing, rerun the reproduction, confirm it passes. Then make the fix durable for the next person: pin the version file, add the missing key to `.env.example`, document the service, or add a preflight check script. Write the setup note with the cause, the fix, and the verification command.

## Worked diagnosis transcript

```text
$ npm run dev
Error: The module '.../node_modules/better-sqlite3/build/Release/better_sqlite3.node'
was compiled against a different Node.js version using NODE_MODULE_VERSION 115.
This version of Node.js requires NODE_MODULE_VERSION 127.
```
ABI 115 is Node 20; 127 is Node 22. Native module built under 20, now running 22.

```text
$ cat .nvmrc            -> 20
$ node -v               -> v22.11.0
$ which -a node
/opt/homebrew/bin/node
/Users/dev/.nvm/versions/node/v20.18.0/bin/node
```
Cause: `brew upgrade` pulled in Homebrew's `node` as a dependency of another formula; `/opt/homebrew/bin` precedes the nvm path in `.zshrc`, so it shadows the project's pinned version.

Fix, smallest first:
```text
$ nvm use               -> Now using node v20.18.0
$ hash -r && node -v    -> v20.18.0
$ npm run dev           -> ready on http://localhost:3000
```
No rebuild needed - the addon was already built for 20. Durable fix: move nvm initialization after Homebrew's PATH setup in `~/.zshrc`, add `"engines": {"node": ">=20 <21"}`, and add a note to the README's setup section. The lockfile and `node_modules` were never touched.

## Thresholds

- Fingerprint captured before the first change: always.
- One change per rerun; if three single changes have not fixed it, stop and re-read the first error.
- Never delete a lockfile to fix an install; regenerate it only as a deliberate, reviewed dependency change.
- Clean-up order: build artifacts, then dependency reinstall from lockfile, then package-manager cache, then toolchain reinstall.
- Secrets: zero values printed, pasted, or committed - key names and lengths only.

## Deliverable

A short diagnosis report: the reproduction command and first error, the environment fingerprint versus the project's declared versions, the root cause with evidence, the minimal fix applied, the verification output, and a setup note (README section, version file, `.env.example` entry, or preflight script) that prevents recurrence.

## Do NOT

- Do not start with `rm -rf node_modules package-lock.json` - it hides the cause and upgrades transitive dependencies.
- Do not print, echo, or paste secret values while checking env vars.
- Do not `sudo npm install -g` or `sudo pip install` to fix permission errors - fix the prefix or use a version manager or venv.
- Do not upgrade the runtime or framework to escape an environment bug.
- Do not fix it only on your machine; make it reproducible for the next person.
- Do not use this for bugs in application logic - use obra-superpowers-skills-systematic-debugging - or for planned dependency upgrades - use wshobson-dependency-upgrade.

## Quality bar

- The root cause is named and backed by a command output, not a guess.
- The fix is the smallest change that made the reproduction pass.
- A fresh clone following the setup note works without tribal knowledge.
- No secrets appear anywhere in the transcript or note.
- The lockfile is unchanged unless dependency drift was the diagnosed cause.
