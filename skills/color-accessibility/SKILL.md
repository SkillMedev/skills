---
name: Color Accessibility
description: Audits and repairs color palettes against WCAG contrast thresholds - 4.5:1 for body text, 3:1 for large text and UI components, 7:1 for AAA - and makes them safe for color-blind users, delivering a verified pairing table. Use when someone asks "does this color pass contrast", "make my brand color accessible", "is this palette color-blind safe", "why did my contrast check fail", or is remediating color findings from an audit. Do NOT use to invent a brand palette from scratch - use color-palette-builder instead; for a full accessibility review beyond color, use accessibility-audit.
---

# Color Accessibility

A palette that fails contrast ships a product that a measurable slice of users cannot read: low-vision users lose the text, and roughly 8% of men (and about 0.5% of women) with red-green color blindness lose any meaning carried by hue alone. The costly mistake this skill prevents is approving colors that look fine on a designer's bright monitor and fail on real screens, in sunlight, and in audits - forcing a token-system rework after components already reference the broken values.

## Operating procedure

Work the steps in order: pairings must be enumerated before ratios are computed, and ratios must pass before color-blindness simulation, because a palette that fails contrast is not worth simulating.

### Step 1: Gather inputs

Collect before starting. Label any assumed value as a guess.

1. Brand and functional colors as hex values (primary, semantic red/green/yellow, neutrals).
2. Every background surface they sit on - including dark mode surfaces if one exists.
3. Target conformance: default to WCAG AA. Use AAA (7:1 body, 4.5:1 large) for reading-heavy products, older audiences, or contractual/regulated requirements.
4. Whether text ever sits over images or gradients.
5. Existing token names, so fixes land as token updates, not one-off overrides.

### Step 2: Enumerate every real pairing

Contrast is a property of a pair, never of a color. List every combination actually rendered: body text on each surface, large text, button labels on button fills, borders and icons against adjacent colors, focus rings against both the component and the page background, hover and pressed states, and text over the darkest and lightest regions of any image.

### Step 3: Compute ratios against the thresholds

- Body text (under 24px, or under 19px bold): **4.5:1** minimum (AA). AAA: **7:1**.
- Large text (at least 24px, or at least 19px bold): **3:1** (AA). AAA: **4.5:1**.
- UI components, icons, and focus indicators: **3:1** against adjacent colors.
- Test the actual pairing as rendered - including text over images and hover states, not just the token sheet.

Run the calculator below on every pair from Step 2 and record pass/fail per threshold.

### Step 4: Fix failures by adjusting lightness, not hue

Contrast ratio is computed from relative luminance, which lightness moves and hue barely does - so darkening or lightening preserves brand identity while fixing the number. If the brand color cannot reach 4.5:1 on its main surface without becoming unrecognizable, keep it for large text and UI components (3:1) and mint a darker "text-safe" variant of the same hue for body copy.

### Step 5: Remove color-only meaning

Color must never be the sole carrier of information. Concrete alternatives:

- Error states: red **plus** an icon **plus** text ("Email is required"), never a red border alone.
- Links in body text: underline or another non-color cue, not blue alone.
- Charts: differentiate series by shape, position, pattern, or direct labels - not hue alone.
- Status dots: pair with a text label or distinct icon per status.

### Step 6: Simulate color blindness

Check the palette under deuteranopia, protanopia, and tritanopia simulation. Red/green must never be the only distinction between two meanings - prefer blue/orange when two hues must contrast. If two adjacent chart series or statuses become indistinguishable in simulation, apply a Step 5 alternative.

### Step 7: Re-check dark mode

Dark mode is a separate set of pairings, not an inversion. Verify every pair again on dark surfaces, avoid pure white text on pure black (halation for astigmatic users - slightly desaturated off-white on a dark gray surface reads better), and confirm focus rings still clear 3:1 on the dark background.

Finally, verify focus indicators meet 3:1 and check disabled states: WCAG exempts disabled controls from contrast minimums, but any disabled text the user must still read (a pre-filled locked value) should stay legible.

## Contrast calculator

Self-contained Node script, no dependencies. Save as `contrast.js`, edit the `pairs` array, run `node contrast.js`.

```javascript
// WCAG 2.x contrast ratio checker. Edit pairs, then: node contrast.js
const pairs = [
  ['#1F1F1F', '#FFFFFF', 'body text / surface'],
  ['#5F6368', '#FFFFFF', 'secondary text / surface'],
  ['#0057B7', '#FFFFFF', 'link text / surface'],
  ['#949494', '#FFFFFF', 'border / surface'],
  ['#999999', '#FFFFFF', 'BAD caption gray'],
  ['#666666', '#FFFFFF', 'GOOD caption gray'],
]

const lin = (c) => {
  const s = c / 255
  return s <= 0.04045 ? s / 12.92 : Math.pow((s + 0.055) / 1.055, 2.4)
}
const luminance = (hex) => {
  const n = parseInt(hex.slice(1), 16)
  return 0.2126 * lin(n >> 16) + 0.7152 * lin((n >> 8) & 255) + 0.0722 * lin(n & 255)
}
const ratio = (a, b) => {
  const [hi, lo] = [luminance(a), luminance(b)].sort((x, y) => y - x)
  return (hi + 0.05) / (lo + 0.05)
}

for (const [fg, bg, label] of pairs) {
  const r = ratio(fg, bg)
  const verdict = r >= 7 ? 'AAA' : r >= 4.5 ? 'AA body' : r >= 3 ? 'large/UI only' : 'FAIL'
  console.log(`${fg} on ${bg}  ${r.toFixed(2)}:1  ${verdict}  (${label})`)
}
```

### Worked palette output

```
#1F1F1F on #FFFFFF  16.48:1  AAA  (body text / surface)
#5F6368 on #FFFFFF  6.05:1  AA body  (secondary text / surface)
#0057B7 on #FFFFFF  6.89:1  AA body  (link text / surface)
#949494 on #FFFFFF  3.03:1  large/UI only  (border / surface)
#999999 on #FFFFFF  2.85:1  FAIL  (BAD caption gray)
#666666 on #FFFFFF  5.74:1  AA body  (GOOD caption gray)
```

Read it: primary and secondary text clear 4.5:1 with room; the link blue passes body text so no underline-only compromise is forced; the border gray at 3.03:1 is legal for UI components but must never carry text. The fail/pass pair shows the fix pattern - same neutral hue, lightness dropped from #999999 (2.85:1, fails everything) to #666666 (5.74:1, passes AA body).

## Deliverable

Produce a contrast report containing: the full pairing table with computed ratios and pass/fail per threshold, the corrected token values (before → after hex), the list of color-only meanings found and the non-color cue added for each, color-blindness simulation notes, and the dark-mode pairing table.

## Do NOT

- Do not check tokens in isolation - a color has no contrast ratio; only a pair does, and the failure hides in the pairing you skipped.
- Do not fix failures by nudging hue - hue barely moves luminance, so the ratio stays broken while the brand drifts.
- Do not rely on the AA minimum for thin or light font weights at small sizes - a 4.5:1 hairline font is legal and still hard to read; add margin.
- Do not treat dark mode as an automatic inversion - it has its own pairings and its own failures.
- Do not use red/green as the only distinction between two meanings - 8% of men cannot separate them.

## Quality bar

- Every rendered pairing appears in the table with a computed ratio - no "looks fine" entries.
- Every body-text pair is at or above 4.5:1 (7:1 if AAA was the target); every UI component and focus indicator at or above 3:1.
- Every meaning carried by color has a named non-color cue.
- The palette survives deuteranopia, protanopia, and tritanopia simulation with all distinctions intact.
- Fixes are expressed as token updates, not per-component overrides.

## Escalation

When conformance is contractual or regulated (government procurement, healthcare, education), pass results to a qualified accessibility specialist for formal conformance claims - this skill produces engineering evidence, not a legal audit. For accessibility issues beyond color (semantics, keyboard, screen readers), route to accessibility-audit. To design the palette itself before auditing it, pair with color-palette-builder.
