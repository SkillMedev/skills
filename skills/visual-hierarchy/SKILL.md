---
name: Visual Hierarchy
description: Diagnoses and fixes visual hierarchy in a page, screen, or layout using ranked levers — size, weight, color, position, whitespace — and the one-focal-point-per-view rule. Use when someone asks "why does this layout feel flat", "what should the eye see first", "everything is competing on this screen", or wants the reading order of a composition made intentional. Do NOT use for a full multi-dimension design review covering usability and craft — use design-critique instead; for building the underlying type scale, use typography-system.
---

# Visual Hierarchy

Visual hierarchy makes the viewer's eye move in the right order. Without it every element competes, nothing lands, and the composition fails before a single word is read consciously. The expensive mistake this skill prevents is the "make it pop" spiral: adding contrast to element after element until everything shouts and nothing is heard — the fix is almost always subtraction, not addition.

## Operating procedure

Work the steps in order: the focal point decision (Step 2) governs every lever adjustment after it, so pulling levers first produces polish on the wrong structure.

### Step 1: Gather inputs

- The composition itself (screenshot, file, or description).
- The single most important action or message on this view. If the user cannot name one, that is the real problem — resolve it before touching design. Default guess: the primary call to action; label it a guess.
- The intended reading order: what should be seen first, second, third.
- Context of use: glanced at (ad, billboard, dashboard) or studied (article, settings page). Glanced compositions need steeper hierarchy.

### Step 2: Enforce one focal point per view

Every view gets exactly one point of maximum visual weight — the first thing the eye lands on. If two elements have equal weight, neither wins; the eye oscillates and comprehension drops. When a stakeholder insists two things are both most important, force the trade-off: "if a viewer sees only one thing, which one?" One focal point per view is the rule; a second "most important" element means the view should be split into two views.

### Step 3: Assign three levels, not more

Map every element to exactly one level:

- Primary — the dominant message, seen first. One element or tight group.
- Secondary — supporting detail, seen second.
- Tertiary — context, labels, metadata, seen on demand.

A fourth or fifth level signals the content needs editing, not more design. When everything is a priority, nothing is.

### Step 4: Apply the levers in ranked order

The levers, strongest first. Reach for the next lever only when the previous one is exhausted or constrained — using a weak lever to do a strong lever's job is the root of most muddy layouts.

1. **Size.** The most powerful tool. A 48px headline over 16px body is a 3:1 relationship readers process instantly; aim for at least 2:1 between primary and secondary text, and roughly 3:1 between primary and body.
2. **Weight.** Bold vs. regular at the same size creates hierarchy within a text block without disturbing the layout. Use it for secondary distinctions, not to rescue an undersized primary.
3. **Color and contrast.** Additive, never a replacement — hierarchy carried by color alone fails users with color vision deficiencies and collapses in single-color contexts (print, dark mode edge cases). Rule: the hierarchy must survive a grayscale conversion.
4. **Position.** Top and left win in left-to-right locales; the F- and Z-pattern entry points get seen first regardless of styling. Do not fight position with color — move the element.
5. **Whitespace.** Proximity groups; separation distinguishes. Increasing space around an element elevates it without changing its size. When a composition feels crowded, the fix is removing elements or adding space, not rearranging what exists.

### Step 5: Run the squint test

Squint (or blur the screenshot) until text is illegible. Whatever remains visible — the largest, highest-contrast masses — is the actual hierarchy. If the wrong element dominates the blurred view, adjust size or contrast relationships; never fix a failed squint test by adding visual noise elsewhere.

### Step 6: Audit the reading path

Trace the intended order (1 → 2 → 3), then check what a first-time viewer actually follows. Name each mismatch and which lever caused it. Common failures: a subheadline outweighing the headline because of color, a call to action buried in low-contrast text, an image dominating purely by size while serving no message.

## Worked critique: pricing page hero

**Bad:** 32px headline in brand purple; 28px bold subheadline in near-black; a photo occupying 60% of the viewport; the "Start free trial" button in gray outline style below the fold. Squint test: the photo wins, the subheadline beats the headline (near-black beats mid-purple on white), the CTA disappears entirely. Four things compete; the one revenue-critical element loses.

**Good:** headline raised to 48px near-black (size and contrast now agree — levers 1 and 3 pointing the same way); subheadline reduced to 20px regular gray; photo cut to 35% width and moved right (position demoted); button filled with the brand color, placed directly under the headline, with 48px of clear space around it (levers 3, 4, 5 stacked on the single action). Squint test now reads: headline → button → photo. One focal point, three levels, reading path matches intent.

## Deliverable

Produce a hierarchy audit containing: the named focal point, a three-level assignment for every significant element, the specific lever changes in ranked order (with before/after values such as "headline 32px → 48px"), the squint-test verdict, and the corrected reading path.

## Do NOT

- Do not create two focal points because two stakeholders each own one. Split the view or lose both.
- Do not use color as the only differentiator — it fails grayscale, accessibility, and print simultaneously.
- Do not add a fourth hierarchy level; edit the content instead.
- Do not fix a weak primary by muting everything else into gray mush — viewers need secondary and tertiary levels to be readable, just subordinate.
- Do not skip the squint test because the layout "obviously" works up close; close inspection hides exactly the problems the test reveals.

## Quality bar

- Exactly one element wins the squint test, and it is the intended one.
- No more than three hierarchy levels, each with named members.
- Hierarchy survives grayscale conversion.
- Primary-to-body size ratio is at least 2:1 (3:1 for glanced contexts).
- The first-time reading path matches the intended order, verified against the composition, not asserted.

For a review that also covers usability, consistency, and craft, escalate to design-critique; if the type scale itself is the constraint, pair with typography-system.
