---
name: Redline Annotation
description: Produces precise redline annotations - spacing, sizing, type, color, radius, z-order, and behavior notes in a consistent, scannable notation - that engineers can build from without asking questions. Use when someone asks "redline this screen", "spec this component", "annotate the spacing for handoff", or is preparing design files for development. Do NOT use for the overall handoff document with flows, edge cases, and acceptance criteria - use design-handoff-doc instead; for animation timing and easing, use motion-spec; for breakpoint behavior, use responsive-spec.
---

# Redline Annotation

Redlines eliminate guesswork. Annotate for the engineer who has never seen the design and cannot ask a question mid-sprint. The costly failure is the half-annotated file: the engineer eyeballs the missing values, QA files a stack of 2px-off tickets, and the fix cycle costs more than annotating properly would have.

## Operating procedure

### Step 1: Gather inputs

- Does a token system exist? If yes, annotate token names, not raw values.
- Is there a spacing grid convention (4px or 8px)? If yes, annotate exceptions to the grid, not the grid itself.
- Which frames and components are in scope, and does the engineer have inspect access to the design file and design system? Access changes how much must be written down.

### Step 2: Establish the notation convention

Fix a compact notation before annotating and include it as a legend in the file. Consistency is what makes redlines scannable; ad-hoc phrasing forces engineers to re-parse every note.

### Step 3: Annotate the always-list

- Spacing: all padding, margin, and gap values in pixels - or the token name if a token system exists. Internal padding as "p: 12 16" (top/bottom left/right), all four values if asymmetric. Draw a gap arrow between siblings and label the value.
- Sizing: width, height, and min/max constraints for every non-trivially-sized element. For auto widths, state "grows to fill container" vs. "shrinks to content".
- Type: font family, weight, size, line-height, letter-spacing, and color for every text style not covered by a named style.
- Color: token name first; raw hex only if no token exists. Include opacity when not 100%.
- Corner radius: per-corner values when they differ (top-left: 8, top-right: 8, bottom: 0).
- Z-order relationships for overlapping elements ("dropdown sits above modal overlay").

Never annotate only half of a symmetrical component - annotate fully so there is no ambiguity.

### Step 4: Annotate behavior

Anything a developer cannot infer from a static frame:

- Hover, focus, active, and disabled states as deltas from default ("background shifts from surface-primary to surface-hover, 150ms ease-out"). Complex motion routes to motion-spec.
- Overflow: scroll direction, hidden vs. clipped vs. visible.
- Conditional visibility: what triggers show/hide, and whether the element is display:none or removed from the DOM.
- Truncation: max lines before ellipsis, tooltip on truncate yes/no.

### Step 5: Skip what is already specified

- Values defined in a shared style or component the engineer can inspect - do not re-document every token.
- Structural markup and class names - annotate intent, not implementation; implementation is the engineer's domain.
- Every pixel that conforms to the stated grid convention - the grid is the rule, redlines mark the exceptions.

### Step 6: Final delivery pass

Run the quality bar below before handing off.

## Worked example

```
REDLINE CONVENTION + EXAMPLE - "Project card" component

Legend
  p: 12 16          padding, top/bottom left/right (4 values if asymmetric)
  g: 8              gap between siblings (arrow points between them)
  r: 8 8 0 0        corner radius per corner when unequal
  ->fill / ->hug    grows to fill container / shrinks to content
  [token]           token name; raw hex only when no token exists

Card container
  w: ->fill, max 400    h: ->hug, min 96
  p: 16                 r: 12
  bg: [surface-default] border: 1px [border-subtle]
  shadow: [elevation-1]

Title row
  g: 8 between avatar and title
  Avatar: 32x32, r: full
  Title: [text-primary], Inter 600 16/1.4, truncate 1 line, tooltip on truncate

States (deltas from default)
  hover:    bg [surface-default] -> [surface-hover], 150ms ease-out
  focus:    2px [focus-ring] outline, 2px offset
  disabled: opacity 0.4, no pointer events

Z-order: card menu sits above sibling cards, below modal overlay.
Grid: 4px convention; only exception here is avatar-to-edge 14px (optical).
```

## Deliverable

Produce an annotated file or spec sheet containing: the notation legend, complete measurements for every in-scope element (spacing, sizing, type, color, radius, z-order), behavior notes for every state and conditional, an explicit list of grid exceptions, and the owner's name with a last-updated date on the file itself.

## Do NOT

- Do not annotate half a component and let symmetry be assumed - assumed symmetry is where 2px bugs live.
- Do not write raw hex when a token exists; raw values silently fork the design system.
- Do not re-document the entire token library; engineers with system access need exceptions, not an echo.
- Do not annotate class names or markup structure - prescribing implementation creates friction and goes stale.
- Do not leave states unannotated because "hover is obvious" - the exact target color and duration are never obvious.

## Quality bar

- All annotations are visible without clicking into layers.
- A legend is included whenever custom notation is used.
- The file carries a last-updated date and the design owner's name.
- Every interactive element has all four states (hover, focus, active, disabled) specified or explicitly marked not applicable.
- Responsive behavior is called out inline or linked to the responsive-spec.
- An engineer with no file access could build the component from the annotations alone.
