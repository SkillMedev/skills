---
name: Docker Compose Wizard
description: Produces production-minded docker-compose configurations - pinned images, healthchecks with real intervals, health-gated startup ordering, named volumes, explicit networks, and env-file secrets - plus the matching .env.example. Use when someone asks "write a compose file for this stack", "why does my app start before the database is ready", "how do I add a healthcheck", "my compose setup works on my machine but not in CI", or is reviewing a docker-compose.yml before it ships. Do NOT use for orchestrating across multiple hosts or cluster workloads - use kubernetes-basics instead; for provisioning the underlying cloud infrastructure - use terraform-expert instead; for CI pipelines that build and push the images - use github-actions instead; for auditing how secrets are stored and rotated - use secrets-hygiene instead.
---

# Docker Compose Wizard

Write compose files that are reproducible and safe to run, not just ones that start. The costly mistake this skill prevents is the works-on-my-machine file: `latest` tags that drift, an app that races its database on startup and crash-loops in CI, and state in anonymous volumes that vanishes on `down`.

## Operating procedure

### Step 1: gather inputs

Collect before writing (label guesses as guesses):

1. The services and their images or build contexts.
2. Which services hold state (databases, queues, caches) - these get named volumes.
3. Startup dependencies: who must be healthy before whom.
4. Which ports must reach the host; everything else stays internal.
5. Secrets involved, and where the file will run (dev only, CI, a real host) - a real host raises the bar on restart policy and resource limits.

### Step 2: pin and size the images

- Pin every image to a specific version tag - never `latest`, which makes yesterday's working file fail today. For maximum reproducibility on critical services, pin by digest.
- Prefer slim/alpine variants where compatible. Guideline budgets: a typical app service image under ~200 MB, utility sidecars under ~50 MB; a 1 GB+ app image usually means a missing multi-stage build (build toolchain shipped in the runtime image) - flag it.

### Step 3: make startup ordering real with healthchecks

- Add a `healthcheck` to every long-running service. `depends_on` without a condition only orders container *creation*; the app will still race the database.
- Use `depends_on: {db: {condition: service_healthy}}` for actual readiness gating.
- Healthcheck conventions that work in practice: `interval: 10s`, `timeout: 5s`, `retries: 5`, and `start_period` covering the service's normal boot time (30s for a database, longer for JVM apps) so boot-time failures don't count against retries.
- The check must probe readiness, not process existence: `pg_isready -U $user` for Postgres, `redis-cli ping` for Redis, an HTTP `/health` endpoint for apps - not `ps aux | grep`.

### Step 4: persist and network deliberately

- State lives in **named volumes**, not bind mounts, for databases - named volumes survive recreation, avoid host-permission drift, and are what `docker volume` tooling manages.
- Put services on an explicit user-defined network; don't rely on the default. Use two networks when there is a trust split (e.g. `frontend` for the app+proxy, `backend` for app+db, with the db only on `backend`).
- Expose only the ports the host needs. Inter-service traffic uses service names on the internal network - a database almost never needs a host port outside local debugging.

### Step 5: secrets and hygiene

- Pass secrets via environment from a `.env` file; never hardcode them in the YAML. Ship a `.env.example` with placeholder values and commit that, never the real `.env`. Deeper secret handling routes to secrets-hygiene.
- `restart: unless-stopped` for services that should survive crashes and host reboots.
- Constrain resources where it matters (`deploy.resources.limits`) - an unbounded memory leak in one service otherwise takes the host down with it.
- Run as a non-root user where the image allows (`user:` or the image's own unprivileged user).
- Comment every non-obvious setting; the file is documentation.

### Step 6: validate

Run `docker compose config` to catch YAML/interpolation errors, then mentally execute the clean-machine test: fresh clone, `cp .env.example .env`, fill values, `docker compose up` - would it come up healthy with no undocumented steps? If not, fix the file, not the README.

## Template

Copy and replace the [FILL] fields.

```yaml
name: [FILL: project]

services:
  app:
    image: [FILL: registry/app]:[FILL: version]   # pinned; never latest
    depends_on:
      db:
        condition: service_healthy    # gate on readiness, not creation
    environment:
      DATABASE_URL: postgres://[FILL: user]:${DB_PASSWORD}@db:5432/[FILL: dbname]
    ports:
      - "[FILL: host_port]:8080"      # only the app is host-reachable
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 20s
    restart: unless-stopped
    networks: [frontend, backend]

  db:
    image: postgres:[FILL: major.minor]
    environment:
      POSTGRES_USER: [FILL: user]
      POSTGRES_PASSWORD: ${DB_PASSWORD}   # from .env, never inline
      POSTGRES_DB: [FILL: dbname]
    volumes:
      - db_data:/var/lib/postgresql/data  # named volume: survives recreation
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U [FILL: user] -d [FILL: dbname]"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s
    restart: unless-stopped
    networks: [backend]                   # no host port: internal only

volumes:
  db_data:

networks:
  frontend:
  backend:
```

Matching `.env.example`:

```
DB_PASSWORD=changeme
```

## Deliverable

Produce the `docker-compose.yml` with pinned tags, healthchecks and health-gated `depends_on` on every long-running service, named volumes for all state, explicit networks, comments on non-obvious settings - plus the `.env.example` with placeholders.

## Do NOT

- Do not use `latest` tags - the file stops being reproducible the day upstream publishes.
- Do not use bare `depends_on` and assume readiness - it orders creation only; the race lands in CI.
- Do not put database state in bind mounts or anonymous volumes; the first `down -v` or permissions mismatch destroys it.
- Do not publish ports for internal services "just in case" - every published port is attack surface, and on a real host it may bypass the firewall.
- Do not inline secrets in the YAML, even for dev - the file gets committed and the secret is now in history.

## Quality bar

- `docker compose config` passes clean.
- Every image pinned; every long-running service has a readiness-probing healthcheck with interval/timeout/retries/start_period set.
- Every dependency is health-gated; all state in named volumes; networks explicit.
- The clean-machine test passes: `.env.example` plus the file is everything a new machine needs.
- Non-obvious settings carry comments.
