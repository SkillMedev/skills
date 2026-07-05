---
name: Remotion Compose
description: Turns a natural-language video brief into a complete, ready-to-preview Remotion composition - extracts duration, scenes, brand colors, aspect ratio, and real copy; plans the frame budget; and writes data-driven React/TypeScript using useCurrentFrame, interpolate, spring, AbsoluteFill, and Sequence, registered in Root.tsx. Use when someone says "build a 20-second product demo video", "animate a feature walkthrough", "write the Remotion code for this marketing clip", or wants a scene-by-scene composition they can scrub in Studio. Do NOT use for rendering, iterating, or batching the finished MP4 - use remotion-render instead - or for installing and scaffolding the project - use remotion-setup instead.
---
# Remotion Compose

You turn a natural-language brief into a **complete, ready-to-preview Remotion
composition** - real React/TypeScript with frame-driven animation. The output is
a `.tsx` file in `src/compositions/`, registered in `src/Root.tsx`, that the user
can immediately scrub in Studio or hand to `remotion-render`.

Trigger on any request to create, generate, animate, or "write the video code
for" a product demo, walkthrough, feature callout, or marketing animation -
including "build a 20-second video showing X."

## Step 1 - Extract the brief

Do not start coding until you have these. Ask in one compact batch; fill obvious
gaps with sensible defaults rather than stalling:

1. **Duration** in seconds, and **fps** (default 30 - the standard for product
   and marketing video, and half the render cost of 60. Reach for 60fps only
   when animating fine cursor motion or scrolling UI where 30 visibly stutters).
2. **Scenes** - what happens, in order. Push for a beat list: "logo, then three
   features, then a CTA" beats "a demo of my app."
3. **Brand** - primary/accent/background hex colors, and the font (a
   `@remotion/google-fonts` family name).
4. **Aspect ratio / platform** - 16:9 landscape (1920×1080), 9:16 vertical
   (1080×1920) for social, or 1:1 square (1080×1080).
5. **Copy** - the actual headlines/feature names/CTA text. Never invent product
   claims; ask for the real words.

Convert seconds to frames everywhere: `durationInFrames = seconds * fps`.
Typical total lengths: 15-60s for a social/product demo, 6-15s for an ad cut.

## Step 2 - Build a scene plan

One **`<Composition>` per video**, one **component per scene**, stitched on the
timeline with `<Sequence>`. Lay out the frame budget before writing code:

```
Total: 20s @ 30fps = 600 frames
  Intro (logo)        frames   0-90    (Sequence from=0   durationInFrames=90)
  Feature 1           frames  90-240   (Sequence from=90  durationInFrames=150)
  Feature 2           frames 240-390   (Sequence from=240 durationInFrames=150)
  Feature 3           frames 390-540   (Sequence from=390 durationInFrames=150)
  CTA outro           frames 540-600   (Sequence from=540 durationInFrames=60)
```

Pacing thresholds while budgeting:

- Give any beat that carries text at least 2 seconds (60 frames at 30fps) -
  enter animation plus hold. A headline needs roughly 150-200ms of hold per
  word to be readable; a 6-word headline wants ~1s of hold *after* it lands.
- Enter animations: 10-20 frames. Cross-fades and exits: 12-15 frames. Longer
  reads as sluggish, shorter as a glitch.
- No scene shorter than ~45 frames unless it is a deliberate flash cut.

`<Sequence from={F}>` shifts a child's local clock so *inside* that component
`useCurrentFrame()` starts at 0. Animate each scene as if it begins at frame 0.

## Step 3 - Core API reference

- **`useCurrentFrame()`** - the current frame (the clock). Everything animates as
  a function of this.
- **`useVideoConfig()`** - `{ fps, width, height, durationInFrames }`.
- **`interpolate(frame, [inFrames], [outValues], opts)`** - linear mapping. Always
  pass `{ extrapolateLeft: "clamp", extrapolateRight: "clamp" }` for enter/exit so
  values do not run past their range.
- **`spring({ frame, fps, config })`** - physics-based 0→1 (overshoots slightly).
  Use for anything that should feel *alive* (pops, slides, scale-ins).
- **`AbsoluteFill`** - a full-frame absolutely-positioned div; the base layer of
  every scene.
- **`Sequence`** - time-shifts and time-bounds children (the scene scheduler).
- **`Series` / `Series.Sequence`** - back-to-back scenes without computing `from`
  offsets by hand.
- **`Img`, `staticFile`** - see Step 6.

## Step 4 - spring() vs interpolate()

| Use `spring()` when… | Use `interpolate()` when… |
|---|---|
| The motion should feel physical - pops, bounces, scale-ins, slide-ins | You need precise, linear control between exact frames |
| You want a natural settle/overshoot | Fades (opacity 0→1), color shifts, progress bars, anything timed-to-the-frame |
| Entrances of cards, logos, badges | Cross-fades and exits where overshoot would look wrong |

Pattern - drive a transform with a spring, an opacity with interpolate:

```tsx
const { fps } = useVideoConfig();
const frame = useCurrentFrame();
const pop = spring({ frame, fps, config: { damping: 200 } });   // 0 → 1, lively
const opacity = interpolate(frame, [0, 15], [0, 1], { extrapolateRight: "clamp" });
const scale = interpolate(pop, [0, 1], [0.8, 1]);
```

`damping: 200` is the workhorse config - a quick settle with barely-there
overshoot. Lower damping (~10-20) bounces visibly; use it only when bounce is
the point.

## Step 5 - Standard product-demo structure

Encode this shape unless the brief says otherwise: **logo/brand intro → N feature
callouts → CTA outro.** Make it data-driven (Step 6) so variants are free.

```tsx
// src/compositions/ProductDemo.tsx
import {
  AbsoluteFill, Sequence, useCurrentFrame, useVideoConfig,
  interpolate, spring,
} from "remotion";
import { loadFont } from "@remotion/google-fonts/Inter";

const { fontFamily } = loadFont();

export type Feature = { title: string; body: string };
export type ProductDemoProps = {
  brand: string;
  colors: { bg: string; fg: string; accent: string };
  logoText: string;
  features: Feature[];
  cta: string;
};

const Intro: React.FC<{ text: string; colors: ProductDemoProps["colors"] }> = ({ text, colors }) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();
  const s = spring({ frame, fps, config: { damping: 200 } });
  const scale = interpolate(s, [0, 1], [0.7, 1]);
  const opacity = interpolate(frame, [0, 12], [0, 1], { extrapolateRight: "clamp" });
  return (
    <AbsoluteFill style={{ backgroundColor: colors.bg, justifyContent: "center", alignItems: "center" }}>
      <h1 style={{ fontFamily, fontSize: 120, color: colors.fg, transform: `scale(${scale})`, opacity }}>
        {text}
      </h1>
    </AbsoluteFill>
  );
};

const FeatureCallout: React.FC<{ feature: Feature; colors: ProductDemoProps["colors"] }> = ({ feature, colors }) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();
  const slide = spring({ frame, fps, config: { damping: 200 } });
  const x = interpolate(slide, [0, 1], [-60, 0]);
  const opacity = interpolate(frame, [0, 15], [0, 1], { extrapolateRight: "clamp" });
  return (
    <AbsoluteFill style={{ backgroundColor: colors.bg, justifyContent: "center", paddingLeft: 160 }}>
      <div style={{ transform: `translateX(${x}px)`, opacity }}>
        <div style={{ fontFamily, fontSize: 32, color: colors.accent, fontWeight: 700, letterSpacing: 2 }}>
          {feature.title.toUpperCase()}
        </div>
        <div style={{ fontFamily, fontSize: 64, color: colors.fg, maxWidth: 1100, marginTop: 16 }}>
          {feature.body}
        </div>
      </div>
    </AbsoluteFill>
  );
};

const Outro: React.FC<{ cta: string; colors: ProductDemoProps["colors"] }> = ({ cta, colors }) => {
  const frame = useCurrentFrame();
  const opacity = interpolate(frame, [0, 18], [0, 1], { extrapolateRight: "clamp" });
  return (
    <AbsoluteFill style={{ backgroundColor: colors.accent, justifyContent: "center", alignItems: "center" }}>
      <h2 style={{ fontFamily, fontSize: 84, color: colors.bg, opacity }}>{cta}</h2>
    </AbsoluteFill>
  );
};

export const ProductDemo: React.FC<ProductDemoProps> = ({ colors, logoText, features, cta }) => {
  const intro = 90;
  const per = 150;
  const outro = 60;
  return (
    <AbsoluteFill>
      <Sequence durationInFrames={intro}>
        <Intro text={logoText} colors={colors} />
      </Sequence>
      {features.map((f, i) => (
        <Sequence key={i} from={intro + i * per} durationInFrames={per}>
          <FeatureCallout feature={f} colors={colors} />
        </Sequence>
      ))}
      <Sequence from={intro + features.length * per} durationInFrames={outro}>
        <Outro cta={cta} colors={colors} />
      </Sequence>
    </AbsoluteFill>
  );
};
```

Register it in `src/Root.tsx`, computing `durationInFrames` from the same numbers
and supplying `defaultProps`:

```tsx
<Composition
  id="ProductDemo"
  component={ProductDemo}
  durationInFrames={90 + 3 * 150 + 60}
  fps={30}
  width={1920}
  height={1080}
  defaultProps={{
    brand: "Acme",
    colors: { bg: "#0B0B0F", fg: "#FFFFFF", accent: "#6C5CE7" },
    logoText: "Acme",
    features: [
      { title: "Fast", body: "Ship in seconds, not sprints." },
      { title: "Simple", body: "One prompt, one video." },
      { title: "Yours", body: "Your brand, your copy." },
    ],
    cta: "Start free →",
  }}
/>
```

For vertical/social, set `width={1080} height={1920}`; for square, `1080×1080`.
Keep the components identical - only the canvas changes.

## Step 6 - Data-driven props & static assets

The composition above is **fully data-driven**: every word and color is a prop.
That is the point - variants (new copy, new palette, localized text) need *no
re-prompting*; just pass different props (`remotion-render` does this from a JSON
file for batches).

Static assets (logos, screenshots, audio) live in `public/assets/` and are
referenced with `staticFile()` - never an import or a raw path:

```tsx
import { Img, staticFile } from "remotion";
<Img src={staticFile("assets/logo.png")} />
```

## Step 7 - Output

Save the finished file to `src/compositions/<VideoName>.tsx`, register it in
`src/Root.tsx`, and tell the user to preview with `npx remotion studio`
(http://localhost:3000). Then hand off: *"Scrub it in Studio. Tell me what to
change - too fast, wrong color, different copy - and I'll edit and re-render
(`remotion-render`)."*

## Do NOT

- Do NOT animate with CSS transitions/animations or `setTimeout`/`setInterval`.
  Remotion renders frames independently and in parallel; anything not derived
  from `useCurrentFrame()` is invisible or inconsistent in the final render even
  though it may look fine in the browser preview.
- Do NOT use `Math.random()` for scattered/particle effects - every render
  worker gets different values and frames stop matching. Use Remotion's
  `random(seed)` for deterministic pseudo-randomness.
- Do NOT fetch data or wait on async work without `delayRender()` /
  `continueRender()` - the renderer captures the frame before the data arrives.
- Do NOT hardcode fps, width, or height inside scene components - read them from
  `useVideoConfig()` so switching 30↔60fps or landscape↔vertical doesn't break
  every timing and layout.
- Do NOT omit `extrapolateLeft/Right: "clamp"` on enter/exit interpolations -
  opacity climbs past 1 and elements drift off-canvas after their range ends.
- Do NOT invent headlines, feature claims, or CTAs - use the user's real copy,
  and ask for it if missing.

## Quality bar

- Every animated value is a pure function of `useCurrentFrame()` (plus props) -
  scrubbing backwards in Studio looks identical to playing forwards.
- The frame budget in the plan sums exactly to `durationInFrames` in
  `Root.tsx`, and no text beat holds for less than its readable minimum.
- All copy and colors arrive via typed props with `defaultProps` supplied - no
  literals buried in scene components.
- Assets load via `staticFile()`; the file registers cleanly and previews in
  Studio without console errors.
