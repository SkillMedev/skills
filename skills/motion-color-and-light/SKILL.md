---
name: Motion Color and Light
description: Choose palette, contrast, and lighting for a moving video — not a static UI — so color survives H.264/VP9 compression, banding, and YouTube/Instagram/TikTok recompression across OLED, LCD, and projectors. Use when picking video colors, building a gradient mesh, adding glow/bloom/depth, setting a dark-background default, asking "why does my gradient band", "why is my neon muddy after upload", "is this contrast video-safe", or "what palette for my video". Do NOT use when laying out scene timing or beats — use video-storyboard instead. Do NOT use for motion-easing or animation-feel decisions — use motion-design-principles instead. Do NOT use to style or animate the on-screen text itself — use kinetic-typography instead (you give it the palette, it sets the type in motion). Do NOT use to structure a product walkthrough or decide what to show — use product-demo-director instead. Do NOT use for audio, beat-matching, or scoring — use sound-and-music-sync instead. Do NOT use to re-crop or re-spec a video for a platform's aspect ratio and safe areas — use social-video-formatter instead.
---

# Motion Color and Light

This is the taste layer for color in motion. A palette that looks immaculate as a
static Figma frame can fall apart the moment it moves and gets encoded: smooth
gradients break into visible stair-step bands, saturated neon turns to muddy
mush, and a contrast ratio that passed for UI text disappears under motion blur.
Video is not UI. The pixels you author are not the pixels the viewer sees — they
pass through H.264/VP9 compression, chroma subsampling, and a second
re-compression pass when YouTube, Instagram, or TikTok ingests the file, then land
on a screen you do not control (a bright OLED phone, a washed-out conference
projector, an old LCD).

You decide palette, contrast, and lighting so the image survives all of that.
This pairs with the `remotion-video-production` mechanics pack: you make the color
decisions here, then the resulting hex values and gradient definitions become
inputs to `remotion-compose`. Storyboard and motion decisions come from
`video-storyboard` and `motion-design-principles`; you take the locked beat list
and give it a palette.

## Workflow

Run these in order. Do not pick a gradient before you have set the background
default, because the background decides which gradients will even survive.

### Step 1 — Default to a dark background

Unless the brief demands otherwise, start on a dark, near-black background (not
pure `#000`). Dark backgrounds are the video default for real reasons:

- Glow, bloom, and light effects only read against darkness. On white, a glow has
  nothing to bloom into.
- Dark fields hide compression noise; large flat bright areas show every
  macroblock and mosquito artifact the encoder leaves behind.
- OLED screens render deep darks beautifully and save power; the look feels
  premium.

Use a very dark desaturated tint, never pure black — pure `#000000` crushes to a
flat void, kills the sense of depth, and gives bloom nothing to sit on. Reach for
something like `#0B0B0F` (the house default in `remotion-compose`) or a tinted
near-black that leans toward your accent hue (`#0A0E14` for a cool/blue brand).

When you DO want a light background (clean SaaS, editorial, daylight product
shot), commit to it fully and raise your shadows — a light video lives or dies on
soft, layered shadows, not glow.

### Step 2 — Set video-safe contrast (not UI contrast)

UI contrast targets (WCAG 4.5:1 for body text) are a floor, not the goal, and
they assume a static pixel the reader can stare at. In motion, text is on screen
for 1–3 seconds, often moving, often motion-blurred, and often recompressed to
mush. Push contrast harder:

- On-screen titles against a dark background: near-white (`#F5F6F8`), not gray.
  Gray-on-dark that passes WCAG still reads as weak and smears under motion blur.
- Avoid pure white `#FFFFFF` on pure black — the maximum-contrast edge is exactly
  where the encoder rings (haloes) and where bright text "buzzes" on cheap
  screens. Pull the white down a hair (`#F2F2F5`) and the black up (`#0B0B0F`).
- Keep critical text inside the title-safe area (≈90% of frame) — TVs and some
  players crop the edges.
- Run `contrast_check.js` (below) on every text/background pair. It reports the
  ratio AND flags the pure-white-on-pure-black ringing trap and weak gray text.

### Step 3 — Build the palette around one accent, on a dark stage

Keep it disciplined: a dark background, a near-white for text, and ONE or two
accents that do the emotional work. More than two saturated accents in motion
reads as chaos and gives the encoder more saturated edges to mangle.

- Saturated accents survive better as light (glows, strokes, small fills) than as
  large flat fills — see Step 5 on what compression does to them.
- Pick accents with enough luminance separation from the background that they pop
  without needing maximum saturation. A mid-bright accent at 80% saturation reads
  cleaner post-compression than a 100%-saturation neon.

### Step 4 — Use gradient meshes for depth, and make them survive encoding

Flat dark backgrounds can feel dead. A subtle gradient mesh — two or three soft
radial pools of color bleeding into the dark — adds depth and a sense of light
source without any literal object. This is the single highest-leverage move for
making a video look expensive. But gradients are exactly what compression
destroys via **banding**: a smooth dark-to-darker ramp gets quantized into
visible stair-step bands.

Defeat banding at author time:

- **Add noise/dither.** A faint film-grain or noise overlay (1–3% opacity)
  breaks up the banding into the encoder by giving it high-frequency detail to
  spend bits on. This is the #1 fix and costs almost nothing.
- **Keep gradient contrast low and the ramp long.** Banding is worst on subtle,
  slow ramps over a large area in the darks. A longer, gentler transition or a
  slightly higher-contrast ramp bands less visibly.
- **Bias the mesh toward the accent hue,** not gray — a gradient that carries a
  little chroma hides banding better than a pure-luminance gray ramp, because the
  encoder treats luma and chroma differently.
- Keep mesh pools soft and off-center; hand the radial-gradient CSS straight to
  `remotion-compose` as the `AbsoluteFill` background.

### Step 5 — Add glow, bloom, and depth deliberately

Light is what sells motion graphics as premium. Layer it:

- **Glow** = a colored blur behind a bright element (a text-shadow or a blurred
  duplicate). Tie the glow color to the element, on the dark stage.
- **Bloom** = the bright core blowing out slightly into its glow — fake it with a
  white-hot center fading to the accent.
- **Depth** = near-large-bright vs far-small-dim, plus a faint atmospheric haze
  (the gradient mesh) so foreground elements feel lit from somewhere.

Compression caveat: glow is a soft gradient too, so it bands and gets crushed on
low bitrate. Keep glows generous in radius (soft, wide blurs survive; tight
hard-edged ones get eaten) and lean on the Step 4 noise overlay.

### Step 6 — Predict what compression does, then verify

Before committing, mentally (and with the calculator) flag the danger zones, then
verify on a real encode — render a few seconds via `remotion-render` and watch it
at the target platform's bitrate, not just in Studio.

- Large flat saturated fills (especially reds and deep blues) → chroma
  subsampling (4:2:0) blurs the color edges and the fill goes muddy. Use the
  accent as light/stroke, not a full-frame fill.
- Subtle dark gradients → banding. Apply Step 4.
- Pure-white-on-pure-black hard edges → ringing/haloes. Apply Step 2.
- Fast motion of fine detail → the encoder spends its bitrate on motion and
  starves detail; do not put your most delicate gradient on your fastest move.

## Quality bar

The color pass is A+ only when all hold:

- Background is a tinted near-black (or a fully-committed light with layered
  shadows), never pure `#000000` or pure `#FFFFFF` as the working stage.
- Every text/background pair has been run through `contrast_check.js` and clears
  the video-safe threshold without sitting on the pure-white/pure-black ringing
  edge.
- The palette is one dark stage + one near-white + at most two accents; accents
  are used as light (glow/stroke/small fill) more than as large flat fills.
- Every gradient and glow ships with a noise/dither overlay, and the banding-prone
  ramps were checked on a real encode at the target bitrate, not only in preview.
- The deliverable is a token set (hex + gradient/glow CSS) ready to paste into
  `remotion-compose`, not a vague mood description.

## Do NOT

- Do NOT use pure `#000000` as the stage or pure `#FFFFFF` for large fills/text —
  they crush depth and ring on encode. Tint them in a hair.
- Do NOT treat WCAG UI contrast as sufficient; motion-blurred, briefly-shown text
  needs more separation than static UI text.
- Do NOT ship a smooth dark gradient or wide glow without a noise/dither overlay —
  it WILL band after upload.
- Do NOT fill the whole frame with a saturated accent; chroma subsampling turns
  big saturated fills muddy. Use saturation as light, not as paint.
- Do NOT judge color only in Remotion Studio — Studio shows your authored pixels,
  not the recompressed ones. Verify on a real `remotion-render` encode at the
  platform bitrate.
- Do NOT set scene timing or beats here — that is `video-storyboard`. Do NOT make
  the easing/animation-feel calls — that is `motion-design-principles`. You own
  color and light only.
- Do NOT animate or style the on-screen text — hand the palette to
  `kinetic-typography` and let it set the type in motion.
- Do NOT decide what the demo shows or how it is structured
  (`product-demo-director`), touch audio/beats/scoring (`sound-and-music-sync`),
  or re-crop and re-spec for a platform's aspect ratio and safe areas
  (`social-video-formatter`).

## Calculator

Self-contained Node script. Save as `contrast_check.js` and run with
`node contrast_check.js`. No dependencies. Edit the `pairs` array with your
actual text/background hex values.

```javascript
// Video-safe contrast checker. Run: node contrast_check.js
// Reports WCAG ratio plus video-specific warnings (ringing edge, weak gray).
const pairs = [
  { label: 'Title on stage',   fg: '#F2F2F5', bg: '#0B0B0F' },
  { label: 'Naive max',        fg: '#FFFFFF', bg: '#000000' },
  { label: 'Gray subtitle',    fg: '#8A8A93', bg: '#0B0B0F' },
  { label: 'Accent on stage',  fg: '#6C5CE7', bg: '#0B0B0F' },
]

const hexToRgb = (h) => {
  const n = parseInt(h.replace('#', ''), 16)
  return [(n >> 16) & 255, (n >> 8) & 255, n & 255]
}
// Relative luminance per WCAG.
const lum = (h) => {
  const c = hexToRgb(h).map((v) => {
    const s = v / 255
    return s <= 0.03928 ? s / 12.92 : Math.pow((s + 0.055) / 1.055, 2.4)
  })
  return 0.2126 * c[0] + 0.7152 * c[1] + 0.0722 * c[2]
}
const ratio = (fg, bg) => {
  const a = lum(fg), b = lum(bg)
  const hi = Math.max(a, b), lo = Math.min(a, b)
  return (hi + 0.05) / (lo + 0.05)
}
const isNearPure = (h, target) => {
  const [r, g, b] = hexToRgb(h)
  const t = target === 'white' ? 255 : 0
  return Math.abs(r - t) <= 6 && Math.abs(g - t) <= 6 && Math.abs(b - t) <= 6
}

// Video-safe floor: push past the WCAG 4.5 UI floor for moving, brief text.
const VIDEO_SAFE = 7.0

for (const p of pairs) {
  const r = ratio(p.fg, p.bg)
  const warns = []
  if (r < 4.5) warns.push('FAILS even UI contrast')
  else if (r < VIDEO_SAFE) warns.push('passes UI but weak for motion (<7:1)')
  if (isNearPure(p.fg, 'white') && isNearPure(p.bg, 'black'))
    warns.push('pure-white-on-pure-black: rings/haloes on encode — tint both in')
  console.log(
    p.label.padEnd(18),
    p.fg, 'on', p.bg,
    '-> ' + r.toFixed(2) + ':1',
    warns.length ? '  [' + warns.join('; ') + ']' : '  ok (video-safe)'
  )
}
```

### Worked example output

With the pairs above the script prints:

```
Title on stage     #F2F2F5 on #0B0B0F -> 17.58:1   ok (video-safe)
Naive max          #FFFFFF on #000000 -> 21.00:1   [pure-white-on-pure-black: rings/haloes on encode — tint both in]
Gray subtitle      #8A8A93 on #0B0B0F -> 5.74:1   [passes UI but weak for motion (<7:1)]
Accent on stage    #6C5CE7 on #0B0B0F -> 4.04:1   [FAILS even UI contrast]
```

Read it: the tinted near-white title is the clean win — high ratio, no ringing.
The naive pure-white-on-pure-black scores the highest ratio of all yet is the
WORST choice, because maximum-contrast edges are exactly where the encoder rings;
tint both ends in. The gray subtitle passes the old UI floor but smears under
motion blur — push it brighter. The accent fails as TEXT, which is fine: an accent
is for glow, strokes, and fills, never body copy on the dark stage. Use the
accent as light (Step 5), not as words.

## Template: color-and-light tokens

Copy this, fill it, and hand it to `remotion-compose` as the composition's color
props. This is the deliverable.

```
MOTION COLOR + LIGHT TOKENS. [FILL: project name]. Platform: [FILL: 16:9 / 9:16]

STAGE
  Background (tinted near-black):  #[FILL e.g. 0B0B0F]
  Stage tint leans toward:         [FILL: neutral / cool / warm / accent]

TEXT
  Primary title (near-white):      #[FILL e.g. F2F2F5]   contrast: ___:1 (>=7)
  Secondary / subtitle:            #[FILL]                contrast: ___:1

ACCENTS (max two; used as light, not full fills)
  Accent 1:                        #[FILL]   used for: [FILL: glow / stroke / fill]
  Accent 2 (optional):             #[FILL]   used for: [FILL]

GRADIENT MESH (background depth)
  radial-gradient CSS:             [FILL: e.g. radial-gradient(60% 50% at 30% 20%,
                                    #1A1340 0%, transparent 60%)]
  second pool (optional):          [FILL]
  Noise/dither overlay:            [FILL: grain PNG or CSS noise @ __% opacity]  (REQUIRED)

GLOW / BLOOM
  Glow on titles:                  [FILL: text-shadow 0 0 __px <accent @ __%>]
  Bloom core:                      [FILL: white-hot center -> accent]

COMPRESSION CHECKLIST (verify on a real remotion-render encode)
  [ ] No large flat saturated fill (chroma subsampling -> muddy)
  [ ] Every gradient/glow has the noise overlay (banding)
  [ ] No pure-white-on-pure-black hard edges (ringing)
  [ ] Checked at target platform bitrate, not just in Studio
```

## references/why-video-color-is-different

The authored pixel is not the delivered pixel. Three transforms sit between them:

1. **Codec quantization (H.264 / VP9 / AV1).** The encoder throws away detail it
   judges imperceptible to hit a bitrate. In smooth gradients it quantizes the
   subtle ramp into discrete steps — that is **banding**. Adding a touch of noise
   gives the encoder high-frequency detail to preserve, which dithers the banding
   away. This is why grain looks expensive: it is cheap insurance against banding.

2. **Chroma subsampling (4:2:0).** Almost all delivery video stores full
   luminance but only quarter-resolution color. Bright/dark detail survives; fine
   color detail and saturated color edges blur. Large flat saturated fills go
   muddy; thin saturated strokes on a dark field survive better because the eye
   reads them by luminance. Use saturation as light, not as paint.

3. **Platform recompression.** YouTube/Instagram/TikTok re-encode your upload to
   their own ladder. Your careful first encode is recompressed a second time, so
   anything marginal (a subtle gradient, a hard max-contrast edge) degrades twice.
   Always preview on the actual platform, not just your local file.

## references/dark-vs-light-stage

- **Dark stage (default).** Best for glow/bloom/depth, hides compression noise in
  the darks, premium on OLED. Risk: banding in the dark gradients (fix with
  noise) and crushed blacks if you go to pure `#000` (fix with a tinted
  near-black). This is the right default for product demos, launches, AI/dev
  tools, and anything cinematic.

- **Light stage (commit fully).** Best for clean SaaS, editorial, daylight
  product. There is no glow to lean on, so depth comes from layered, soft
  shadows and generous whitespace. Risk: big flat near-white areas show every
  macroblock — keep large light fields slightly off-white and add the faintest
  texture. Never go pure `#FFFFFF` edge to edge.

Whichever you pick, do not sit in the muddy middle. A confident dark or a
confident light reads as designed; a mid-gray stage reads as a default nobody
chose.
