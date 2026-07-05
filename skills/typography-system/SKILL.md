---
name: Typography System
description: Chooses and pairs typefaces and builds a modular type scale — ratio, named roles, line heights, measure limits, and responsive values — as a complete design-system type layer. Use when someone asks "what fonts should we use", "build our type scale", "why does our typography feel inconsistent", or is establishing the typographic voice for a brand or product. Do NOT use for animated or motion typography — use kinetic-typography instead; for page-level emphasis, spacing, and layout decisions, use visual-hierarchy; for assembling the full brand book, use brand-guidelines.
---

# Typography System

Typography carries more of a brand's personality than any other design element because it is always present — and a good type system is invisible when it works, painful when it doesn't. The costly mistake is picking fonts before defining roles: teams end up with three typefaces, forty ad-hoc sizes, and no way to tell a heading from a label. Roles and a scale come first; fonts fill them.

## Operating procedure

Follow the order: roles constrain the font choice, the ratio constrains the scale, and line height and measure are set per style — reversing any of these produces a system that must be rebuilt.

### Step 1: Gather inputs

- Brand adjectives (3-5) and the product type: UI-heavy tool, editorial/content site, or display-heavy marketing. The product type picks the scale ratio later.
- Target languages and scripts — glyph coverage eliminates typefaces early.
- Platforms: web, native app, print. Each changes licensing and delivery constraints.
- Font budget and any existing fonts under license. Label assumptions as assumptions.

### Step 2: Define roles before choosing fonts

Roles: Display (hero headlines, campaign type), Heading (page titles, section headers), Body (long-form reading), UI (labels, captions, buttons), Mono (code, data). A brand needs two typefaces at most — one covering Display/Heading, one covering Body/UI. A third is a tax on every future designer. Strong pairings: one serif plus one sans-serif, or a distinctive display face with a neutral workhorse.

### Step 3: Evaluate candidates on functional criteria

Beyond personality, a candidate must pass: weight range of at least Regular, Medium, Bold plus italics; legibility at small sizes (check 12-13px on a real screen); glyph and language coverage for target markets; web and app licensing; acceptable file size for web delivery. Prefer variable fonts for web — fewer requests and fine-grained weight control.

### Step 4: Pick the scale ratio and build the scale

Use a modular scale from a 16px (1rem) base. Pick the ratio by product type:

- 1.2 (Minor Third) — dense, data-heavy UIs where sizes must stay close together.
- 1.25 (Major Third) — the default for UI-heavy products.
- 1.333 (Perfect Fourth) — editorial products that need clear hierarchy in long reading.
- 1.5 (Perfect Fifth) — display-heavy marketing layouts.

Name steps by role, never pixel size: body-sm, body-base, heading-md, display-lg. Pixel values change; role names stay stable.

### Step 5: Set line height and measure per style

These are readability standards, not aesthetics:

- Body: line height 1.4-1.6 (default 1.5); measure 45-75 characters, 60-75 ideal. Enforce measure with a max-width — it is the most neglected spec, and overlong lines destroy comprehension.
- Headings: line height 1.1-1.3.
- UI labels: line height 1.2-1.4; never set type below 12px.

### Step 6: Define responsive values

Display sizes drop hard on mobile: a 72px desktop hero commonly becomes 40px. Document a mobile value for every Display and Heading step. Body and UI usually hold one size across breakpoints with minor tweaks. Hand breakpoint mechanics to responsive-spec.

### Step 7: Document do/don't pairs and anti-patterns

For each role, write one sentence on what it is for and one on what it is not for, then list at least three anti-patterns: all-caps body text, centered body paragraphs, justified text without hyphenation, more than two weights in a single component.

## Worked example

```
TYPE SCALE — ratio 1.25 (Major Third), base 16px / 1rem
Product: UI-heavy SaaS dashboard. Fonts: Inter (all roles), JetBrains Mono (code)

Token        Size   Line-height  Weight  Mobile  Notes
display-lg   61px   1.1          700     40px    Hero only; max 2 lines
display-sm   49px   1.1          700     34px    Section heroes
heading-lg   39px   1.2          600     31px    Page titles
heading-md   31px   1.2          600     25px    Section headers
heading-sm   25px   1.3          600     20px    Card titles
body-lg      20px   1.5          400     20px    Intro paragraphs; 60-75ch max-width
body-base    16px   1.5          400     16px    Default body; 60-75ch max-width
body-sm      13px   1.4          400     13px    Captions, metadata
ui-label     13px   1.2          500     13px    Buttons, form labels; never <12px

Don't: all-caps body text; centered paragraphs; justified text without
hyphenation; mixing >2 weights in one component.
```

## Deliverable

Produce a type specification containing: the chosen typefaces with the functional-criteria rationale, the full scale table (token, size, line height, weight, mobile value), measure limits for every body style, do/don't sentences per role, and the anti-pattern list.

## Do NOT

- Do not choose fonts before roles — personality-first selection is how three-typeface systems happen.
- Do not add a third typeface; every future design decision pays for it.
- Do not name styles by pixel size; renaming ripples through every component when the scale shifts.
- Do not skip measure. Line height without a max-width still yields unreadable 120-character lines on wide screens.
- Do not ship a scale without mobile values for Display and Heading steps — engineers will improvise them.

## Quality bar

- Two typefaces maximum, each passing all five functional criteria.
- Every step has a role name, size, line height, weight, and mobile value.
- Body styles sit in the 1.4-1.6 line-height band with an enforced 45-75 character measure.
- The ratio matches the product type and is stated in the doc, so future additions extend the scale instead of inventing sizes.

Pair with color-palette-builder for the color layer, responsive-spec for breakpoint behavior, and brand-guidelines to fold the system into full identity documentation.
