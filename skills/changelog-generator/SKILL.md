---
name: Changelog Generator
description: Generate or update a repository CHANGELOG.md in Keep a Changelog format from git history - grouping commits since the last tag into Added/Changed/Deprecated/Removed/Fixed/Security, rewriting them as user-facing entries, and recommending the semantic version bump. Use when someone asks "generate a changelog from the git log", "update CHANGELOG.md for this release", "what version bump does this release need", or "turn these commits into release notes". Do NOT use for writing benefit-first product announcement changelogs for end users from release notes - use changelog-writer instead; this skill produces the versioned CHANGELOG.md file that lives in the repo.
---

# Changelog Generator

A changelog exists so a human deciding whether to upgrade can answer "what breaks, what's fixed, what's new" in thirty seconds. The costly failure is dumping `git log` into a file: raw commit messages describe the code's history, not the release's meaning, and they bury the one breaking change under forty chores. This skill turns commit history into a Keep a Changelog-formatted CHANGELOG.md and a defensible version bump.

## Inputs to collect

Gather before writing: the repo (or the raw log), the last released tag (`git describe --tags --abbrev=0` - if there are no tags, the whole history is the first release), the existing CHANGELOG.md if any (append to its conventions rather than reformatting history), whether commits follow Conventional Commits (`feat:`, `fix:` prefixes make categorization mechanical; otherwise categorize by reading each diff's intent), and the project's versioning scheme (default: SemVer). Label any inferred categorization from an ambiguous commit as a guess for the maintainer to confirm.

## Operating procedure

Order matters: categorize before writing, and decide the version last, because the bump is a function of the finished category lists.

1. **Collect the range.** `git log <last-tag>..HEAD --oneline --no-merges`. Pull PR numbers from merge commits or squash-commit suffixes like `(#123)`.
2. **Drop invisible commits.** Remove chores, CI tweaks, test-only changes, dependency bumps, refactors, and formatting - unless one produced a user-visible result, in which case describe the result, not the work ("Reduced install size by 40%", not "Switched bundler").
3. **Categorize every surviving commit** into exactly one Keep a Changelog category:
   - **Added** - new features and capabilities (`feat:`).
   - **Changed** - existing behavior that now works differently.
   - **Deprecated** - still works, will be removed; say what to use instead.
   - **Removed** - gone; this is breaking.
   - **Fixed** - bug fixes (`fix:`), described by the symptom the user saw.
   - **Security** - vulnerability fixes always get their own section, never hidden under Fixed, because scanners and humans grep for it.
4. **Rewrite each commit as a user-facing entry.** One line, what changed from the consumer's perspective, ending with the PR link when available. Consumers of a library "see" the API, so API changes are user-facing here even when no end user notices.
5. **Decide the version bump** from the categories, strictest rule wins: anything in Removed, or any breaking change in Changed (or a `BREAKING CHANGE:` footer) → **major**; anything in Added or Deprecated → **minor**; only Fixed/Security/perf → **patch**. Pre-1.0 projects: breaking changes bump minor, everything else patch.
6. **Assemble the file**: newest version at top, `## [Unreleased]` section always present (and emptied into the new release section when cutting one), categories ordered Added > Changed > Deprecated > Removed > Fixed > Security, ISO dates.
7. **Flag what you dropped.** List the commits excluded as internal-only so the maintainer can confirm nothing user-facing was lost.

## Format

```markdown
# Changelog

All notable changes to this project are documented in this file,
following the Keep a Changelog format and Semantic Versioning.

## [Unreleased]

## [1.2.0] - 2026-06-15
### Added
- Export reports as CSV in addition to PDF (#123)
### Changed
- Session timeout is now configurable; the default remains 30 minutes (#127)
### Deprecated
- `client.fetchAll()` - use `client.list({ paginate: true })` instead; removal planned for the next major (#130)
### Fixed
- Exporting an empty report no longer fails with a blank error page (#125)
### Security
- Upgraded the JWT library to patch a signature-bypass vulnerability (#131)
```

## Worked contrast - translating a commit

Commit: `fix: handle NPE in ExportService when rows==0 (#125)`

Bad entry: `- fix: handle NPE in ExportService when rows==0 (#125)` - internal class name, internal jargon, describes the code, tells an upgrader nothing.

Good entry: `- Exporting an empty report no longer fails with a blank error page (#125)` - the symptom the user hit, in their vocabulary, with the reference preserved.

## Deliverable

Produce: the updated CHANGELOG.md section (or full file for a first release) in the exact format above, the recommended version number with the one-line rule that justifies it ("Removed section non-empty → major"), and the list of dropped internal-only commits for confirmation.

## Do NOT

- Do not paste commit messages verbatim - a changelog is written for the reader of the release, not the author of the commit.
- Do not bury a breaking change in Changed prose; breaking changes lead their section and force the major bump.
- Do not merge Security fixes into Fixed - their own section is what makes them findable.
- Do not put one change in two categories; pick the one that matches how a consumer experiences it.
- Do not write "various improvements and bug fixes" - an entry that conveys nothing erodes trust in every future entry.
- Do not backfill or reformat old released sections when appending a new one; released history is immutable.

## Quality bar

- Every entry is one line, symptom/benefit-phrased, free of internal class and service names, with a PR link where one exists.
- Categories appear in the canonical order and none is empty (omit empty sections).
- The version bump follows mechanically from the categories, and the justification names the triggering rule.
- `[Unreleased]` exists at the top of the file after the update.
- The dropped-commit list is included, so the maintainer can audit the cut in under a minute.

Hand off adjacent jobs: for a marketing-facing announcement version of the same release, route to changelog-writer; the semantic-version decision for API surface changes can be sanity-checked with api-versioning-strategist.
