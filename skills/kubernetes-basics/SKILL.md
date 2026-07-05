---
name: Kubernetes Basics
description: Writes production-ready Kubernetes manifests — Deployments with correct resource requests/limits, readiness/liveness/startup probes, PodDisruptionBudgets, safe rollouts, and graceful shutdown. Use when someone asks "write a Deployment for my service", "my pods keep getting OOMKilled", "why do I get 502s during deploys", "what should my resource limits be", or "why did my pod restart in a loop". Do NOT use for local multi-container development environments — use docker-compose-wizard instead; for CI/CD pipelines that deploy to the cluster, use github-actions; for the Terraform that provisions the cluster itself, use terraform-expert.
---

# Kubernetes Basics

The gap between a manifest that runs and one that is production-ready is exactly the fields people omit: no resource requests means the scheduler packs pods blind and the node OOMs; no readiness probe means every rollout routes traffic to cold pods and users see 502s; no PodDisruptionBudget means a routine node drain takes the whole service down. This skill produces manifests where every one of those fields is set deliberately.

## Core objects

- Deployment: manages replica Pods and rolling updates.
- Service: stable network endpoint for a set of Pods.
- ConfigMap/Secret: configuration and credentials, injected as env vars or files.
- Ingress: HTTP routing into the cluster.

## Operating procedure

### Step 1: Gather inputs

1. The container image and its listening port; whether an HTTP health endpoint exists (if not, add one before writing probes).
2. Observed resource usage — idle and p95 CPU/memory from staging or a load test. If unmeasured, label the numbers guesses, start at requests of `100m` CPU / `128Mi` memory, and revisit after a week of real metrics.
3. Startup time — seconds from process start to serving. This drives probe timing.
4. Traffic profile — steady or bursty — and availability requirement, which set replicas and the PodDisruptionBudget.
5. Shutdown behavior — does the app handle SIGTERM and drain in-flight requests?

### Step 2: Set resources with the right asymmetry

- Requests = what the scheduler reserves. Set them to observed p95 usage. Too low and the node overcommits; too high and you pay for idle reservation.
- Memory limit: set it, at roughly 1.5-2x the request. Exceeding it is an OOMKill — so a limit tight against real usage is a restart loop.
- CPU limit: unlike memory, exceeding it only throttles. Either omit the CPU limit (let bursts use idle node CPU) or set it 3-5x the request; a tight CPU limit silently adds latency through throttling.
- Never omit requests: without them the scheduler cannot place pods sensibly and one greedy pod starves the node.

### Step 3: Configure the three probes

- Readiness: gates traffic. Point it at an endpoint that confirms the pod can serve (dependencies reachable). Without it, rollouts send requests to pods that have not warmed up.
- Liveness: restarts a hung process. Keep it cheap and dependency-free — a liveness probe that checks the database converts a database blip into a cluster-wide restart storm.
- Startup: for slow-booting apps, so liveness does not kill a pod mid-boot. Budget: `failureThreshold x periodSeconds` must exceed worst-case startup (e.g. 30 x 10s = 5 minutes of grace).

Defaults that work for a typical HTTP service: readiness every 5s with 3 failures to unready; liveness every 10s with 3 failures to restart.

### Step 4: Write the Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: { name: api }
spec:
  replicas: 3
  strategy:
    rollingUpdate: { maxUnavailable: 0, maxSurge: 1 }
  selector: { matchLabels: { app: api } }
  template:
    metadata: { labels: { app: api } }
    spec:
      terminationGracePeriodSeconds: 30
      containers:
        - name: api
          image: registry/api:1.4.2          # immutable tag, never :latest
          resources:
            requests: { cpu: "100m", memory: "128Mi" }
            limits:   { memory: "256Mi" }    # CPU limit deliberately omitted
          readinessProbe:
            httpGet: { path: /healthz, port: 8080 }
            initialDelaySeconds: 5
            periodSeconds: 5
          livenessProbe:
            httpGet: { path: /livez, port: 8080 }
            periodSeconds: 10
          lifecycle:
            preStop:
              exec: { command: ["sleep", "5"] }   # let endpoint removal propagate
---
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata: { name: api-pdb }
spec:
  minAvailable: 2
  selector: { matchLabels: { app: api } }
```

### Step 5: Make rollouts and shutdown zero-downtime

- `replicas >= 2` always, plus the PodDisruptionBudget, so node drains and rollouts never hit zero.
- `maxUnavailable: 0, maxSurge: 1` for a capacity-safe rolling update; loosen only when spare capacity is proven.
- Graceful shutdown sequence: SIGTERM arrives, the preStop sleep (about 5s) holds the process alive while the endpoint removal propagates to load balancers, the app drains in-flight requests, and everything completes inside `terminationGracePeriodSeconds` (default 30s — raise it for long requests).
- ConfigMap changes do not restart pods. Roll the deployment or embed a checksum annotation on the pod template so config changes trigger a rollout.

### Step 6: Handle configuration and secrets

- Pin image tags by digest or immutable version; `:latest` makes rollbacks meaningless and deploys non-reproducible.
- Secrets live in a Secret (or an external secrets manager), never in the image or a ConfigMap.
- Apply declaratively via GitOps; `kubectl edit` in production creates drift no one can reconstruct.

## Worked contrast

Bad — runs fine in dev, fails on the first real deploy:

```yaml
containers:
  - name: api
    image: registry/api:latest    # unpinned: rollback impossible
    # no resources: scheduler packs blind, node OOMs under load
    # no readiness: rollout routes traffic to cold pods -> 502s
    # no PDB, replicas: 1        # node drain = outage
```

Every omission above maps to a named incident: OOMKilled neighbors, deploy-time 502s, drain-time downtime, and an unreproducible "what was running yesterday?"

Good: the Step 4 manifest — same app, each field closing one of those incidents.

## Deliverable

Produce a manifest set containing: the Deployment with requests/limits justified by measured (or explicitly guessed) usage, all applicable probes with timing arithmetic shown, an immutable image reference, the PodDisruptionBudget, the Service, and a one-paragraph shutdown-path note confirming SIGTERM handling and the preStop/grace-period budget.

## Do NOT

- Do not ship without resource requests — this is the root cause behind most "Kubernetes is flaky" complaints.
- Do not set a tight CPU limit by reflex; throttling is invisible latency. Memory is the limit that must exist.
- Do not point liveness at dependencies — restarts don't fix a down database, they amplify it.
- Do not use `:latest` or mutable tags in production.
- Do not run a single replica of anything availability matters for, and do not skip the PodDisruptionBudget.
- Do not assume ConfigMap edits reach running pods — they do not, until pods restart.

## Quality bar

- Every container has requests; memory has a limit; the CPU-limit decision is stated, not defaulted.
- Probe timing arithmetic covers worst-case startup, and liveness has no external dependencies.
- Rollout strategy plus PDB guarantee the service never drops below stated capacity during deploys and drains.
- Image reference is immutable; secrets are not in ConfigMaps or images.
- Shutdown path is verified: endpoint removal precedes process exit.

## Escalation

Autoscaling (HPA), multi-cluster, and service-mesh design are beyond a basics pass — flag them rather than improvising. Route deploy pipelines to github-actions, cluster provisioning to terraform-expert, and telemetry for the probes' `/healthz` endpoints to observability-stack.
