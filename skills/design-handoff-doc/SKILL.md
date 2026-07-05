---
name: Design Handoff Doc
description: Writes a complete design handoff document covering components, design tokens, interaction states, and edge cases for engineering implementation. Use when handing off a feature or screen to developers.
---

# Design Handoff Doc

A handoff document is the single source of truth for what is being built. It replaces Slack threads, live walkthroughs, and guesswork. Write it for an engineer who joins the project the day implementation starts and has no prior context.

## Document Structure

Every handoff doc contains these sections in order:
1. Overview: what the feature does and why it exists (2-4 sentences max).
2. Design file link: direct link to the specific page or frame, not the project root.
3. Component inventory: list of every new or modified component.
4. Design tokens used: all color, spacing, typography, and radius tokens referenced.
5. Interaction states: every state for every interactive element.
6. Edge cases and constraints: what can go wrong and what the design does about it.
7. Out of scope: explicit list of what is NOT being built in this iteration.
8. Open questions: unresolved decisions that engineering may need to flag or unblock.

## Component Inventory

For each component, document:
- Name (must match the design system component name exactly, or note if it is new).
- Variants or props that this feature uses.
- Any one-off overrides applied that deviate from the base component.
- Whether the component is new, modified, or used as-is.

Do not attach a full component API spec here — link to the design system documentation and annotate only the delta.

## Design Tokens

List tokens in four groups: color, spacing, typography, shadow/elevation. For each:
- Token name as it appears in the design system (e.g. 'color-surface-primary').
- Where it is used in this feature (e.g. 'card background, modal overlay').
- Flag any place where a raw value was used instead of a token — these are tech debt and must be resolved before implementation or explicitly accepted as exceptions.

## Interaction States

For every interactive element, enumerate all states:
- Default, hover, focus, active, disabled.
- Loading, empty, error, and success states where applicable.
- For each state: what changes visually (color, icon, text, layout shift) and what triggers it.
- Do not assume engineers will infer states from a single default-state frame.

## Edge Cases and Constraints

This section prevents the most implementation bugs. Document:
- Empty states: what renders when a list has zero items, a field is blank, or an image fails to load.
- Long content: maximum character counts, what happens when they are exceeded (truncate, wrap, error).
- Error states: per-field and form-level errors, API failure states, and timeout messaging.
- Internationalization: if the product is translated, flag any layout that breaks with 2x text length.
- Permission and role variations: if the UI differs by user role, document each variant.

## Acceptance Criteria Alignment

Close the document with a checklist an engineer can use to self-QA before handing back for design review. Each item is a binary pass/fail statement (e.g. 'Empty state illustration renders when zero results are returned'). Limit to 8-12 items focused on design fidelity, not engineering correctness.

## Deliverable

Produce a single handoff document containing all eight sections in order — overview, design file link, component inventory, design tokens, interaction states, edge cases and constraints, out of scope, and open questions — closed by the 8-12 item acceptance checklist. It should be self-sufficient: an engineer with no prior context can implement from it without a walkthrough, and a designer can QA the build against it without re-opening the design file.

## Do NOT

- **Do not link the project root instead of the specific frame.** The engineer opens a file with forty pages and implements the wrong iteration. Link the exact page or frame, and mark superseded explorations as archived.
- **Do not document only the default state.** "Engineers will figure out hover and error" is how every button ships with no focus ring and every form fails silently. If a state isn't in the doc, assume it won't be built.
- **Do not paste raw hex values or pixel numbers where a token exists.** Raw values fork the design system at implementation time; every one either maps to a token or is flagged as an accepted exception before the doc ships.
- **Do not smuggle scope in through the design file.** If a screen appears in the file but not in the doc's component inventory, engineers either build it unplanned or skip it unannounced. The out-of-scope section exists to prevent both.
- **Do not leave open questions implicit.** An undocumented ambiguity gets resolved by whoever hits it first — usually mid-sprint, usually wrong. Name each open question and who owns the answer.
- **Do not write the acceptance checklist as vibes.** "Looks polished" is not checkable. Every item must be binary pass/fail, verifiable by someone who didn't design the feature.

## Quality bar

- Every interactive element has all of its applicable states enumerated, not just default.
- Every color, spacing, and type value maps to a named token or a flagged exception.
- Empty, error, and long-content behavior is documented for every list, field, and image.
- Out-of-scope and open-questions sections are present, even if the entry is "none."
- The acceptance checklist contains only binary pass/fail statements.
