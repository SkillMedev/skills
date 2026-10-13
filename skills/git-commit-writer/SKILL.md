---
name: git-commit-writer
description: Write commit messages from a staged diff in Conventional Commits 1.0.0 format - correct type and scope, `!` and BREAKING CHANGE footers, GitHub closing keywords, and a body that explains why - and split mixed changes into separately revertable commits. Use when someone asks "write a commit message for this diff", "commit this", "is this a breaking change?", or "split this into proper commits". Do NOT use for the pull request description - use pr-description-writer instead - or for turning a release's commits into a CHANGELOG.md - use changelog-generator instead.
metadata:
  title: "Git Commit Writer"
---

# Git Commit Writer

A commit message is the only documentation guaranteed to travel with the code: `git blame` surfaces it at the exact line someone is about to change, years after the ticket tracker is gone. The costly mistake this skill prevents is the message that restates the diff ("update user service", "fix bug", "wip") and drops the one thing the diff cannot show - why. A close second is the mixed commit, where a refactor, a fix, and a formatting sweep share one SHA and none can be reverted or bisected alone.

## Operating procedure

### Step 1: Read the actual change, not the file list

Run `git diff --staged` (or `git show <sha>` when rewording) and read every hunk. Collect: what behavior changed, which component owns it, whether a public surface changed (exported API, CLI flag, config key, HTTP contract, schema, event payload), and the motivation. Motivation is rarely in the diff - get it from the user, the issue, or the branch name, and flag it if inferred.

Read `git log --oneline -20` for conventions: Conventional Commits or not, existing scopes, trailers like `Signed-off-by:` (DCO). Match the repo; without Conventional Commits, write a plain imperative subject with the same body discipline.

### Step 2: Decide whether this is one commit or several

One commit means one logical change: you can describe it in a single subject without "and", and reverting it would undo exactly one decision. Split when the staged diff contains any of these:

- A refactor or rename alongside a behavior change - split so the refactor is provably behavior-neutral and the fix is small.
- Formatting or lint autofixes across files the change does not otherwise touch.
- An unrelated fix noticed in passing.

Propose the split as an ordered list of commits, each with its message and the files or hunks it contains (`git add -p` for hunk-level splits). Order them so every commit builds and passes tests on its own - that is what keeps `git bisect` usable.

### Step 3: Pick the type and scope

Conventional Commits 1.0.0 gives only two types semantic meaning: `feat` (SemVer MINOR) and `fix` (PATCH). Others - `refactor`, `perf`, `docs`, `test`, `build`, `ci`, `chore`, `style`, `revert` - are allowed by convention. Type by effect on the consumer, not files touched:

- A bug fix that required a refactor is `fix`.
- A performance improvement with no behavior change is `perf`, not `fix`.
- A change to a test helper only is `test`; a change to production code plus its tests is typed by the production change.

Scope is a noun naming the area - `fix(auth):`. Reuse scopes from the log; omit it for cross-cutting changes rather than invent one.

### Step 4: Mark breaking changes correctly

A breaking change forces consumers to change code, config, or data to keep working. Either form alone signals a MAJOR bump:

- `!` immediately before the colon: `feat(api)!: remove v1 pagination params`
- A footer whose token is exactly `BREAKING CHANGE` (uppercase; `BREAKING-CHANGE` is the only accepted synonym), followed by `: ` and a description.

Use `!` so the break shows in `--oneline`, and add the footer whenever there is a migration step - it is where tooling and humans look for upgrade instructions. Any type can be breaking, not only `feat`.

### Step 5: Write the subject line

Format: `type(scope): description`. Rules:

- Imperative mood - it completes the sentence "If applied, this commit will ___". "add retry to webhook sender", not "added" or "adds".
- Describe the outcome, not the mechanics: "reject expired invite tokens", not "change if statement in invites.ts".
- Lowercase after the colon and no trailing period, unless the repo's history clearly does otherwise.

### Step 6: Write the body - why, then how, then consequences

Separate the body from the subject with one blank line. Wrap at 72 columns. Answer, in this order and only as far as needed:

1. **Why** - the problem, symptom, or requirement. Include the observed failure (error text, metric, user report) when there is one.
2. **What approach and why this one** - especially if a reviewer would ask "why not just X?". Name the alternative you rejected and the reason.
3. **Consequences** - anything a future reader must know: a behavior that intentionally changed, a known limitation, a follow-up deliberately left out.

Do not narrate the diff; it already says what changed.

### Step 7: Add footers

Footers follow the body after one blank line, one per line, in `Token: value` or `Token #value` form, with hyphens instead of spaces in tokens (except `BREAKING CHANGE`). Common ones:

- `Fixes #142` / `Closes #142` / `Resolves #142` - GitHub closes the issue when the commit reaches the default branch. Each issue needs its own keyword: `Fixes #142, fixes #150`. Cross-repo: `Fixes owner/repo#142`.
- `Refs #142` - links without closing, for partial progress.
- `Co-authored-by: Name <email>` - preserve any the user already has.

Never add `Fixes` for an issue the commit does not actually resolve - it silently closes real bugs.

## Worked example

Staged diff (abridged):

```diff
--- a/src/billing/invoice.ts
+++ b/src/billing/invoice.ts
-export function totalCents(lines: Line[]): number {
-  return Math.round(lines.reduce((s, l) => s + l.price * l.qty, 0) * 100);
+export function totalCents(lines: Line[]): number {
+  return lines.reduce((s, l) => s + l.priceCents * l.qty, 0);
 }
--- a/src/billing/types.ts
-  price: number;        // dollars
+  priceCents: number;   // integer cents
--- a/src/billing/format.ts
-const fmt = (n) => '$' + n.toFixed(2)
+const fmt = (n: number): string => `$${n.toFixed(2)}`;
```

Context from the user: some customers' invoice totals were one cent off the accounting ledger; issue #318.

The `format.ts` change is a type annotation and template-literal tidy-up with no behavior change - it is split out.

Commit 1:

```
fix(billing)!: store line prices as integer cents

Invoice totals drifted from the ledger by a cent on some orders
because prices were summed as floating-point dollars and rounded once
at the end (0.1 + 0.2 === 0.30000000000000004), while the ledger sums
integer cents. Accumulated float error occasionally crossed a rounding
boundary.

Prices are now carried as integer cents end to end, and totals are an
exact integer sum. Rounding at the edge was rejected because it still
sums floats before rounding.

BREAKING CHANGE: Line.price (dollars) is replaced by Line.priceCents
(integer). Callers constructing Line objects must multiply by 100 and
round once at input.

Fixes #318
```

Commit 2:

```
refactor(billing): type the currency formatter
```

## Thresholds

- Subject: aim for 50 characters, hard ceiling 72 - beyond that, GitHub and many tools truncate it.
- Body lines: wrap at 72 columns.
- Body required for any `fix`, any breaking change, and any change over about 20 lines; typos and version bumps may be subject-only.
- If the subject needs "and", or the diff spans more than 2 unrelated scopes, propose a split before writing anything.

## Deliverable

For each commit: the complete message in a fenced block, ready to paste or pass to `git commit -F`; for a split, the ordered list of commits with the files or hunks in each; and one line flagging anything you inferred (motivation, issue number, breaking status) for the user to confirm.

## Do NOT

- Do not write "fix bug", "update", "changes", "wip", or "address review comments" - each tells a future reader nothing.
- Do not describe the diff mechanically ("change x to y in foo.ts"); describe the behavior and the reason.
- Do not judge breaking by size - the test is whether consumers must change.
- Do not put `BREAKING CHANGE` in lowercase or in the subject line; only the footer token or the `!` count.
- Do not bundle a refactor with a behavior change "to save a commit" - it costs every future bisect and revert.

## Quality bar

- `git blame` on any changed line explains why it changed, with no other tool needed.
- Every commit in a split builds and passes tests on its own.
- Type, `!`, and footers are spec-valid, so release tooling computes the correct version bump.
- Inferred facts are flagged, not stated as truth.

For the pull request that wraps these commits, use pr-description-writer; to turn a release's worth of commits into a CHANGELOG.md, use changelog-generator.
