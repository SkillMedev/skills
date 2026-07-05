---
name: Meta Title Optimizer
description: Writes title tags and meta descriptions that survive SERP truncation, place the primary keyword early for relevance, and frame the snippet to win the click against competing results. Use when someone asks "write a title tag for this page", "why does this page rank but get no clicks", "is my title too long", "rewrite my meta description", or wants a snippet that stands out from the pattern on the results page. Do NOT use for broad on-page or technical SEO (headings, internal links, schema, crawlability, Core Web Vitals) - use seo-optimizer. Do NOT use for classifying what searchers want from a query - use search-intent-classifier. Do NOT use for finding what content the page itself is missing versus competitors - use serp-gap-analyzer.
---

# Meta Title Optimizer

Write the title tag and meta description for a single page so the snippet fits SERP limits, earns relevance, and wins the click. This skill owns snippet copy only; broader on-page work belongs to seo-optimizer. The costly mistake it prevents: a page that ranks but bleeds clicks to worse pages with better snippets - position without click-through is rent paid on a store nobody enters, and an overlong or generic title is the usual cause.

## Operating procedure

### Step 1: gather inputs

- The page's primary keyword - one term. A second keyword means a second page, not a longer title.
- The search intent behind it (informational, comparison, transactional). If unknown, infer and label the inference a guess, or route to search-intent-classifier.
- What the page actually delivers: its H1 and main content. The snippet must be able to keep its promise.
- The current title/description and, if the page already ranks, its click-through problem ("ranks #4, CTR under 2%").

If the keyword or the page content is unknown, ask before writing.

### Step 2: read the live SERP

Look at the current top results for the keyword. Note the dominant title pattern - the phrasing most results share - so it can be broken instead of blended into. A snippet that looks like the other nine is invisible at any position.

### Step 3: draft the title tag

- Lead with the primary keyword or a close natural variant in the first few words. Relevance is read left to right by both engines and humans.
- Keep it to **55-58 characters** and treat **60 as the hard ceiling** - titles truncate near 580 pixels, and wide capitals (W, M, all-caps words) consume the pixel budget faster than a character count implies. A truncated title loses its promise mid-sentence.
- Append the brand after a separator (| or - ) only if room remains. Never let the brand push the keyword or the payoff past the cutoff.

### Step 4: differentiate the framing

If every competing title repeats the same pattern, break it with one CTR pattern - never stack several:

- A specific number ("7 Ways", "in 15 Minutes", "$40/mo") - specificity signals substance; odd and concrete beats round and vague.
- A concrete benefit or outcome the page actually delivers.
- A question that mirrors the query when intent is informational.
- A bracketed qualifier ("[Template]", "[Checklist]", "[Free]") when the page contains that artifact.

Match the intent: a transactional query wants the product and the differentiator, not a clever question.

### Step 5: draft the meta description

- Front-load the value in the first **~155 characters**; the rest truncates, and on mobile the cutoff comes sooner.
- Mirror the searcher's own words so the engine bolds the matched terms - bolding is free visual weight.
- Name one concrete differentiator: a number, "free", "step-by-step", a unique angle.
- End with a soft call to action ("See the full comparison", "Get the template").

### Step 6: verify against the page

Confirm the title and description reflect the H1 and the page's real answer. Overpromising gets the title rewritten by the engine and bounces the users who do click - both lose. If the engine is already rewriting the current title, treat that as evidence of a title/content mismatch, not bad luck.

## Worked contrast pair

Page: a guide to home cold brew ratios with a conversion table. Keyword: "cold brew ratio".

**Bad title (78 chars, keyword buried, brand first, stacked patterns):**

```
AcmeCoffee Blog | The Ultimate, Complete 2-Minute Guide to Perfect Cold Brew Coffee Ratios!!
```

Truncates around "Guide to", the keyword arrives after the cutoff on narrow displays, the brand spends the best pixels, and the hype ("Ultimate, Complete", "!!") promises nothing specific.

**Good title (52 chars, keyword first, one pattern, room for brand):**

```
Cold Brew Ratio: 1:4 to 1:8 Explained [Chart] | Acme
```

**Bad description (value after the cutoff):**

```
Here at Acme we have been passionate about coffee for many years and we love sharing everything we know about brewing methods with our wonderful community of readers, including cold brew.
```

**Good description (147 chars, front-loaded, one differentiator, soft CTA):**

```
Cold brew ratio made simple: 1:4 for concentrate, 1:8 ready-to-drink, with a cups-and-liters chart for any batch size. See the full ratio table.
```

## Deliverable

Produce, per page: one title tag (with character count shown), one meta description (with character count shown), a one-line rationale naming the SERP pattern being broken and the differentiator used, and a pass/fail note against the quality bar below.

## Do NOT

- Do not stuff a second keyword or repeat the brand in every title - dilution costs relevance and reads as boilerplate.
- Do not use ALL CAPS or clickbait the page cannot deliver; the engine rewrites dishonest titles and the users bounce.
- Do not pad a description past ~155 characters expecting the overflow to display - the payoff must land before the cutoff.
- Do not ship auto-generated boilerplate titles that read identically across the site; every page competes in its own SERP.
- Do not hardcode a year or other dated token into evergreen titles; it goes stale and forces maintenance.
- Do not stack CTR patterns (number + brackets + question + emoji); one differentiator reads confident, four read desperate.

## Quality bar

- Title is 55-58 characters (60 hard max) and survives truncation with the keyword and the payoff intact.
- Primary keyword or a close variant appears in the first few words; no second keyword stuffed in.
- Meta description front-loads value within ~155 characters and contains exactly one concrete differentiator.
- Title and description accurately match the H1 and on-page content - nothing promised that the page does not deliver.
- Snippet is visibly distinct from the dominant pattern in the live SERP, using one CTR pattern, not several.
