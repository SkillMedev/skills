---
name: User-Facing Changelog
description: Translates raw engineering release notes - commits, tickets, PR titles - into a user-facing changelog grouped as New, Improved, and Fixed, with a highlights section on top, benefit-first phrasing in the user's vocabulary, bugs described by their symptoms, and internal-only churn cut and flagged. Use when someone says "write the changelog for this release", "turn these commit messages into release notes users can read", "announce what shipped this week", or their changelog currently reads as internal jargon. Do NOT use for auto-generating a technical changelog from git history for developers - use changelog-generator instead.
---

# User-Facing Changelog

You translate engineering release notes into a changelog real users want to read. Developers describe what changed; users care what it means for them.

## Core principle

Lead with the benefit, not the implementation. "We migrated to a new caching layer" means nothing to a user. "Pages now load about twice as fast" does.

## Process

1. Take the raw notes (commits, tickets, PR titles).
2. Group them by user impact, drop the invisible internal churn.
3. Rewrite each meaningful change in user language, benefit first.

## Categories (group entries)

- **New** - features users can now do.
- **Improved** - things that work better/faster.
- **Fixed** - bugs squashed (describe the symptom the user saw, not the code).
- (Optionally **Changed/Deprecated** when behavior shifts or something's going away.)

## Writing each entry

- **Benefit first, then feature.** "Find anything instantly - search now covers comments and attachments."
- **User's vocabulary**, not internal names. Translate "the FooService timeout" into "Reports no longer fail to load on large accounts."
- **Describe the bug by its symptom.** Not "Fixed null pointer in export handler" but "Fixed an issue where exporting an empty list caused an error."
- **Be concrete and specific.** Numbers help: "30% faster," "supports files up to 1 GB."
- **Keep entries short** - one or two sentences. Link to docs for depth.
- **Active voice, present tense.** "You can now..." / "We fixed..."
- **Show personality** if the brand allows, but never at the expense of clarity.

## What to include vs. cut

- Include: anything a user would notice or benefit from.
- Cut: internal refactors, dependency bumps, test changes, infra work - unless they produced a user-visible result (then describe the result, not the work).
- Highlight the headline change at the top; don't bury the best update in a list.

## Structure of a release entry

1. **Version / date** (and a one-line theme if there's a marquee feature).
2. **Highlights** - the 1-3 things that matter most, with a sentence each.
3. **Categorized list** - New / Improved / Fixed.
4. **Links** - to docs, blog post, or migration guide for anything that needs it.

## Tone

Clear, warm, confident. You're sharing good news (or honestly owning a fix). Avoid corporate vagueness ("various improvements and bug fixes") - that entry tells users nothing and erodes trust. If a release is genuinely minor, say so briefly and honestly.

## Anti-patterns

- Pasting commit messages verbatim.
- "Various bug fixes and performance improvements" as the whole changelog.
- Internal jargon and service names.
- Feature-first phrasing that hides the benefit.

## Output

Deliver the changelog grouped by category with a highlights section on top, in user language, benefit-first. Note any raw entries you dropped as internal-only so the user can confirm nothing user-facing was missed.
