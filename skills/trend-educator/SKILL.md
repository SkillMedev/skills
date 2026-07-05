---
name: Trend Educator
description: Turns a raw Instagram trend-scout report into a plain-language briefing that teaches WHY each trend is working - the algorithm mechanics, audience psychology, and format dynamics behind it - and surfaces specific opportunity angles as a screenshot-ready trend card. Use when someone asks "explain these trends to me", "why is this format blowing up", "turn this trend report into a briefing", or has instagram-trend-scout output and needs to understand it before creating. Do NOT use to gather the trend data itself - use instagram-trend-scout instead; do NOT use to draft the actual post - use instagram-post-builder instead; do NOT use for statistical trend detection in business or product data - use trend-analysis instead.
---

# Trend Educator

Data alone does not change what a creator makes - understanding does. This skill converts a structured instagram-trend-scout report into a short, jargon-free briefing the user can internalize: after reading the trend card, they could explain why a format works to a friend and reuse the underlying principle after the specific trend fades. The costly mistake it prevents is blind imitation - copying a trending format without its mechanism, which produces content that mimics the surface and misses the engine.

The rule throughout: teach, do not dump. Every claim ties back to a mechanism - how the ranking system rewards it, what it does to the viewer's attention, or how the format itself carries the message. No buzzword ships without a plain-English unpacking.

## Operating procedure

### Step 1: Gather and validate inputs

- scout_report (required): the full output object from instagram-trend-scout - top_content, winning_formats, winning_hooks, content_gaps, engagement_benchmark, plus the keyword and time range. If it is missing, empty, or malformed, stop and say: "There is no trend report to explain yet - run instagram-trend-scout first, or paste its output here." Never invent trends.
- depth (default "standard"): "quick" = summary plus top 2 explanations; "standard" = full card; "deep" = extra mechanism detail per trend.
- audience_literacy (default "beginner"): "beginner" = define everything; "intermediate" = skip basics. Controls how much jargon gets unpacked.

If engagement_benchmark confidence is low, lead the briefing with that caveat and frame explanations as hypotheses ("this is likely working because…"), not certainties.

### Step 2: Pick the teachable trends

Select the 3-5 strongest signals across winning_formats and winning_hooks, prioritizing items that recurred (frequency of common or dominant) and have corroborating top_content examples. If depth is "quick", take the top 2. If the report is thin (1-2 signals), teach what is there honestly, note that the briefing is narrow, and suggest re-scouting with a broader keyword - never pad.

### Step 3: Write the trend summary

3-5 bullets stating, in plain language, what is working in this niche right now. Each bullet is one sentence a busy creator reads in five seconds. Translate metric bands into meaning ("saves are the signal that matters here, not likes") - no metrics dumps.

### Step 4: Explain WHY for each trend

For every selected trend, name three forces in plain English:

- Algorithm factor - what the ranking system mechanically rewards ("rewatches and saves push it to non-followers"), stated as a mechanism, never mystically.
- Psychology factor - what it does to the viewer ("an open loop makes the brain need the resolution, so they finish the video").
- Format mechanics - how the format itself carries attention ("text-on-screen lets it work muted in-feed").

Close each with a transferable principle: the durable lesson that outlives this specific trend. Unpack inline any term a beginner would not know.

### Step 5: Derive opportunity angles

Combine the scout's content_gaps with the explained trends into specific, do-able angles the user could own. Each angle pairs what to say with the format to say it in and why it should land, and is tagged if it came from a content gap.

### Step 6: Assemble the trend card

One screenshot-keepable card, sized to depth: the summary, the whys, the angles, and a closing confidence note restating the scout's confidence so the user weighs the briefing accordingly.

### Step 7: Jargon pass

Re-read the whole card. If a sentence does not tie to a mechanism, cut it. If a term would stump a beginner, define it inline or replace it. If the user asks to skip the teaching, give the one-line quick summary and hand straight off - but keep the opportunity angles intact so the next stage still has direction.

## Worked artifact: one trend-card block

Bad (buzzword dump - what this skill must never output):

> Carousels are crushing it right now. The algorithm loves them. Lean into value-driven storytelling and optimize for saves to boost your engagement.

Good (mechanism-taught block, one per trend on the card):

> **"Mistake-first" carousels** - the first slide names a common mistake ("You're warming up wrong"), slides 2-6 fix it.
> - Algorithm: each swipe is a re-engagement signal, and unfinished carousels get re-served - Instagram gives the post a second impression on the same viewer.
> - Psychology: naming a mistake triggers self-check ("wait, do I do that?"), which is a stronger open than a promise of tips.
> - Format: one idea per slide means it works at skim speed, and the last slide's summary is what makes people save it.
> - *Principle: open by threatening a habit the viewer already has, not by promising value they can't picture.*

Card layout: title ("Trend Card - {keyword}"), "What's working now" (summary bullets), "Why it works" (one block per trend as above), "Where your opening is" (numbered angles, each tagged with format: reel / carousel / single image / story), "Read with this in mind" (confidence note), and a closing handoff line: "Ready to build a post? Tell instagram-post-builder what you actually want to say."

## Deliverable

Produce a single trend card containing: 3-5 plain-language summary bullets, a why-it-works block per trend (algorithm, psychology, format, transferable principle), numbered opportunity angles each with a format and rationale, and a confidence note - sized to the requested depth and clean of unexplained jargon.

## Do NOT

- Do not fabricate trends when the scout report is missing or thin - an invented briefing is worse than none, because the user will build on it.
- Do not dump metrics; translate every number into what it means for what the creator should make.
- Do not attribute performance to the algorithm mystically ("the algorithm likes it") - name the mechanical signal being rewarded.
- Do not ship a claim without one of the three mechanisms attached.
- Do not let low-confidence scout data read as certainty; carry the caveat into the framing of every explanation.
- Do not skip the transferable principle - the specific trend fades; the principle is what the user paid for.

## Quality bar

- Every explained trend has all three mechanism lines plus a transferable principle.
- A beginner could read the card without hitting one undefined term.
- Every opportunity angle names a concrete thing to make, its format, and why it should land now.
- The confidence note matches the scout's actual confidence level.
- Nothing on the card fails the "could the user explain this to a friend?" test.

## Neighbors

This is the teach stage of the Instagram trend flywheel: it consumes instagram-trend-scout output and feeds instagram-post-builder. For statistical trend detection in business data, use trend-analysis. For hook writing beyond Instagram, social-hook-generator; for scheduling what the angles become, creator-content-calendar.
