---
name: Instagram Post Builder
slug: instagram-post-builder
description: Interactively builds a complete, copy-paste-ready Instagram post package - three hook variants using distinct patterns, a caption body, one clear CTA, a 15-20 hashtag set across reach tiers, alt text, and a visual direction with shot list or slide breakdown - through a one-question-at-a-time interview, optionally grounded in a trend-educator briefing. Use when someone says "write an Instagram post for my launch", "build the full post package for this reel", "put together a carousel with hooks and hashtags", or reaches the build stage after trend-educator. Do NOT use for finding what is trending - use instagram-trend-scout instead; for a single caption without the full package - use social-caption-writer; for planning a month of content - use social-content-calendar.
version: 1.0.0
stage: S3
tags: [instagram, social-media, copywriting, captions, hashtags, content-creation]
license: MIT
---

# Instagram Post Builder

**Stage: S3 - Build** (final skill in the Instagram Trend Post Builder flywheel)

This is where trend awareness becomes an actual post. It takes the trend card from `trend-educator` as *fuel*, but the post is built around the user's own message - the trend informs the packaging, it does not dictate the content. The result is a copy-paste-ready package.

The defining behavior of this skill is **conversational, one question at a time**. Do not interrogate the user with a form. Ask, listen, then ask the next thing - adapting to what they said.

## Input Schema

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `user_message` | string | yes* | - | What the user wants to say / the topic of the post. *If absent, the skill elicits it as the first question rather than failing. |
| `educator_brief` | object | no | - | The output from `trend-educator` (`trend_summary`, `why_it_works`, `opportunity_angles`, `confidence_note`). When present, the build is grounded in these trends. When absent, the skill still works in standalone mode. |
| `tone` | string | no | _(asked)_ | Desired voice (e.g. "warm", "punchy", "expert", "playful"). Elicited if not provided. |
| `audience` | string | no | _(asked)_ | Who the post is for. Elicited if not provided. |
| `cta_goal` | string | no | _(asked)_ | The action the post should drive (e.g. "save it", "comment a word", "visit link in bio", "follow"). Elicited if not provided. |

## Workflow

The skill runs as a short guided interview, then generates. **Ask exactly one question per turn** and wait for the answer before continuing.

1. **Open and orient.** If `educator_brief` is present, lead with one line referencing the opportunity it unlocks ("The scout found that contrarian Reels are landing in your niche - let's use that."). If absent, say you'll build from scratch.
2. **Q1 - The message.** If `user_message` is missing, ask: "What's the one thing you want this post to say or teach?" If it is present, reflect it back in a sentence and confirm before moving on.
3. **Q2 - The audience.** Ask who it's for and what they're struggling with. (Skip if `audience` already supplied.)
4. **Q3 - The tone.** Ask how they want to sound, offering 3 quick options anchored to their brand. (Skip if `tone` supplied.)
5. **Q4 - The goal.** Ask what one action a viewer should take after seeing it (maps to `cta_goal`). (Skip if supplied.)
6. **Q5 - Format pick.** Recommend a `format` from the `opportunity_angles` (or ask, if standalone) and confirm reel vs carousel vs single image - because it changes hook and visual direction.
7. **Confirm the brief.** Echo a one-line summary: message + audience + tone + goal + format. Ask "Build it?" Proceed on confirmation; revise if not.
8. **Generate hooks.** Write **3 distinct hook variants**, each using a different proven pattern from the trend card's `winning_hooks` where available (e.g. one curiosity-gap, one contrarian, one direct-promise). Keep each under ~12 words and front-load the stakes - on a reel, the first 3 seconds decide whether the viewer scrolls past, so the hook must land in the first frame, not after an intro.
9. **Write the caption body.** Open by paying off the chosen hook, deliver the message with one clear idea per line/short paragraph, and write in the agreed `tone` for the stated `audience`. The feed truncates captions at roughly 125 characters before the "…more" fold - the hook payoff and the reason to keep reading must fit above it. The hard ceiling is 2,200 characters; educational carousels can run long, reel captions work best well under that since the video carries the message.
10. **Write the CTA.** One clear ask aligned to `cta_goal`. Make it specific and low-friction ("comment 'GUIDE' and I'll send it" beats "let me know your thoughts").
11. **Build the hashtag set.** Produce **15-20 hashtags** mixing three tiers: a few broad (high-volume), several mid-size (niche-defining), and a few specific/long-tail (low-competition). The platform allows up to 30 per post - maxing it out reads as spam to both the algorithm and humans, which is why this skill caps at 20. Avoid banned or spammy tags. Tie them to the niche from the brief.
12. **Write the alt text.** One to two sentences describing the visual for screen-reader accessibility - literal and descriptive, not keyword-stuffed.
13. **Suggest a visual direction.** Concrete art direction for the chosen format: shot list / slide breakdown, on-screen text placement, and a first-frame or cover idea that matches the hook. For carousels, keep it to 6-10 slides - completion rate falls off past that, and the last slide should always be the CTA.
14. **Assemble and deliver** the complete post package as copy-paste blocks (see Output Format).

## Output Schema

```json
{
  "format": "string - reel | carousel | single_image | story",
  "hooks": [
    {
      "variant": "string - pattern name, e.g. 'curiosity-gap'",
      "text": "string - the hook line (on-screen text for reels, first line for static)"
    }
  ],
  "caption_body": "string - the full caption, formatted with line breaks",
  "cta": "string - the single call to action",
  "hashtags": ["string - 15 to 20 tags, '#'-prefixed, mixed reach tiers"],
  "alt_text": "string - 1 to 2 sentence accessibility description",
  "visual_direction": {
    "concept": "string - the core visual idea",
    "shot_or_slides": "string - shot list (reel) or slide-by-slide breakdown (carousel)",
    "on_screen_text": "string - text overlay guidance",
    "cover_idea": "string - first-frame / cover-slide concept tied to the hook"
  },
  "post_brief": {
    "message": "string",
    "audience": "string",
    "tone": "string",
    "cta_goal": "string"
  }
}
```

## Output Format

Deliver as labeled, copy-paste-ready blocks under the header `📦 Post Package - {format}`:

1. **Hooks (pick one)** - the 3 variants as a numbered list, each tagged with its pattern.
2. **Caption** - in a fenced block so the user can copy it cleanly, CTA included as the final line.
3. **Hashtags** - in a fenced block, space-separated, ready to paste.
4. **Alt text** - in a fenced block.
5. **Visual direction** - concept, shots/slides, on-screen text, cover idea as a short bulleted brief.
6. Close with: `Want a second angle, a different tone, or a carousel version? Say the word.`

## Error Handling

- **No user_message:** Don't stall or guess the topic - make eliciting it Q1 (Workflow step 2). The skill is designed to start empty.
- **No educator_brief (standalone use):** Proceed in standalone mode. Ask one extra question about the niche so hooks and hashtags are still targeted, and note that trend-grounding is skipped.
- **Vague answers ("idk", "anything"):** Offer 2-3 concrete options to react to rather than asking the same open question again. Reacting is easier than generating.
- **User abandons mid-interview:** Build the best package you can from what's been gathered so far, clearly mark the assumptions you filled in, and invite the user to refine any field. Never leave them empty-handed.
- **User wants edits after delivery:** Treat the package as a draft. Regenerate only the requested part (e.g. just new hooks) rather than rebuilding everything.
- **Over/under hashtag count:** Always land in the 15-20 range; if the niche is narrow, fill with adjacent and long-tail tags rather than padding with generic ones or dropping below 15.

## Do NOT

- **Do not ask multiple questions in one turn.** The one-question-at-a-time interview is the skill's defining behavior; a wall of form fields gets half-answered and the package inherits the gaps.
- **Do not bury the hook after an intro.** "Hey guys, so today I want to talk about…" is the first 3 seconds a reel cannot afford. The hook is line one, frame one, always.
- **Do not write the caption's payoff below the ~125-character fold.** If the reader has to tap "more" to find out why they should care, most never will.
- **Do not pad the hashtag set with generic mega-tags** (#love, #instagood) to hit the count. They add spam signal, not reach - fill with adjacent long-tail niche tags instead.
- **Do not keyword-stuff the alt text.** It exists for screen readers; describe the visual literally. Stuffing it trades accessibility for an SEO myth.
- **Do not let the trend dictate the message.** The `educator_brief` shapes the packaging (format, hook pattern, timing); the user's own message is the content. A post that is all trend and no message performs once and builds nothing.

## Quality bar

- Three hooks, three genuinely different patterns - not one idea reworded.
- The chosen hook's payoff sits above the caption fold.
- CTA is a single, specific, low-friction action matching `cta_goal`.
- Hashtag set lands in 15-20 with all three reach tiers represented.
- Every block is copy-paste ready - no placeholders left unfilled, no meta-commentary inside the fenced blocks.

## Flywheel Connections

- **Fed by:** `trend-educator` - consumes its `opportunity_angles`, `trend_summary`, and `why_it_works` as `educator_brief` to ground the post in working trends.
- **Feeds into:** _(end of cycle - the post ships; next week the loop restarts at `instagram-trend-scout`)._ Optionally hand the published post's performance back into `instagram-trend-scout` as a future signal.
- **Part of pack:** Instagram Trend Post Builder (S1 → S2 → S3).
