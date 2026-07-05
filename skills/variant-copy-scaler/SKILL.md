---
name: Variant Copy Scaler
description: Generates distinct, on-brand copy for every size, color, and bundle variant from a single master description, splitting a shared core from a thin per-variant layer so variant pages avoid thin or duplicate-content problems. Use when someone asks "write descriptions for all 12 colorways", "our variant pages are cannibalizing each other", "should each SKU get its own page", or when one product ships in many variants and needs per-variant PDP copy, or near-identical variant pages are creating duplicate-content or canonical problems. Do NOT use to write the single master or parent PDP description from specs - use product-description-writer instead; do NOT use for category or collection page copy - use category-page-copywriter instead.
---

# Variant Copy Scaler

Scale one master product description across many variants so each page is distinct enough to rank and to answer "is THIS the one I want," without rewriting the brand story per SKU. The costly mistake this prevents is twofold: thin near-duplicate pages that compete with each other and dilute authority, and the wasted effort of hand-writing thirty full descriptions when only 20% of each page should differ.

## Operating procedure

The order is strict: the indexing decision comes before any copy, because consolidation makes per-variant copy unnecessary and writing it first wastes the work.

### Step 1: Gather inputs

- The master description (the parent PDP copy). If it does not exist, stop and route to product-description-writer first.
- The full variant matrix: every color, size, and bundle, with SKU and any attribute that genuinely differs (dimensions, weight, price, materials).
- Platform behavior: does each variant mint a separate indexable URL, or does one URL serve all variants via a selector?
- Brand voice constraints, if documented.

Stop if the master or the matrix is missing.

### Step 2: Decide the indexing strategy first

If the platform mints a separate indexable URL per variant, choose deliberately and state the decision explicitly in the output:

- Rank variants individually - only when variants attract distinct search demand (people search "olive green linen curtains" separately from "white linen curtains") AND you will write materially unique copy per page.
- Consolidate to the parent - set canonical tags on variant URLs pointing to the parent product. The default for most matrices.

### Step 3: Apply the consolidation rule

If you chose consolidation, or the matrix is large and undifferentiated (e.g. 30 near-identical shades), recommend a single parent page with a swatch/size selector and stop before generating thin copy. Generating per-variant pages for an undifferentiated matrix is the failure mode, not the deliverable.

### Step 4: Extract the shared core once

Pull out everything true of every variant: brand story, materials, core benefit, care instructions, warranty, shipping. This block is reused verbatim on every variant page and is never rewritten per SKU.

### Step 5: Build the template

Fix the section order, bullet count, and tone so output scales without drift. Every variant page renders as: shared core + variant layer, in identical structure. Target roughly 80% shared, 20% variant-specific.

### Step 6: Write the thin variant layer per option

Fill the template slots with genuinely variant-specific text. What changes per variant: the hue or dimension description, who it fits, the use case, the pairing cue, the bundle math. What never changes: brand story, materials, core benefit, care, warranty, shipping (unless they genuinely differ).

- Color: name the actual hue honestly ("warm oatmeal, not bright white") plus one styling or pairing cue.
- Size: state who it fits and the use case ("the 12oz suits a single espresso; the 20oz is for all-day desk sipping").
- Bundle: show per-unit value math and the occasion ("the 3-pack works out to $14 per unit vs $19 alone - stock the whole entryway").

### Step 7: Render and verify

Render each page and read variant layers side by side. If any two read as near-duplicates - a single swapped word - rewrite or fold those variants into consolidation.

## Worked artifact: good/bad variant layer pair

Master product: linen throw blanket, 6 colorways, separate URLs, decision = rank individually.

Bad (swapped-word copy - this is the duplicate-content trap):

> Our beautiful linen throw in **Sage** adds comfort to any room. Made of 100% linen. Machine washable.
> Our beautiful linen throw in **Rust** adds comfort to any room. Made of 100% linen. Machine washable.

Good (shared core + genuine variant layer):

> [SHARED CORE - identical on both pages] Stonewashed 100% European flax linen, pre-softened so it drapes from day one. Machine washable cold; gets softer with every wash. Free 30-day returns.
>
> [SAGE layer] A muted gray-green that reads calmer than it photographs - closer to dried eucalyptus than mint. Pairs with warm woods and cream; the shade most buyers choose for bedrooms.
>
> [RUST layer] A deep burnt terracotta with brown undertones, not orange. The highest-contrast colorway - built for neutral sofas that need one bold layer.

Template skeleton:

```
[FILL: variant name] - [FILL: 1-sentence honest hue/size/bundle description]
[FILL: 1 pairing, fit, or per-unit-math cue]
[SHARED CORE - paste verbatim]
[FILL: 3 bullets, same order every variant: material/benefit, care, guarantee]
```

## Deliverable

Produce: (1) the stated canonical/indexing decision, (2) the shared core block, (3) the fixed template, and (4) one variant layer per option in the matrix - or, if consolidation wins, a one-paragraph recommendation for a parent page with selector and the canonical setup.

## Do NOT

- Do not swap one color or size word and call it new copy.
- Do not publish multiple indexable variant URLs with near-identical text that compete and dilute authority.
- Do not claim a benefit for one variant that is not true of it (a black fabric is not "slimming" by virtue of color).
- Do not rewrite the brand story, materials, or core benefit per variant - those live in the shared core.
- Do not generate thin pages for an undifferentiated matrix; consolidate to a parent with a selector instead.
- Do not leave the canonical decision implicit - state it, because copy and indexing must agree.

## Quality bar

- Every variant page has materially distinct text in its variant layer - a reader could identify which variant it describes with the name removed.
- Section order, bullet count, and voice are identical across all variants.
- The canonical/indexing decision is stated and consistent with the copy produced.
- Care, warranty, and shipping are identical across variants unless they genuinely differ.
- Roughly 80/20 shared-to-unique ratio holds; a page that is 99% shared should not exist as an indexable URL.

## Neighbors

Master description from specs: product-description-writer. Amazon-specific listings: amazon-listing-optimizer. Category pages above the PDP: category-page-copywriter.
