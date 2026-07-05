---
name: Captions From Transcript
description: Produce an accurate, properly timed caption track (SRT or WebVTT) from a video's audio - transcribing or aligning to the voiceover script, timing cues to speech, and enforcing line-length and reading-speed rules so captions are readable and in sync. Use when someone says "generate captions", "make subtitles from the audio", "transcribe and caption this", "export an SRT or VTT", "the captions are out of sync", or "the subtitles flash by too fast to read". Do NOT use to burn-in, style, reframe, or position an existing caption track for a platform (9:16, brand styling, safe areas) - that is social-video-formatter; do NOT use to animate text word-by-word as a motion graphic - that is kinetic-typography; do NOT use to write the spoken script in the first place - that is narration-script.

---

# Captions From Transcript

Captions are not optional polish - they are accessibility, they hold viewers watching muted, and for tutorials they reinforce exact UI terms. This skill produces a **clean, accurately timed caption file**. Styling and burn-in happen downstream.

## Source the most accurate text first

Accuracy comes from where you start:

- **If a narration script exists, use it as ground truth.** It is already correct on technical terms and UI labels. Align it to the audio rather than re-transcribing from scratch.
- **Otherwise transcribe the audio** (Whisper, a platform's auto-caption, or any ASR), then **correct it against the video**. ASR reliably mangles product names, code, and acronyms - fix every one to match what is on screen exactly.

Never ship raw ASR output. The errors are always in the highest-value words.

## Time the cues to speech

- Each cue appears as its line is spoken and clears when it ends - align to speech, not to arbitrary intervals.
- Minimum ~1 second on screen (even for a short cue), maximum ~7 seconds. Split anything longer.
- No gaps mid-sentence; small gaps between sentences are fine and aid readability.

## Line and reading rules

- 1-2 lines per cue, never 3.
- ~32-42 characters per line. Past that it crowds the frame and overruns safe areas.
- Reading speed ≤ ~17 characters/second (≈160-180 wpm). If a cue exceeds it, the words flash by - split the cue or extend its duration.
- Break lines at clause boundaries, never mid-phrase. Keep "to the **Settings** page" together; don't strand "the" alone on a line.

## Choose the format

- **SRT** - universal, index + `HH:MM:SS,mmm --> HH:MM:SS,mmm` + text. Use for upload to most platforms and editors.
- **WebVTT (.vtt)** - `WEBVTT` header, `HH:MM:SS.mmm` timestamps, supports positioning/styling cues. Use for HTML5 `<track>` and the web player.

Produce valid, parseable output - correct timestamp punctuation (SRT uses a comma before milliseconds, VTT a period), blank line between cues, no trailing whitespace.

## Clean vs verbatim

For tutorials, caption **clean**: drop filler ("um", "uh", false starts), keep meaning verbatim. Preserve technical terms, code, and UI labels exactly. Only go strict-verbatim if the user explicitly needs it (legal, research, exact-quote contexts).

## QA the sync

Spot-check against the actual video at the start, a middle point, and the end - drift accumulates. Confirm cues land on their lines and clear before the next begins. Fix any caption that lingers over the wrong shot.

## Hand off

The finished `.srt` / `.vtt` feeds **social-video-formatter** for burn-in and platform styling, or the web player's `<track>` element directly. Leave color, font, position, and animation to those steps - this skill outputs accurate, well-timed text and nothing more.

## Don't

- Don't style, color, position, or burn captions into the frame - that is social-video-formatter.
- Don't animate words word-by-word - that is kinetic-typography.
- Don't ship uncorrected ASR; the mistakes cluster exactly on the terms that matter.
- Don't pack 3 lines or overrun the reading-speed budget to avoid splitting a cue - split it.
