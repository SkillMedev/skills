---
name: Keyword Cluster Builder
description: Collapses a raw keyword list into topic clusters and a pillar/cluster page architecture with one primary keyword and a prioritized build order per cluster. Use when you have a keyword export (CSV/GSC/keyword-tool dump) and need a content plan that says which pages to build, not a flat list. Do NOT use when labeling individual queries by intent or recommending page types - use search-intent-classifier instead; do NOT use when choosing internal links or anchor text between pages - use internal-linking-mapper instead.
---
# Keyword Cluster Builder

Turn hundreds of queries into a small set of clusters, each earning ONE page, arranged so topical authority compounds. A keyword list is not a content plan: one thin page per keyword cannibalizes your own rankings and wastes crawl budget.

## Workflow

1. **Dedupe and discard.** Drop near-zero-volume singletons (under ~10 searches/month with no transactional intent), branded/navigational queries you cannot rank for, and queries whose SERP is pure ads/maps/widgets with no organic slot. Normalize obvious variants (plurals, word order, stop words).
2. **Cluster by shared SERP, not string similarity.** Group queries Google answers with the same page: if two queries share roughly 4 or more of the same top-10 organic URLs, they belong in one cluster; below ~3 shared URLs, keep them separate. Never split a cluster only because wording differs; never merge two queries that surface different result types just because they share words.
3. **Separate clusters that demand different page formats.** When a cluster's SERP is dominated by a different result type (a guide vs. a comparison vs. a product page), split it - one page cannot satisfy two formats. Treat the coarse intent signal as a grouping input only; do not produce per-query intent labels or page-type recommendations here (that is search-intent-classifier).
4. **Build the pillar/cluster topology.** For each topic, define one broad pillar targeting the head term and the cluster pages targeting long-tail subtopics that report to it - a healthy pillar typically supports 5-15 cluster pages. Record the parent→child structure (which clusters belong under which pillar). Define structure only - leave link selection and anchor text to internal-linking-mapper.
5. **Pick the primary keyword per cluster.** Choose the one keyword the page can realistically win (highest relevant volume at achievable difficulty), then list the secondary keywords the same page should also satisfy. State difficulty honestly; on a low-authority domain, favor long-tail clusters with keyword difficulty under ~30 (on the standard 0-100 tool scales) first.
6. **Output a prioritized build order.** Score each cluster by opportunity = (revenue relevance × winnability) ÷ current coverage. Flag overlaps where two existing pages compete for one cluster (consolidate). Flag gaps where a cluster has demand but no page.

## Worked example

Three queries from a CRM keyword export: "best crm for small business", "top crm software for small companies", "small business crm comparison".

Bad: three separate pages, one per phrasing. All three SERPs return the same listicle-comparison URLs, so the three pages compete for the same slot, split their link equity, and Google picks one arbitrarily - often the weakest.

Good: check the SERPs, see 6-7 of the top-10 URLs overlap, and collapse all three into one cluster → one comparison page with "best crm for small business" as the primary keyword and the other two as secondaries. Meanwhile "what does crm stand for" shares the word CRM but returns definitional guides, not comparisons - different SERP, different cluster, likely a child of an educational pillar rather than the commercial one.

## Quality bar

- Every retained query lands in exactly one cluster; no query appears twice.
- Each cluster maps to exactly one planned page and one primary keyword.
- Cluster membership is justified by SERP overlap, not lexical overlap.
- Every cluster carries a pillar/child role, an opportunity score, and an overlap/gap flag.
- The deliverable names the pages to build in priority order - not a re-sorted keyword list.

## Do NOT

- Do not label clusters by the informational/commercial/transactional/navigational taxonomy or recommend page types - that is search-intent-classifier.
- Do not specify internal links or anchor text between pages - that is internal-linking-mapper.
- Do not create one page per keyword, or split a cluster because of synonym wording.
- Do not promise ranking timelines; organic gains take months.
- Do not recommend doorway pages, spun variants, or thin micro-keyword pages to chase coverage.
