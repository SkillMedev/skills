---
name: Visual Asset Curation
description: Art-direct a set of stock images so they look intentional and on-brand instead of like stock, and choose and sequence the final picks. Use when you have candidate images and need to decide which ones, in what order, and why a set "looks cheap" or "like stock", or when someone says "these don't feel cohesive", "make this look less like stock photos", "pick the best images for this page", or "why does this look generic". It covers spotting the stock-cliche tells, defining a visual system (palette, light, tone, angle), curating a set to consistency, choosing compositions that hold text overlays, and a color-grade pass to unify a mismatched set. Do NOT use to find or search for images -> use stock-photo-finder; do NOT use to check licensing or releases -> use image-license-rights; do NOT use to fetch images via an API -> use stock-photo-api.
---

# Visual Asset Curation

The difference between "looks like a real brand" and "looks like stock" is almost
never the individual photo - it is the **selection and treatment of the set**.
This skill takes candidates (from `stock-photo-finder` or `stock-photo-api`) and
turns them into a cohesive, intentional, on-brand selection. It owns taste and
consistency; it does not search for images and does not rule on licensing.

## When to use a sibling instead

- **"I need to find candidates first."** -> **stock-photo-finder**.
- **"Can I legally use these?"** -> **image-license-rights**.
- **"Pull these in via an API."** -> **stock-photo-api**.

## The "looks like stock" problem - learn the tells

Generic imagery shares recognizable signatures. If you see these, reject or
replace:

- Staged emotion: the over-bright laughing-alone-with-salad, the forced
  handshake, "diverse team pointing at a laptop", a thumbs-up to camera.
- Isolated-on-pure-white business props with hard shadows.
- Over-saturated, HDR-crunched, heavily-vignetted "stocky" grading.
- Subjects making direct eye contact and posing - it reads as transactional.
- A set where every image has a different color temperature, lighting, and
  crop - the inconsistency itself signals "grabbed from anywhere".

## Step 1: Define the visual system before you pick

Decide the rules the set must obey, so selection has a target:

- **Palette** - the brand's colors plus the temperature you will pull everything
  toward (warm/neutral/cool). Hold the set within roughly a 500K color-temperature
  band; a 3200K tungsten interior next to a 6500K overcast exterior is the single
  most common "grabbed from anywhere" tell.
- **Light key** - bright and airy (high-key) vs moody and shadowed (low-key).
  Pick one and hold it.
- **Tone / register** - candid and documentary vs polished and staged. Modern
  brands lean candid.
- **Subject distance & angle** - close and intimate vs wide and environmental;
  eye-level vs high/low. Consistency here does more than color.
- **Texture/grain** - clean digital vs filmic grain. A subtle shared grain can
  unify disparate sources.

## Step 2: Curate to consistency

Judge images **as a set, never one at a time**. A great photo that breaks the
system hurts more than a good photo that fits it.

- Start from a shortlist of 3-5 candidates per final slot; if you cannot cut
  at least two-thirds of what you gathered, you gathered too narrowly.
- Lay candidates out together at the size they will appear.
- Cut anything whose lighting, color, or tone fights the others, even if it is
  the "best" single shot.
- Prefer a slightly weaker image that matches over a stronger one that clashes -
  cohesion reads as intentional; one outlier reads as careless.
- Aim for variety within the system: vary subject and crop, but keep light,
  color, and tone consistent.

## Step 3: Beat the cliche

- Prefer **candid over posed**, **environmental over isolated**, **asymmetric
  over centered**.
- Favor negative space, real settings, motion, and moments over staged setups.
- Crop aggressively to change a familiar stock composition - a tight, off-center
  crop can rescue an over-used image.
- When the obvious literal image is a cliche, choose an *adjacent* one that
  implies the idea (hands and a tool instead of a posed "craftsman" portrait).

## Step 4: Compose for the placement

The winning image must work where it lives, not just in isolation:

- **Text overlay** needs negative space or a calm region; check contrast for
  legibility - overlay text must clear the WCAG 4.5:1 contrast ratio against the
  region behind it (3:1 for large display type), and plan an overlay/scrim if
  the image alone cannot deliver it. Respect platform safe areas.
- **Focal point** should sit on a third, not dead center, and not where UI or
  text will cover it.
- **Orientation/aspect** must match the slot - re-crop to the exact ratio rather
  than letterboxing; keep the focal point inside the crop.

## Step 5: Grade to unify

When the best available set still mismatches, a light, consistent **color grade**
ties it together: nudge every image toward the chosen temperature, match contrast
and black levels, and apply the same subtle grain or fade. A unified grade is
often what separates a brand-grade set from a stock dump - and it is cheaper than
re-shooting. (Stay within the license: edits are allowed, but re-selling the
edited file as stock is not - see **image-license-rights**.)

## Deliverable

Produce a curation package containing:

1. **The visual system** - a short written spec of palette/temperature, light
   key, tone, subject distance/angle, and texture rules the set obeys.
2. **The final picks** - each image mapped to its exact placement (slot, aspect
   ratio, crop notes, focal-point position), in display order.
3. **The cut list** - what was rejected and the one-line reason (cliche tell,
   system violation, weak placement fit), so the client learns the taste, not
   just the answer.
4. **Grade notes** - the unifying adjustments to apply (temperature target,
   contrast/black-level match, shared grain), if the sources mismatched.

## Do NOT

- Do not judge images one at a time - a set of individually "best" photos with
  clashing light and temperature reads cheaper than a consistent set of good ones.
- Do not keep the strongest single image when it breaks the system; the outlier
  is what makes the whole page look accidental.
- Do not letterbox or squash to fit a slot - re-crop to the exact aspect and keep
  the focal point inside the frame.
- Do not place text over a busy region and fix it with a heavy black scrim; pick
  an image with genuine negative space instead.
- Do not "fix" a mismatched set with a heavy filter - a strong Instagram-style
  look is itself a stock tell; the unifying grade should be light enough that no
  single image looks graded.
- Do not skip the system definition and curate by gut feel - without written
  rules, every reviewer argues taste and the set drifts back to inconsistency.

## Quality bar

- The set was judged together, against a written visual system - not one image at
  a time.
- No image carries a stock-cliche tell.
- Light, color temperature, and tone are consistent across the set; variety lives
  in subject and crop.
- Each final pick holds its placement (text safe area, focal point, exact aspect).
- A unifying grade was applied if the sources mismatched.
