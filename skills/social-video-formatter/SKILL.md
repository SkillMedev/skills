---
name: Social Video Formatter
description: Reframe and export a finished video for social platforms — 9:16 ↔ 16:9 cropping, burned-in captions, a platform-native first frame, loop design, and per-platform specs (aspect, length, safe areas) for TikTok, Reels, Shorts, X, and LinkedIn. Use when someone says "make this vertical", "crop to 9:16", "format for TikTok/Reels/Shorts", "add captions/subtitles", "why is my video cut off on mobile", "resize for Instagram", or "export for social". Do NOT use when the question is about the storyboard or shot order before any video exists (use video-storyboard); the animation craft of how elements move (use motion-design-principles); animated word-by-word caption styling (use kinetic-typography); palette, contrast, or lighting (use motion-color-and-light); directing the product-demo content and screen choreography (use product-demo-director); or scoring, beat-syncing, or audio (use sound-and-music-sync) — this is the craft layer that decides WHAT to format for the feed, then hands a concrete spec to remotion-compose / remotion-render to author and export.
---

# Social Video Formatter

You take a video that is already *correct* — the story, motion, and brand are
done — and make it *land on the feed*. That is a different job from making it. A
landscape demo that looks great on a laptop dies in a vertical feed: it is
letterboxed to a stripe, the caption sits under a UI button, the first frame is
black, and it does not loop. This skill fixes all five, per platform, and emits
the exact specs the mechanics pack needs.

This is the taste layer. It pairs with the **remotion-video-production** pack:
you decide the reframe, the safe areas, the caption style, and the loop here,
then hand a concrete spec to **remotion-compose** (to author the new
`<Composition>` width/height and burn captions) and **remotion-render** (to
export per platform). Do not write composition code or run renders here — name
the spec and defer.

Trigger eagerly on any request to reframe, resize, crop, verticalize, caption,
subtitle, or "format / export for social," or a named platform (TikTok, Reels,
Shorts, X/Twitter, LinkedIn) — even just "make this work on mobile."

## Workflow

Run these in order. Reframe before captions (the caption safe area depends on the
new aspect), and design the first frame and loop before you hand off to render.

### Step 1 — Establish the target platforms and pull their specs

Ask which platforms ship, then pull each one's hard constraints from the table in
`references/platform-specs`. You need four numbers per platform: aspect ratio,
resolution, max length, and the safe-area insets (the band of pixels the platform
covers with its own UI — username, caption, action rail, progress bar). Never
guess these; a caption inside the action-rail gutter is invisible on the device
and fine in preview. One source video usually fans out to two or three exports
(e.g. a 9:16 master for TikTok/Reels/Shorts plus a 1:1 or 16:9 for LinkedIn/X).

### Step 2 — Choose the reframe strategy (9:16 ↔ 16:9)

Cropping changes which pixels survive, so the subject must stay inside the new
frame. Pick a strategy per export — see `references/reframe-strategies`:

- **Re-layout (best).** Do not crop the 16:9 pixels at all — re-author the scene
  for the new canvas: stack what was side-by-side, enlarge text, move the subject
  to the vertical center. This is a `remotion-compose` change (new width/height,
  same content), and it always beats a crop. Hand off the target dimensions.
- **Center crop.** Keep the middle column of a 16:9 frame for 9:16. Fast, but it
  discards the outer thirds — only safe when the subject is centered.
- **Pad / blur-bars.** Fit the whole 16:9 frame into 9:16 with a blurred,
  scaled copy filling the top/bottom. Acceptable for talking-head or screen
  recordings; looks lazy on motion-design pieces.
- **Punch-and-track.** Crop to the subject and move the crop window over time to
  follow the action. Strongest result, most work; specify the keyframes for
  `remotion-compose`.

Going 9:16 → 16:9 is the same logic in reverse: re-layout to use the new
horizontal room rather than pillarboxing a vertical clip into a landscape stripe.

### Step 3 — Map the safe areas onto the new frame

For each export, overlay the platform's safe-area insets and keep every essential
element — captions, logo, CTA, key subject — inside the safe rectangle. The
right/bottom gutters (action rail + caption block) are the usual killers on 9:16.
Run `safe_area.js` (below) to convert the percentage insets into pixel boxes for
your exact resolution, then pass those numbers to `remotion-compose` as the
layout bounds. Treat the unsafe band as off-limits for anything that must be read.

### Step 4 — Design the platform-native first frame

The first frame is the thumbnail and the scroll-stopper; autoplay starts muted,
so frame 0 must carry meaning with zero audio. Do not open on black, on a logo
sting, or mid-fade. Open on a legible hook: the payoff result, a bold caption
line, or the subject already on screen. This is a `remotion-compose` edit to the
intro scene (or a 1–2s prepended hook). Specify exactly what frame 0 shows.

### Step 5 — Specify burned-in captions

Assume muted playback: captions are mandatory, not optional, and must be *burned
in* (rendered into the pixels), because uploaded .srt sidecars are inconsistent
and un-styleable across platforms. Specify, for `remotion-compose`: position
(inside the safe area, typically lower third but above the caption-block gutter),
style (large, high-contrast, semibold, with a stroke or solid backing for
legibility over any background), timing (phrase-level chunks synced to speech,
not a wall of text), and never more than ~2 lines on screen. For animated
word-by-word captions, defer the motion design to **kinetic-typography** and just
name the safe-area box and timing here.

### Step 6 — Design the loop

Feeds replay clips instantly, so a clean loop multiplies watch time. Decide one:

- **Seamless loop** — last frame matches first frame (same composition,
  position, color) so the replay is invisible. Best for short ambient/brand
  loops.
- **Hook-back loop** — end on a line that sends the viewer back to the start
  ("…and that's step one"), making the rewatch intentional.
- **Hard stop** — a definitive CTA end card; correct for longer narrative pieces
  where a loop would feel broken.

For a seamless loop, the open and close are a `remotion-compose` constraint
(match frame 0 and the last frame). Name which loop type and the matching frames.

### Step 7 — Emit the export spec and hand off

Fill the `export-spec` template below — one block per platform. That spec is the
briefing for the mechanics pack: **remotion-compose** authors a `<Composition>`
per aspect with the safe-area layout, first frame, and burned captions; then
**remotion-render** exports each (a 9:16 export is a Composition authored at
1080×1920, per remotion-render's vertical preset). Do not render here — deliver
the spec.

## Quality bar

An export is A+ only when all hold:

- Every platform export names a real aspect, resolution, max length, AND
  safe-area box — pulled from the spec table, never guessed.
- No essential element (caption, logo, CTA, subject) sits in an unsafe gutter on
  any target platform; verified against the `safe_area.js` boxes.
- The reframe is a re-layout or tracked crop where the subject would otherwise be
  lost — not a blind center crop or lazy blur-bar pad on a motion piece.
- Frame 0 reads with sound off: a hook, result, or subject — never black, never
  a bare logo, never mid-fade.
- Captions are burned in, inside the safe area, phrase-chunked, ≤2 lines, high
  contrast.
- The loop type is chosen on purpose, and a seamless loop's first/last frames are
  specified to match.
- The output is a spec handed to remotion-compose / remotion-render — this skill
  authored no composition code and ran no render.

## Do NOT

- Do NOT scaffold the project, write composition code, or run the render here —
  name the spec and defer to remotion-setup / remotion-compose / remotion-render.
- Do NOT center-crop a 16:9 source to 9:16 when the subject is off-center; you
  will crop the subject out. Re-layout or track instead.
- Do NOT place captions, CTAs, or logos in the right/bottom gutters where the
  platform's action rail and caption block cover them.
- Do NOT rely on uploaded .srt/.vtt sidecar captions for social; burn them into
  the pixels so they always render and you control the style.
- Do NOT open on black, a logo sting, or a fade — frame 0 is the thumbnail.
- Do NOT ship one 16:9 file to every platform; vertical feeds letterbox it to a
  stripe. Export per aspect.
- Do NOT exceed a platform's max length expecting it to "still post" — it gets
  truncated or rejected; trim to the limit in the cut.
- Do NOT design word-by-word caption animation here — that is kinetic-typography;
  only specify the safe box and timing.
- Do NOT plan the storyboard or shot order here — if the video does not exist
  yet, that is video-storyboard; this skill reframes a video that is already cut.
- Do NOT tune how elements move (easing, spacing, follow-through) here — that is
  motion-design-principles; this skill only names the new canvas and bounds.
- Do NOT pick the palette, contrast, or lighting for the reframe here — defer
  those choices to motion-color-and-light.
- Do NOT direct the product-demo content or screen choreography here — that is
  product-demo-director; this skill assumes the content is locked.
- Do NOT design the score, beat-sync, or sound here — captions assume muted
  autoplay, but audio is sound-and-music-sync's job.

## Related skills

This is one craft skill in a video pack; defer to a sibling when the request is
really about its concern, and hand the format spec to the mechanics skills:

- **video-storyboard** — plan beats and shot order before a video exists. Comes
  *before* this skill.
- **product-demo-director** — direct the demo content and on-screen choreography.
- **motion-design-principles** — the easing, spacing, and movement craft of the
  animation; lean on it when re-laying-out a frame for a new aspect.
- **motion-color-and-light** — palette, contrast, and lighting decisions.
- **kinetic-typography** — animated word-by-word caption motion (you name the
  safe box and timing; it designs the animation).
- **sound-and-music-sync** — score and beat-sync; the audio layer this skill
  deliberately does not touch.
- **remotion-compose** / **remotion-render** — the mechanics that consume this
  skill's export spec to author the per-aspect `<Composition>` and export the MP4.

## Calculator

Self-contained Node script. Save as `safe_area.js` and run with
`node safe_area.js`. It converts each platform's percentage safe-area insets into
pixel boxes for that platform's resolution, so you can hand exact layout bounds to
remotion-compose. No dependencies. Edit the `platforms` block as specs change.

```javascript
// Social safe-area calculator. Run: node safe_area.js
// Insets are the % of each edge the platform UI covers (top/right/bottom/left).
// Right + bottom are large on 9:16 because of the action rail and caption block.
const platforms = [
  { name: 'TikTok',         w: 1080, h: 1920, top: 6,  right: 11, bottom: 17, left: 3 },
  { name: 'Reels',          w: 1080, h: 1920, top: 7,  right: 12, bottom: 18, left: 3 },
  { name: 'Shorts',         w: 1080, h: 1920, top: 5,  right: 12, bottom: 13, left: 3 },
  { name: 'X (in-feed)',    w: 1080, h: 1080, top: 4,  right: 4,  bottom: 12, left: 4 },
  { name: 'LinkedIn',       w: 1080, h: 1350, top: 4,  right: 4,  bottom: 10, left: 4 },
]

function box(p) {
  const insetX = (pct) => Math.round((pct / 100) * p.w)
  const insetY = (pct) => Math.round((pct / 100) * p.h)
  const x = insetX(p.left)
  const y = insetY(p.top)
  const safeW = p.w - insetX(p.left) - insetX(p.right)
  const safeH = p.h - insetY(p.top) - insetY(p.bottom)
  return { x, y, safeW, safeH }
}

for (const p of platforms) {
  const b = box(p)
  console.log(
    p.name.padEnd(14),
    `${p.w}x${p.h}`.padEnd(11),
    'safe box -> x:' + String(b.x).padEnd(4),
    'y:' + String(b.y).padEnd(4),
    'w:' + String(b.safeW).padEnd(5),
    'h:' + b.safeH
  )
}
```

### Worked example output

```
TikTok         1080x1920   safe box -> x:32   y:115  w:929   h:1479
Reels          1080x1920   safe box -> x:32   y:134  w:918   h:1440
Shorts         1080x1920   safe box -> x:32   y:96   w:918   h:1574
X (in-feed)    1080x1080   safe box -> x:43   y:43   w:994   h:907
LinkedIn       1080x1350   safe box -> x:43   y:54   w:994   h:1161
```

Read it: on TikTok, lay out every must-read element inside x 32→961, y
115→1594. The right gutter (the action rail: like, comment, share) eats the last
~119px, and the bottom (username + caption) eats ~326px — anything there is
covered on the device though it looks fine in Studio preview. Pass these as the
layout bounds to remotion-compose; on a 1080×1920 vertical Composition, position
captions and CTA inside the box, not at the raw frame edges.

## Template: export-spec

Fill one block per platform. This is the briefing remotion-compose and
remotion-render consume — paste the safe-area numbers from `safe_area.js`.

```
SOCIAL EXPORT SPEC. [FILL: video name]. Source: [FILL: 16:9 master, 1920x1080]

PLATFORM: [FILL: TikTok / Reels / Shorts / X / LinkedIn]
  Aspect / resolution:   [FILL: 9:16 / 1080x1920]
  Max length:            [FILL: e.g. <= 60s in-feed]
  Safe box (px):         x:[FILL] y:[FILL] w:[FILL] h:[FILL]   (from safe_area.js)
  Reframe strategy:      [FILL: re-layout / center crop / blur-pad / punch-and-track]
    -> remotion-compose: [FILL: new Composition WxH + layout notes / crop keyframes]
  First frame (frame 0): [FILL: the muted hook shown at 0 — never black/logo/fade]
  Captions:              burned-in, [FILL: position in safe box], <=2 lines,
                         phrase-chunked, [FILL: style — size/contrast/backing]
  Loop:                  [FILL: seamless / hook-back / hard stop]
    -> if seamless:      frame 0 matches last frame at [FILL: position/color]
  Render:                remotion-render [FILL: preset, e.g. 1080x1920 H.264 MP4]

(repeat the block for each target platform)
```

## references/platform-specs

Verify against each platform's current creator docs before shipping; these are
the stable shape, but limits drift. Aspect and safe areas matter more than exact
max-length.

- **TikTok** — 9:16, 1080x1920. In-feed sweet spot is short (under ~60s),
  uploads can run much longer. Heavy right action rail and a tall bottom caption
  block; keep must-read content high and centered. Sound-on culture, but still
  caption for accessibility and muted autoplay.
- **Instagram Reels** — 9:16, 1080x1920. Up to ~90s. Similar right/bottom UI to
  TikTok plus the profile/audio row at the bottom. The in-grid crop is 1:1, so
  the center must read as a square thumbnail too.
- **YouTube Shorts** — 9:16, 1080x1920. Up to 60s (anything longer is a regular
  video, not a Short). Bottom title/description and right action column; top is
  relatively clean.
- **X / Twitter** — 16:9 or 1:1 in-feed; 1:1 (1080x1080) is the safest scroll
  format because it claims more vertical feed height than 16:9 without going full
  vertical. Keep length short; engagement drops fast past ~45s. Minimal platform
  chrome over the video itself.
- **LinkedIn** — 1:1 (1080x1080) or 4:5 (1080x1350) for maximum feed real
  estate; 16:9 also supported. Professional, often-muted, scroll context —
  burned captions and a text-forward first frame matter most here. Keep under a
  couple of minutes.

The one-to-many rule: author a single 9:16 master that serves
TikTok/Reels/Shorts (their safe areas mostly overlap — design to the tightest,
which is Reels' bottom inset), and a separate 1:1 or 4:5 master for X/LinkedIn.
Two source layouts cover all five platforms.

## references/reframe-strategies

When you must change aspect, in order of quality:

1. **Re-layout.** The motion-design answer. The 16:9 frame had room to spread out
   horizontally; the 9:16 frame has room to stack vertically. Re-author the scene
   to the new canvas — bigger type, stacked elements, subject centered in the new
   frame. No pixels are thrown away because you are not cropping; you are
   re-composing. This is the only strategy that makes a piece look *designed for*
   the platform rather than *squeezed into* it. Hand remotion-compose the new
   width/height and the re-layout notes. For the re-layout's spacing and visual
   balance, lean on motion-design-principles.
2. **Punch-and-track.** When re-layout is impractical (e.g. a screen recording),
   crop to the subject and animate the crop window to follow the action over
   time. Specify crop keyframes (which region of the source is visible at which
   timestamps) for remotion-compose.
3. **Center crop.** Keep the middle column. Only safe when the subject is dead
   center the whole time; it silently discards the outer thirds.
4. **Blur-pad / pillarbox.** Scale the whole frame to fit and fill the gaps with
   a blurred enlarged copy. Acceptable for talking-head or screen content, weak
   for designed motion — it signals "reposted from landscape." Last resort.

General rule: re-layout when you control the composition (you do — it's a
Remotion project), crop/track only when re-layout is impossible. Never blind
center-crop a piece whose subject moves.

## references/first-frame-and-loop

**First frame.** Autoplay is muted and the first frame is the thumbnail, so frame
0 does double duty: stop the scroll and make the topic legible without sound.
Strong frame-0 patterns: the end result shown up front ("here's the finished
thing"), a bold caption stating the payoff, the subject already mid-action, or a
provocative question on screen. Anti-patterns: opening on black, a logo sting, an
empty stage, or a cross-fade in progress — all read as "nothing here" and get
scrolled. If the natural intro is slow, prepend a 1–2s hook in remotion-compose.

**Loop.** A feed replays a clip the instant it ends, so a deliberate loop buys
free extra watch time. Seamless: make the last frame identical to the first
(position, scale, color) so the replay is invisible — ideal for short brand or
ambient loops. Hook-back: end on a line or visual that motivates a rewatch (a
question, a "watch again for the detail you missed"). Hard stop: a clear CTA end
card, correct when the piece tells a complete story and a loop would feel like a
glitch. Pick on purpose; for a seamless loop, tell remotion-compose exactly which
elements frame 0 and the final frame must share.
