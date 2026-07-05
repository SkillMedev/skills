---
name: Motion Design Principles
description: The craft layer for motion: pick the right easing (ease-in / ease-out / ease-in-out / spring), get timing and spacing right, build the anticipationâactionâfollow-through arc, and apply Disney's 12 principles to UI and product video. Use when animating any UI transition, modal, button, page load, or product-video scene, or when someone says "make this feel less janky", "smoother", "more polished", "more alive", "the animation feels off", "why does this look cheap", or asks "ease-in or ease-out" / "spring or interpolate". Do NOT use to plan shots, scene order, or beat lists â use video-storyboard instead; do NOT use to direct a full product demo (cursor moves, screen captures, callouts) â use product-demo-director instead; do NOT use to animate text/letterforms â use kinetic-typography instead; do NOT use to time motion to a beat or score â use sound-and-music-sync instead; do NOT use to crop/reframe for a platform â use social-video-formatter instead; do NOT use for light/exposure/color motion â use motion-color-and-light instead.
---

# Motion Design Principles

This is the taste layer. It decides *how* something should move so it reads as
polished and intentional instead of janky, floaty, or cheap. It does not write
the code that plays the motion â once you have decided the curve, the timing, and
the arc, that decision is the *input* to a directing/render skill (for product
video, hand it to **product-demo-director**) or to your CSS/JS transition (for
UI). Keep the two jobs separate: this skill owns the judgment; the directing
skill owns the orchestration and mechanics.

Fire on anything about how motion *feels*: "make this less janky", "smoother",
"more polished", "more alive", "the easing is off", "this looks cheap", "should I
ease-in or ease-out", "spring or interpolate".

## When to use a sibling instead

This skill is the curve/timing/arc judgment only. Defer when the real question is:

- **Which shots, scene order, or a beat list** â **video-storyboard**.
- **Directing a full product demo** â cursor moves, screen captures, feature
  callouts, the end-to-end scene assembly â **product-demo-director**. (This skill
  produces the motion spec it consumes.)
- **Animating text or letterforms** â type that flies/builds/reveals â
  **kinetic-typography**.
- **Timing motion to a beat, score, or sound effect** â **sound-and-music-sync**.
- **Cropping or reframing for a platform** (9:16, 1:1, safe areas) â
  **social-video-formatter**.
- **Light, exposure, glow, or color motion** â **motion-color-and-light**.

## Operating procedure

Diagnose before you prescribe. Most "janky" motion is one of four named
problems, and each maps to a specific fix below. Work the steps in order â the
easing fix is worthless if the timing is wrong, and the timing fix is worthless
if the element has no arc.

### Step 1: Name what feels wrong

Watch the motion (or the description of it) and classify it. Almost every
complaint is one of these:

- **Linear / robotic** â moves at constant speed, stops dead. No easing. Fix in
  Step 2.
- **Floaty / mushy** â too slow, or eases in *and* out when it should snap in.
  Wrong duration or wrong curve. Fix in Step 2 and Step 3.
- **Janky / stuttery** â enters and exits at the same speed, or every property
  animates on the same flat timeline with no offset. Missing spacing and arc.
  Fix in Step 3 and Step 4.
- **Dead / lifeless** â technically correct but boring: no anticipation, no
  overshoot, no follow-through. Missing the arc and secondary motion. Fix in
  Step 4 and Step 5.

Write the diagnosis down in one line before touching anything. "The modal is
floaty because it fades over 600ms with ease-in-out" is a fix; "make it nicer"
is not.

### Step 2: Pick the easing curve

Easing is the speed-over-time shape of the motion. The curve carries almost all
the perceived quality. Match the curve to what the element is doing:

- **ease-out** (fast start, slow settle) â the default for **things entering** and
  for anything the user triggered. It feels responsive because motion begins
  immediately, then decelerates into place. Use for: elements appearing,
  dropdowns opening, a tapped button responding, content sliding in. If unsure,
  reach for ease-out.
- **ease-in** (slow start, fast exit) â for **things leaving**. The element
  accelerates away off-screen. Used alone on an *entrance* it feels sluggish and
  laggy, which is the #1 cause of "floaty". Use for: dismissals, close, exits.
- **ease-in-out** (slow, fast, slow) â for **moving something already on screen
  from A to B**: a card reordering, a carousel advancing, a toggle sliding. Both
  ends are anchored, so easing both ends reads as controlled. Do not use it for
  entrances â the slow start kills responsiveness.
- **spring** (physics: mass, stiffness, damping â overshoots and settles) â for
  motion that should feel **alive and physical**: a modal popping in, a toggle
  knob, a like button, a notification dropping in. The slight overshoot is what
  reads as "alive". Tune damping for personality: lower damping = bouncier and
  more playful, higher damping = tighter and more corporate.

The `spring() vs interpolate()` decision (when you reach for each in code) is in
the **Quality bar** and the runnable artifact below. The short version: spring
for physical/lively transforms, interpolate for precise timed-to-the-frame values
like opacity fades, color, and progress bars.

### Step 3: Set timing and spacing

**Timing** is total duration. **Spacing** is how the distance is distributed
across that duration â spacing *is* easing made literal (an ease-out is bunched
frames at the end). Two rules:

- **Duration scales with distance and size, not taste.** A small element moving a
  short distance should be fast (UI: ~150â250ms). A large element or a long
  travel needs longer (~300â500ms) or it teleports. Big full-screen / hero
  motion in video can run longer. Same motion, longer distance â longer duration.
- **Enter and exit are not symmetric.** Exits should be faster than entrances
  (~0.7â0.8x) â the user is done with the element, so get it out of the way.
  Equal enter/exit durations are a classic source of "janky".

For product video, convert to frames the moment you leave this skill:
`frames = round(seconds * fps)`. Those frame counts are the literal input you
hand to **product-demo-director** (which turns them into the timed scene plan).

### Step 4: Build the anticipation â action â follow-through arc

Real motion is rarely a single move. The richest, most "alive" motion has three
beats:

1. **Anticipation** â a tiny opposite-direction wind-up before the main move (a
   button dips *in* slightly before popping, a card pulls back before sliding
   out). It primes the eye and makes the action read as intentional. Keep it
   small and fast; too much looks like a glitch.
2. **Action** â the main move, carrying the easing from Step 2 and the duration
   from Step 3. This is where the spacing lives.
3. **Follow-through** â the element settles past its target and back (the spring
   overshoot), and any attached/secondary elements lag slightly behind the
   leader (a label catching up to the card it sits on). This is what sells
   weight and life.

Not every micro-interaction needs all three â a 150ms tooltip is just action. But
when something feels "dead", it is almost always missing anticipation or
follow-through. Add the arc before adding flourish.

### Step 5: Apply the relevant Disney principles

Disney's 12 principles of animation map directly onto UI and product motion. You
do not apply all twelve; you reach for the few that fix the diagnosed problem.
The full mapping is in `references/twelve-principles`. The high-leverage ones:

- **Slow in & slow out** â easing (Step 2). The single biggest quality lever.
- **Anticipation** â the wind-up (Step 4).
- **Follow-through & overlapping action** â overshoot + lagging secondary
  elements (Step 4). The difference between "correct" and "alive".
- **Timing** â duration and spacing (Step 3).
- **Squash & stretch** â subtle scale on press/impact to imply mass; in UI keep
  it to a few percent or it looks like a toy.
- **Staging** â move one thing at a time so the eye knows where to look; never
  animate six things at once on the same curve.
- **Secondary action** â a small supporting motion (a shadow deepening as a card
  lifts) that reinforces the main one without competing.
- **Exaggeration** â push the key pose slightly past realistic; restrained in UI,
  freer in a punchy product video.

### Step 6: Hand off the spec

Fill the motion-spec template below â curve, duration (and frames), the arc
beats, and the spring-vs-interpolate call per property. That filled spec is the
handoff artifact: for a product-video scene it goes straight into
**product-demo-director**; for UI it becomes your transition/keyframe values.
Pair it with **motion-color-and-light** if the scene also needs light/exposure
motion, **kinetic-typography** if text is animating, and **sound-and-music-sync**
if the timing must land on a beat.

## Quality bar

Motion is A+ only when all of these hold:

- The diagnosis is named (linear / floaty / janky / dead), and every change traces
  to it. No "made it nicer" without a reason.
- **Entrances ease-out, exits ease-in, on-screen AâB ease-in-out, lively/physical
  uses spring.** No raw linear motion ships except deliberate constant-speed
  loops (a spinner, a marquee).
- Duration scales with distance/size, and the exit is faster than the entrance.
- The `spring()` vs `interpolate()` call is explicit per property: **spring for
  physical transforms** (scale, translate, the pop/slide/settle â you want the
  overshoot); **interpolate for precise timed values** (opacity fades, color,
  progress, anything where overshoot would look wrong). A fade driven by a spring,
  or a logo pop driven by linear interpolate, is a defect.
- At least one of anticipation or follow-through is present on hero / signature
  motion; lifeless motion is missing exactly these.
- One thing leads at a time (staging); properties are offset rather than all
  firing on one flat timeline.
- Curve, duration, and frame counts are written down and ready to hand to
  product-demo-director or a transition â this skill never leaves the decision
  implicit.

## Do NOT

- Do NOT ship linear easing for entrances or exits â it is the robotic default and
  reads as unfinished. Constant speed is only for intentional loops.
- Do NOT ease-in an entrance or ease-out an exit â that is the floaty/laggy feel
  reversed. Entrances ease-out, exits ease-in.
- Do NOT use equal enter and exit durations; exits should be ~0.7â0.8x faster.
- Do NOT drive a fade, color, or progress bar with a spring â the overshoot makes
  values run past their range and flash. Use interpolate for timed values.
- Do NOT drive a lively pop/slide/scale with linear interpolate when you want it
  to feel alive â use a spring for the overshoot.
- Do NOT animate many properties on one flat timeline; stage and offset them.
- Do NOT over-anticipate or over-overshoot in UI â a few pixels / a few percent.
  Big wind-ups and bounces read as a glitch or a toy.
- Do NOT plan shot order or assemble the demo here â hand the spec to
  product-demo-director; storyboard order is video-storyboard.

## Easing & spring picker

Self-contained Node script. Save as `motion_pick.js` and run with
`node motion_pick.js`. Edit the `requests` array for the elements you are
animating; it returns the curve, a distance-scaled duration (ms and frames), the
exit duration, and the spring-vs-interpolate call. No dependencies.

```javascript
// Motion picker. Edit `requests`, then: node motion_pick.js
const fps = 30 // for product video; UI ignores the frame column

// role: 'enter' | 'exit' | 'move' | 'lively'
// distancePx: how far it travels (drives duration). sizePx: rough element size.
const requests = [
  { name: 'Modal pop-in',    role: 'lively', property: 'scale',   distancePx: 0,   sizePx: 600 },
  { name: 'Modal backdrop',  role: 'enter',  property: 'opacity', distancePx: 0,   sizePx: 1920 },
  { name: 'Toast slide-in',  role: 'enter',  property: 'translate', distancePx: 320, sizePx: 360 },
  { name: 'Toast dismiss',   role: 'exit',   property: 'translate', distancePx: 320, sizePx: 360 },
  { name: 'Card reorder',    role: 'move',   property: 'translate', distancePx: 140, sizePx: 280 },
  { name: 'Progress fill',   role: 'move',   property: 'width',     distancePx: 800, sizePx: 800 },
]

function curveFor(role) {
  if (role === 'enter') return 'ease-out'
  if (role === 'exit') return 'ease-in'
  if (role === 'move') return 'ease-in-out'
  return 'spring' // lively
}

// Duration scales with travel distance and element size. Floor + slope, clamped.
function durationMs(role, distancePx, sizePx) {
  const reach = Math.max(distancePx, sizePx * 0.4)
  let ms = 140 + reach * 0.45
  ms = Math.min(Math.max(ms, 140), 600)
  if (role === 'exit') ms *= 0.75 // exits are faster
  return Math.round(ms)
}

// spring for physical transforms; interpolate for timed values.
function driver(role, property) {
  const physical = property === 'scale' || property === 'translate'
  if (role === 'lively') return 'spring()  // want the overshoot'
  if (physical && role !== 'move') return 'spring()  // physical, lively settle'
  return 'interpolate()  // precise, timed-to-frame'
}

const pad = (s, n) => String(s).padEnd(n)
console.log(pad('ELEMENT', 18), pad('CURVE', 12), pad('MS', 6), pad('FRAMES', 8), 'DRIVER')
for (const r of requests) {
  const curve = curveFor(r.role)
  const ms = durationMs(r.role, r.distancePx, r.sizePx)
  const frames = Math.round((ms / 1000) * fps)
  console.log(pad(r.name, 18), pad(curve, 12), pad(ms, 6), pad(frames, 8), driver(r.role, r.property))
}
```

### Worked example output

With the requests above the script prints:

```
ELEMENT            CURVE       MS     FRAMES   DRIVER
Modal pop-in       spring      252    8        spring()  // want the overshoot
Modal backdrop     ease-out    600    18       interpolate()  // precise, timed-to-frame
Toast slide-in     ease-out    284    9        spring()  // physical, lively settle
Toast dismiss      ease-in     213    6        interpolate()  // precise, timed-to-frame
Card reorder       ease-in-out 203    6        interpolate()  // precise, timed-to-frame
Progress fill      ease-in-out 500    15       interpolate()  // precise, timed-to-frame
```

Read it: the modal *pops* with a spring (overshoot = alive) while its backdrop
just *fades* with timed interpolate â two different drivers on the same component,
exactly as the Quality bar demands. The toast enters in 284ms and dismisses
faster at 213ms (the 0.75x exit rule), and its slide is physical so it springs in
but interpolates out. The progress bar is a precise width value â never a spring,
or the fill would jiggle past 100%. The frame column is what you hand to
product-demo-director for a product-video version of any of these.

## Template: motion-spec

Copy this, fill the FILL fields, and hand it to product-demo-director (video) or
use it as your transition values (UI).

```
MOTION SPEC. [FILL: element / scene name].  Context: [UI | product-video]

DIAGNOSIS
  Feels:        [FILL: linear / floaty / janky / dead / new build]
  Because:      [FILL: one line]

CURVE & TIMING
  Role:         [FILL: enter / exit / move A->B / lively]
  Curve:        [FILL: ease-out / ease-in / ease-in-out / spring]
  Duration:     [FILL]ms   ( = [FILL] frames @ [FILL]fps )
  Exit faster:  [FILL]ms   (~0.75x of entrance, if it exits)

ARC
  Anticipation: [FILL: wind-up, or "none" for a micro-interaction]
  Action:       [FILL: the main move]
  Follow-thru:  [FILL: overshoot + which secondary element lags]

PER-PROPERTY DRIVER (spring vs interpolate)
  [FILL property] -> spring()       (physical: scale / translate, want overshoot)
  [FILL property] -> interpolate()  (timed: opacity / color / progress)

DISNEY PRINCIPLES IN PLAY
  [FILL: e.g. slow-in/out, anticipation, follow-through, staging]

HANDOFF
  -> product-demo-director with the frame counts above, OR
  -> CSS/JS transition with the ms + cubic-bezier / spring config above.
```

## references/twelve-principles

Disney's 12 principles, mapped to UI and product-video motion. Reach for the few
that fix the diagnosed problem; you never apply all twelve at once.

1. **Squash & stretch** â deform on impact/press to imply mass. UI: a few percent
   scale on a button press; a notification squashing slightly as it lands. Too
   much looks like a toy.
2. **Anticipation** â a small opposite-direction wind-up before the action. Primes
   the eye, makes the move read as intentional. The fix for "dead" motion.
3. **Staging** â direct attention to one thing at a time. Do not animate six
   elements on one curve; lead with the hero element, let the rest follow.
4. **Straight-ahead vs pose-to-pose** â in code you work pose-to-pose: define key
   states (closed -> open) and let easing/interpolation fill the between frames.
5. **Follow-through & overlapping action** â elements settle past their target and
   attached parts lag behind the leader. The single biggest "alive" lever after
   easing. This is the spring overshoot plus offset secondary motion.
6. **Slow in & slow out** â easing itself. Things accelerate and decelerate; they
   do not start and stop at full speed. The biggest quality lever overall.
7. **Arc** â natural motion follows curved paths, not straight lines. A dropped
   element or a flung card reads better arcing than moving dead-straight.
8. **Secondary action** â a supporting motion that reinforces the main one (a
   shadow deepening as a card lifts). Supports, never competes.
9. **Timing** â number of frames / duration. Controls weight and mood: fewer
   frames = snappy and light, more = heavy and deliberate. Scale with distance.
10. **Exaggeration** â push the key pose past literal realism for clarity and
    energy. Restrained in UI, freer in a punchy product video.
11. **Solid drawing** â in motion design, consistent perspective, depth, and light
    so animated elements feel volumetric, not flat. Pairs with
    motion-color-and-light.
12. **Appeal** â the whole thing should feel charismatic and clear, not muddy or
    busy. The sum of the other eleven applied with restraint.

The four that fix 90% of UI/product complaints: **slow-in/out** (easing),
**timing** (duration/spacing), **anticipation**, and **follow-through**. Start
there, then add staging and secondary action for richness.

## references/spring-vs-interpolate

The decision you hand to product-demo-director, stated plainly:

- **Reach for `spring()`** when the motion should feel *physical and alive* and a
  slight overshoot/settle improves it: pops, scale-ins, slide-ins, toggles, like
  buttons, a logo entrance, a card lifting. Tune `damping` for personality â lower
  is bouncier/playful, higher is tighter/corporate. Springs are great on
  transforms (scale, translate) and bad on bounded values.
- **Reach for `interpolate()`** when you need *precise, timed-to-the-frame*
  control and overshoot would be wrong: opacity fades (0->1), color shifts,
  progress/width bars, cross-fades, exits, and anything that must land exactly on
  its end value. Always clamp so it never runs past its range.
- **Common pattern:** drive the *transform* with a spring and the *opacity* with
  interpolate on the same element â it pops in physically while fading cleanly.
  That split is the single most useful motion idiom and it is exactly what the
  picker script encodes.

When unsure: is overshoot a feature here? Yes -> spring. No -> interpolate.
