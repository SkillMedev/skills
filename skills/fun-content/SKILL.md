---
name: Fun Content APIs
description: Use when a task needs light entertainment content from a live API — "build a quiz/trivia game", "random dog picture", "cat fact", "advice of the day", "random fun fact" — for a demo, bot, party feature, or filler content slot. Open Trivia DB is the quiz default (its session-token mechanics are documented here); catfact.ninja, random.dog, Advice Slip, and Useless Facts cover the rest, all keyless. Do NOT use for placeholder users/products/images in a prototype — use test-placeholder-data instead; do NOT use for dictionary or encyclopedia lookups — use language-reference instead; if the request is a vague "I need live data", route through public-data-api-picker.
---

# Fun Content APIs

Pull trivia, animal images, and one-liner facts from verified keyless endpoints. The mistakes this prevents: shipping a quiz that repeats questions because Open Trivia DB's token mechanics were skipped, printing raw HTML entities in questions, and citing one of this category's chronically-dead quote APIs from training memory.

## Ranked APIs

1. **Open Trivia DB** (opentdb.com) — DEFAULT for quiz/trivia: 4,000+ questions, categories, difficulties. No key, but has session-token and encoding mechanics (below) that naive integrations get wrong.
2. **catfact.ninja** — random cat facts. **random.dog** — random dog images (the better-known Dog CEO API at dog.ceo is returning HTTP 520s, verified — see Do NOT).
3. **Advice Slip** (api.adviceslip.com) and **Useless Facts** (uselessfacts.jsph.pl) — one-liner advice and fun facts.

## Procedure

1. **Trivia beyond one throwaway call → get a session token first.** The token makes the API track served questions so users never see repeats. Rate limit: **1 request per IP per 5 seconds** — batch up to 50 questions per call instead of calling per-question.
2. **Decode the questions.** Default responses are HTML-encoded (`&quot;`, `&#039;`). Either decode entities client-side or request `&encode=url3986` and URL-decode — do one of them, or apostrophes render as garbage.
3. **Check `response_code` on every trivia call.** `0` ok; `1` not enough questions for the filters (loosen category/difficulty); `3`/`4` token missing or exhausted (request/reset token); `5` rate-limited (wait 5s).
4. **Shuffle answers.** `correct_answer` and `incorrect_answers` arrive separated — merge and shuffle before display or the correct answer's position leaks.
5. **Animal images: fetch the JSON, then use the URL inside it.** For random.dog, filter for image extensions — it also serves `.mp4`/`.webm`.

Ask the user only if missing — trivia: question count (default: 10), category (default: any), difficulty (default: any).

## URL templates + real response shapes

**Trivia session + questions:**

```
https://opentdb.com/api_token.php?command=request              ← once per user session
https://opentdb.com/api.php?amount=10&type=multiple&token={token}
https://opentdb.com/api.php?amount=10&category=9&difficulty=easy&token={token}   ← 9 = General Knowledge
https://opentdb.com/api_category.php                           ← id → name list
https://opentdb.com/api_token.php?command=reset&token={token}  ← on response_code 4
```

```json
{"response_code":0,"results":[{"type":"multiple","difficulty":"easy",
  "category":"Entertainment: Musicals &amp; Theatres",
  "question":"In Shakespeare&#039;s play Julius Caesar, Caesar&#039;s last words were...",
  "correct_answer":"Et tu, Brute? ",
  "incorrect_answers":["Iacta alea est!","Vidi, vini, vici.","Aegri somnia vana."]}]}
```

Tokens expire after 6 hours of inactivity. Max `amount=50` per call.

**One-liners and animals:**

```
https://catfact.ninja/fact                     → {"fact":"...","length":94}
https://random.dog/woof.json                   → {"url":"https://random.dog/….jpg","fileSizeBytes":170059}
https://api.adviceslip.com/advice              → {"slip":{"id":89,"advice":"Don't be afraid to ask questions."}}
https://api.adviceslip.com/advice/search/{q}   → {"slips":[...]} — 404s (JSON body) on no match
https://uselessfacts.jsph.pl/api/v2/facts/random → {"text":"...","source":"...","permalink":"..."}
```

Advice Slip caches responses for ~2 seconds — identical repeat calls return the same slip; don't build "another one!" loops tighter than that.

## Deliverable

For a quiz: working token lifecycle (request → use → reset on code 4), entity-decoded questions, shuffled answers, `response_code` handled. For content slots: the fetched item plus the URL used, with image extensions filtered where the API mixes media types.

## Do NOT use — reach for X instead

- **Dog CEO (dog.ceo)** — returning HTTP 520 on all endpoints, verified; random.dog replaces it.
- **quotes.rest, favqs unauthenticated, zenquotes at volume, kanye.rest and friends** — this category's quote APIs are chronically down or throttled to uselessness; Advice Slip and Useless Facts are the verified survivors. Probe before adding any other.
- **Placeholder people/products/images for a prototype** — test-placeholder-data (Picsum for generic images; this skill's animals are content, not layout filler).
- **Word definitions, encyclopedia facts** — language-reference.
- **Anything load-bearing.** These are volunteer-run free services — fine for demos and bots, wrong as a paid product's sole content source.

## Quality bar

- Multi-question trivia sessions use a token; zero repeated questions.
- No raw `&quot;` or `&#039;` ever reaches the user.
- Answer order is shuffled; `response_code` paths are handled, not assumed zero.

Sourced and liveness-verified from the public-apis project (MIT).
