---
name: SEO Optimizer
description: Audits and rewrites a page for organic search - title and meta lengths, heading hierarchy, intent match, internal links, schema markup, and Core Web Vitals - and returns a prioritized fix list. Use when someone asks "why isn't this page ranking", "audit my page for SEO", "rewrite my title and meta description", "does this need schema markup", or before publishing a page that must earn organic traffic. Do NOT use for grouping keywords into topical clusters across a site - use keyword-cluster-builder instead; do NOT use for planning a site-wide internal-link architecture - use internal-linking-mapper instead; do NOT use for restructuring content into AI-answer-ready blocks - use aeo-answer-blockifier instead; do NOT use for deep page-speed engineering - use web-performance instead.
---

# SEO Optimizer

Improve one page's organic visibility through content quality, technical signals, and structured data - without keyword stuffing. The costly mistake this prevents is optimizing the wrong layer: teams rewrite copy on a page whose title truncates in the SERP, whose intent mismatches the query, or that cannibalizes a sibling page, then conclude "SEO doesn't work." Audit in order; fix the highest layer that fails first.

## Operating procedure

Intent comes before on-page elements, because a perfectly optimized page targeting the wrong intent cannot rank. On-page comes before schema and vitals, because rich results and speed amplify a page that already deserves to rank.

### Step 1: Gather inputs

1. The page URL or draft, and its one primary keyword. If the user has no primary keyword, that is the first deliverable - propose one and label it a guess.
2. 2-4 semantic variants (default: derive from the primary).
3. The intent behind the query: informational, navigational, or transactional. When unsure, check what currently ranks - the SERP is the ground truth for intent.
4. Sibling pages targeting adjacent queries, to check cannibalization.

### Step 2: Verify intent match

Match content type to intent: informational queries want guides and answers, transactional want product or category pages, navigational want the brand destination. If the page type contradicts the intent of its primary keyword, stop - no on-page fix rescues an intent mismatch; re-target or re-build. Each page must serve a distinct intent; consolidate cannibalizing pages rather than letting two half-strength pages split one query.

### Step 3: Fix the head elements

- Title tag: 50-60 characters - keep under 60 or the SERP truncates it; primary keyword near the front; unique per page.
- Meta description: 140-155 characters, include a call to action. Not a ranking factor, but it is the ad copy that drives click-through.
- URL slug: short, hyphenated, keyword-bearing, no stop words.

Bad: `Home | Acme Inc | Solutions, Products, Services, About Us and More` (74 chars, brand first, keyword nowhere).
Good: `Standing Desk Buying Guide: Height, Stability, Price | Acme` (60 chars, keyword front-loaded).

### Step 4: Fix the body structure

1. Exactly one `<h1>` per page; logical `<h2>`/`<h3>` hierarchy that a reader could skim as an outline.
2. Answer the query in the first 100 words - this is what earns featured-snippet eligibility.
3. Keyword usage: target the primary plus 2-4 semantic variants; keep density natural at roughly 0.5-1.5%. Optimize for topic coverage, not exact-match repetition.
4. Internal links: 2-5 contextual links to related pages with descriptive anchor text ("standing desk stability test", never "click here"). For architecture across many pages, hand off to internal-linking-mapper.
5. Image `alt` text describes the image for a reader who cannot see it - not a keyword slot.

### Step 5: Add schema markup where it fits

Add JSON-LD in the `<head>`; use `Article`, `FAQPage`, `Product`, `BreadcrumbList`, and `HowTo` only where the visible content genuinely matches the type - schema describing content that is not on the page invites a manual action.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Standing Desk Buying Guide: Height, Stability, Price",
  "author": { "@type": "Person", "name": "Jordan Lee" },
  "datePublished": "[FILL: ISO date]"
}
</script>
```

### Step 6: Check Core Web Vitals

- LCP under 2.5s: preload the hero image, serve next-gen formats, avoid render-blocking CSS.
- CLS under 0.1: explicit width/height on images; reserve space for ads and embeds.
- INP under 200ms: break up long tasks, defer non-critical JS.

Flag failures and hand deep remediation to web-performance.

### Step 7: Check crawl hygiene

- Canonicalize duplicates with `rel="canonical"`; pagination uses self-referencing canonicals, not `rel=prev/next` (deprecated).
- JS-rendered content: confirm crawlers see the text - server-side rendering or dynamic rendering.
- Migrations: 301-redirect old URLs one-to-one; never mass-redirect to the homepage.
- XML sitemap submitted, under 50k URLs per file.

## Deliverable

Produce a page audit containing: the intent verdict, rewritten title and meta with character counts shown, the heading outline, a keyword-and-variant map with placement notes, 2-5 internal-link recommendations with anchor text, the JSON-LD block ready to paste, Core Web Vitals pass/fail against the three thresholds, and a prioritized fix list ordered by the layer that fails first.

## Do NOT

- Do not stuff or cloak - the helpful-content system demotes thin, spun pages, and recovery is slower than doing it right.
- Do not ship a title over 60 characters or with the keyword at the end; truncation hides it exactly where it matters.
- Do not write meta descriptions past 155 characters - the SERP cuts them mid-sentence.
- Do not add schema for content the page does not visibly contain.
- Do not let two pages target the same intent; the split kills both.
- Do not treat this as site-wide strategy - clustering belongs to keyword-cluster-builder and link architecture to internal-linking-mapper.

## Quality bar

- Title 50-60 chars with the primary keyword front-loaded; meta 140-155 chars with a CTA; both counts stated in the deliverable.
- The query is answered in the first 100 words.
- One h1; every h2/h3 earns its place in the outline.
- 2-5 internal links with descriptive anchors; zero "click here".
- Schema validates and matches visible content.
- Each fix in the list names its layer (intent, head, body, schema, vitals, crawl) so the user knows what moves rankings versus click-through.
