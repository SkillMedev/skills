---
name: Kinetic Typography
description: Put text in motion the right way — title cards, animated captions, lower-thirds, callouts, and word-by-word reveals — with a reading-time-per-word budget so copy holds long enough to read, plus enter/exit timing, weight and size transitions, and type hierarchy in motion. Use when someone asks to "animate this text", "make a title card", "animated captions/subtitles", "kinetic typography", "animate the headline", "lower-third", "word-by-word reveal", "text that pops in", or "how long should this line stay on screen". Do NOT use when the question is the cut, pacing, or easing curve of non-text motion — use motion-design-principles instead; do NOT use to plan which scenes or shots exist — use video-storyboard instead; do NOT use to resize or crop a caption track for a specific platform (9:16 TikTok, 1:1 feed) — use social-video-formatter instead; do NOT use for the color, contrast, or lighting of the type against its background — use motion-color-and-light instead.
---

# Kinetic Typography

Text in motion has one job before it has any other: it must be readable. The most common failure in animated type is not ugliness, it is text that flashes past before the viewer finishes the first word, or text that animates so much the eye chases the motion instead of reading the words. This is the craft layer. It decides *how long every line stays, how it enters and leaves, and what is allowed to move* — and hands a concrete frame budget to `remotion-compose`, which writes the React/TypeScript that actually renders it.

The governing rule: **animate to serve the read, never to decorate it.** Motion earns its place only when it directs the eye, establishes hierarchy, or marks a beat. Everything below enforces that.

Work one running example throughout: a 16:9 product title card reading **"Ship video in one prompt"** (5 words), at 30fps, plus an animated lower-third caption track underneath.

## Workflow

Run these in order. Do not pick an entrance animation before you have the hold budget — the budget is what tells you whether you even have room to animate.

### Step 1 — Compute the reading-time-per-word budget first

Text must stay on screen long enough to read at a relaxed pace, *plus* the time its entrance and exit consume. Budget hold time before you touch any animation.

- Baseline reading speed for on-screen video is slower than print: assume **~3.5 words/second** read comfortably (≈ 200 wpm). Caption/subtitle convention is similar — never drop below ~0.3s of readable hold per word.
- **Minimum on-screen hold = words ÷ 3.5 seconds**, with a **floor of 0.8s** for any line (even a single word needs a beat to register) and a **+0.3s tax** for unfamiliar terms, numbers, or names the eye cannot predict.
- Entrance and exit time is *additional* — it does not count as reading time, because the text is not fully legible mid-transition. Total slot = entrance + hold + exit.

Run `type_budget.js` (below) to turn a line of copy into a frame budget. For the title card it returns a hold of ~43 frames; that is the number you hand to `remotion-compose` as the Sequence's readable window, with entrance/exit added on top.

### Step 2 — Choose enter/exit timing to match the cut, not a default

Entrances and exits are punctuation, not the sentence. Keep them short and let the hold do the work.

- **Entrance: 8–15 frames (0.25–0.5s) @ 30fps.** Fast enough to feel snappy, slow enough to read as deliberate. A title card can take the long end; a fast caption track takes the short end.
- **Exit: equal to or shorter than the entrance.** Exits should never linger — once read, text should clear. 6–12 frames.
- **Match the exit to what follows.** Hard cut to the next scene → exit the text *on* the cut (no fade, let the cut do it). Cross-dissolve → fade text out over the dissolve. Never animate an elaborate exit into a hard cut; it fights the edit. This is the seam where `motion-design-principles` (the cut and easing) and this skill meet — take the easing curve from there, apply it to the text here.
- **Stagger multi-element reveals**, do not animate them in unison. A title + subtitle reads best with the subtitle entering 4–8 frames after the title settles, so the eye is led top-to-bottom.

### Step 3 — Pick ONE entrance technique per line and animate one property

The more properties move at once, the harder the text is to read mid-transition. Default to animating a single property; combine two only with intent (e.g. opacity + a *small* translate).

- **Fade (opacity 0→1)** — safest, always readable, the right default for caption tracks and body lines.
- **Rise / slide (small translate, 20–60px, paired with fade)** — adds direction and life; the workhorse for title cards. Keep the distance small — large slides make the eye track travel instead of read.
- **Word-by-word reveal** — each word fades/rises in sequence on its own short stagger. Use for emphasis lines and lyric-style pieces, *not* for dense captions (the read gets choppy). Budget the whole line's reveal to finish well inside the hold.
- **Scale / pop (spring from 0.85→1)** — energetic, good for a single hero word or a number callout. Never scale a full sentence; the reflow is unreadable.
- **Mask / wipe reveal** — premium, directional, great for a single headline. More expensive to build; reserve for the one title that matters.

Whatever you pick, the property animates; **the text itself never reflows mid-animation.** Animate `opacity` and `transform` (translate/scale/clip) — never `font-size`, `width`, or letter-spacing in a way that re-lays-out the glyphs, because layout-driven animation jitters and is unreadable.

### Step 4 — Animate weight and size as transitions, not noise

Weight and size are how type expresses emphasis in motion — but they are *transitions between held states*, not continuous wobble.

- **Weight transition:** a word can settle from a lighter to a heavier weight to land emphasis (e.g. 400→700 over 8 frames as it settles). Use a real variable-font weight axis or two cuts of the family; never fake bold by scaling.
- **Size transition:** a number or key word can scale up *once* to claim hierarchy, then hold. Scale-and-hold, not scale-and-pulse. A looping pulse on text reads as a broken render, not emphasis.
- These are accents. At most **one** weight/size accent per line, on the single most important word. If everything emphasizes, nothing does.

### Step 5 — Set type hierarchy in motion (order, size, weight, timing)

Hierarchy in motion is built from four signals; use them together so the eye knows where to look and in what order.

1. **Order of entrance** — the first thing to animate in is read first. Lead with the headline, follow with the subtitle, end with the CTA.
2. **Size contrast** — keep a clear ratio (headline ≥ 1.6× subtitle). Motion does not rescue weak static hierarchy.
3. **Weight contrast** — heavier = more important. Two weights max on screen at once.
4. **Timing** — the most important line gets the longest hold and, if anything, the entrance with the most presence. The least important (a persistent lower-third, a watermark) does not animate after its single entrance.

Fill the `motion-type-spec` template (below) with the per-line budget, technique, and hierarchy role. That filled spec *is* the handoff to `remotion-compose`.

### Step 6 — Decide where NOT to animate

The senior move is restraint. Before finalizing, cut animation from anything in the "Do NOT" list below. A title card with one well-budgeted rise reads better than one where every word does something.

## Quality bar

A finished motion-type spec is A+ only when all hold:

- **Every line passes the reading-time budget** — hold ≥ `max(0.8s, words ÷ 3.5 + term tax)`, computed by `type_budget.js`, with entrance/exit *added on top*, never subtracted from it.
- **Each line animates one property** (two only with explicit intent), and **no line reflows its glyphs** mid-animation — `opacity`/`transform` only.
- **Entrances are 8–15 frames, exits ≤ entrance**, and the exit is matched to the following edit (cut vs. dissolve), not defaulted.
- **Hierarchy reads in order**: entrance order, size ratio (≥1.6×), and weight all point to the same most-important line.
- **At most one weight/size accent per line**, and nothing loops or pulses.
- **The spec names the frame numbers** (entrance in, hold start, hold end, exit out) so `remotion-compose` can write Sequences without guessing.

## Do NOT

- Do **not** ship text that holds shorter than the reading budget — the single most common kinetic-type failure. If there is not room to hold it, cut words, not hold time.
- Do **not** animate `font-size`, `width`, `letter-spacing`, or anything that re-lays-out glyphs; the layout thrash is unreadable. Scale via `transform` instead.
- Do **not** animate more than one or two properties per line, and never word-by-word a dense caption — the read goes choppy.
- Do **not** loop, pulse, bounce repeatedly, or keep text in continuous motion; motion that does not stop cannot be read.
- Do **not** animate every element. Lower-thirds, watermarks, persistent labels, and legal text get one entrance and then hold still. Do not animate body/caption text with elaborate entrances — fade is correct.
- Do **not** decide the underlying easing curve or the scene cut here — that is `motion-design-principles`. Do **not** write the Remotion component here — hand the filled spec to `remotion-compose`.
- Do **not** invent the copy. Animate the words the user gave you; if a line is too long to hold, ask them to shorten it.

## Sibling skills — hand off, do not absorb

This skill owns *text timing and the read*. Adjacent decisions belong to siblings; pull from them, do not redo their work here.

- **`motion-design-principles`** — the cut, the pacing of non-text motion, and the *easing curve* (the acceleration shape). Take the curve from there and apply it to the text here.
- **`video-storyboard`** — which scenes and shots exist, and in what order. Get the scene list first; this skill only times the text *within* a scene.
- **`motion-color-and-light`** — the color, contrast, and legibility of the type against its background. If text is hard to read because of color, that is a job for that skill, not a timing change here.
- **`product-demo-director`** — the overall narrative and beat list of a product demo. It decides *what* the title cards say and when they land; this skill times *how* each one animates.
- **`sound-and-music-sync`** — landing text reveals on a beat or downbeat. Hand it the `holdStart@` / entrance frame numbers so a reveal can be snapped to the track.
- **`social-video-formatter`** — resizing and cropping the caption track for a platform (9:16 TikTok, 1:1 feed, safe areas). Author the timing once here; let that skill reflow it per platform.

## Calculator

Self-contained Node script. Save as `type_budget.js` and run with `node type_budget.js`. No dependencies. Edit the `lines` array with your actual copy; it returns the frame budget for each line at your fps.

```javascript
// Kinetic-type reading budget. Edit lines + fps, then: node type_budget.js
const fps = 30
const WORDS_PER_SEC = 3.5   // comfortable on-screen read (~200 wpm)
const FLOOR_SEC = 0.8       // minimum hold for any line, even one word
const TERM_TAX_SEC = 0.3    // per line containing a number/name/jargon term
const ENTRANCE_FRAMES = 12  // 0.4s entrance
const EXIT_FRAMES = 9       // exit <= entrance

const lines = [
  { text: 'Ship video in one prompt', hasUnfamiliarTerm: false }, // title card
  { text: 'Rendered headless in 4K',  hasUnfamiliarTerm: true  }, // caption: '4K'
  { text: 'Go',                       hasUnfamiliarTerm: false }, // hero word
]

const sec2frames = (s) => Math.ceil(s * fps)

function budget(line) {
  const words = line.text.trim().split(/\s+/).length
  const raw = words / WORDS_PER_SEC + (line.hasUnfamiliarTerm ? TERM_TAX_SEC : 0)
  const holdSec = Math.max(FLOOR_SEC, raw)
  const holdFrames = sec2frames(holdSec)
  const totalFrames = ENTRANCE_FRAMES + holdFrames + EXIT_FRAMES
  return { words, holdSec, holdFrames, totalFrames }
}

let cursor = 0
for (const line of lines) {
  const b = budget(line)
  const enterIn = cursor
  const holdStart = enterIn + ENTRANCE_FRAMES
  const holdEnd = holdStart + b.holdFrames
  const exitOut = holdEnd + EXIT_FRAMES
  console.log(`"${line.text}" (${b.words}w)`)
  console.log(`  hold:        ${b.holdSec.toFixed(2)}s = ${b.holdFrames} frames`)
  console.log(`  total slot:  ${b.totalFrames} frames (enter ${ENTRANCE_FRAMES} + hold ${b.holdFrames} + exit ${EXIT_FRAMES})`)
  console.log(`  Sequence:    enter@${enterIn}  holdStart@${holdStart}  holdEnd@${holdEnd}  exitOut@${exitOut}`)
  cursor = exitOut
}
console.log(`Total track length: ${cursor} frames (${(cursor / fps).toFixed(2)}s)`)
```

### Worked example output

```
"Ship video in one prompt" (5w)
  hold:        1.43s = 43 frames
  total slot:  64 frames (enter 12 + hold 43 + exit 9)
  Sequence:    enter@0  holdStart@12  holdEnd@55  exitOut@64
"Rendered headless in 4K" (4w)
  hold:        1.44s = 44 frames
  total slot:  65 frames (enter 12 + hold 44 + exit 9)
  Sequence:    enter@64  holdStart@76  holdEnd@120  exitOut@129
"Go" (1w)
  hold:        0.80s = 24 frames
  total slot:  45 frames (enter 12 + hold 24 + exit 9)
  Sequence:    enter@129  holdStart@141  holdEnd@165  exitOut@174
Total track length: 174 frames (5.80s)
```

Read it: the 5-word title needs 43 frames of *held, fully-legible* time — and that is on top of the 12-frame entrance and 9-frame exit, so its real slot is 64 frames. The 4-word caption looks shorter but carries a number (`4K`), so the term tax pushes its hold to 44 frames — slightly longer than the title despite fewer words. The single word `Go` hits the 0.8s floor: even one word gets a beat. Those `enter@ / holdStart@ / holdEnd@ / exitOut@` frame numbers drop straight into `remotion-compose` as `<Sequence from={…} durationInFrames={…}>` boundaries — no guessing about how long text should stay up.

## Template: motion-type-spec

Fill this and hand it to `remotion-compose`. One block per text line.

```
MOTION TYPE SPEC. [FILL: composition name].  fps: [FILL]  canvas: [FILL: 1920x1080 / 1080x1920]

LINE 1
  Copy (verbatim):     [FILL]
  Hierarchy role:      [FILL: headline / subtitle / caption / lower-third / CTA / hero-word]
  Size:                [FILL]px        Weight: [FILL: 400 / 700 / variable axis]
  Reading budget:      [FILL]w -> hold [FILL]s = [FILL] frames   (from type_budget.js)
  Entrance:            [FILL: fade / rise+fade / word-by-word / pop / wipe]  over [FILL: 8-15] frames
  Exit:                [FILL: fade / cut-on-edit]  over [FILL: <=entrance] frames
  Property animated:   [FILL: opacity / opacity+translateY / scale]   (NO font-size/width)
  Accent (max one):    [FILL: none / weight 400->700 on word "X" / scale-up on "X"]
  Frame numbers:       enter@[FILL]  holdStart@[FILL]  holdEnd@[FILL]  exitOut@[FILL]

LINE 2
  (repeat)

HIERARCHY CHECK
  Entrance order leads to:   [FILL: which line reads first]
  Size ratio headline:sub:   [FILL: >= 1.6:1]
  Exit matched to edit:      [FILL: cut / dissolve] after this block
  Lines that do NOT animate:  [FILL: watermark / lower-third label / legal]
```

## references/reading-budget

Why ~3.5 words/second and not faster: print reading runs 250–300 wpm, but on-screen text competes with moving imagery, the viewer is often watching rather than reading, and there is no scroll-back. Broadcast caption standards land around 160–200 wpm for exactly this reason. 3.5 w/s (≈200 wpm) is a comfortable ceiling; go slower for an older audience, a second-language audience, or copy dense with numbers and names.

The entrance/exit-is-extra rule is the part people get wrong. During a fade-in, the text is at partial opacity and not yet readable; during an exit it is leaving. So reading time is the *held* window only, and the entrance and exit are added to it, never carved out of it. A line that needs 43 frames to read and has a 12-frame entrance occupies at least 55 frames before its exit — budget the whole slot.

The term tax exists because the eye predicts familiar words and stalls on unpredictable ones. Numbers (`4K`, `$2,400`, `2026`), proper nouns, and jargon cannot be guessed from context, so they cost extra dwell. Add the tax once per line that contains any.

## references/what-moves-what-holds

A simple decision rule for whether a line should animate at all:

- **Animate (one entrance):** the headline, a hero word or number, a CTA, a scene-establishing title. These mark beats and lead the eye.
- **Fade only:** caption/subtitle tracks, body copy. They appear and clear; an elaborate entrance on a caption is noise.
- **One entrance, then hold dead still:** lower-thirds, name/title labels, watermarks, logos, legal text. They are reference, not performance. A lower-third that keeps moving steals attention from the speaker.
- **Never animate continuously:** nothing on screen should be in perpetual motion if it carries words. Looping, pulsing, drifting, or bouncing text cannot be read and signals a broken render.

When in doubt, fade. A clean fade at the right hold length beats a clever entrance at the wrong one every time.

## references/handoff-to-remotion

This skill produces numbers and decisions; it does not write code. The filled `motion-type-spec` is the contract handed to `remotion-compose`:

- The `enter@ / holdStart@ / holdEnd@ / exitOut@` frame numbers become `<Sequence from={…} durationInFrames={…}>` boundaries.
- "Entrance: rise+fade over 12 frames" becomes an `interpolate(frame, [0, 12], …)` on `opacity` plus a `spring`/`interpolate` on `translateY` — `remotion-compose` owns the `interpolate` vs `spring` choice.
- "Property animated: opacity+translateY (NO font-size)" tells `remotion-compose` to drive `transform`, never a layout property.
- The easing *curve* (the shape of the acceleration) comes from `motion-design-principles`; this spec only says how long and which property.

Do the budget and the spec here. Let `remotion-compose` turn it into a `.tsx` composition, and `remotion-render` export it. Keep the layers separate: taste and timing here, mechanics there.
