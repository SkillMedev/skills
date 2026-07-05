---
name: Remotion Setup
description: Scaffolds a new Remotion video project wired for Claude Code Agent Skills - Node check, create-video scaffold, skills install, folder conventions, Google Fonts, and a smoke-test render. Use whenever someone wants to start making videos with Remotion and Claude, even just "make product videos with Claude."
---
# Remotion Setup

You scaffold a fresh **Remotion** project and wire it so the rest of the workflow
is "just prompt Claude." This is the one-time environment work. Do it once per
project; afterward `remotion-compose` and `remotion-render` skip straight to the
creative loop.

Trigger eagerly. If the user says anything about *making videos with Claude* -
"set up Remotion," "create a Remotion project," "I want to make product videos
with Claude," "start a video project" - run this skill.

## Step 1 - Verify the toolchain

Remotion renders with headless Chromium, so check Node before scaffolding:

```bash
node --version   # need >= 16; prefer an active LTS release
```

If Node is older than 16, stop and tell the user to upgrade (nvm: `nvm install --lts && nvm use --lts`).
Also confirm `npx` is available (ships with npm 5.2+).

## Step 2 - Scaffold the project

```bash
npx create-video@latest
```

This is interactive. Recommend the **Blank** (Hello World) template - it is the
cleanest base for product videos. The CLI asks for a project directory and a
package manager; let the user pick, then `cd` into the new folder and install:

```bash
cd <project-name>
npm install
```

## Step 3 - Install the Remotion Agent Skills

So Claude Code has Remotion's own skill context available in this repo:

```bash
npx remotion skills add remotion
```

This drops Remotion's agent-skill files into the project. If the command is
unavailable in the installed Remotion version, point the user at a newer
`@remotion/cli` and continue - it is a convenience, not a hard dependency.

## Step 4 - Establish folder conventions

Standardize the layout so later skills always know where things go:

```
src/
  Root.tsx              # registers every <Composition> (the entry registry)
  index.ts              # registerRoot(RemotionRoot)
  compositions/         # one .tsx file per video - remotion-compose writes here
public/
  assets/               # images, logos, audio - referenced via staticFile()
```

Create the folders if the template did not:

```bash
mkdir -p src/compositions public/assets
```

`src/Root.tsx` is the registry. Every video is a `<Composition>` registered here:

```tsx
// src/Root.tsx
import { Composition } from "remotion";
import { HelloWorld } from "./compositions/HelloWorld";

export const RemotionRoot: React.FC = () => {
  return (
    <Composition
      id="HelloWorld"
      component={HelloWorld}
      durationInFrames={90}
      fps={30}
      width={1920}
      height={1080}
    />
  );
};
```

```ts
// src/index.ts
import { registerRoot } from "remotion";
import { RemotionRoot } from "./Root";
registerRoot(RemotionRoot);
```

Sensible defaults to register with: 30 fps and 1920x1080 for landscape product
videos (1080x1920 for vertical social), and short durations - 90 frames at
30 fps is 3 seconds, and most product demo videos land between 15 and 60
seconds (450-1800 frames).

## Step 5 - Load a Google font

Remotion has first-class Google Fonts so text renders deterministically. Install
and load once:

```bash
npm install @remotion/google-fonts
```

```tsx
// load near the top of a composition file
import { loadFont } from "@remotion/google-fonts/Inter";
const { fontFamily } = loadFont();
// then use: <div style={{ fontFamily }}>...</div>
```

`loadFont()` blocks the render until the font is ready, so on-screen text never
flashes in an unstyled fallback. Pick the font that matches the brand - swap
`Inter` for `Poppins`, `Roboto`, etc.

## Step 6 - Smoke-test composition

Write a trivial composition to prove the environment renders end to end:

```tsx
// src/compositions/HelloWorld.tsx
import { AbsoluteFill, useCurrentFrame, interpolate } from "remotion";
import { loadFont } from "@remotion/google-fonts/Inter";

const { fontFamily } = loadFont();

export const HelloWorld: React.FC = () => {
  const frame = useCurrentFrame();
  const opacity = interpolate(frame, [0, 30], [0, 1], {
    extrapolateRight: "clamp",
  });
  return (
    <AbsoluteFill style={{ backgroundColor: "#0B0B0F", justifyContent: "center", alignItems: "center" }}>
      <h1 style={{ fontFamily, color: "white", fontSize: 90, opacity }}>
        It works.
      </h1>
    </AbsoluteFill>
  );
};
```

Make sure it is registered in `src/Root.tsx` (Step 4), then render it:

```bash
npx remotion render src/index.ts HelloWorld out/smoke-test.mp4
```

The first render downloads headless Chromium (a few hundred MB - expect a one-time
wait of a few minutes on a normal connection); after that, this 90-frame smoke test
should render in well under a minute. If `out/smoke-test.mp4` plays a fading-in
title, the environment is good. If the render fails, it is almost always
(a) Chromium download/permissions, (b) the composition not registered in
`Root.tsx`, or (c) a Node version issue from Step 1.

## Step 7 - Launch Remotion Studio for preview

For the interactive preview (scrub the timeline, hot-reload on edits):

```bash
npx remotion studio
```

This serves the Studio at **http://localhost:3000**. The `create-video` template
also wires `npm run dev` to the same thing. Leave Studio running while iterating -
edits to composition files hot-reload instantly.

## Deliverable

The user walks away with a working Remotion project: the scaffolded repo with the
`src/compositions/` + `public/assets/` layout in place, Remotion agent skills
installed, a Google font wired, a registered smoke-test composition, a rendered
`out/smoke-test.mp4` that actually plays, and Remotion Studio running for preview.
Nothing is "set up" until the smoke-test MP4 exists and plays.

## Do NOT

- Do not skip the Node check and scaffold anyway - a sub-16 Node fails deep inside the Chromium render with a confusing error, not at install time.
- Do not declare setup done without rendering the smoke test; a project that compiles but has never produced an MP4 has not proven the Chromium pipeline works.
- Do not put compositions or assets in ad-hoc locations - later skills (`remotion-compose`, `remotion-render`) rely on `src/compositions/` and `public/assets/` existing.
- Do not reference assets by relative path or URL; use `staticFile()` from `public/`, or renders will work locally and break elsewhere.
- Do not use system or CSS-imported fonts for on-screen text - without `loadFont()` blocking, text can render in a fallback font on the render machine.
- Do not pick a feature-heavy template for product videos; the Blank template avoids carrying demo code the user must delete later.

## Quality bar

- `node --version` is >= 16 and was checked before scaffolding.
- `out/smoke-test.mp4` exists and plays a fading-in title.
- The folder conventions exist exactly as specified, and the smoke composition is registered in `src/Root.tsx`.
- Text renders through `@remotion/google-fonts` with `loadFont()`, not a system fallback.
- Remotion Studio launches at localhost:3000 and hot-reloads an edit.

## Handoff

Once the smoke test renders and Studio is up, tell the user the project is ready
and hand off: *"Setup is done. Describe the video you want - duration, scenes,
brand colors - and I'll write the composition (`remotion-compose`), then render
and iterate (`remotion-render`)."*
