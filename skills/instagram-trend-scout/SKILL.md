---
name: Instagram Trend Scout
slug: instagram-trend-scout
description: Researches what is currently working on Instagram for a niche using web search only — no API or login — and returns a structured scouting report of top content, winning Reel and carousel formats, reusable hook patterns, content gaps, and an engagement benchmark with honest confidence labels. Use when someone asks "what's trending on Instagram for my niche", "scout Reels formats for home baristas", "what hooks are working right now", or is starting a weekly content cycle and wants signals before creating. Do NOT use for explaining why trends work in plain language — use trend-educator instead; for drafting the actual post, use instagram-post-builder.
version: 1.0.0
stage: S1
tags: [instagram, social-media, trend-research, content-strategy, reels, web-search]
license: MIT
---

# Instagram Trend Scout

**Stage: S1 — Research** (first skill in the Instagram Trend Post Builder flywheel)

This skill answers one question: *what is working on Instagram for this niche, right now?* It gathers public signals through web search — it does not log into Instagram or call any private API — and returns a structured, machine-readable scouting report that the next skill (`trend-educator`) turns into a plain-language briefing.

Be honest about confidence. Web search surfaces secondhand signals (roundup articles, creator breakdowns, public engagement screenshots), not the Instagram Graph API. Label every number as an estimate and never invent precise metrics.

## Input Schema

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `keyword` | string | yes | — | The niche, topic, or audience to scout (e.g. "home barista", "B2B SaaS founders", "postpartum fitness"). |
| `time_range` | string | no | `"7d"` | How fresh the trends must be. One of `"7d"`, `"14d"`, `"30d"`, `"90d"`. Controls how aggressively to bias search queries toward recency. |
| `format_focus` | string | no | `"all"` | Narrow the scout to one format. One of `"all"`, `"reels"`, `"carousels"`, `"single_image"`. |
| `region` | string | no | `"global"` | Optional audience region to bias search and benchmarks (e.g. "US", "UK", "India"). |

## Workflow

1. **Validate input.** If `keyword` is missing or under 2 characters, stop and ask the user for a niche. If `time_range` is not one of the allowed values, default to `"7d"` and note the substitution.
2. **Build the search plan.** Construct 4–6 web_search queries that triangulate the niche from different angles. Always include recency terms derived from `time_range` (e.g. for `"7d"` use "this week" / "2026"). Template queries:
   - `best performing Instagram Reels {keyword} {recency}`
   - `viral Instagram carousel {keyword} {recency}`
   - `Instagram hook ideas {keyword} that get views`
   - `{keyword} Instagram engagement trends {recency}`
   - `top {keyword} creators Instagram {recency}`
   - (if `format_focus` is set, replace the format word accordingly)
3. **Run searches and collect raw signals.** Execute each query with web_search. For each promising result, capture: the content format, the topic/angle, the hook (first line or on-screen text if quoted), any caption-length cues, any hashtags mentioned, and any engagement numbers stated (views, likes, saves, shares).
4. **Normalize each piece of content** into a `top_content` row. Where a hard number is unavailable, set `engagement_signal` to a qualitative band (`"low"`, `"medium"`, `"high"`, `"viral"`) inferred from how the source describes it, and set `source_url` so the claim is traceable.
5. **Cluster formats.** Group the collected content by `format` + structural pattern (e.g. "talking-head Reel with text hook", "5-slide problem→solution carousel"). Rank clusters by how often they recur across sources. Emit the top 3–5 as `winning_formats`.
6. **Extract hook patterns.** Pull recurring opening-line structures (e.g. "POV:", "Nobody tells you…", "3 mistakes…"). Generalize each into a reusable `pattern` template plus a concrete `example`. Emit as `winning_hooks`.
7. **Find the gaps.** Identify angles, audience questions, or sub-topics that the niche audience clearly cares about but that few creators are covering well. Emit as `content_gaps` with the evidence that points to each gap.
8. **Set the benchmark.** Synthesize an `engagement_benchmark` describing what "good" looks like in this niche this period, as bands (not false precision), plus a `confidence` rating reflecting how much corroborating data was found.
9. **Assemble and return** the full output object below. Pass it forward to `trend-educator`.

## Output Schema

```json
{
  "keyword": "string — echoes the input niche",
  "time_range": "string — the effective time range used",
  "scanned_at": "string — ISO date the scout was run",
  "top_content": [
    {
      "rank": "number — 1-based, most relevant first",
      "format": "string — one of reel | carousel | single_image | story",
      "topic": "string — the angle/subject of the piece",
      "hook_text": "string — the opening line or on-screen hook, quoted if available else paraphrased",
      "hook_style": "string — the structural pattern, e.g. 'POV', 'listicle', 'contrarian', 'before-after'",
      "caption_length": "string — one of short | medium | long (short <125 chars, medium 125-500, long >500)",
      "hashtags": ["string — observed or inferred hashtags for this piece"],
      "engagement_signal": "string — one of low | medium | high | viral (qualitative band)",
      "source_url": "string — where this signal was found"
    }
  ],
  "winning_formats": [
    {
      "format": "string — the recurring structural format",
      "frequency": "string — one of rare | common | dominant (how often it recurred across sources)",
      "why_now": "string — short note on why this format appears to be surfacing this period"
    }
  ],
  "winning_hooks": [
    {
      "hook_style": "string — the pattern name",
      "pattern": "string — a reusable fill-in-the-blank template",
      "example": "string — a concrete example seen in the niche"
    }
  ],
  "content_gaps": [
    {
      "gap": "string — an underserved angle or question",
      "evidence": "string — why this looks like a gap (demand without good supply)"
    }
  ],
  "engagement_benchmark": {
    "median_engagement_band": "string — e.g. '3-6% engagement rate' or 'unknown'",
    "saves_signal": "string — qualitative note on what drives saves in this niche",
    "comments_signal": "string — qualitative note on what drives comments",
    "confidence": "string — one of low | medium | high, based on corroboration"
  }
}
```

## Output Format

Present the report to the user as a scannable markdown brief, then hand the full JSON object to `trend-educator`:

- **Header line:** `🔎 Trend Scout — {keyword} (last {time_range})`
- **Top Content** as a table with columns: Rank · Format · Topic · Hook · Signal · Source.
- **Winning Formats**, **Winning Hooks**, **Content Gaps** as bulleted lists.
- **Engagement Benchmark** as a short callout block, leading with the `confidence` level.
- Close with: `→ Piping this to trend-educator to explain why these are working.`

## Error Handling

- **No keyword / too vague:** Do not guess a niche. Ask: "What niche or audience should I scout? (e.g. 'home barista', 'B2B SaaS founders')" and wait.
- **No data found:** If searches return nothing usable, return `top_content: []`, set `engagement_benchmark.confidence` to `"low"`, and tell the user plainly: "I couldn't find recent public signals for this niche. Try a broader keyword or a longer `time_range`." Offer 2–3 adjacent keywords to retry.
- **Search blocked / rate-limited:** If web_search is unavailable or throttled, do not fabricate results. Report the failure, return whatever partial signals were gathered with `confidence: "low"`, and suggest the user re-run later or supply example posts manually.
- **Thin / single-source data:** If only one source supports a claim, still include it but mark `engagement_signal` conservatively and lower `confidence`. Never upgrade a qualitative band into a fake precise metric.
- **Stale results:** If the freshest results predate `time_range`, surface that mismatch ("best available data is ~6 weeks old") rather than silently presenting it as current.

## Flywheel Connections

- **Fed by:** _(entry point — this is where the weekly cycle begins; the user supplies a niche)_
- **Feeds into:** `trend-educator` — consumes this entire output object as its input and translates the data into a plain-language briefing.
- **Part of pack:** Instagram Trend Post Builder (S1 → S2 → S3).
