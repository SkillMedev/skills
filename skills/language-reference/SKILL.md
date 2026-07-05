---
name: Language & Reference Data
description: Use when a task needs dictionary or encyclopedia lookups from a live source - "define this word", "pronunciation / phonetics / synonyms of X", "get the Wikipedia summary of Y", "search Wikipedia for Z", or pulling a topic's intro paragraph and thumbnail into an app. Free Dictionary API covers words; Wikipedia's REST API covers topics; both keyless. Do NOT use for country facts or geography - use geo-places instead; do NOT use for trivia questions or random facts - use fun-content instead; if the request is a vague "I need live data", route through public-data-api-picker.
---

# Language & Reference Data

Look up words and topics against live keyless sources. The mistake this prevents: paraphrasing a definition or article summary from training memory when the task calls for a *citable* source - and, on the Wikipedia side, scraping HTML or hitting the heavyweight action API when the REST summary endpoint returns exactly the intro + thumbnail an app needs.

## Ranked APIs

1. **Free Dictionary API** (api.dictionaryapi.dev) - DEFAULT for word lookups: definitions, part of speech, phonetics with audio, synonyms/antonyms. English is reliable; other language codes exist but coverage is thin. No key.
2. **Wikipedia REST API** (en.wikipedia.org/api/rest_v1 and /w/rest.php/v1) - topic summaries, search, full page content. No key, but Wikimedia etiquette is enforced: send a real `User-Agent` (app name + contact), serialize requests, never hammer in parallel.

## Procedure

1. **Word vs topic.** A dictionary question ("define", "how do you pronounce", "synonym") → Free Dictionary. A knowledge question ("what is", "who was", "summary of") → Wikipedia.
2. **Wikipedia is two steps when the title is uncertain**: search first, then summary - guessing at title spelling gets 404s. Titles in URLs use underscores (`Albert_Einstein`) and are case-sensitive after the first letter.
3. **Attribute.** Wikipedia content is CC BY-SA - quote with a link to the page (`content_urls.desktop.page` in the summary response). Dictionary audio files carry their own license field.

Ask the user only if missing: the word/topic itself; language edition (default: `en`).

## URL templates + real response shapes

**Word lookup:**

```
https://api.dictionaryapi.dev/api/v2/entries/en/{word}
```

```json
[{"word":"serendipity","phonetic":"/ˌsɛ.ɹən.ˈdɪ.pɪ.ti/",
  "phonetics":[{"text":"/…/","audio":"https://api.dictionaryapi.dev/media/pronunciations/en/serendipity-us.mp3"}],
  "meanings":[{"partOfSpeech":"noun",
    "definitions":[{"definition":"An unsought, unintended…discovery…","example":"..."}],
    "synonyms":["luck","chance"]}]}]
```

Gotchas: top level is an **array** of entries; a miss is a 404 with a JSON body (`"title":"No Definitions Found"`) - handle it as "not in this dictionary", not an outage. Some `phonetics[].audio` fields are empty strings; filter before offering audio.

**Topic summary (intro + thumbnail in one call):**

```
https://en.wikipedia.org/api/rest_v1/page/summary/{Title_With_Underscores}
```

```json
{"title":"Albert Einstein","description":"German-born theoretical physicist",
 "extract":"Albert Einstein was a German-born theoretical physicist…",
 "thumbnail":{"source":"https://upload.wikimedia.org/...jpg","width":244},
 "content_urls":{"desktop":{"page":"https://en.wikipedia.org/wiki/Albert_Einstein"}}}
```

`extract` is plain text (also `extract_html`). Disambiguation pages return `"type":"disambiguation"` - go back to search rather than presenting the disambiguation text as an answer.

**Search:**

```
https://en.wikipedia.org/w/rest.php/v1/search/page?q={query}&limit=5
```

```json
{"pages":[{"key":"Quantum_computing","title":"Quantum computing",
  "description":"Computer hardware technology that uses quantum mechanics",
  "excerpt":"…<span class=\"searchmatch\">quantum</span>…"}]}
```

`key` is the URL-ready title for the summary call; `excerpt` contains HTML `<span>` highlights - strip tags before display.

**Other language editions:** swap the subdomain - `fr.wikipedia.org/api/rest_v1/page/summary/…`.

## Deliverable

The definition or summary with its source linked (Wikipedia page URL, or the dictionary entry's word + license for audio), phonetics/synonyms where asked, and the exact URL used.

## Do NOT use - reach for X instead

- **Scraping wikipedia.org HTML or the action API (`/w/api.php`) for a summary** - the REST summary endpoint above is the sanctioned, lighter path.
- **Wordnik, Merriam-Webster, Oxford APIs** - key-walled (some with waitlists); Free Dictionary covers the standard lookup keyless.
- **Country/place facts** - geo-places. **Statistics over time** - government-open-data.
- **Trivia, quotes, random facts** - fun-content; this skill is for looking up something specific.
- **Presenting Wikipedia's text as your own** - always attribute; CC BY-SA is a license, not a suggestion.

## Quality bar

- Wikipedia calls carry a real User-Agent and run serially.
- Uncertain titles went search → summary; disambiguation pages were resolved, not quoted.
- Every quoted definition/summary is attributed with a link; HTML highlight spans are stripped from displayed excerpts.

Sourced and liveness-verified from the public-apis project (MIT).
