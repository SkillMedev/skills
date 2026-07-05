---
name: Internal Linking Mapper
description: Maps internal links and anchor text between a new or updated page and an existing URL inventory to strengthen a topic cluster, producing a source/target/anchor link table plus a hub-and-spoke check. Use when someone asks "which pages should this article link to", "how do I internally link my new post", "is this page orphaned", or has a draft plus a sitemap, URL export, or pillar/cluster map and needs to decide the inbound links, outbound links, and anchors. Do NOT use to design the cluster or pillar architecture from a keyword export — use keyword-cluster-builder instead; do NOT use for on-page rewrites of titles, headings, or body copy — use seo-optimizer instead; do NOT use when a page is slipping in rankings and needs SERP-intent update actions — use content-refresh-auditor instead.
---

# Internal Linking Mapper

Wire one page into an existing site so the whole topic cluster gains authority: pick the outbound links from the new page, the inbound links into it, and descriptive anchor text for each. The costly mistake this prevents is the orphan — a page with zero inbound internal links that search engines rarely crawl and never rank — and its opposite, exact-match anchor spam that reads as manipulation. This skill assumes the cluster already exists and the page content is set; it only decides the links.

## Operating procedure

Order matters: you cannot plan links until you know the inventory, and you cannot write anchors until you know the links.

### Step 1: Gather inputs

- The target page: topic, primary keyword, and draft or URL.
- The existing URL inventory: sitemap, URL export, or pillar/cluster map. If none is supplied, ask for one — never invent URLs.
- Optional but valuable: which page is the top money/conversion page, and any pages known to be thin, noindex, or non-canonical (these are link dead-ends).

If the site architecture is unknown, ask one framing question: how many clicks from the homepage is this page's section? Every important page should sit within 3 clicks of the home page; if the new page would land deeper, flag it — internal links from higher-level pages are the fix.

### Step 2: Locate the cluster

Identify the page's pillar and its sibling cluster pages by topic match. Anything outside the cluster is a candidate only when genuinely relevant to the reader.

### Step 3: Plan outbound links from the new page

- One link up to the pillar — mandatory in a hub-and-spoke structure.
- Cross-links to siblings only where the content actually references them.
- One contextual link to the top money/conversion page when the relevance is real, not forced.
- Practical cap: roughly 3-8 contextual in-body links for a standard article. Beyond that, each additional link dilutes the rest and reads as padding.

### Step 4: Plan inbound links into the new page

Scan the inventory for pages whose body naturally mentions the new topic, and specify exactly where each adds a contextual in-body link pointing to the new page. Target a minimum of 2-3 inbound links, always including one from the pillar. A page with zero inbound internal links is effectively orphaned; one inbound link is a single point of failure.

### Step 5: Write anchor text per link

- Use a descriptive phrase containing the target's topic — a reader should predict the destination from the anchor alone.
- Vary wording across links to the same target; never repeat one exact-match anchor site-wide.
- Never "click here", "read more", or a bare URL.
- Keep anchors natural in-sentence, typically 2-6 words.

### Step 6: Place and prune

Links go in body context, not footer or sidebar boilerplate — boilerplate links carry little ranking value and no reader intent. Cut every link that does not help the reader; the map should end shorter than the candidate list.

### Step 7: Output the map

Return a table of source page, target page, anchor text, and direction.

## Worked artifact: hub-and-spoke link map

New page: /blog/email-warmup-schedule (cluster: email deliverability; pillar: /guides/email-deliverability).

```
LINK MAP — /blog/email-warmup-schedule

| # | Source page                        | Target page                        | Anchor text                     | Direction |
|---|-----------------------------------|-----------------------------------|---------------------------------|-----------|
| 1 | /blog/email-warmup-schedule       | /guides/email-deliverability      | email deliverability guide      | outbound (to pillar) |
| 2 | /blog/email-warmup-schedule       | /blog/spf-dkim-dmarc-setup        | authenticating your domain      | outbound (sibling) |
| 3 | /blog/email-warmup-schedule       | /product/email-tool               | automate your warmup schedule   | outbound (money page) |
| 4 | /guides/email-deliverability      | /blog/email-warmup-schedule       | domain warmup schedule          | inbound (from pillar) |
| 5 | /blog/new-sending-domain-checklist| /blog/email-warmup-schedule       | how fast to ramp sending volume | inbound (sibling) |

Checks: pillar link up (row 1) — yes. Inbound >= 2 incl. pillar (rows 4-5) — yes.
No repeated exact-match anchors. All URLs from supplied inventory. Depth from home: 2 clicks.
```

Bad anchor, for contrast: five inbound links all reading "email warmup schedule" verbatim — exact-match repetition that looks manipulative. Good: the varied, descriptive anchors in rows 4-5.

## Deliverable

Produce the link map table — source, target, anchor, direction — plus a three-line check block confirming the pillar link, the inbound minimum, anchor variety, and click depth from home.

## Do NOT

- Do not link unrelated pages to inflate link count — volume padding dilutes every real link.
- Do not repeat one exact-match anchor across many links; vary the phrasing.
- Do not bury links in footers or sidebars and expect ranking value.
- Do not orphan the new page by skipping inbound links, and do not settle for a single inbound link.
- Do not link to thin, noindex, or non-canonical pages.
- Do not fabricate URLs not present in the supplied inventory.

## Quality bar

- The new page has at least 2-3 inbound internal links, including one from its pillar, and a link up to the pillar.
- Every anchor is descriptive, natural in-sentence, and no exact-match anchor repeats site-wide.
- Every link is contextual (in body) and topically justified.
- The page sits within 3 clicks of the homepage, or the map flags the depth problem with a fix.
- Output names real URLs from the supplied inventory only.

## Neighbors

To design the cluster itself from keywords, use keyword-cluster-builder. For on-page optimization of the linked pages, seo-optimizer. For refreshing a decaying page before re-linking it, content-refresh-auditor.
