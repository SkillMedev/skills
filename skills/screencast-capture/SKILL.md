---
name: Screencast Capture
description: Capture clean raw screen and camera footage for a tutorial or product demo - choosing the recorder (macOS Screenshot toolbar, QuickTime, OBS, Windows Game Bar), setting resolution / frame-rate / cursor / microphone, prepping a distraction-free stage, and recording in retakeable segments. Use when someone says "record my screen", "capture a screencast", "how do I record this demo", "what settings for screen recording", "set up OBS", "my recording looks blurry or laggy", "the cursor is hard to follow", or "record a tutorial walkthrough". Do NOT use to direct what happens on screen - cursor choreography, zoom/pan, callouts, screen-record-vs-recreate - that is product-demo-director; do NOT use to plan scenes or beat order before recording - that is video-storyboard; do NOT use to write what is spoken over the footage - that is narration-script; do NOT use to trim, cut, or export captured footage - that is downstream Remotion / editing.

---

# Screencast Capture

Capture is the one stage you cannot fix in post. A blurry, laggy, notification-littered recording forces a re-shoot no amount of editing redeems. Get the raw footage clean and everything downstream gets easier.

## When to use

Reach for this when the user needs to **record** something on their screen - the act of capturing, not staging or editing it. That includes picking a recorder, dialing in settings, prepping the machine, and running the actual take. If the footage already exists and the question is about cursor moves, zooms, cuts, or narration, hand off to the skills named in the description.

## Pick the recorder by need

- **Quick, single-screen, no overlays** → macOS: `Cmd+Shift+5` (Screenshot toolbar) or QuickTime "New Screen Recording". Windows: `Win+G` (Game Bar) or the Snipping Tool video mode. Zero setup, fine for short clips.
- **Webcam bubble, multiple sources, scene switching, or system + mic on separate tracks** → OBS Studio (free, cross-platform). Worth the setup the moment you need a talking-head overlay or more than one source.
- **Polished product walkthroughs with auto-zoom and cursor smoothing** → a dedicated tool (Screen Studio on macOS, or similar). Only reach here if the user already has it; do not make a paid tool a prerequisite.

State the tradeoff plainly: OBS buys control at the cost of a 10-minute setup; the built-in recorders buy speed at the cost of overlays and track separation.

## Capture settings that matter

- **Resolution**: capture at the delivery resolution or higher, never lower. 1080p (1920×1080) is the floor for tutorials; 1440p/4K if the final will be 1080p and you want room to punch in.
- **Frame rate**: 60 fps when there is cursor motion, scrolling, or animation - it reads dramatically smoother. 30 fps is acceptable for mostly-static talking-head segments. Pick one and keep it consistent across takes.
- **Cursor**: enable click visualization / highlight if the tool offers it. Move deliberately and slowly; the cursor is the viewer's eye. Hide it entirely for segments where it is not the subject.
- **Microphone**: record mic on a **separate track** from system audio (OBS does this natively) so you can balance them later. Do a 10-second level check first - aim for peaks around −12 to −6 dB, never clipping. Record 3-5 seconds of room tone with no speech for the editor.
- **Bit rate**: let the tool default unless footage looks compressed; if so, raise it. Prefer a lightly-compressed large file now over a small artifact-ridden one.

## Prepare the stage before you hit record

This is where most amateur screencasts are lost. Before the first take:

- Turn on **Do Not Disturb / Focus** so no notification banner ever lands mid-recording.
- Clean the desktop: neutral wallpaper, no personal files, hidden dock clutter.
- Close every irrelevant tab and app. Quit chat apps, mail, calendar pop-ups.
- Use a **demo account with seeded, fake data** - never real customer PII, real emails, or real names on screen. Scrub anything sensitive.
- Set the app's zoom so text is legible at the *delivery* size, not just on your big monitor. In a browser, zoom to roughly 125-150% and hide the bookmarks bar; consider a fresh, empty browser profile.
- Match the capture window to your target aspect ratio (usually 16:9 for tutorials) so you are not cropping later.

## Record in retakeable segments

Do not try to nail a ten-minute take in one pass. Record **one storyboard beat per clip**:

- Rehearse the beat once silently to get the motions right.
- Start recording, pause a beat, then perform - leave a second of silence at the head and tail as editing handles.
- If you fumble, stop, breathe, and re-record just that segment. Cheap to redo one beat, expensive to redo the whole video.
- Keep a consistent pace between beats so they cut together cleanly.

## Hand off

Raw clips go to the editor / Remotion pipeline for assembly, to narration-script for the voiceover that rides over them, and to captions-from-transcript once audio is locked. This skill's job ends when clean footage exists.

## Don't

- Don't record at a lower resolution than the final output "to save space" - you can never get the detail back.
- Don't leave notifications, real data, or a cluttered desktop on screen; a single Slack banner can invalidate a whole take.
- Don't direct the demo here (where to zoom, how to move the cursor for emphasis) - that is product-demo-director.
- Don't try to fix shaky cursor work or wrong zoom in editing; re-capture it.
