---
name: Icon System
description: Designs or audits an icon set so every glyph shares one grid, stroke weight, corner language, naming convention, and export pipeline, and delivers the written icon spec engineering builds against. Use when someone asks "our icons look inconsistent", "set up an icon grid", "how should we name icons", "audit this icon set", "prep our SVGs for handoff", or is commissioning new icons and needs the rules first. Do NOT use for a single logo mark — use logo-brief-writer instead; for delivering full design specs beyond icons, use design-handoff-doc.
---

# Icon System

An icon set without shared rules decays one icon at a time: each new glyph is drawn against a slightly different grid, stroke, or metaphor, and within a year the product carries three visual dialects that no single redraw can fix cheaply. This skill produces the spec that makes every icon — including ones drawn by future contributors — feel like one family, and the audit checklist that catches drift before it ships.

## Operating procedure

Follow the order: the grid constrains the stroke, the stroke constrains the drawing rules, and naming/export only matter once the drawing rules are fixed. Changing the grid after 200 icons exist means redrawing 200 icons.

### Step 1: Gather inputs

Collect before drawing anything. Label assumptions as guesses.

1. Rendering sizes in product (commonly 16, 20, 24px). The smallest real size drives detail decisions.
2. Style direction: outline, filled, or both. If both, they are two coordinated sets, not one.
3. Existing icons to keep — an audit target — or a greenfield set.
4. Platform constraints (web `currentColor` inheritance, iOS/Android asset pipelines).
5. Who will draw future icons — the spec must be executable by someone who was not in the room.

### Step 2: Define the grid and keylines

Pick a base canvas, standard is **24x24px**. Inside it define:

- **Live area** — 20x20, where the icon body sits.
- **Padding** — the 2px margin on all sides; nothing crosses it except deliberate optical overshoot.
- **Keylines** — reference shapes that anchor optical size: a 20x20 square, a 22px-diameter circle, a 22x16 horizontal rectangle, and a 16x22 vertical rectangle. A circle drawn to the square keyline looks smaller than the square; the oversized circle keyline compensates.

### Step 3: Fix the stroke and corner language

- One stroke weight for the whole set: **2px at 24px** is the common default; 1.5px reads lighter and more editorial. Never mix weights — mixed weights are the single fastest way to break cohesion.
- Standardize caps and joins (round vs butt caps) and one corner radius for stroke endpoints.
- Decide interior corner radius (commonly 0.5–1px at 24px) and apply it everywhere.

### Step 4: Set drawing rules

- Align edges to the pixel grid at the base size; off-pixel edges render blurry.
- Optical, not mathematical, sizing: circles slightly overshoot the live area to match squares; triangles and diagonals are optically centered, not geometrically centered. Test glyphs side by side at target size and nudge until none looks heavier.
- One canonical form per recurring metaphor — a "plus" is always the same plus, reused, never redrawn.
- Pick one detail level and hold it; a detailed glyph next to a minimal one reads as two sets.
- If filled variants exist, define the outline→filled transformation rule once (which strokes become fills, which counters stay open).

### Step 5: Name by meaning

Names describe function, not appearance, so a visual redesign never forces renames:

- `delete` not `trash-can`; `settings` not `gear`.
- Pattern: `name-variant` with optional category prefix — `arrow-left`, `arrow-left-filled`.
- Lowercase, hyphenated, singular.
- Maintain an alias table mapping synonyms to canonical names (`remove` → `delete`, `preferences` → `settings`) so searches succeed.

### Step 6: Define the export pipeline

- Export SVGs with a uniform `viewBox="0 0 24 24"` and no width/height attributes.
- Use `fill="currentColor"` (or stroke) wherever color should inherit; no hard-coded brand hex values in the file.
- Strip editor metadata and run every file through an SVG optimizer (SVGO or equivalent); a clean icon is typically under 1KB.
- Flatten strokes to outlines only if the platform requires it — outlined strokes cannot be re-weighted later, so keep stroke sources.

### Step 7: Redraw for small sizes

Never scale 24px art down to 16px — it goes muddy. For 16px, redraw with reduced detail, simplified counters, and a stroke that stays near 1.5–2px rendered. Ship size variants as separate optically-tuned files (`icon-name-16`, `icon-name-24`).

## Worked icon spec (copy and fill)

```
ICON SYSTEM SPEC — [FILL: product name]

GRID
  Canvas: 24x24px    Live area: 20x20    Padding: 2px
  Keylines: square 20x20, circle Ø22, h-rect 22x16, v-rect 16x22

STROKE
  Weight: 2px (never mixed)   Caps: round   Joins: round
  Corner radius: 1px interior, round endpoints

DRAWING RULES
  Pixel-aligned edges at 24px. Optical centering for diagonals.
  Circles overshoot live area to Ø22. One detail level: [FILL: minimal / moderate]
  Canonical metaphors: plus, chevron, arrow drawn once, reused everywhere.

NAMING
  Pattern: name-variant (lowercase-hyphenated-singular)
  Examples: arrow-left, arrow-left-filled, delete, settings
  Aliases: remove→delete, preferences→settings, [FILL: more]

EXPORT
  viewBox="0 0 24 24", no width/height, fill="currentColor"
  SVGO-optimized, no editor metadata, <1KB target
  16px variants redrawn, suffixed -16

SIZES SHIPPED: [FILL: 16 / 20 / 24]
OWNER OF FUTURE ICONS: [FILL]
```

## Consistency audit checklist

Run against an existing set; every "no" is a defect to log:

- Same canvas and live area on every file? Same stroke weight measured, not assumed?
- Caps, joins, and corner radii uniform? Detail level uniform?
- Circles/squares/rectangles optically equal at target size when placed side by side?
- Edges pixel-aligned at base size? Any glyph blurry at 100% zoom?
- Names follow the pattern, meaning-based, no orphan synonyms outside the alias table?
- SVGs share one viewBox, inherit color, pass the optimizer with no diff?
- Small sizes redrawn rather than scaled?

## Deliverable

Produce an icon system spec containing: the grid and keyline definitions, stroke and corner rules, drawing rules with optical-sizing notes, the naming convention plus alias table, the export pipeline checklist, and — for audits — the defect list from the checklist with a fix priority (grid violations first, naming last, because grid fixes require redraws while renames are mechanical).

## Do NOT

- Do not mix stroke weights or detail levels — either one instantly reads as two icon sets.
- Do not center diagonal forms geometrically — they will look off-balance; center optically.
- Do not scale 24px icons to 16px — redraw them; scaled icons render muddy.
- Do not name icons after their appearance — `gear` breaks the day the settings icon becomes sliders.
- Do not hard-code fills in exported SVGs where the product needs color inheritance — every theme change becomes a re-export.
- Do not change the grid after the set is large without budgeting a full redraw; there is no partial migration.

## Quality bar

- A designer who never saw the set can draw a conforming new icon from the spec alone, without asking a question.
- All keyline shapes look the same size side by side at the smallest shipped size.
- Every file passes the audit checklist clean.
- Icon color changes in product via CSS alone, with no asset re-export.

Pair with design-handoff-doc when the spec ships to engineering alongside other design deliverables, and with design-qa-checklist to verify implemented icons match the spec post-build.
