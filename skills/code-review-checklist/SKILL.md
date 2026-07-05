---
name: Code Review Checklist
description: Run a systematic multi-pass code review — correctness, design, security, performance, tests — and report findings ordered by severity with concrete, respectful suggestions. Use when someone asks "review this PR", "review this diff", "what's wrong with this change", or wants a pre-merge quality gate on a branch. Do NOT use for a security-only deep audit of a change — use secure-code-review instead — or for writing the ticket or tracking artifact that describes the change — use jira-ticket-writer instead.
---

# Code Review Checklist

A good review catches the bug before production and leaves the author better at their job; a bad review rubber-stamps the diff or nitpicks formatting while an injection hole ships. This skill turns review into an ordered procedure — separate passes with separate questions — because reading once for everything at once is how correctness bugs hide behind style comments.

## Inputs to collect

Before the first pass, gather: the diff itself, the stated intent (PR description, ticket, or ask the author — reviewing a change without knowing what it claims to do is guessing), and how the change reaches users (user-facing, internal tool, data migration) since that sets the severity bar. Review the change, not the whole codebase: read surrounding context only where the diff demands it.

## Operating procedure

Run the passes in this order. Correctness comes first because a beautifully designed wrong answer is worthless, and security before performance because a fast vulnerability is worse than a slow safe path. Do each pass as a separate read of the diff.

### Pass 1 — Correctness

Does the code do what the PR says it does? Red flags:

- Edge cases unhandled: empty inputs, null/undefined, zero, negative numbers, very large values, unicode, concurrency.
- Errors swallowed silently — an empty catch block or a logged-and-ignored failure is a future 3am page.
- Off-by-one and boundary conditions: `<` vs `<=`, inclusive vs exclusive ranges, first/last iteration.
- The diff and the description disagree — either the code or the claim is wrong; find out which.
- State mutated where a caller won't expect it (shared objects, module-level variables).

### Pass 2 — Design and readability

Would the next person understand and safely extend this? Red flags:

- Names that lie or say nothing (`data2`, `handleStuff`, a `getUser` that also writes).
- The change fights the surrounding codebase's existing patterns instead of following or explicitly replacing them.
- Abstraction introduced for one caller, or duplication at its third copy left unextracted.
- Unrelated orthogonal edits riding along — they hide the real change and bloat the blast radius; ask for a split.
- A public interface that will be hard to change later shipping without discussion.

### Pass 3 — Security

Assume hostile input. Red flags:

- Untrusted input reaching a query, shell, path, or HTML without validation/escaping (injection, XSS, path traversal).
- Secrets, tokens, or credentials in code, config, or log statements.
- Missing authN/authZ checks on anything that changes state or reads private data — especially new endpoints.
- Sensitive data (PII, passwords) written to logs or error messages.

If this pass finds more than one real issue, or the change touches auth, crypto, or money, stop and run secure-code-review as a dedicated pass.

### Pass 4 — Performance

Only flag what plausibly matters at real scale. Red flags:

- N+1 queries, or I/O inside a loop that could batch.
- Unbounded reads: queries without limits, loading a whole table or file into memory.
- Missing pagination on list endpoints; missing cache on hot repeated work.
- Do NOT flag micro-optimizations in cold paths — that is noise that trains authors to ignore you.

### Pass 5 — Tests

- New behavior and its failure paths are covered; a bug fix includes a test that fails without the fix.
- Tests assert behavior, not implementation (no asserting on internals or call counts of private collaborators).
- No new flakiness sources: real clocks, sleeps, order dependence, shared state.

### Pass 6 — Report

Order findings by severity: **blocking** (correctness or security defects, missing tests on risky paths) → **should-fix** (design problems that will cost later) → **nit** (style, naming; prefix them "nit:" so the author can triage). For each finding give the location, the problem in one line, and a concrete suggestion. Praise what is genuinely good — review is signal, not just defect-finding. State an explicit verdict: approve, approve-with-nits, or request changes.

## Worked contrast — phrasing a finding

Bad: "This is wrong. Why would you do it this way? Use a map."

Good: "`orders.find()` inside this loop makes the handler O(n²); with ~10k orders that's a visible stall. Suggest building a `Map` by id before the loop — one pass, O(n). Blocking, since the checkout path hits this."

The good version names the location, the concrete consequence, the fix, and the severity — and critiques the code, never the author.

## Deliverable

Produce a review report containing: the verdict, findings grouped blocking → should-fix → nit with location + one-line problem + concrete suggestion each, at least one genuine positive when one exists, and a note of which passes were run so gaps are explicit.

## Do NOT

- Do not review everything in one read — single-pass review reliably finds style and misses logic.
- Do not block on personal style preferences the codebase doesn't enforce; that's a linter's job and it erodes trust.
- Do not say "this is wrong" without saying what to do instead — every blocking finding carries a suggestion.
- Do not approve a diff too large to actually read; ask for a split rather than skimming 2,000 lines.
- Do not let a clean style pass launder a skipped security pass — the passes are not interchangeable.

## Quality bar

- Every pass was executed, or its skip is stated in the report.
- Every blocking finding names a concrete consequence and a concrete fix.
- Severity ordering is honest — no security defect filed as a nit.
- The tone critiques code, not people, and the report ends with an explicit verdict.
