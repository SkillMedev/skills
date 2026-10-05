---
name: design-token-system
description: Build a three-tier token architecture (global, semantic, component) with theming.
metadata:
  title: "Design Token System"
---

# Design Token System

Use this skill to design a scalable token architecture that survives rebrands,
theming, and growth - instead of hard-coded values sprinkled through the codebase.

## The three tiers

### Tier 1: Global (primitive) tokens

Raw values with no meaning attached. The full palette of options.

```js
const global = {
  blue500: '#3B82F6',
  gray900: '#111827',
  space4: '16px',
  fontSize3: '18px',
};
```

Rules: name by value, never by use. `blue500`, not `primary`. Components never
reference these directly.

### Tier 2: Semantic (alias) tokens

Assign meaning by pointing at globals. This is where theming lives.

```js
const semantic = {
  colorTextPrimary: global.gray900,
  colorActionDefault: global.blue500,
  colorSurfaceBase: global.white,
  spaceInline: global.space4,
};
```

Rules: name by intent (`colorActionDefault`), not appearance (`colorBlue`). Most
components consume this tier.

### Tier 3: Component tokens

Optional, for components that need fine control. Point at semantic tokens.

```js
const button = {
  buttonBackground: semantic.colorActionDefault,
  buttonText: semantic.colorTextOnAction,
  buttonPaddingX: semantic.spaceInline,
};
```

## Why three tiers

- A rebrand changes **global** values; semantics and components follow automatically.
- A theme (dark mode) re-points **semantic** tokens to different globals.
- A one-off component tweak touches only its **component** tokens, not the system.

## Theming with token sets

Define one semantic set per theme that re-points to the same global palette:

```js
const semanticDark = {
  colorTextPrimary: global.gray050,
  colorSurfaceBase: global.gray900,
  colorActionDefault: global.blue400, // lightened for dark surfaces
};
```

Components reference `colorSurfaceBase`; switching the active semantic set flips
the whole theme with zero component changes.

## Naming convention

Adopt a predictable structure: `category-property-variant-state`.

- `color-text-primary`
- `color-action-default-hover`
- `space-inline-sm`

Keep it consistent and machine-parseable so tooling can generate platform outputs.

## Token categories to cover

Color, spacing, typography (family, size, weight, line-height, letter-spacing),
radii, borders, shadows, z-index, motion (duration, easing), and breakpoints.

## Tooling

- Store source tokens as JSON (W3C DTCG format) so they are platform-agnostic.
- Use Style Dictionary or similar to transform into CSS variables, JS, iOS, Android.
- Validate references resolve and no component points directly at a global.

## Guardrails

- No raw hex or px in component code - everything resolves through tokens.
- Every semantic token must have a value in every theme.
- Deprecate, don't delete: keep an alias when renaming so consumers migrate gradually.

## Output

Deliver the three-tier JSON source, a light and dark semantic set, the naming
convention doc, and the build config that emits platform-specific token files.
