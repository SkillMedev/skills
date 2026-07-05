---
name: Print Layout
description: Prepares a design for commercial print - document setup with correct bleed and safe zones, CMYK color management, print typography, image resolution, and a full prepress checklist through PDF/X export and proofing. Use when someone asks "get this file print-ready", "what bleed do I need", "why did my printer reject this PDF", "will these colors print right", or is sending business cards, brochures, posters, or packaging to a print shop. Do NOT use for on-screen design deliverables or developer handoff - use design-handoff-doc instead.
---

# Print Layout

Print failures are paid for twice: once for the ruined run, once for the reprint. The classic disasters - content cut off at the trim, colors that shifted from screen to press, files rejected at prepress - are all preventable with numbers known before the file is built. This skill takes a design to print without those surprises.

## Inputs to collect

1. **Trim size** - the final cut dimensions. No default; this must come from the user or the printer's spec sheet.
2. **Printer's specifications** - ask for the spec sheet or at minimum: required PDF standard, ICC profile, ink limit, and bleed requirement. If unavailable, use the defaults below and label the file "pending printer confirmation".
3. **Print method** - offset, digital, or large-format. This sets resolution and color decisions.
4. **Color-critical?** - brand colors or product photography that must match. If yes, plan a hard proof from the start.

## Operating procedure

Follow the steps in order - color and type decisions depend on the document setup, and the checklist only means something after everything upstream is done.

### Step 1: Set up the document

- **Trim size** - the final cut dimensions of the piece.
- **Bleed** - extend any artwork that touches the edge by 3mm (0.125in) past trim, so a slightly off cut never reveals white paper.
- **Safe zone** - keep all critical content (text, logos) at least 3-5mm inside the trim to survive cutting tolerance.
- **Resolution** - design at 300 DPI for the final print size; 150 DPI may suffice for large-format viewed from a distance (roughly beyond arm's length).

### Step 2: Manage color

- Work in **CMYK** for offset and digital print, not RGB - screen colors won't all reproduce on press. Convert and review before delivery; expect vivid blues, greens, and oranges to dull, and check them individually after conversion.
- Assign the correct **ICC profile** for the press and paper - ask the printer (e.g., US Web Coated SWOP for US web offset, FOGRA39 for European coated stock).
- Use **spot/Pantone** colors only when specified; each costs an extra plate.
- **Rich black** for large dark areas (e.g., C60 M40 Y40 K100); **pure K100** for small text and thin rules, so misregistration cannot blur them.

### Step 3: Set type for print

- Embed or outline all fonts so the printer's system never substitutes them.
- Minimum text size around 6pt; fine text prints on a single plate (K only) to avoid registration blur.
- No hairline rules thinner than 0.25pt - they may not print reliably.

### Step 4: Check images and links

- All raster images at 300 DPI **at placed size**, not source size - a 300 DPI image scaled to 200% is effectively 150 DPI.
- All links embedded or packaged; never deliver a file with missing links.
- Total ink coverage under the press limit (commonly ~300%) to prevent smearing and slow drying.

### Step 5: Run the prepress checklist

Copy and complete before export:

```
PREPRESS CHECKLIST - [FILL: job name] / [FILL: printer]
Trim size:                        [FILL]           Verified: [ ]
Bleed 3mm/0.125in on all edge-touching elements:   [ ]
No critical content outside safe zone (3-5mm):     [ ]
Color mode CMYK, RGB elements converted:           [ ]
ICC profile attached:             [FILL: profile]  [ ]
Rich black on large areas / K100 on small text:    [ ]
Fonts embedded or outlined:                        [ ]
Images 300 DPI at placed size:                     [ ]
No missing links:                                  [ ]
Total ink under limit:            [FILL: e.g. 300%] [ ]
Overprint/knockout reviewed (no white set to overprint): [ ]
Crop marks and bleed marks on export:              [ ]
Export standard:                  [FILL: PDF/X-1a or PDF/X-4]
Proof plan:                       [FILL: hard / soft]
```

Watch overprint especially: white text accidentally set to overprint vanishes on press and no soft proof will show it unless overprint preview is on.

### Step 6: Export

- Export to **PDF/X-1a** or **PDF/X-4** - confirm which the printer wants (X-1a flattens and forces CMYK; X-4 preserves transparency and layers).
- Include bleed and trim marks; set the correct output intent to the agreed ICC profile.
- Flatten transparency only if the printer requires it.

### Step 7: Proof

- Request a **hard proof** for any color-critical job; soft proofs miss paper stock and press shifts.
- Check the physical proof for registration, color, and trim position before approving the full run.

## Worked contrast

**Bad handoff:** an RGB PDF at trim size with no bleed, text 1mm from the edge, fonts not embedded, exported "High Quality Print". Result: prepress rejection, or worse, a run with white slivers at the edges and substituted fonts.

**Good handoff:** a PDF/X-4 with 0.125in bleed on all sides, safe zone respected, CMYK with FOGRA39 output intent, fonts embedded, 300 DPI images at placed size, crop marks on - plus the completed checklist in the delivery email so prepress can verify in one pass.

## Deliverable

Produce the print-ready PDF/X file plus the completed prepress checklist confirming bleed, safe zone, color profile, fonts, image resolution, and ink limits were all verified - and a note of any items pending printer confirmation.

## Do NOT

- Do not trust screen color for anything color-critical - the gamut lies; only a hard proof tells the truth.
- Do not fake bleed by scaling artwork up; it shifts the composition and drops effective resolution.
- Do not build rich black from four full channels (C100 M100 Y100 K100) - it exceeds ink limits and smears.
- Do not set body text in four-color builds; misregistration turns it fuzzy.
- Do not export the "print" preset and hope - the printer's required PDF/X standard is a spec, not a suggestion.

## Quality bar

Every checklist line checked or explicitly waived by the printer; bleed present on every edge-touching element; no RGB objects remaining; effective image resolution ≥ 300 DPI at placed size (150 for large-format); ink coverage under the stated limit; the export matches the printer's named PDF/X standard.

## Escalation

When the printer's spec conflicts with these defaults, the printer wins - they know their press. For screen-bound deliverables and developer handoff, route to design-handoff-doc; for type-system decisions before layout, pair with typography-system.
