---
name: dark-mode-design
description: Build a dark theme with correct elevation, surface hierarchy, and contrast.
metadata:
  title: "Dark Mode Design"
---

# Dark Mode Design

Use this skill to design a dark theme that is more than an inverted light theme.
Dark mode has its own rules for depth, color, and contrast.

## Core mistakes to avoid

- **Pure black backgrounds** (`#000`). Use a dark gray (e.g. `#121212`) so shadows and
  elevation are visible and OLED smearing is reduced.
- **Pure white text** (`#FFF`) on dark. Use ~87% opacity white for body text to cut
  harsh contrast and eye strain.
- **Saturated colors** carried over from light mode. They vibrate against dark
  backgrounds - desaturate and lighten brand colors for dark surfaces.

## Step 1: Establish surface elevation

In dark mode, higher elevation = lighter surface (light "falls" from above), not
bigger shadows. Define an elevation scale:

- Level 0 (base): `#121212`
- Level 1 (card): `#1E1E1E`
- Level 2 (raised): `#242424`
- Level 3 (dialog): `#2C2C2C`
- Level 4 (menu/popover): `#333333`

Each step adds a subtle white overlay (~5% per level). Shadows still help but carry
less weight than in light mode.

## Step 2: Text opacity hierarchy

Layer text with opacity rather than separate hex colors:

- High emphasis (headings, body): 87% white
- Medium emphasis (secondary, labels): 60% white
- Disabled: 38% white

## Step 3: Color adaptation

For each brand and semantic color, create a dark variant:

- Lower saturation by ~15-25%.
- Raise lightness so it reads on dark surfaces.
- Verify contrast against the surface it sits on, not against pure black.

## Step 4: Contrast targets

- Body text: at least 4.5:1 against its surface (WCAG AA).
- Large text and UI components: at least 3:1.
- Don't over-shoot to maximum contrast everywhere - that recreates the eye strain
  you came to dark mode to avoid. Aim for comfortable, not blinding.

## Step 5: Elevation + color interaction

When a colored surface is elevated, blend it toward the lighter elevation tone so it
still reads as "higher" without losing its hue.

## Step 6: Tokenize for theming

Define semantic tokens that swap by theme:

```js
const surface = {
  light: { base: '#FFFFFF', raised: '#F5F5F5' },
  dark:  { base: '#121212', raised: '#1E1E1E' },
};
```

Components reference `surface.base`, never a raw hex, so a single switch flips themes.

## Step 7: Test in context

- Check images and illustrations - add a subtle border or dim overlay if they glow.
- Verify shadows are still perceptible.
- Test in a dark room and a bright room; comfort changes with ambient light.

## Output

Deliver the elevation scale, text opacity tiers, adapted color set with contrast
checks, and the semantic token map for both themes.
