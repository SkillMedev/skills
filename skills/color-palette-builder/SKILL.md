---
name: Color Palette Builder
description: Builds an accessible brand color palette — one anchor color, role assignments, 9-step tonal scales, 60-30-10 distribution, and documented usage rules — ready for a developer to implement without guessing. Use when someone asks "pick our brand colors", "build a color system for our app", "what shade should our buttons be", or is defining the color layer of a design system. Do NOT use for auditing or fixing contrast failures and color-blindness issues in an existing product — use color-accessibility instead; for assembling the full brand book around the palette, use brand-guidelines.
---

# Color Palette Builder

A brand color palette is not a mood board — it is a functional system with roles, scales, and rules. The costly failure is shipping pretty swatches with no usage rules: developers guess, screens drift, and within months the product has fourteen slightly different blues and body text that fails contrast. Build the palette so a developer can implement it without asking a single question.

## Operating procedure

Work the steps in order: roles before scales, scales before contrast checks — contrast is verified per color pair, and pairs only exist once the scales do.

### Step 1: Gather inputs

Collect these before generating anything; label any guess as a guess.

- Anchor color, if one exists (hex value). If none, collect 3-5 brand adjectives plus the category's color conventions to derive one.
- Surfaces in scope: marketing site, product UI, or both. Product UI needs a deeper neutral scale.
- Dark mode: in scope now, later, or never. Default to architecting semantic tokens regardless — it adds no cost now and prevents significant rework later.
- Accessibility target: default WCAG 2.1 AA. Ask whether AAA is required (government, healthcare, and education products often require it).
- Constraints: category semantics (red and green in finance already mean loss and gain), competitor colors to avoid, and any existing token naming convention to match.

### Step 2: Choose or validate the anchor

Every strong palette derives from a single anchor — the primary brand color. It must pass three tests: works on white, works on dark surfaces, and survives single-color reproduction (favicon, embroidery, one-color print). Derive the rest of the palette from the anchor rather than assembling unrelated colors; derivation is what keeps the system coherent.

### Step 3: Assign roles before names

Every color gets a role before it gets a name:

- Primary — main brand actions, links, focus rings.
- Secondary — supporting accents, hover states, data visualization.
- Neutral — text, borders, backgrounds. The most underestimated role: a well-stepped gray scale does more work than any accent color.
- Semantic — success green, warning amber, error red, info blue. Keep them visually distinct from Primary, or status and interactivity blur together.

### Step 4: Build tonal scales, not single swatches

For Primary and Secondary, generate a 9-step scale from near-white to near-black (100 through 900) with the anchor at 500. For Neutral, use 50 through 900 — UI work burns through subtle background steps fast. Step by perceptual lightness (manual HSL/OKLCH stepping, or generators like Radix Colors or Tailwind's palette tooling) and confirm adjacent steps are visually distinguishable on a real screen.

### Step 5: Apply the 60-30-10 distribution

On a typical screen, target roughly 60 percent neutral surfaces and backgrounds, 30 percent secondary and supporting color, 10 percent primary accent. When primary exceeds about 10 percent of the screen it stops functioning as an accent and interactive elements lose their signal. Use this as a review test against real screens, not as a paint-by-numbers rule.

### Step 6: Verify contrast per pair

Apply WCAG 2.1 AA minimums as non-negotiable: 4.5:1 for normal text, 3:1 for large text (24px+, or roughly 19px+ bold) and for UI components. Test Primary 600 and darker against white; test Primary 200 and lighter under dark text (Neutral 900). Aim for AAA (7:1) on body text wherever feasible. Document every passing pair explicitly — developers need the pairs, not the claim that contrast was checked.

### Step 7: Write usage rules per role

For each token band, state one sentence on what it is for and one on what it is not for. Example: "Primary 500 — use for interactive elements, primary buttons, and focus rings. Do not use as a background behind body text." Usage rules are the difference between a palette and a system.

### Step 8: Define the semantic token layer

Define semantic tokens (color-background-default, color-text-primary, color-border-subtle) that map to palette values per mode. Semantic tokens decouple component code from raw values and make dark mode a mapping exercise instead of a redesign.

## Worked example

```
PALETTE — "Meridian" (B2B analytics product)

Role       Token          Hex       Usage
Primary    primary-500    #2563EB   Buttons, links, active nav (5.2:1 on white)
           primary-600    #1D4ED8   Button hover (6.7:1 on white)
           primary-100    #DBEAFE   Selected-row background; dark text only
Secondary  secondary-500  #0D9488   Charts, tags, secondary CTAs
Neutral    neutral-900    #111827   Headings, body text (17:1 on white)
           neutral-600    #4B5563   Secondary text (7.6:1 on white — AAA)
           neutral-300    #D1D5DB   Borders, dividers (decorative)
           neutral-50     #F9FAFB   Page background
Semantic   success-600    #16A34A   Icons/large text only (3.3:1 — fails body text)
           error-600      #DC2626   Error text on white (4.8:1)

Distribution check (dashboard screen): neutral surfaces ~60%,
secondary/data color ~30%, primary accent ~10%.
Passing text pairs: neutral-900 and neutral-600 on white and neutral-50;
white on primary-500 and darker; neutral-900 on primary-100.
```

## Deliverable

Produce a palette specification containing: the full token table (role, token, hex, usage rule), the explicit list of passing contrast pairs with measured ratios, a 60-30-10 distribution check against at least one real screen, and the semantic token layer with per-mode mappings when dark mode is in scope or plausible.

## Do NOT

- Do not deliver swatches without usage rules — an undocumented palette is decoration, and drift is guaranteed.
- Do not assemble unrelated colors by taste; derive from the anchor or coherence dies at the first new component.
- Do not skimp on neutrals; the gray scale carries most of the interface.
- Do not let semantic colors collide with Primary — a blue primary beside a blue info color destroys both signals.
- Do not claim contrast compliance without listing the measured pairs.
- Do not attempt color-blindness simulation or remediation of an existing failing UI here — route that to color-accessibility.

## Quality bar

- Every token has a role, a value, and at least one usage rule.
- Every text-bearing combination is listed with its measured ratio; nothing under 4.5:1 is documented for body text.
- The anchor passes the white, dark-surface, and single-color reproduction tests.
- A developer could implement the entire system from the document alone, with zero follow-up questions.

For deep accessibility work (simulation, non-text contrast audits, remediation), hand off to color-accessibility. Pair with typography-system for the type layer and brand-guidelines for full identity documentation.
