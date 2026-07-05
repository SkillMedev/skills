---
name: Video Storyboard
description: Plan a product demo, feature announcement, or launch video as a beat sheet and shot list BEFORE animating — hook in the first 3s, problem, reveal, proof, CTA — with target duration and pacing per scene type, output as a JSON scene plan that remotion-compose consumes directly. Use when someone says "storyboard my demo", "plan the video", "what scenes do I need", "plan my launch video", "map out the demo", or "break this feature into scenes" before any code. Do NOT use when the request is the animation mechanics — writing the .tsx, using interpolate/spring, registering the Composition — that is remotion-compose; do NOT use for the easing and choreography craft of a single scene — use motion-design-principles instead.
---

# Video Storyboard

Do this BEFORE a single frame is animated. A demo video lives or dies on its plan: the wrong order, a missing hook, or a 40-second runtime kills it no matter how clean the motion is. You are the craft layer that decides *what scenes exist, in what order, for how long, and what each one says* — then hands a concrete scene plan to the mechanics pack (`remotion-setup`, `remotion-compose`, `remotion-render`) to actually build.

Fire eagerly on planning intent: "storyboard my demo," "plan the video," "what scenes do I need," "map out the launch video," "break this feature into scenes." If the user jumps straight to "animate this," still run the beat sheet first — a storyboard takes two minutes and saves a re-render.

Work one example throughout: a 30-second launch video for an AI code-review tool called **Sentinel** that flags risky diffs before merge.

## Operating procedure

Run these in order. Do not write copy before the beats are fixed, and do not set durations before the beats exist — pacing is a function of structure, not the other way around.

### Step 1 — Lock the one-line objective and the platform

Before beats, pin two things:

1. **The single takeaway.** One sentence the viewer must remember: *"Sentinel catches the bug your reviewer missed."* If you cannot say it in one line, the video has no spine. Everything downstream serves this line.
2. **Platform and aspect ratio.** Landscape 16:9 (1920×1080) for a site hero or YouTube; vertical 9:16 (1080×1920) for social/Shorts; square 1:1 for feed. This sets the canvas `remotion-compose` will author and caps the runtime — social wants 15–30s, a hero loop 20–40s, a feature walkthrough up to 60s.

### Step 2 — Lay the beat sheet (the five-beat spine)

Every demo, announcement, and launch video is a version of these five beats. Drop a beat only with a reason; never reorder past the hook.

1. **Hook (first 3 seconds).** The most important 3 seconds in the video — viewers decide to keep watching here. Open on the result, the tension, or a bold claim, NOT your logo. "Your reviewer just approved a bug" beats "Introducing Sentinel." The logo can ride in the corner; it is not the hook.
2. **Problem.** Name the pain the viewer already feels, concretely. "Risky diffs slip through review at 5pm on a Friday." One beat, one pain.
3. **Reveal.** Introduce the product as the turn. This is where the name and the core mechanic land. Show the thing doing the thing.
4. **Proof.** Make the claim believable — a real before/after, a real number, a real screen. This is the beat most videos skip and the one that converts. Use real product data, never invented metrics.
5. **CTA.** One action, one URL. "Try Sentinel free → sentinel.dev." Never two asks.

For a longer walkthrough, the Reveal and Proof beats *repeat per feature* (reveal feature 1 → proof, reveal feature 2 → proof). The hook, problem, and CTA stay single.

### Step 3 — Assign a scene type and target duration to each beat

Each beat becomes one or more scenes. Scene type drives pacing — text needs reading time, a UI demo needs dwell time, a logo is fast. Use these targets (at 30fps; `durationInFrames = seconds × fps`):

| Scene type        | Target duration | Why |
|-------------------|-----------------|-----|
| Hook / title card | 2.5–3.5s        | Long enough to read one line, short enough to keep the open tight |
| Text statement    | 2.5–4s          | ~2.5 words/sec reading speed; a 7-word line needs ~3s on screen |
| UI / product demo | 4–6s            | The eye needs dwell time to parse a screen and the motion within it |
| Proof / data      | 3–5s            | A number or before/after needs a beat to register |
| Logo / CTA outro  | 2.5–4s          | Read the URL, let it breathe, end |

Pacing rule: the **hook is the fastest cut, the proof is the slowest.** If the total runs long, cut a scene — do not speed every scene up. A rushed demo reads as nervous.

### Step 4 — Write the shot list (copy + visual per scene)

For each scene, fix four fields: the beat it serves, the scene type, the on-screen copy (the *real* words — ask the user, never invent claims), and a one-line visual description that tells `remotion-compose` what to render. This is the deliverable. The visual line is a brief, not code: "terminal types out a git diff, one risky line highlights red" — `remotion-compose` decides the `interpolate`/`spring` mechanics.

### Step 5 — Emit the JSON scene plan

Run the generator below to turn the shot list into a structured `scene-plan.json`. This is the artifact that hands off: `remotion-compose` reads it as the scene plan (one `<Sequence>` per scene, `from`/`durationInFrames` already computed), and the per-scene `motion` hint is where a `motion-design-principles` or `motion-color-and-light` decision plugs in. Save it next to the project so the mechanics pack can pick it up.

### Step 6 — Hand off

Give the user the beat sheet, the shot-list table, and the JSON. Then hand off explicitly: *"Here's the scene plan. `remotion-setup` if the project isn't scaffolded yet, then `remotion-compose` will turn this JSON into the composition and `remotion-render` will export it. Want me to adjust pacing or copy before we animate?"* If the video is specifically a product demo and you want a director's eye on what to *show* on screen per scene, pair with `product-demo-director`. Kinetic title treatments go to `kinetic-typography`; the sound bed and beat-synced cuts go to `sound-and-music-sync`; a vertical re-cut for social goes to `social-video-formatter`.

## Quality bar

A storyboard is A+ only when all hold:

- The hook lands in the first 3 seconds and is NOT the logo — it opens on result, tension, or claim.
- All five beats are present, or each missing beat has a stated reason (a teaser may legitimately drop Proof).
- Every scene has a real-words copy line and a concrete visual brief — no "some UI here," no invented metrics.
- Total runtime fits the platform ceiling, and the durations follow the scene-type table (hook fast, proof slow), not a flat cut every 3s.
- The CTA is a single action with a single URL.
- The JSON validates and its summed durations equal the composition total `remotion-compose` will register — a mismatch is the #1 render surprise.

## Do NOT

- Do NOT open on a logo or a slow fade-in title — you lose the viewer before the hook.
- Do NOT invent numbers, testimonials, or product claims for the Proof beat; ask for real data or cut the beat.
- Do NOT write the `.tsx`, choose `interpolate` ranges, or set spring `damping` here — that is `remotion-compose`. This skill outputs a plan, not code.
- Do NOT pace every scene the same; a flat 3s cut makes the demo feel like a slideshow.
- Do NOT stack two CTAs or two URLs; one ask converts, two split attention.
- Do NOT let the runtime drift past the platform ceiling to fit more — cut a scene instead.

## Generator

Self-contained Node script. Save as `scene_plan.js`, edit the `beats` array for the video in question, and run `node scene_plan.js > scene-plan.json`. No dependencies. It computes frame offsets, validates the total, and emits the plan `remotion-compose` consumes.

```javascript
// Storyboard -> scene plan. Edit `meta` and `beats`, then:
//   node scene_plan.js > scene-plan.json
const meta = {
  title: 'Sentinel Launch',
  fps: 30,
  width: 1920,   // 1080 for 9:16 vertical, 1080 square
  height: 1080,  // 1920 for 9:16 vertical, 1080 square
}

// One row per scene. `seconds` follows the scene-type table.
// `motion` is a hint for motion-design-principles / motion-color-and-light;
// it is a brief, not code.
const beats = [
  { beat: 'hook',    type: 'title',   seconds: 3.0, copy: 'Your reviewer just approved a bug.', visual: 'Dark screen, line of code types in; one line snaps red.', motion: 'snappy, low damping on the red flag' },
  { beat: 'problem', type: 'text',    seconds: 3.5, copy: 'Risky diffs slip through review at 5pm on a Friday.', visual: 'Calendar/clock motif, a PR marked merged.', motion: 'calm fade, no overshoot' },
  { beat: 'reveal',  type: 'ui',      seconds: 5.0, copy: 'Sentinel reviews every diff before merge.', visual: 'Sentinel scans the same diff, surfaces the risky line with a reason.', motion: 'scan sweep, then settle' },
  { beat: 'proof',   type: 'proof',   seconds: 4.5, copy: 'Caught on real PRs. Before it shipped.', visual: 'Before/after: red flag -> fixed diff, real screenshot.', motion: 'slow cross-dissolve, hold' },
  { beat: 'cta',     type: 'cta',     seconds: 3.0, copy: 'Try Sentinel free -> sentinel.dev', visual: 'Logo + URL on brand color.', motion: 'gentle pop, end clean' },
]

const fps = meta.fps
let from = 0
const scenes = beats.map((b, i) => {
  const durationInFrames = Math.round(b.seconds * fps)
  const scene = {
    id: `${i + 1}-${b.beat}`,
    beat: b.beat,
    type: b.type,
    from,
    durationInFrames,
    seconds: b.seconds,
    copy: b.copy,
    visual: b.visual,
    motion: b.motion,
  }
  from += durationInFrames
  return scene
})

const durationInFrames = from
const totalSeconds = durationInFrames / fps
const hook = scenes[0]
const warnings = []
if (hook.beat !== 'hook') warnings.push('First scene is not the hook.')
if (hook.seconds > 3.5) warnings.push('Hook is longer than 3.5s — tighten the open.')
if (scenes.filter((s) => s.beat === 'cta').length > 1) warnings.push('More than one CTA.')
if (!scenes.some((s) => s.beat === 'proof')) warnings.push('No proof beat — claim may not convert.')
if (totalSeconds > 60) warnings.push(`Runtime ${totalSeconds}s exceeds 60s ceiling — cut a scene.`)

const plan = {
  composition: {
    id: meta.title.replace(/[^A-Za-z0-9]/g, ''),
    fps,
    width: meta.width,
    height: meta.height,
    durationInFrames, // remotion-compose registers exactly this on the <Composition>
  },
  scenes,
  meta: { totalSeconds, sceneCount: scenes.length, warnings },
}

process.stdout.write(JSON.stringify(plan, null, 2) + '\n')
if (warnings.length) console.error('WARNINGS:\n  - ' + warnings.join('\n  - '))
```

### Worked example output

With the `beats` above the script writes `scene-plan.json`:

```json
{
  "composition": {
    "id": "SentinelLaunch",
    "fps": 30,
    "width": 1920,
    "height": 1080,
    "durationInFrames": 570
  },
  "scenes": [
    { "id": "1-hook",    "beat": "hook",    "type": "title", "from": 0,   "durationInFrames": 90,  "seconds": 3.0, "copy": "Your reviewer just approved a bug.", "visual": "Dark screen, line of code types in; one line snaps red.", "motion": "snappy, low damping on the red flag" },
    { "id": "2-problem", "beat": "problem", "type": "text",  "from": 90,  "durationInFrames": 105, "seconds": 3.5, "copy": "Risky diffs slip through review at 5pm on a Friday.", "visual": "Calendar/clock motif, a PR marked merged.", "motion": "calm fade, no overshoot" },
    { "id": "3-reveal",  "beat": "reveal",  "type": "ui",    "from": 195, "durationInFrames": 150, "seconds": 5.0, "copy": "Sentinel reviews every diff before merge.", "visual": "Sentinel scans the same diff, surfaces the risky line with a reason.", "motion": "scan sweep, then settle" },
    { "id": "4-proof",   "beat": "proof",   "type": "proof", "from": 345, "durationInFrames": 135, "seconds": 4.5, "copy": "Caught on real PRs. Before it shipped.", "visual": "Before/after: red flag -> fixed diff, real screenshot.", "motion": "slow cross-dissolve, hold" },
    { "id": "5-cta",     "beat": "cta",     "type": "cta",   "from": 480, "durationInFrames": 90,  "seconds": 3.0, "copy": "Try Sentinel free -> sentinel.dev", "visual": "Logo + URL on brand color.", "motion": "gentle pop, end clean" }
  ],
  "meta": { "totalSeconds": 19, "sceneCount": 5, "warnings": [] }
}
```

Read it: 5 scenes, 570 frames = 19s at 30fps, well under the social ceiling. The hook is 3s and opens on tension, not a logo. The proof beat (4.5s) is the slowest cut; the hook and CTA (3s) are the tightest. No warnings, so it is ready for `remotion-compose` — which registers a `<Composition durationInFrames={570}>` and one `<Sequence from={…} durationInFrames={…}>` per scene, reading `from` and `durationInFrames` straight off this file.

## Template: shot-list

Use this when you want the human-readable table alongside the JSON. Replace the FILL fields with the real video.

```
VIDEO STORYBOARD. [FILL: video name]. [FILL: platform / aspect ratio]
Objective (one line):  [FILL: the single takeaway]
Target runtime:        [FILL: 15-30s social / 20-40s hero / up to 60s walkthrough]

BEAT      SCENE TYPE   DURATION   ON-SCREEN COPY (real words)        VISUAL BRIEF
Hook      title        [FILL]s    [FILL: result/tension/claim]      [FILL]
Problem   text         [FILL]s    [FILL: one concrete pain]         [FILL]
Reveal    ui           [FILL]s    [FILL: product + core mechanic]   [FILL]
Proof     proof        [FILL]s    [FILL: real number / before-after][FILL]
CTA       cta          [FILL]s    [FILL: one action + one URL]      [FILL]
  (repeat Reveal + Proof per feature for a walkthrough)

TOTAL:    [FILL]s   (must equal the JSON composition.durationInFrames / fps)
HANDOFF:  scene-plan.json -> remotion-compose
```

## references/why-the-first-3-seconds

Viewers decide whether to keep watching in the first ~3 seconds, so the hook is not the place for your logo or a slow fade. Open on the payoff or the tension: the bug that shipped, the number that dropped, the claim that stops the scroll. The logo can live in a corner the whole time; making it the opening beat spends your most valuable seconds on the least persuasive content. If you only nail one beat, nail this one.

## references/pacing-by-scene-type

Different scenes need different time on screen, and a flat cut every N seconds reads as a slideshow:

- **Title/hook** — fast. One line, snappy entrance, cut before it goes stale.
- **Text** — give reading time. People read ~2.5 words/sec; a 7-word line needs ~3s, a 12-word line ~5s. Under-timing text is the most common pacing mistake.
- **UI/product demo** — slowest entrances, longest dwell. The eye has to find the cursor, parse the screen, and follow the motion. Rushing a demo scene wastes the demo.
- **Proof/data** — hold. A number or before/after needs a beat of stillness to land; motion competing with the reveal undercuts it.
- **Logo/CTA** — read the URL, breathe, end. Do not cut on the URL before it can be read.

The whole-video rhythm: fast in (hook), vary the middle by type, slow on the proof, clean end. If the total runs long, cut a scene rather than speeding everything — a rushed demo reads as nervous, and the fix for length is fewer beats, not faster ones.

## references/handoff-to-remotion

This skill is the craft/taste layer; the `remotion-video-production` pack is the mechanics. The contract between them is `scene-plan.json`:

- `composition.durationInFrames` is exactly what `remotion-compose` registers on the `<Composition>`. Keep the summed scene durations equal to it — a mismatch causes a dead tail or an early cut at render (`remotion-render`'s #1 troubleshooting case).
- Each scene's `from` and `durationInFrames` map one-to-one to a `<Sequence from={…} durationInFrames={…}>` child.
- `copy` is the real on-screen words; `visual` is the brief `remotion-compose` turns into components; `motion` is the hint a `motion-design-principles`, `motion-color-and-light`, or `kinetic-typography` decision refines.
- Per-scene `seconds × fps` must be an integer frame count — keep durations on clean half-second boundaries (`.5s` at 30fps = 15 frames) so nothing rounds oddly.

When the plan validates with no warnings, hand off: scaffold with `remotion-setup` if needed, build with `remotion-compose`, export with `remotion-render`, then re-cut for vertical with `social-video-formatter`.
