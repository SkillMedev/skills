---
name: pr-description-writer
description: Write pull request titles and descriptions from a branch diff - problem and evidence, approach, risk and blast radius, real test evidence, rollout and rollback, a reviewer guide, before/after screenshots for UI, and correct closing keywords - inside the repo's PR template when one exists. Use when someone asks "write the PR description", "open a PR for this branch", "summarize this branch for reviewers", or "my PRs keep getting rubber-stamped". Do NOT use for reviewing someone else's PR - use code-review-checklist instead - or for individual commit messages - use git-commit-writer instead.
metadata:
  title: "PR Description Writer"
---

# PR Description Writer

A pull request description tells a time-limited reviewer what to check, what could break, and what is already verified. The costly mistake this skill prevents is the description that restates the title ("Updates the checkout flow") or lists files, forcing the reviewer to reverse-engineer intent from the diff. They either rubber-stamp it or spend an hour rediscovering context the author had - and either way the riskiest part gets the least scrutiny.

## Operating procedure

### Step 1: Gather the inputs

Collect before writing:

- The full branch diff against its base: `git diff <base>...HEAD` (three dots, so you see only this branch's changes) and `git log <base>..HEAD --oneline`.
- The motivation: linked issue, ticket, incident, or the user's own explanation. If none exists, ask. Never invent a motivation.
- The repo's PR template, if any (`.github/pull_request_template.md`, `.github/PULL_REQUEST_TEMPLATE/`, or `docs/`). If one exists, fill its headings in its order; the sections below go inside it, not around it.
- How the repo merges. With squash merges the PR title usually becomes the commit subject (GitHub's default for multi-commit PRs); check the repo's squash-message setting and follow its commit convention.
- What testing was actually done: commands, results, manual checks. Ask; do not assume.

### Step 2: Classify the change and its risk

Sort changed files into core logic, mechanical (renames, generated files, lockfiles, snapshots), and tests. Then answer explicitly:

- Does it change a contract? That covers public API, request/response shape, CLI flags, config keys, database schema, event payloads, and file formats.
- Does it touch auth, permissions, payments, PII, or data deletion?
- Does it need a migration, a backfill, or a new environment variable or secret?
- Is it behind a feature flag, or is it live for everyone at merge?
- What is the blast radius if it is wrong? Name the users, endpoints, jobs, or services affected, and whether failure is loud (errors) or silent (wrong data).

### Step 3: Write the title

Imperative, outcome-focused, under about 72 characters: "Retry failed webhook deliveries with backoff", not "Webhook changes" or "Fix #812". Use the repo's convention (`feat(webhooks): ...`) if it has one.

### Step 4: Write the body in this order

1. **Why.** Two to four sentences on the problem: the symptom, who hit it, and the evidence (error rate, ticket, incident link). A reviewer who disagrees with the problem statement should be able to say so before reading any code.
2. **What changed.** The approach in a few bullets, organized by behavior, not by file. Call out the decision a reviewer is most likely to question, along with the alternative you rejected and why.
3. **Risk and blast radius.** The answers from Step 2 in plain sentences. If the honest answer is "low risk: internal admin page, behind a flag", say exactly that, because it tells the reviewer where not to spend time.
4. **Test evidence.** List the commands run with their results, new or changed tests and what each one proves, and manual verification steps that a reviewer can repeat. "Tested locally" is not evidence.
5. **Rollout and rollback.** Cover deploy order (for example, migration before code), flag plan, monitoring to watch after merge, and how to undo it. State whether rollback is a plain revert or not: a migration that drops a column or rewrites data is not reversible by revert, and the reviewer must know that before approving.
6. **Reviewer guide.** Say where to start reading, which files are mechanical and can be skimmed, and the one or two places where you want careful eyes.
7. **Screenshots** (any UI change). Before/after at the same viewport, plus changed empty/error/loading states and a mobile width if responsive. GIF for interactions.
8. **Links and closing keywords.** Use `Closes #812` (or `Fixes` / `Resolves`) on its own line for each issue this PR fully resolves; GitHub closes them when the PR merges into the default branch. Each issue needs its own keyword. Use `Refs #812` for partial work so the issue stays open.

Omit sections that do not apply rather than writing "N/A", unless the template requires them.

### Step 5: Check the description against the diff

Re-read the diff beside the draft. Every claim must be visible in the diff or the evidence; every risky hunk must be mentioned. Unexplained behavior in the diff gets explained or flagged to the author as possibly accidental.

## Worked example

Diff summary: a new `webhook_deliveries.attempt_count` column (migration), a retry loop with exponential backoff in `WebhookSender`, a `WEBHOOK_MAX_ATTEMPTS` env var defaulting to 5, 3 new tests, and a renamed helper across 9 files.

```markdown
## Retry failed webhook deliveries with exponential backoff

### Why
Merchants miss order events whenever their endpoint has a brief outage:
we attempt each delivery once and drop it. Support logged 23 tickets
about missing events in September (#812).

### What changed
- Failed deliveries (5xx, timeout, connection error) are retried with
  exponential backoff (30s, 2m, 8m, 32m) for up to `WEBHOOK_MAX_ATTEMPTS`
  total attempts (default 5). 4xx responses are not retried, because they indicate a
  merchant-side configuration error that retrying will not fix.
- Retries run on the existing job queue, not in-process, so a deploy
  does not drop pending retries. An in-process retry was rejected for
  that reason.
- New column `webhook_deliveries.attempt_count` (default 0).

### Risk
- Merchants with slow endpoints will now receive duplicate deliveries
  if they time out but still process the request. Payloads already
  carry `event_id` for idempotency.
- Worst case if wrong: a retry storm against a merchant endpoint. This
  is capped at 5 attempts per event, and the queue's per-host
  concurrency limit applies.
- Migration adds `webhook_deliveries.attempt_count` (default 0); new
  env var `WEBHOOK_MAX_ATTEMPTS` defaults to 5 if unset.

### Test evidence
- `pnpm test webhooks`: 41 passed, including 3 new tests covering 5xx
  retried, 4xx not retried, and attempts capped at the max.
- Staging: pointed a test merchant at an endpoint returning 503 and
  observed 5 attempts at the expected intervals in the job dashboard.

### Rollout / rollback
- The migration is additive (new column, default 0) and safe to deploy
  before the code.
- Rollback: a plain revert is safe; the extra column is ignored.
- After deploy, watch `webhook.retry.scheduled` and queue depth for
  24h.

### Reviewer guide
Start at `WebhookSender.deliver()`. The 9 files touched by the
`buildSignature` -> `signPayload` rename are mechanical. Careful eyes
on the 4xx/5xx classification in `isRetryable()`.

Closes #812
```

## Thresholds

- Over about 400 changed non-generated lines: suggest a split, or give a per-commit reading order.
- The "why" section should be 4 sentences at most. If it needs more, link a design doc or ADR instead.
- Any migration, new environment variable, or contract change must appear under Risk, every time.
- Any visible UI change requires at least one before/after screenshot pair.

## Deliverable

Provide the title and a ready-to-paste Markdown body, in the repo's template if one exists. After it, add a short list of what you could not verify and need the author to confirm: test results you did not see, motivation you inferred, and whether the issue is fully or only partly resolved.

## Do NOT

- Do not list files or paraphrase the diff line by line. The reviewer has the diff; give them the intent and the risk.
- Do not write "tested locally", "should be safe", or "no breaking changes" without the evidence behind the claim.
- Do not hide a migration, a new secret, or a behavior change in the middle of a bullet list. Put it under Risk.
- Do not invent ticket numbers, metrics, or test results.

## Quality bar

- A reviewer who reads only the description knows why the change exists, where to look hardest, and how to undo it.
- Every claim traces to the diff or to stated evidence, and every risky hunk is mentioned.
- Closing keywords match how much of each issue is actually resolved.
- The title would make a good squash-commit subject.

Write the individual commits with git-commit-writer. For reviewing someone else's PR, use code-review-checklist.
