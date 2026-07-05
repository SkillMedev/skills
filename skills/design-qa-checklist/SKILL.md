---
name: Design QA Checklist
description: Runs an ordered design QA pass over an implemented UI — layout, type, color, states, motion, content — against the design spec, with concrete tolerances and a filed defect list. Use when someone asks "QA this build against the designs", "does the implementation match Figma", "is this ready for design sign-off", or before releasing a UI change. Do NOT use for critiquing the design itself — use design-critique instead; for a full WCAG conformance audit, use accessibility-audit; for writing the spec the build is checked against, use design-handoff-doc.
---

# Design QA Checklist

Design QA catches drift between design intent and shipped code before it compounds into a product that looks 90% right everywhere and fully right nowhere. The costly failure is an unordered spot-check: motion gets polished while the grid underneath is wrong, so half the findings are invalidated by the layout fix. Run the pass in order — layout first, because layout errors move everything downstream — and file every deviation with expected vs. actual values, not adjectives.

## Operating procedure

### Step 1: Gather inputs

- The design source of truth (Figma file, spec, or design-handoff-doc output) and the build URL or branch.
- The token set (spacing scale, type scale, color tokens, motion tokens). If none exists, note that and QA against explicit spec values; flag "no token system" as its own finding.
- Supported breakpoints and the narrowest supported viewport (default: 320px).
- Whether dark mode and motion specs exist (default: assume light mode only, no motion spec — label as assumption).

### Step 2: Layout pass

- Every margin, padding, and gap matches its token or explicit spec value (space-4 = 16px, not 15px or 18px). **Tolerance: any deviation greater than 2px is a defect.** Deviations of 2px or less at fractional zoom may be sub-pixel rendering — re-measure at 100% zoom and 1x density before filing.
- Grid columns, gutters, and container max-widths correct at every breakpoint.
- Alignment verified with a grid overlay or devtools ruler, not eyeballing.
- Flag every magic-number hardcoded value that should reference a token.
- Resize from 320px to the widest supported viewport: zero horizontal overflow anywhere.

### Step 3: Typography pass

- Font family, weight, size, line-height, and letter-spacing match spec exactly — type values are tokens, so the tolerance is zero, not 2px.
- No system fallback fonts rendering in place of the intended typeface (check computed styles, not appearance).
- Truncation behavior on long strings matches design: ellipsize, wrap, or clip as specified. Test with a 3x-length string.
- Heading hierarchy semantically correct (H1 > H2 > H3) even where visual sizes differ.

### Step 4: Color pass

- Every color references a design token, not a raw hex. Flag raw hex values in CSS or Tailwind arbitrary values.
- Semantic tokens used correctly: surface-danger on error states, not red-500.
- Contrast meets WCAG AA: 4.5:1 for body text, 3:1 for large text and UI components. Measure with a contrast checker; do not estimate.
- Both light and dark modes checked if supported — dark mode defects are as blocking as light.

### Step 5: States pass

- Hover, focus, active, disabled, and loading states all implemented and matching spec. **Tolerance: a missing state is a blocking defect, not a nice-to-have** — a button with no focus state fails keyboard users, and a missing loading state reads as a hang.
- Focus rings visible and styled; never outline: none without an equivalent replacement.
- Disabled controls genuinely non-interactive (not just visually muted) and excluded from tab order.
- Empty and error states of data-driven components render as designed, not as blank space or raw error text.

### Step 6: Motion pass

- Transitions match specified duration and easing from the motion spec. Tolerance: duration deviation over 10% or a wrong easing curve is a defect; if no motion spec exists, flag inconsistent durations across similar interactions.
- Animations respect prefers-reduced-motion.
- No layout shift during enter/exit animations.

### Step 7: Content and platform pass

- Copy matches approved content: no lorem ipsum, no placeholder labels, no double spaces or straight quotes where curly are specced.
- Touch targets at least 44x44px on mobile — buttons, links, icon controls.
- Images sharp at 1x, 1.5x, and 2x pixel density; alt text descriptive, decorative images alt="".
- Form inputs have visible associated labels, not placeholder-only.
- Automated accessibility pass (axe or Lighthouse) run; all violations reviewed before sign-off. Deep conformance work routes to accessibility-audit.

### Step 8: File the defect log

Every finding gets: location (file path, component name, or URL + selector), expected value with source, actual value as measured, severity. Severity rubric: **Blocker** = missing state, broken layout, contrast failure, overflow; **Major** = wrong token, spacing off by more than 2px, wrong truncation; **Minor** = sub-token polish visible only side-by-side.

## Worked defect entries

```
Bad:  "Card padding looks off on mobile" — no location, no values, unactionable.

Good: [Major] PricingCard (components/PricingCard.tsx) — padding
      Expected: space-6 (24px) per Figma frame "Pricing / Card"
      Actual:   20px hardcoded
      Note: exceeds 2px tolerance; also a magic number — should reference token.

Good: [Blocker] Primary button — missing states
      Expected: hover, focus, disabled per spec p.4
      Actual:   hover only; outline: none on focus with no replacement
```

## Deliverable

Produce a defect log covering all seven passes in order, each entry with location, expected vs. actual, and severity — plus a sign-off verdict: ship, ship with listed minors, or blocked with the blocker list.

## Do NOT

- Do not QA out of order; motion and color findings on a broken layout get invalidated by the layout fix.
- Do not file findings as adjectives ("feels cramped") — every entry needs a measured actual and a sourced expected.
- Do not wave through missing states because the happy path looks right; states are where implementations quietly diverge most.
- Do not eyeball spacing or contrast; use the ruler and the contrast checker every time.
- Do not let this pass substitute for accessibility-audit — Step 7 is a baseline, not conformance.

## Quality bar

- All seven passes completed in order, none skipped silently — a skipped pass is declared with a reason.
- Every defect has location, expected (with source), actual (as measured), and severity.
- Zero unfiled deviations beyond tolerance: over 2px spacing, any missing state, any contrast failure.
- The 320px-to-max resize and the 1x/1.5x/2x density checks were actually performed.
- The verdict is one of ship / ship-with-minors / blocked, never "mostly fine".
