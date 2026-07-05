---
name: Product Demo Director
description: Direct the craft of putting a real software UI on screen - cursor choreography, zoom/pan/callout language, screen-recording vs recreated-UI, and making state changes legible with highlights, focus pulls, and slow-downs. Use when filming or recreating an app for a product demo, feature walkthrough, onboarding clip, or release reel and asking "how do I show this click", "where should the camera zoom", "should I screen-record or rebuild the UI", "why does my demo feel confusing", "how do I make the state change readable", or "how do I move the cursor". Do NOT use for the underlying motion grammar (easing, timing) - use motion-design-principles; for the beat-by-beat plan - use video-storyboard; for animating words on screen - use kinetic-typography; for the color/light of highlights and glows - use motion-color-and-light; for sound design or a click/whoosh hit - use sound-and-music-sync; for reframing the demo to 9:16 or 1:1 - use social-video-formatter; for the React/MP4 mechanics of building it - use remotion-compose and remotion-render.
---

# Product Demo Director

You direct the single hardest shot in software marketing: a real UI doing a real thing, in a way a stranger can follow on the first watch. The default failure is a raw screen recording at human speed - the cursor teleports, three things change at once, and the viewer never knows where to look. Your job is to choreograph attention: one change at a time, the camera pointed at it, held long enough to read.

This is the taste layer. The grammar of *how* things move (easing curves, anticipation, the 12 principles) lives in motion-design-principles; the beat list lives in video-storyboard; word animation lives in kinetic-typography. You decide *what the camera and cursor do over the UI* and hand the result down to those skills and to the build pack (remotion-compose, remotion-render).

Work one example throughout: a 12-second clip showing a user turn on a feature flag in a settings dashboard and watch a metric tile light up.

## Workflow

Run these in order. Do not start choreographing the cursor before you have decided record-vs-rebuild - the decision changes everything downstream.

### Step 1 - Decide: screen-record or recreate the UI

This is the fork. Pick before any other work.

- **Screen-record** when the UI is real, stable, and the demo's credibility depends on "this is the actual product." Fastest to capture; you trade away control. You cannot fix a janky cursor, a slow network spinner, real customer data, or a font that anti-aliases badly at zoom. Record at 2x the final resolution so you have room to push in without mush.
- **Recreate the UI** (rebuild the relevant screens as components) when you need a clean cursor path, deterministic timing, fake-but-honest data, partial-product states that do not exist yet, or pixel-perfect zoom. More work; total control. This is the path that hands to remotion-compose, where each screen becomes a component and the cursor becomes an animated layer.
- **Hybrid** (common and underused): screen-record the *texture* (real scrolling, real video playing inside the app), then composite recreated callouts, cursors, and highlights *on top* so the choreography is still authored. Use when the product is real but the raw recording is too messy to direct.

Write the decision down with its reason. See references/record-vs-recreate for the full tradeoff table.

### Step 2 - Reduce to one change per beat

A demo is legible only if exactly one thing changes at a time and the viewer is looking at it when it does. List every state change in the flow (focus moves, a field fills, a toggle flips, a tile updates, a toast appears). If two happen on the same frame, you must either separate them in time or subordinate one (dim it, push it out of the zoom). "Turn on the flag" is not one beat - it is: cursor arrives at toggle → toggle flips → metric tile recomputes → confirmation toast. Four beats, four held reads.

### Step 3 - Choreograph the cursor

The cursor is the narrator. Treat it like an actor, not a mouse log.

1. **Move with intent, arc slightly.** Never teleport, never jitter. The cursor accelerates out of rest and decelerates into the target (ease-in-out - borrow the curve from motion-design-principles). A faint arc reads as more deliberate than a ruler-straight line.
2. **Settle before acting.** Arrive, pause ~6-10 frames, *then* click. The pause is the "I am about to do this" beat the eye needs.
3. **Show the click.** A real click is invisible. Add a visible affordance: a quick scale-down/up on the cursor, a radial ripple at the click point, or a brief press state on the target. One of these, not all three.
4. **One cursor, one path.** If the flow needs the cursor in two places, cut - do not whip it across the frame. A hard cut reads cleaner than a 4-frame dash.
5. **Retire the cursor when it is not the subject.** When a tile is updating and the cursor is just parked, fade it out or move it off the zoom. A static cursor competing with the real subject splits attention.

### Step 4 - Direct the camera: zoom, pan, callout

The frame is a camera over the UI. Use a consistent move vocabulary so the viewer learns your language:

- **Push in (zoom)** to commit to a region before the change happens - arrive at the toggle *before* it flips. The push is anticipation; the change is the payoff.
- **Pan/track** to follow the cursor between two regions that are too far apart to hold in one frame. Keep pans slow and motivated by the cursor - the camera follows the actor.
- **Pull out (zoom out)** to re-establish context after a detailed beat, so the viewer re-anchors before the next region.
- **Callouts** (a highlight ring, a label, a connector line, a spotlight that dims everything else) point at the thing words would otherwise have to name. Prefer a callout over narration when the UI element is on screen.

Rule: the camera should already be looking at the element *before* it changes. If the toggle flips and *then* you zoom to it, you missed the moment.

### Step 5 - Make the state change legible

The whole demo exists for the state changes. Each one gets deliberate treatment:

- **Slow the world down on the key change.** Hold a normal pace through navigation, then stretch the actual flip/update to ~1.3-1.6x its real duration so the eye registers cause→effect. Speed-ramp back up after.
- **Highlight the before/after delta.** Pulse the changed element (a glow, a brief color wash, a scale bump). For a number changing, count it up rather than hard-cutting the value - the motion *is* the message.
- **Pull focus.** When the metric tile updates, dim or blur everything else for ~10 frames so the tile is unambiguously the subject, then bring the room back.
- **Sequence cause and effect with a beat between them.** Toggle flips (hold), *then* tile updates (hold). If they fire on the same frame the viewer cannot tell the toggle *caused* the tile.

### Step 6 - Lint the shot list, then hand off

Run the linter (below) on your beat list to catch the classic legibility failures - overlapping changes, missing holds, cursor teleports, a camera that arrives late. Fix every flag. Then hand the directed shot list down: timing/easing specifics to motion-design-principles, the beat ordering to video-storyboard, and the actual implementation (cursor layer, zoom transforms, component screens) to remotion-compose for build and remotion-render for the iterate loop.

## Quality bar

The demo is A+ only when all hold:

- The record-vs-recreate decision is written down with its reason, and the production honors it (no janky raw recording smuggled into a "clean" rebuild).
- Exactly one change is the subject per beat; any simultaneous change is deliberately subordinated (dimmed or out of frame), never accidental.
- Every click shows a visible affordance, and the cursor settles before it acts - no teleports, no jitter, no whip-pans.
- The camera is already framed on each element *before* that element changes.
- Every state change gets at least one legibility treatment (slow-down, highlight, focus pull, or count-up) and a held read after it.
- The shot list passes the linter with zero flags.

## Do NOT

- Do not ship a raw, real-time screen recording as the demo - direct it, or it is footage, not a demo.
- Do not let two state changes land on the same frame; the viewer cannot assign cause and effect.
- Do not teleport, jitter, or whip-pan the cursor; if it must be in two places, cut.
- Do not zoom to an element *after* it changes - the camera arrives first, always.
- Do not narrate what a callout could point at on screen.
- Do not specify exact easing curves or frame-by-frame timing here - that is motion-design-principles; do not write the beat order from scratch - that is video-storyboard; do not animate on-screen words here - that is kinetic-typography; do not write the React/Remotion code or render - that is remotion-compose and remotion-render.

## Linter

Self-contained Node script. Save as `demo_lint.js` and run with `node demo_lint.js`. No dependencies. Describe your demo as an ordered list of beats; the linter flags the classic legibility failures from the workflow. Edit the `beats` array for your demo.

```javascript
// Product-demo shot-list linter. Run: node demo_lint.js
// Each beat is one moment on the timeline. Fields:
//   t          start time in seconds (ordered, ascending)
//   dur        how long the beat is held, in seconds
//   changes    UI state changes that happen on this beat (0, 1, or more)
//   camera     'push' | 'pan' | 'pull' | 'hold'  (camera move into this beat)
//   framedOn   the element the camera is pointed at when the beat starts
//   cursorTo   where the cursor moves this beat (null = stays / not the subject)
//   click      true if a click happens this beat
//   clickAffordance  true if the click is made visible (ripple/press/scale)
//   legibility 'slowdown' | 'highlight' | 'focuspull' | 'countup' | null
const beats = [
  { t: 0.0, dur: 1.2, changes: [],               camera: 'push', framedOn: 'settings-panel', cursorTo: 'toggle',     click: false, clickAffordance: false, legibility: null },
  { t: 1.2, dur: 0.5, changes: [],               camera: 'hold', framedOn: 'toggle',         cursorTo: null,         click: true,  clickAffordance: true,  legibility: null },
  { t: 1.7, dur: 1.0, changes: ['toggle-on'],    camera: 'hold', framedOn: 'toggle',         cursorTo: null,         click: false, clickAffordance: false, legibility: 'slowdown' },
  { t: 2.7, dur: 1.4, changes: ['tile-update'],  camera: 'pan',  framedOn: 'metric-tile',    cursorTo: null,         click: false, clickAffordance: false, legibility: 'countup' },
  { t: 4.1, dur: 0.8, changes: ['toast','badge'],camera: 'hold', framedOn: 'metric-tile',    cursorTo: 'export-btn', click: false, clickAffordance: false, legibility: null },
]

const HOLD_MIN = 0.4 // a state change needs at least this long to read
const flags = []
for (let i = 0; i < beats.length; i++) {
  const b = beats[i]
  const id = `beat ${i + 1} (t=${b.t}s)`
  // 1. One change per beat.
  if (b.changes.length > 1)
    flags.push(`${id}: ${b.changes.length} changes at once (${b.changes.join(', ')}) - split or subordinate all but one`)
  // 2. Every change gets a held read.
  if (b.changes.length === 1 && b.dur < HOLD_MIN)
    flags.push(`${id}: change "${b.changes[0]}" held only ${b.dur}s - hold >= ${HOLD_MIN}s so it reads`)
  // 3. Every change gets a legibility treatment.
  if (b.changes.length >= 1 && !b.legibility)
    flags.push(`${id}: change "${b.changes[0]}" has no legibility treatment (slowdown/highlight/focuspull/countup)`)
  // 4. Clicks must be visible.
  if (b.click && !b.clickAffordance)
    flags.push(`${id}: click has no visible affordance - add a ripple/press/scale`)
  // 5. Camera frames the element BEFORE it changes, not on the change beat.
  if (b.changes.length >= 1 && (b.camera === 'push' || b.camera === 'pan'))
    flags.push(`${id}: camera moves (${b.camera}) on the same beat the change happens - arrive first, change second`)
  // 6. No cursor teleport: a click beat whose cursor just arrived has no settle.
  if (b.click && b.cursorTo)
    flags.push(`${id}: cursor moves AND clicks on the same beat - settle (~6-10 frames) before clicking`)
}

if (flags.length === 0) console.log('PASS - shot list is legible.')
else {
  console.log(`${flags.length} flag(s):`)
  for (const f of flags) console.log('  - ' + f)
}
```

### Worked example output

With the beats above the script prints:

```
3 flag(s):
  - beat 4 (t=2.7s): camera moves (pan) on the same beat the change happens - arrive first, change second
  - beat 5 (t=4.1s): 2 changes at once (toast, badge) - split or subordinate all but one
  - beat 5 (t=4.1s): change "toast" has no legibility treatment (slowdown/highlight/focuspull/countup)
```

Read it: the toggle click (beats 2-3) is clean - the cursor settles, the click has a ripple, the flip is slowed down and held. But beat 4 pans to the metric tile *while* it updates, so the camera arrives late on the payoff; fix it by panning during beat 3's hold so the tile is framed before it recomputes. And beat 5 is doing two wrong things at once: it fires a toast *and* a badge on the same frame, and the toast has no legibility treatment. Subordinate the badge (dim it, or push it outside the zoom) so the toast is the single subject, and give the toast a highlight or hold. Fix all three and the linter passes.

## Template: demo-shot-card

Copy this, fill the FILL fields, and feed the result to the linter, then down to the sibling skills.

```
PRODUCT DEMO SHOT CARD. [FILL: feature name] - [FILL: length]s

PRODUCTION DECISION
  Record / Recreate / Hybrid:   [FILL]
  Reason:                       [FILL: why this path]
  Capture resolution:           [FILL: 2x final if recording]

SUBJECT OF THE CLIP (one sentence the viewer should take away)
  [FILL]

BEAT SHEET (one change per row; camera arrives before the change)
  #  TIME   CAMERA   FRAMED ON        CURSOR          CHANGE            LEGIBILITY        HOLD
  1  [FILL] [push]   [FILL]           [moves to FILL] -                 -                 [FILL]s
  2  [FILL] [hold]   [FILL]           [settles+click] -                 -                 [FILL]s
  3  [FILL] [hold]   [FILL]           [parked/fade]   [FILL: the flip]  [slowdown]        [FILL]s
  4  [FILL] [pan]    [FILL]           [off-frame]     [FILL: the effect][countup/glow]    [FILL]s
  (add rows; never two changes in one row)

CALLOUT LANGUAGE (kept consistent across the clip)
  Highlight style:              [FILL: ring / spotlight-dim / connector]
  Click affordance:             [FILL: ripple / press / cursor-scale]
  Focus-pull treatment:         [FILL: dim others / blur others]

HANDOFF
  Easing + timing detail ->     motion-design-principles
  Beat order / story arc ->     video-storyboard
  On-screen words ->            kinetic-typography
  Color + light of highlights ->motion-color-and-light
  Sound design / hit on click ->sound-and-music-sync
  Reframe for 9:16 / 1:1 ->     social-video-formatter
  Build (screens, cursor, zoom)->remotion-compose, then remotion-render
```

## references/record-vs-recreate

| Dimension | Screen-record | Recreate the UI |
|---|---|---|
| Setup cost | Low - capture the real app | High - rebuild screens as components |
| Cursor control | None (real mouse path) | Total (animated cursor layer) |
| Timing control | None (real latency, spinners) | Deterministic per-frame |
| Data | Real (may need redaction) | Fake-but-honest, fully chosen |
| Zoom quality | Limited by capture res | Pixel-perfect at any zoom |
| Credibility | "This is the real product" | Needs to look real to be honest |
| States you don't have yet | Impossible | Easy (mock the future state) |
| Best for | Stable, shippable, credibility-critical | Pre-launch, messy UI, control-critical |

The honesty line: a recreated UI must represent behavior the product actually has (or will have at ship). Never recreate a state the product cannot reach - that is a fabricated demo, not a directed one. When in doubt, hybrid: record the real texture, author the choreography on top.

## references/legibility-toolkit

The five moves that make a state change readable, fastest to reach for first:

1. **Hold.** Do nothing but keep the camera on the change for a beat. The cheapest legibility tool and the most skipped.
2. **Slow-down.** Stretch the key transition to ~1.3-1.6x real duration; speed-ramp the boring parts to compensate so total length holds.
3. **Focus pull.** Dim or blur everything that is not the subject for ~10 frames. Forces the eye; relax it back so the viewer re-anchors.
4. **Highlight the delta.** Pulse/glow the changed element, or count a number up instead of cutting it. Animate the *difference*, not just the end state.
5. **Cause→effect spacing.** Put a beat between the trigger and its result so the viewer can attribute one to the other. Simultaneous = ambiguous.

Layer at most two of these on any single change. More than two competes with itself and the change stops reading - the exact problem you were solving.
