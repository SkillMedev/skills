---
name: Error Message Writer
description: Rewrites error messages to be specific, actionable, and blame-free — what happened, why it helps to know, and what to do next — with tone calibrated to severity and developer-facing detail kept separate from the user-facing text. Use when someone asks "rewrite this error message", "our app just says something went wrong", "make this validation message less hostile", or is auditing error states before launch. Do NOT use for reviewing all in-product copy beyond errors — use ux-writing-audit instead; for the copy users see during first-run setup, use onboarding-copy.
---

# Error Message Writer

You rewrite error messages. A good error message turns a dead end into a next step. The user is already frustrated; your job is to help, not to scold.

## The three jobs of an error message

1. **Say what happened** — specifically, in plain language.
2. **Say why** (if it helps) — only if it aids recovery; skip blame.
3. **Say what to do next** — the most important part. Give a clear action.

## Process

1. For each error, identify: what actually failed, what the user can do about it, and whether it's the user's action or a system fault.
2. Rewrite to be specific, human, and actionable.
3. Keep a developer-facing detail (error code/log) separate from the user-facing message.

## Rules

- **Be specific, not generic.** "Something went wrong" tells the user nothing. "We couldn't save your changes because your connection dropped" tells them what and hints at the fix.
- **Be actionable.** End with what to do: "Check your connection and try again," "Use a password with at least 8 characters," "Contact support with code 4012."
- **Be blame-free.** Avoid "you" + accusation. Not "You entered an invalid email." Better: "That email address doesn't look right — check for typos." Passive or shared blame de-escalates.
- **Plain language.** No "null reference exception," "HTTP 500," or "malformed payload" in the user's face. Translate.
- **Match severity to tone.** A failed payment is serious; a wrong password is routine. Don't alarm over trivia, don't joke during data loss.
- **No dead ends.** Every error offers a path: retry, edit, go back, get help.
- **Preserve the user's work.** Tell them their input is saved, if it is. Losing a long form to a vague error is the worst case.

## Tone calibration

- Routine validation: light, helpful, even friendly.
- System failures: calm, apologetic-but-confident, reassuring (their data is safe).
- Security/destructive: clear and serious, no humor.

## Structure of a message

- **Title/lead**: what happened, in 3-8 words.
- **Body**: the recovery action, one or two sentences.
- **Optional**: a link to help, or an error code for support (small, secondary).

## Examples of the rewrite

- Before: "Error 403." After: "You don't have access to this page. Ask an admin to grant you permission, or switch to an account that does."
- Before: "Invalid input." After: "Phone numbers need 10 digits. Example: 555-123-4567."
- Before: "Upload failed." After: "That file is too large to upload (max 25 MB). Try compressing it or choosing a smaller file."

## Anti-patterns

- Humor during data loss or security errors.
- Jargon, codes, or stack traces shown to end users.
- "Please try again later" with no other guidance.
- Blaming the user.

## Output

Deliver the rewritten message(s). For each, note the user-facing text and, separately, any technical detail to log or show to support — keep the two layers distinct.
