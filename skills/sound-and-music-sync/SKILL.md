---
name: Sound and Music Sync
description: Score a video like an editor: beat-match cuts to the music, land SFX on transitions, pace the voiceover so it breathes, source royalty-free tracks safely, and duck the music so the VO sits clearly on top. Use when adding music, sound effects, or voiceover to a video, when asked to "sync cuts to the beat", "the music is drowning out the voice", "add whooshes on the transitions", "where do I get royalty-free music", "mix the audio", or "the VO feels rushed". Do NOT use when wiring audio into the Remotion timeline mechanically (Audio/staticFile/render) - that is remotion-compose and remotion-render; do NOT use when choosing the shot order or scene beats - use video-storyboard instead; do NOT use when tuning the visual look (color grade, lighting, contrast) - use motion-color-and-light instead.
---

# Sound and Music Sync

Sound is half the video and the half people forget. A mediocre animation cut to the
beat with a clean voiceover reads as professional; a beautiful animation with cuts
that float and a VO buried under the music reads as amateur. This is the audio
taste layer. You decide where cuts land, where sound effects punctuate, how the
voiceover breathes, and how the mix sits - then you hand the concrete numbers
(beat frames, SFX timestamps, duck levels) to **remotion-compose** to wire onto the
timeline and **remotion-render** to export. You do not write the `<Audio>` JSX or
run the renderer; you make the creative decisions that feed them.

This pairs with the rest of the motion-video-direction pack: a **video-storyboard**
scene list and **motion-design-principles** timing decisions are your input - you
score against beats that already exist. If text is animating to the audio, sync it
with **kinetic-typography**; if the cut rhythm is carrying a product walkthrough,
coordinate with **product-demo-director**; if you are cutting a vertical/social
version, re-derive beats per platform with **social-video-formatter**. The audio
mix is its own layer - leave the visual look (color grade, lighting, contrast) to
**motion-color-and-light** and score against whatever it grades.

## Workflow

Run these in order. Do not start placing SFX before the cuts are beat-locked, and
do not finalize the mix before the VO timing is right - each step depends on the
one before.

1. **Pick the track and lock the tempo.** Choose music that matches the video's
   energy and length, confirm its license (see references/royalty-free-sourcing),
   and get its BPM. Most royalty-free libraries list BPM; if not, tap it out or use
   a detector. BPM is the spine of everything downstream - one beat lasts
   `60 / BPM` seconds, and at the project fps that is `fps * 60 / BPM` frames.

2. **Build the beat grid and snap cuts to it.** Convert beats to frame numbers and
   place every hard cut and major scene change on a beat (run `beat_grid.js`
   below). Cut on the downbeat - beats 1, 5, 9 in 4/4 - for emphasis; cut on any
   beat for routine transitions. A cut that lands two or three frames off the beat
   reads as "wrong" even to viewers who cannot name why. Hand the snapped frame
   list to remotion-compose as the `<Sequence from={...}>` boundaries.

3. **Place sound effects on transitions, not over them.** SFX exist to make motion
   feel physical. The rule: the sound peaks the instant the motion peaks. A whoosh
   *leads into* a swipe (start it ~3-5 frames before the cut so its tail lands on
   the cut); an impact/thud lands *on* the frame a card slams in; a click/pop lands
   on the frame a UI element appears; a riser builds *across* the 10-20 frames
   before a reveal and resolves on it. One SFX per transition - stacking them turns
   punctuation into noise. See references/sfx-placement.

4. **Pace the voiceover so it breathes.** VO sets the real pace of the video; the
   visuals serve it, not the reverse. Target a conversational ~150 words per
   minute (≈2.5 words/second) - faster reads as rushed, slower as a hostage video.
   Leave a beat of silence (8-15 frames) before a key line and after the CTA. Write
   for the ear: short sentences, one idea each, contractions. Never let a scene cut
   mid-word - align scene boundaries to the gaps *between* sentences. Time the VO
   first, then stretch or compress scene durations to fit it (this becomes the
   `durationInFrames` remotion-compose uses).

5. **Mix: duck the music so the VO sits on top.** This is the single most common
   audio failure. Music and full-volume VO compete in the same frequency range and
   the words get lost. Duck the music to roughly 25-35% of its level *whenever the
   VO is speaking*, then bring it back up in the gaps. Run `mix_levels.js` for
   concrete level and timing numbers. Target the VO around -3 to -6 dB (clear,
   never clipping), ducked music around -18 to -22 dB. Hand the duck envelope
   (which frames to duck, the floor level) to remotion-compose, which applies it via
   per-`<Audio>` `volume` callbacks.

6. **Spot-check the export.** After remotion-render produces the MP4, listen on
   laptop speakers and on phone speakers, not just good headphones - most viewers
   are on bad speakers. Confirm: every word of VO is intelligible over the music,
   no SFX clips or startles, cuts feel locked to the beat, and the loudest moment
   does not distort. If a word is lost, duck deeper in that span; if SFX startle,
   pull them down 6 dB.

## Quality bar

The audio is A+ only when all of these hold:

- Every hard cut lands on a beat frame from the grid - none float between beats.
- Every transition SFX peaks on the exact frame its motion peaks; risers resolve on
  the reveal, not before or after.
- VO runs ~140-160 wpm with deliberate silence before key lines and after the CTA;
  no scene cuts mid-word.
- The VO is fully intelligible over the music on phone speakers - verified, not
  assumed - because the music ducks to ~30% under speech.
- Exactly one SFX per transition; the track loops or ends cleanly with no abrupt
  cutoff on the final frame.
- Every audio asset's license permits the intended use (commercial / paid ads /
  client work) and attribution requirements are recorded.

## Do NOT

- Do not leave cuts floating off-beat to preserve a scene's "natural" length -
  stretch or trim the scene to the nearest beat instead.
- Do not run music at full level under the voiceover; un-ducked music is why VO
  sounds muddy. Duck it every time someone speaks.
- Do not stack multiple SFX on one transition, or reuse the same whoosh on every
  cut - vary them or the ear stops hearing them.
- Do not write the `<Audio>` JSX, set `startFrom`/`volume` callbacks, or invoke the
  renderer here - produce the numbers and hand them to remotion-compose /
  remotion-render.
- Do not pull tracks from YouTube rips, "free" sites with no stated license, or a
  client's Spotify - commercial use needs a real royalty-free or licensed source.
- Do not normalize the whole mix to be as loud as possible; leave headroom so the
  loudest impact does not clip.
- Do not let the VO race to fit a fixed runtime - re-cut the visuals to the VO, not
  the VO to the visuals.

## Calculator: beat grid

Self-contained Node script. Save as `beat_grid.js` and run with `node beat_grid.js`.
It turns a BPM and an fps into the exact frame numbers your cuts should land on, and
flags downbeats. No dependencies. Hand the printed frames to remotion-compose as
`<Sequence from={...}>` boundaries.

```javascript
// Beat grid. Edit inputs, then: node beat_grid.js
const input = {
  bpm: 120,            // tempo of the chosen track
  fps: 30,             // project frame rate
  durationSeconds: 12, // length of the video
  beatsPerBar: 4,      // 4/4 time; downbeat is every 4th beat
}

const framesPerBeat = (input.fps * 60) / input.bpm
const totalFrames = Math.round(input.durationSeconds * input.fps)
const beats = []
for (let b = 0; ; b++) {
  const frame = Math.round(b * framesPerBeat)
  if (frame > totalFrames) break
  beats.push({ beat: b + 1, frame, downbeat: b % input.beatsPerBar === 0 })
}

console.log('Frames per beat:', framesPerBeat.toFixed(2), '(' + (60 / input.bpm).toFixed(3) + 's each)')
console.log('Cut on these frames (* = downbeat, best for hard cuts):')
for (const b of beats) {
  console.log('  beat ' + String(b.beat).padStart(2), '-> frame', String(b.frame).padStart(4), b.downbeat ? '*' : '')
}
console.log('Downbeat frames only:', beats.filter((b) => b.downbeat).map((b) => b.frame).join(', '))
```

### Worked example output

With the inputs above the script prints:

```
Frames per beat: 15.00 (0.500s each)
Cut on these frames (* = downbeat, best for hard cuts):
  beat  1 -> frame    0 *
  beat  2 -> frame   15 
  beat  3 -> frame   30 
  beat  4 -> frame   45 
  beat  5 -> frame   60 *
  beat  6 -> frame   75 
  beat  7 -> frame   90 
  beat  8 -> frame  105 
  beat  9 -> frame  120 *
  ...
Downbeat frames only: 0, 60, 120, 180, 240, 300, 360
```

Read it: at 120 BPM and 30 fps a beat is exactly 15 frames. Put routine transitions
on any listed frame and your hardest cuts - the intro-to-content cut, the CTA reveal -
on a downbeat (0, 60, 120…). If a scene wants to run 70 frames, snap it to 75
(beat 6) rather than letting the cut float at 70. Those snapped numbers are exactly
what remotion-compose drops into `<Sequence from={...}>`.

## Calculator: mix levels and duck timing

Self-contained Node script. Save as `mix_levels.js` and run with `node mix_levels.js`.
It computes the ducked music level and the per-VO-line frame spans to duck, so you
hand remotion-compose a concrete envelope instead of a vibe. No dependencies.

```javascript
// Mix and ducking planner. Edit inputs, then: node mix_levels.js
const input = {
  fps: 30,
  musicVolume: 0.8,       // music level when no one is speaking (0-1)
  duckToPercent: 0.30,    // duck music to this share of its level under VO
  voVolume: 1.0,          // voiceover level (keep VO the loudest element)
  duckFadeFrames: 8,      // ramp in/out so the duck is not a hard jump
  // VO lines as [startSeconds, endSeconds]; duck music across each, with fade pad
  voLines: [
    [0.5, 3.2],
    [4.0, 7.5],
    [8.2, 11.0],
  ],
}

const duckedLevel = +(input.musicVolume * input.duckToPercent).toFixed(3)
const toFrame = (s) => Math.round(s * input.fps)
// rough dB for reference: 20*log10(level)
const dB = (v) => (v <= 0 ? '-inf' : (20 * Math.log10(v)).toFixed(1) + ' dB')

console.log('VO level:           ', input.voVolume, '(' + dB(input.voVolume) + ')  <- loudest element')
console.log('Music (no speech):  ', input.musicVolume, '(' + dB(input.musicVolume) + ')')
console.log('Music ducked under VO:', duckedLevel, '(' + dB(duckedLevel) + ')')
console.log('Duck fade:          ', input.duckFadeFrames, 'frames in/out')
console.log('Duck music across these frame spans (pad ' + input.duckFadeFrames + 'f each side for the fade):')
for (const [start, end] of input.voLines) {
  const a = toFrame(start) - input.duckFadeFrames
  const b = toFrame(end) + input.duckFadeFrames
  console.log('  VO ' + start + 's-' + end + 's  -> duck frames', Math.max(0, a), 'to', b)
}
```

### Worked example output

```
VO level:            1 (0.0 dB)  <- loudest element
Music (no speech):   0.8 (-1.9 dB)
Music ducked under VO: 0.24 (-12.4 dB)
Duck fade:           8 frames in/out
Duck music across these frame spans (pad 8f each side for the fade):
  VO 0.5s-3.2s  -> duck frames 7 to 104
  VO 4s-7.5s    -> duck frames 112 to 233
  VO 8.2s-11s   -> duck frames 238 to 338
```

Read it: the VO stays at full level (the loudest thing in the mix), music rides at
0.8 in the gaps and ducks to 0.24 - about a third - wherever someone speaks, fading
over 8 frames so the dip is not a jarring jump. The frame spans are exactly the
ranges remotion-compose feeds to the music `<Audio>` `volume` callback: full level
outside the spans, `duckedLevel` inside, linear-ramped across the fade pad. That is
why the words stay clear without the music vanishing.

## Template: audio cue sheet

Copy this, fill the FILL fields with your beat-grid and mix-level outputs, and hand
the completed sheet to remotion-compose. It is the single source of truth for every
audio decision in the video.

```
AUDIO CUE SHEET. [FILL: video name]. fps [FILL] / [FILL] BPM / [FILL]s total

MUSIC
  Track:                 [FILL: title + source]
  License:               [FILL: e.g. Epidemic Sound commercial / CC0] - attribution: [FILL: yes/no, text]
  Level (no speech):     [FILL: 0-1]    Ducked under VO: [FILL: 0-1] (~30%)

BEAT GRID (from beat_grid.js) - cuts land here
  Downbeat frames:       [FILL: 0, 60, 120, ...]
  Hard cuts on:          [FILL: which downbeats carry the big cuts]

SFX (one per transition; peak ON the motion peak)
  FRAME    SFX            ROLE
  [FILL]   [FILL whoosh]  leads into swipe (start ~4f early, tail on cut)
  [FILL]   [FILL impact]  card slam-in, lands ON cut frame
  [FILL]   [FILL riser]   builds across 10-20f, resolves on reveal
  [FILL]   [FILL pop]     UI element appears

VOICEOVER (~150 wpm; scenes fit the VO, not the reverse)
  LINE                                   START   END    SCENE
  [FILL line]                            [FILL]  [FILL] [FILL]
  (silence 8-15f before key line / after CTA)

DUCK ENVELOPE (from mix_levels.js) - frame spans to lower music
  [FILL: 7-104, 112-233, ...]  fade [FILL]f in/out

HANDOFF: remotion-compose wires <Audio> + volume callbacks; remotion-render exports.
```

## references/royalty-free-sourcing

Commercial video needs music you are actually licensed to use - "I found it on
YouTube" is not a license and a client's Spotify is not either. Licensed/royalty-free
sources, by use case:

- **Subscription libraries (safest for client/commercial/ads):** Epidemic Sound,
  Artlist, Musicbed, Soundstripe. Flat fee, broad commercial license, large SFX
  libraries, no per-video attribution. The default for paid work.
- **Free with terms (check each track):** YouTube Audio Library (free, some tracks
  need attribution), Pixabay Music and Mixkit (free, commercial OK, no attribution),
  Free Music Archive and ccMixter (Creative Commons - read the specific CC license,
  some forbid commercial use or require attribution).
- **SFX specifically:** Pixabay and Mixkit (free), Epidemic/Artlist (bundled with
  subscription), freesound.org (Creative Commons - verify each clip's license).

License hygiene, always: confirm the license covers your exact use (personal vs.
commercial vs. *paid advertising*, which is often a separate tier); record any
required attribution text in the cue sheet; keep proof of license/download. CC0 and
public-domain are the only "use freely anywhere" cases - everything else has terms.
When in doubt, a paid subscription library is cheaper than a takedown or a claim.

## references/sfx-placement

SFX make motion physical; placement is everything. The peak of the sound must hit
the peak of the motion, or the brain registers a mismatch.

- **Whoosh / swipe** - directional motion (a slide, a swipe transition). Start it
  ~3-5 frames *before* the cut so its energy *leads into* the motion and the tail
  resolves on the cut. A whoosh placed after the motion sounds like an echo.
- **Impact / thud** - something arriving with weight (a card slamming in, a heavy
  title drop). Land the transient on the exact frame the object stops. Pair with a
  tiny scale-overshoot in the motion so sound and picture punch together.
- **Click / pop / tick** - small discrete UI events (a button, a checkmark, a tab).
  Land on the frame the element appears. Keep them quiet; they punctuate, they do
  not announce.
- **Riser / swell** - building to a reveal (the logo, the CTA, the "after" state).
  Build across the 10-20 frames *before* the reveal and resolve its peak on the
  reveal frame. A riser that peaks early deflates the moment.
- **Ambience / drone** - a continuous bed under a scene for mood. Low level, sits
  under everything, no transient - different job from the punctuation SFX above.

Discipline: one SFX per transition. Vary the whooshes across cuts (pitch or sample)
so the ear keeps noticing them. Mix SFX a touch under the VO and above the ducked
music - they punctuate, they never compete with the words.
