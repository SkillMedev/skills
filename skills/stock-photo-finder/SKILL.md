---
name: Find the Right Stock Photo
description: Source the right stock photograph for a brief by picking the best library and crafting the search. Use when you need a real photo or image for a hero, blog post, ad, slide, mockup, social post, or thumbnail and you are not sure where to look or what to search, or when someone says "find me a photo of...", "where do I get images for this", "I need a hero image", or "good free stock photos for X". It covers free libraries (Unsplash, Pexels, Pixabay), curated and boutique sources (Stocksy, Stills, Death to Stock), broad paid catalogs (Adobe Stock, Shutterstock, iStock), and editorial archives (Getty, AP, Reuters). Do NOT use to decide whether a license actually permits your use or whether you need a release -> use image-license-rights; do NOT use to fetch images programmatically in code -> use stock-photo-api; do NOT use to art-direct a cohesive set or fix a cliched look -> use visual-asset-curation.
---

# Find the Right Stock Photo

This skill gets you from "I need an image of X" to a short list of strong
candidates from the *right* source. It owns two decisions: **which library to
search** and **what to type into it**. It does not decide whether you may legally
use the result (that is `image-license-rights`), it does not fetch images in code
(that is `stock-photo-api`), and it does not turn a pile of candidates into a
cohesive, on-brand set (that is `visual-asset-curation`).

## When to use a sibling instead

- **"Can I legally use this? Do I need attribution or a model release?"** ->
  **image-license-rights**. Always run it before you ship anything commercial.
- **"Pull stock photos into my app / script automatically."** ->
  **stock-photo-api**.
- **"I have ten options, which ones and in what order, and why do they look
  cheap?"** -> **visual-asset-curation**.

## Step 1: Write the one-line visual brief

Never search before you can fill this in. Vague searches return the most generic,
most-used (most cliched) images on the platform.

- **Subject** - the concrete noun ("a barista steaming milk", not "coffee").
- **Context / setting** - where it happens ("small independent cafe, morning light").
- **Mood / emotion** - "calm and focused", "energetic", "quiet luxury".
- **Use + placement** - hero banner, blog inline, ad, slide background. This sets
  orientation and resolution.
- **Commercial or editorial** - is it selling something (commercial) or
  illustrating a story/news (editorial)? This changes which sources are even
  legal. Hand the call to **image-license-rights** if unsure.
- **Budget** - free-only, or is paid on the table?

## Step 2: Pick the source by use case

Match the brief to a library before you search. The order below is the default
decision path.

- **Free, general-purpose, fast** -> **Unsplash, Pexels, Pixabay.** Huge free
  catalogs, generous licenses, no cost. Trade-off: the popular shots are heavily
  reused - a first-page result on these platforms can have hundreds of thousands
  of downloads, which is exactly why it reads as "stock". Best for blogs, side
  projects, MVPs, and backgrounds. Pexels and Pixabay also carry free stock *video*.
- **Curated / authentic / premium-but-affordable** -> **Stocksy, Stills, Death to
  Stock.** Smaller, art-directed collections that dodge the cliche look. Reach
  here when the free libraries feel generic or the brand needs to look considered.
  (Verify the license tier on the provider before relying on it - see
  image-license-rights.)
- **Broadest selection, paid, releases handled** -> **Adobe Stock, Shutterstock,
  iStock / Getty RF.** When you need a specific, hard-to-find shot and want
  model/property releases already in place for commercial use. Subscriptions or
  credit packs.
- **Editorial: news, sports, celebrities, real events, brands** -> **Getty
  Images, AP, Reuters.** These are *editorial-use-only* unless explicitly licensed
  otherwise - you cannot use them to imply endorsement or to advertise. This is a
  licensing trap; confirm with **image-license-rights**.
- **Illustration, vector, icon, or 3D instead of a photo** -> Adobe Stock,
  Freepik, unDraw, or icon-specific libraries. (Photo search heuristics below
  still apply.)

Rule of thumb: start free for anything internal or low-stakes; move to curated or
paid the moment the image is the hero of a brand-facing surface.

## Step 3: Craft the search query

Search like a photo editor, not like Google.

- **Lead with a concrete, photographable noun.** "engineer debugging at night",
  not "productivity". Abstractions return the worst stock cliches.
- **Add context and emotion as separate terms.** subject + setting + mood:
  "founder laptop coffee shop candid".
- **Use the platform filters**, not just words: orientation (landscape /
  portrait / square), color, "people / no people", and (on paid sites) number of
  people, age, and editorial vs commercial toggles.
- **Search synonyms and adjacent scenes.** If "team meeting" is all cliche,
  try "whiteboard sketching", "two people reviewing a screen", "office hallway".
  The strongest shot is often one query sideways from the obvious one. Run at
  least 3 query variants before settling, and scroll past the first page or two
  of results on free libraries - that is where the least-reused shots live.
- **Match orientation to placement up front** - a wide hero needs landscape with
  negative space on one side for text; a phone splash needs portrait. Filter for
  it rather than cropping a square later.

## Step 4: Evaluate the candidates

For each shortlisted image, check, in order:

1. **Resolution / aspect fits the placement** at full size. A full-width hero
   needs an original of roughly 2400px or more on the long edge (about 2x the
   rendered slot for retina screens); social posts and thumbnails need at least
   ~1080px. Avoid upscaling.
2. **Licensing is viable for the use** - flag editorial-only and
   release-required shots and pass them to **image-license-rights** before
   committing.
3. **Identifiable people, logos, or artwork present?** -> likely needs a release
   for commercial use. Note it; do not assume the platform handled it (free
   libraries often have not).
4. **Cliche check** - staged handshake, fake laughing at salad, isolated-on-white
   "teamwork"? If the set looks generic, hand the finalists to
   **visual-asset-curation** to tighten and de-cliche.

## Deliverable

Produce a shortlist of 3-8 candidate images, each with: the source URL, the
library and license type, resolution and orientation, and any editorial-only or
release-required flag - plus the one-line brief and the queries that produced
the set, so the search can be re-run or extended. Hand the shortlist to
image-license-rights before anything ships commercially.

## Quality bar

- A written brief existed before the first search.
- Source was chosen deliberately (free vs curated vs paid vs editorial), not by
  habit.
- At least one "sideways" query was tried beyond the obvious keyword.
- Every finalist has its source URL recorded for the license check.
- Editorial-only and release-required images are flagged, not silently shipped.

## Do NOT

- Do not grab the top first-page result on a free library - the most-downloaded
  shots are the most reused, and readers have seen them on ten other sites.
- Do not search abstract nouns ("success", "innovation", "growth") - they return
  the platform's worst cliches; translate the concept into a photographable scene
  first.
- Do not use editorial-archive images (Getty editorial, AP, Reuters) in ads,
  landing pages, or anything commercial - editorial-use-only is a legal line,
  not a suggestion.
- Do not crop your way out of a wrong orientation - a square chopped into a wide
  hero loses composition and resolution; filter for the right orientation at
  search time.
- Do not assume "free" means release-free - free libraries frequently host
  images of identifiable people and logos with no model or property release
  attached.
- Do not ship a finalist without its source URL and license type recorded - an
  untraceable image is an unlicensable image.
