---
name: Image License & Usage Rights
description: Decides whether a specific image use is legally permitted, what the license obligates, and whether a model or property release is required — covering CC0 and Creative Commons variants, Unsplash/Pexels/Pixabay terms, royalty-free vs rights-managed, standard vs extended tiers, editorial-use-only, and AI-image caveats — and delivers a per-image license record. Use when someone asks "is this free for commercial use", "do I need to credit the photographer", "can I put this on merchandise", "what is royalty-free vs rights-managed", "is editorial-use-only a problem", or "do I need a model release", or is about to publish, advertise, or sell anything containing a stock image. Do NOT use to find or search for a photo — use stock-photo-finder instead; do NOT use to fetch images via an API with attribution rules — use stock-photo-api instead.
---

# Image License & Usage Rights

The license travels with the image, not with the website you found it on — the same photo can be free on one site and cost $500 on another, and the most common violation is running an editorial-use-only image in an ad. This skill decides whether a specific use is permitted, what the license obligates, and when a release is needed. It is the gate before anything commercial ships.

First principle: verify on the source page, never from memory. Licenses change and sites revise terms; open the image's actual license page on the provider before relying on any summary, including this one.

## Operating procedure

Order matters: the use classification (Step 2) determines which license terms even apply, so classify before reading.

### Step 1: Gather inputs

- The image: source URL, provider, and any license/asset ID.
- The intended use: where it will appear, for how long, and at what scale.
- Whether the image contains identifiable people, private property, artworks, logos, or distinctive buildings.
- Whether the image is AI-generated or from a provider's AI tool.

### Step 2: Classify the use first

The same image can be fine or forbidden depending on use. Pin down:

- Commercial or editorial? Commercial promotes, advertises, or sells something (ads, packaging, product sites, merch). Editorial illustrates news, commentary, or education with no commercial pitch. Editorial-use-only images cannot be used commercially — the single most common violation.
- Internal or public? A deck shown in a meeting is lower-risk than a public ad, but "internal" is not a blanket license.
- Scale — print run, impressions, and whether it goes on a product for resale (mugs, t-shirts, templates, app UI sold to others). High scale and resale/merchandise usually require an extended/enhanced license.

### Step 3: Read the license type against this map

- CC0 / Public Domain — do anything, no attribution, no permission (many museum and government archives). Still check for identifiable people and property (Step 4).
- Creative Commons variants — the letters are the obligations: BY requires exact attribution as specified; SA requires derivatives carry the same license; NC forbids commercial use entirely; ND forbids modification. CC BY-NC on a product page is a violation, full stop.
- Unsplash License — free for commercial and non-commercial use; attribution appreciated, not required. May not sell unaltered copies or compile Unsplash photos into a competing stock service.
- Pexels License — free, commercial and personal, no attribution required, modification allowed. May not sell unaltered copies, redistribute on a competing platform, or imply identifiable people or brands endorse you.
- Pixabay Content License — free, no attribution required, but NOT CC0 (it moved off CC0 years ago — this trips people who remember the old terms). No selling/distributing as-is on other stock sites; identifiable persons may not be portrayed negatively or as endorsing without a release.
- Adobe Stock, Standard vs Extended — Standard covers most web/print up to a cap (commonly 500,000 impressions/copies) but not merchandise for resale or templates/products sold to others. Extended lifts the cap and allows resale. Match the tier to Step 2's scale.
- Getty / iStock, Royalty-Free vs Rights-Managed — RF: pay once, reuse broadly within the terms. RM: licensed for a specific use, duration, region, and exclusivity — going outside that scope is a breach. Both carry the editorial-use-only flag where applicable.
- Shutterstock, Standard vs Enhanced — same shape as Adobe: Enhanced is required for merchandise/resale and very high print runs.

If the license is not one of these, read it in full; do not analogize.

### Step 4: Check releases — people and property

A license to use the image is not consent from the people or property in it.

- Model release — required to use an image with identifiable people commercially. Without it, the image is editorial-only regardless of the stock license.
- Property release — required for recognizable private property, artworks, trademarks/logos, or distinctive buildings in commercial use.
- Paid sites (Adobe, Getty, Shutterstock) usually mark "model released" / "property released" and hold the paperwork. Free libraries usually do not — treat free images of identifiable people as editorial-only unless proven otherwise.
- Editorial use generally does not need releases, but then the image cannot be used commercially. You cannot have it both ways.

### Step 5: Check attribution and redistribution

- Attribution: follow the exact license. "Appreciated" is not "required," but where required (CC BY, some boutique tiers), credit precisely as specified. API usage has stricter attribution rules — route to stock-photo-api.
- Redistribution: nearly every license, even the free no-attribution ones, forbids selling unaltered copies and re-uploading to a competing stock library. A use was licensed, not ownership.

### Step 6: Apply the AI-image gate

Rights for AI-generated stock are unsettled and vary by provider: some grant commercial use, some require disclosure, some exclude depictions of real people or brands, and copyright status of purely AI output is contested in several jurisdictions. Read that provider's AI terms specifically, and prefer assets with an explicit commercial grant.

### Step 7: Record the verdict

Complete the license record below. If any field cannot be filled for a commercial asset, do not ship it — default to the more restrictive reading or pick a clearly-licensed alternative via stock-photo-finder.

## Worked artifact: license record

One record per shipped image; keep them in a shared register.

```
IMAGE LICENSE RECORD
  Image + source URL:      [FILL]
  Provider + license name: [FILL: e.g. Adobe Stock Standard #1234567]
  License/asset ID:        [FILL]
  Date acquired:           [FILL]
  Use classification:      [FILL: commercial-ad / commercial-merch / editorial / internal]
  Scale check:             [FILL: under standard cap? resale? -> tier required]
  Identifiable people:     [FILL: none / model release held by provider / editorial-only]
  Property/logos/art:      [FILL: none / property release / editorial-only]
  Attribution required:    [FILL: none / exact credit text]
  AI-generated:            [FILL: no / yes -> provider AI terms checked]
  VERDICT:                 [FILL: cleared for stated use / restricted to X / rejected]
```

Example verdict line for a common trap: "Unsplash photo of a recognizable barista, intended for a paid ad → no model release available → REJECTED for commercial use; editorial or replace via stock-photo-finder."

## Deliverable

Produce a per-image verdict — permitted, restricted, or rejected for the stated use — plus the completed license record and, where rejected, the concrete alternative (different tier, different image, or editorial-only placement).

## Do NOT

- Do not use editorial-use-only images commercially — the most common and most enforced violation.
- Do not treat a free library's photo of an identifiable person as commercially safe; free libraries rarely hold model releases.
- Do not rely on remembered license terms; verify on the source page every time.
- Do not put standard-license images on merchandise or resale products; that is the extended/enhanced tier's job.
- Do not sell or re-upload unaltered images anywhere — no license permits it.
- Do not analogize an unfamiliar license to a familiar one; read it.

## Quality bar

- Every shipped commercial image has a complete license record — source URL, license name, asset ID, date, attribution text, release status.
- The use classification (commercial/editorial, scale, resale) is stated before the license verdict.
- People and property checks are answered explicitly, never skipped for "obviously fine" images.
- Ambiguity resolves to the more restrictive reading.

## Escalation

This is rights hygiene, not legal advice. Bring in counsel for: celebrity or public-figure likenesses, trademark-adjacent uses, anything in pharmaceuticals/finance/political advertising, disputed AI-work ownership, or an actual infringement claim. To find a replacement image, use stock-photo-finder; to fetch and attribute programmatically, stock-photo-api; for building brand-wide asset rules, pair with visual-asset-curation and brand-guidelines.
