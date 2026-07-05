---
name: Terraform Expert
description: Writes safe, modular, idiomatic Terraform - remote state layout, module structure, plan-review discipline, version pinning, and blast-radius control. Use when someone asks "structure my Terraform repo", "how should I split state between environments", "terraform plan wants to destroy and recreate my database", "should I use count or for_each", or "how do I import existing infrastructure". Do NOT use for CI pipeline authoring in GitHub Actions - use github-actions instead; for Kubernetes manifests the infrastructure hosts, use kubernetes-basics; for managing app secrets outside IaC, use secrets-hygiene.
---

# Terraform Expert

The costly failure mode in Terraform is not syntax - it is an unreviewed apply that destroys a production database because a rename read as destroy-and-recreate, or two engineers corrupting shared state because it lived on a laptop. This skill structures Terraform so every change is a reviewed plan with a known blast radius, and state is a locked, encrypted, per-environment artifact.

## Operating procedure

State comes first because everything else is recoverable from code; state is not.

### Step 1: Gather inputs

1. Cloud and providers in play, and whether infrastructure already exists (import path) or is greenfield.
2. Environments - dev/staging/prod at minimum? Any per-region splits?
3. Team size and apply model - local applies, CI-driven, or Terraform Cloud? Default for more than one engineer: CI-driven with plan output posted to the PR.
4. Criticality tiers - which resources are irreplaceable (databases, DNS zones, KMS keys)? These get `prevent_destroy`.

### Step 2: Set up state before writing resources

- Remote backend always - S3 + DynamoDB locking, GCS, or Terraform Cloud. Local state for shared infrastructure guarantees eventual corruption or loss.
- One state file per environment. Never share state across environments: a bad apply in dev must be physically unable to touch prod.
- State stores secrets and all attribute values in plaintext - encrypt the backend at rest and restrict read access as tightly as the secrets it holds.
- Never edit state by hand. Refactors use `terraform state mv`; adopting existing infrastructure uses `import` (or import blocks). A hand-edited state file diverges from reality silently.

### Step 3: Lay out the repository

```text
modules/                    # reusable building blocks, no env-specific values
  vpc/
    main.tf                 # resources
    variables.tf            # typed inputs with validation
    outputs.tf              # what consumers may depend on
  ecs-service/
    ...
environments/               # thin per-env roots - wiring only
  dev/
    main.tf                 # module calls + env values
    backend.tf              # this env's state config
    terraform.tfvars
  staging/
  prod/
```

Rules the skeleton encodes:

- Environments are thin: they call modules and pass variables. If an environment root contains a raw `resource` block, that resource belongs in a module.
- A module hardcodes zero environment values; everything env-specific arrives via `variables.tf`.
- Modules expose only deliberate `outputs.tf` - consumers depending on undeclared internals break on refactor.

### Step 4: Write idiomatic resources

```hcl
variable "instance_count" {
  type    = number
  default = 2
  validation {
    condition     = var.instance_count > 0
    error_message = "instance_count must be positive."
  }
}

resource "aws_instance" "app" {
  for_each      = var.instances            # stable keys, not count indexes
  instance_type = each.value.type
  tags          = merge(local.common_tags, { Name = "app-${each.key}" })
}
```

- `for_each` over `count` whenever items have stable identities. With `count`, deleting item 0 renumbers every following item and Terraform destroys and recreates all of them.
- Tag everything through one `common_tags` local - untagged resources are unattributable in the bill and invisible to cleanup.
- Mark sensitive outputs `sensitive = true`.
- Secrets come from a secrets manager via data sources; never hardcode them (they land in state and git either way, but hardcoding puts them in both).

### Step 5: Pin versions and enforce plan review

- Pin providers and modules with `~>` constraints and commit `.terraform.lock.hcl`. An unpinned provider means every teammate and CI run can resolve different code.
- Every apply is preceded by a `terraform plan` reviewed by a human - in a PR for teams. The review question is always: does the plan contain any `destroy` or `replace` you did not intend?
- `lifecycle { prevent_destroy = true }` on every Step 1 irreplaceable resource. It turns a catastrophic apply into an explicit error.

### Step 6: Operate - upgrades, drift, blast radius

- Provider upgrades: one minor version at a time, read the upgrade guide, plan in dev first. A plan full of forced replacements after a bump means stop and read, not apply.
- Drift: run plan on a schedule (daily in CI is a reasonable default); reconcile manual console changes back into code or revert them. Unreconciled drift compounds until plans become unreadable.
- Blast radius: when a single state's plan routinely exceeds a few hundred resources or takes many minutes, split it into independently-applied stacks (networking / data / compute) connected by remote state outputs or data sources. Smaller states mean smaller worst-case applies.

## Worked contrast

Bad - environment root that will drift from staging within a month:

```hcl
# environments/prod/main.tf
resource "aws_instance" "app" {          # raw resource in an env root
  count         = 3                      # count: delete one, recreate the rest
  ami           = "ami-0abc123"          # hardcoded, differs from staging by accident
  instance_type = "m5.large"
}
```

Good - the same intent through the module contract:

```hcl
# environments/prod/main.tf
module "app" {
  source    = "../../modules/app-service"
  instances = { a = { type = "m5.large" }, b = { type = "m5.large" }, c = { type = "m5.large" } }
  env       = "prod"
}
```

Prod and staging now differ only in the values they pass, and the diff between them is reviewable in one screen.

## Deliverable

Produce a Terraform setup containing: the remote backend configuration with locking and encryption per environment; the `modules/` + `environments/` skeleton above populated for the actual stack; pinned provider constraints and a committed lockfile; `prevent_destroy` on every named irreplaceable resource; and a documented plan-review workflow (who reviews, where plan output lands).

## Do NOT

- Do not keep shared state locally or share one state file across environments - the failure is corruption or cross-env damage, and both are unrecoverable by git.
- Do not apply without a reviewed plan; the plan is the only thing standing between a rename and a destroyed database.
- Do not use `count` for collections with identity - removals renumber and recreate everything after them.
- Do not hand-edit state; use `state mv` and `import`.
- Do not leave providers unpinned or the lockfile uncommitted.
- Do not put raw resources in environment roots - that is how prod and staging quietly diverge.

## Quality bar

- `terraform validate` and a clean review of `plan` pass in every environment.
- No environment root contains a `resource` block; no module contains an environment-specific literal.
- Every irreplaceable resource carries `prevent_destroy`; a test plan proves a rename does not schedule a replace on them.
- Backend is remote, locked, encrypted; states are one-per-env.
- All providers pinned with `~>`, lockfile committed.

## Escalation

Route CI pipeline mechanics (plan-on-PR, apply-on-merge jobs) to github-actions, workload manifests to kubernetes-basics, and secret storage policy to secrets-hygiene. For compliance-audited infrastructure (SOC 2 change-management evidence), pair with soc2-evidence-helper.
