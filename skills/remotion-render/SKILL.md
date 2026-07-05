---
name: Remotion Render
description: Renders a Remotion composition to MP4 and runs the edit-and-re-render loop - CLI render commands, 1080p/4K/9:16 presets, concurrency tuning, codec selection, batch variants from a JSON data file, and Lambda for cloud rendering. Use when someone says "render this Remotion video", "export the composition to MP4", "the animation is too fast, fix and re-render", "batch render one video per customer", or "move rendering to Lambda". Do NOT use for authoring or restructuring scenes, animations, or components - use remotion-compose instead; do NOT use for project scaffolding and initial setup - use remotion-setup instead. This skill owns everything from a finished composition to a delivered file.
---
# Remotion Render

Drive the render-and-iterate loop: turn a composition into an MP4, take natural-language feedback, make the smallest edit that satisfies it, and re-render. The costly failure mode is the opposite loop - rewriting whole scene files for a copy tweak, or re-rendering at full quality for every check - which destroys the approved work and the iteration speed that makes Remotion worth using.

## Operating procedure

### Step 1: Gather inputs

- Composition ID (the `id` on the `<Composition>` in `src/Root.tsx`, not a filename) and the entry point (the file calling `registerRoot`, typically `src/index.ts`).
- Target: platform/aspect (landscape 16:9, vertical 9:16, square), resolution (1080p default; 4K only on request), and whether transparency is needed.
- Machine cores (for concurrency) and whether this is one render, an iteration session, or a batch.

### Step 2: The base render

```bash
npx remotion render src/index.ts <CompositionId> out/video.mp4
```

Default output is H.264 MP4 at the composition's own width/height/fps; Remotion creates `out/` if needed.

### Step 3: Resolution, fps, and codec presets

Resolution and fps live on the `<Composition>` (width/height/fps) - change them at the source. Use flags only for scaling and container:

```bash
# 1080p landscape - Composition authored 1920x1080@30
npx remotion render src/index.ts ProductDemo out/1080p.mp4

# 4K - upscale the 1080p canvas without touching layout
npx remotion render src/index.ts ProductDemo out/4k.mp4 --scale=2

# 9:16 vertical - render a Composition authored at 1080x1920
npx remotion render src/index.ts ProductDemoVertical out/vertical.mp4
```

Codec rules:

- Social/web delivery stays H.264 MP4 (the default) - maximum compatibility.
- Transparency: `--codec=vp8` or `vp9` (WebM), or `--codec=prores` with alpha for editing pipelines. H.264 cannot carry alpha.
- True 60fps means `fps={60}` on the Composition with frame counts scaled to match - a flag-only fps change desyncs every animation timed in frames.

### Step 4: Tune concurrency

Rendering is CPU-bound (each frame is a headless-Chromium screenshot). Default is roughly half the cores; explicit tuning is the easiest local speedup:

```bash
npx remotion render src/index.ts ProductDemo out/video.mp4 --concurrency=8
```

Start at the number of physical cores (`--concurrency=50%` accepts a percentage); back off if the machine swaps or thermal-throttles - past the sweet spot, more Chromium tabs make it slower, not faster.

### Step 5: Run the iterative refinement loop

Keep Studio open (`npx remotion studio`, http://localhost:3000) so the user previews instantly; reserve full renders for sign-off.

1. User previews in Studio and gives feedback in plain language.
2. Translate it to a surgical edit - change only the relevant value:
   - "Too fast" → widen the `interpolate` input range or raise spring `damping`.
   - "Logo should pop more" → lower spring `damping` / add overshoot.
   - "Change the CTA copy" → edit the prop / `defaultProps`, touch nothing else.
   - "Different accent color" → change one hex in the colors object.
   - "Hold the last feature longer" → bump that `<Sequence>`'s `durationInFrames` AND re-derive the Composition's total `durationInFrames`.
3. Re-render, or let Studio hot-reload for the visual check.

Surgical edit vs rewrite: edit in place when the change is a value, prop, color, or one scene's timing; rewrite only when scene *structure* changes (adding/removing/reordering scenes - and route structural authoring to remotion-compose). Default to surgical: it preserves everything already approved.

### Step 6: Batch variants

The composition is data-driven, so N variants = N prop sets in a JSON file:

```json
[
  { "out": "out/acme.mp4",   "props": { "logoText": "Acme",   "colors": { "accent": "#6C5CE7" }, "cta": "Try Acme" } },
  { "out": "out/globex.mp4", "props": { "logoText": "Globex", "colors": { "accent": "#00B894" }, "cta": "Try Globex" } }
]
```

`--props` takes inline JSON or a file path; for more than a couple of variants, write a small Node script that reads the JSON and shells out per entry, or use `@remotion/renderer`'s `renderMedia()` for a typed loop. Localize or A/B copy and colors entirely by editing the JSON.

### Step 7: Lambda when local is the bottleneck

Reach for Lambda when a single render takes minutes locally or the batch is large - it fans frames out across serverless workers. Otherwise local is simpler and free.

```bash
npx remotion lambda functions deploy            # one-time per AWS account/region
npx remotion lambda sites create src/index.ts --site-name=product-demo
npx remotion lambda render product-demo ProductDemo --props='{"logoText":"Acme"}'
```

Requires AWS credentials and IAM permissions per Remotion's Lambda setup.

### Step 8: Troubleshoot the three common failures

1. **Frame-count mismatch** - animation cuts off early or the clip has a dead tail. Cause: a `<Sequence>` duration changed but the Composition total did not. Fix: make the total equal the sum of its sequences; recompute after every timing edit. This is the #1 render surprise.
2. **Missing `staticFile()` reference** - render fails or an asset is blank. Cause: an asset imported by path or outside `public/`. Fix: put it in `public/assets/` and reference `staticFile("assets/<name>")`.
3. **Font flash/fallback** - text renders in the wrong font or shifts. Cause: `@remotion/google-fonts` `loadFont()` missing or called too late. Fix: call `loadFont()` at module top level and apply the returned `fontFamily`; the render then waits for the font.

## Deliverable

A rendered file in `out/` at the requested preset, plus - for iteration sessions - the loop offer: name the tweakable dimensions (pacing, copy, color, timing) and re-render on feedback. For batches, the variants JSON and the per-variant outputs.

## Do NOT

- Do NOT rewrite a scene file for a value-level change - surgical edits preserve approved work.
- Do NOT change fps via flags alone - set it on the Composition and rescale frame counts.
- Do NOT change a Sequence duration without re-deriving the Composition's total `durationInFrames`.
- Do NOT full-render every iteration - Studio hot-reload is the preview loop; render at sign-off.
- Do NOT push concurrency past physical cores on a machine that is swapping or throttling.
- Do NOT reach for Lambda when local renders finish in seconds - it adds AWS setup and cost for nothing.
- Do NOT author new scenes or restructure the composition here - that is remotion-compose.

## Quality bar

- The output plays at the requested resolution/aspect/fps with no dead tail and no early cutoff (Composition total matches sequence sum).
- Assets and fonts render correctly on the first frame - no fallback-font flash, no blank assets.
- An iteration changed only the lines the feedback required; everything previously approved is untouched.
- Batch outputs differ only by their prop sets.

## Escalation

Scene authoring, animation design, and component structure go to remotion-compose; project scaffolding to remotion-setup; scoring and audio timing to sound-and-music-sync.
