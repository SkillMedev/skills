---
name: Responsive Spec
description: Specifies responsive behavior across breakpoints - layout reflow, content priority, touch targets, and fluid-versus-stepped scaling rules - precisely enough for engineers to implement directly. Use when someone asks "how should this work on mobile", "spec the breakpoints", "write the responsive rules for this page", or is adapting a single-viewport design for multi-device use. Do NOT use for static measurements inside one frame - use redline-annotation instead; for the overall handoff package with flows and edge cases, use design-handoff-doc.
---

# Responsive Spec

A static design at one viewport does not specify a responsive product. The costly failure is handing engineers a desktop frame and a mobile frame with nothing in between: every width between 768px and 1024px becomes an improvisation, and the improvised versions are the ones users actually see. A responsive spec documents the rules that govern how layout, content, and interaction change as the viewport changes.

## Operating procedure

### Step 1: Gather inputs

- The existing breakpoint or grid system, if any. If the codebase uses a Tailwind config or similar, match those pixel values exactly - never introduce a parallel system.
- Device analytics: which viewport widths carry real traffic. Label guesses as guesses.
- Minimum supported width (default 320px) and whether touch, pointer, or both are in scope.
- The frames or pages being specified, and whether a token/component system already encodes any behavior.

### Step 2: Define breakpoints explicitly

State every breakpoint with pixel values and a semantic name. Default convention when the product has none:

- mobile: 0-767px
- tablet: 768-1023px
- desktop: 1024-1439px
- wide: 1440px and above

Never use vague names like "small" without a pixel value.

### Step 3: Document layout per breakpoint

For each breakpoint, record: column count and gutter width; container max-width and horizontal padding; which elements stack, collapse, or disappear; and any ordering changes (e.g. "CTA moves above image on mobile") - flag when visual order diverges from DOM order, because that has accessibility consequences. A table with breakpoints as rows and layout properties as columns works well.

### Step 4: Decide fluid versus stepped, per element

Apply these decision rules instead of choosing by feel:

- Structural properties - column count, element order, show/hide, navigation pattern - are always stepped: they change at a breakpoint, never continuously.
- Continuous properties - type size, spacing, media height, container width - go fluid (percentage widths, clamp(), viewport units) when they would otherwise need three or more discrete values across the range; keep them stepped when one or two values suffice.
- Touch-target sizes are never fluid.
- Images: specify object-fit (cover vs. contain), aspect ratio at each breakpoint, and whether the focal point shifts.

### Step 5: Set content priority

On mobile, not everything fits. Document: which elements hide below a breakpoint and the rationale (progressive disclosure, never arbitrary omission); which elements change behavior instead of disappearing (e.g. "horizontal tabs become a select dropdown below 768px"); mobile-only truncation rules; and the navigation pattern swap - desktop nav bar to hamburger or bottom tab bar - with the exact breakpoint where it happens.

### Step 6: Specify touch targets and gestures

- Minimum 44x44px for any tappable element; if the visual size is smaller, add invisible padding.
- Minimum 8px between adjacent touch targets to prevent mis-taps.
- Swipe gestures: direction, threshold, triggered action, and conflicts with system gestures (e.g. iOS back swipe).

### Step 7: Verify edge cases

- Landscape phone orientation: state whether it is treated as mobile or tablet.
- Very long strings in labels and headings: define the overflow rule so the layout cannot break.
- 320px: confirm the spec remains functional at the minimum supported width - check it explicitly, not by assumption.

## Worked example

```
RESPONSIVE SPEC - Pricing page

Breakpoint  Range        Cols  Gutter  Container          Nav
mobile      0-767px      4     16px    fluid, 16px pad    Hamburger
tablet      768-1023px   8     24px    fluid, 24px pad    Hamburger
desktop     1024-1439px  12    24px    960px max          Horizontal bar
wide        1440px+      12    32px    1200px max         Horizontal bar

Element rules
- Pricing cards: 3-across desktop+, 2-across tablet, stacked mobile.
  Recommended plan moves first on mobile (visual order != DOM order - noted).
- Hero heading: fluid, clamp(32px, 5vw, 61px).  [3+ values needed -> fluid]
- CTA button: 240px fixed on desktop+; full-width mobile.  [2 values -> stepped]
- Comparison table: horizontal scroll below 768px, first column sticky.
- FAQ accordion: unchanged at all widths (no rule needed).

Touch: all targets >=44x44px with >=8px gaps, verified at 375px and 320px.
Landscape phones: treated as mobile. Long plan names: truncate at 2 lines.
```

## Deliverable

Produce a responsive specification containing: the breakpoint table with pixel values, the per-breakpoint layout table, a fluid-vs-stepped ruling for every significant element, content-priority and navigation-swap rules with rationale, touch-target confirmation, and the edge-case rulings (landscape, long strings, 320px).

## Do NOT

- Do not deliver only static frames per breakpoint - the widths between frames are where products break.
- Do not invent breakpoints when the codebase already has them; two competing systems guarantee drift.
- Do not hide content on mobile without stating why; arbitrary omission is how mobile users lose features silently.
- Do not make everything fluid. Fluid structural layout (columns, ordering) is unimplementable; fluid belongs to continuous properties only.
- Do not skip the 320px check because "nobody uses those phones" - small viewports also appear in split-screen and embedded webviews.

## Quality bar

- Every breakpoint has a pixel range; every significant element has an explicit fluid-or-stepped ruling.
- Every hidden element has a rationale; every behavior swap names its trigger breakpoint.
- Touch targets meet 44x44px with 8px separation on all touch viewports.
- The layout is verified functional at 320px.

Pair with redline-annotation for within-frame measurements and design-handoff-doc for the surrounding handoff package.
