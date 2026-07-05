---
name: Government & Open Data
description: Use when a task needs official country-level statistics - "GDP / population / life expectancy / inflation / unemployment for country X over time", "compare indicator Y across countries", or EU-official figures ("Eurostat says…"). World Bank Indicators is the default (no key, every country, 1,400+ series back decades); Eurostat covers EU-official statistics. Do NOT use for a country's static facts like capital or currency - use geo-places instead; do NOT use for live FX rates or crypto - use finance-fx instead; if the request is a vague "I need live data", route through public-data-api-picker.
---

# Government & Open Data

Pull official statistics from the World Bank and Eurostat keyless. The mistakes this prevents: quoting a GDP figure from training memory when the real series is one call away, and the two structural traps - World Bank's off-by-default JSON and its indicator-code lookup step - that make first attempts fail.

## Ranked APIs

1. **World Bank Indicators** (api.worldbank.org/v2) - DEFAULT. ~1,400 indicators × every country × back to 1960. No key. The catch: you query by opaque indicator code (`NY.GDP.MKTP.CD`), so the lookup step below is mandatory for unfamiliar metrics.
2. **Eurostat** (ec.europa.eu/eurostat/api) - EU-official statistics when the user wants the EU source of record or EU-only granularity. No key, JSON-stat format (denser to parse - shape below).

## Procedure

1. **Resolve the indicator code first.** Verified ones to use directly: `NY.GDP.MKTP.CD` (GDP, current US$), `SP.POP.TOTL` (population), `SP.DYN.LE00.IN` (life expectancy), `FP.CPI.TOTL.ZG` (inflation %), `SL.UEM.TOTL.ZS` (unemployment %). Anything else: search `https://api.worldbank.org/v2/indicator?format=json&per_page=50&source=2` filtered client-side, or hit `/v2/indicator/{guess}?format=json` to confirm a code exists before building the real query. Never invent a code - a bad code returns an error object inside a 200. Emissions codes (`EN.ATM.CO2E.PC`, `EN.GHG.*`) are served from a chronically failing backend - expect 502s/hangs and fall back to Eurostat (`env_air_gge`) for EU countries rather than retry-looping.
2. **Always append `format=json`.** The World Bank default is XML; forgetting this is the #1 failure.
3. **Prefer `mrv=N` over `date=` for "latest" queries.** `mrv=10` (most recent 10 values) hits the API's cache and skips the null-trimming problem; `date=` ranges on less-trafficked indicators frequently hang or 502 where the same indicator's `mrv` query answers instantly.
4. **Handle nulls.** Recent years are often `null` (reporting lag ~1-2 years); the latest *published* value is not the latest *year*. Report the year you actually used.
5. **Compare countries in one call** with `country/US;CN;DE/...` (semicolon-separated ISO codes), not N calls.

Ask the user only if missing: countries (no default), metric (no default), years (default: last 10 published).

## URL templates + real response shapes

**Indicator series:**

```
https://api.worldbank.org/v2/country/US/indicator/SP.POP.TOTL?format=json&mrv=10
https://api.worldbank.org/v2/country/US;CN;DE/indicator/NY.GDP.MKTP.CD?format=json&mrv=1&per_page=100
https://api.worldbank.org/v2/country/US/indicator/SP.POP.TOTL?format=json&date=2013:2022   ← explicit range
```

```json
[{"page":1,"pages":1,"per_page":50,"total":1,"lastupdated":"2026-07-01"},
 [{"indicator":{"id":"SP.POP.TOTL","value":"Population, total"},
   "country":{"id":"US","value":"United States"},
   "date":"2022","value":333996304,"unit":"","obs_status":""}]]
```

Gotchas: the top level is a **two-element array** - `[0]` is paging metadata, `[1]` is the data (or `null` when the query matched nothing - check before iterating). Rows are newest-first. `value: null` means not-yet-reported. Default page size is 50 - set `per_page` on multi-country/multi-year pulls.

**Country metadata** (income level, region, capital):

```
https://api.worldbank.org/v2/country/JPN?format=json
```

`[1][0]` carries `incomeLevel.value` ("High income"), `region.value`, `capitalCity`. Aggregates like "European Union" have `region.id: ""` - filter them out of per-country rankings.

**Eurostat:**

```
https://ec.europa.eu/eurostat/api/dissemination/statistics/1.0/data/{dataset}?format=JSON&geo=DE&time=2023
```

Example dataset: `tps00001` (population). Response is JSON-stat: values live in `value` keyed by a flat index (`{"value":{"0":83118501}}`), decoded via `dimension.{geo,time}.category.index`. With one geo and one time filter, index `"0"` is your number; for anything multi-dimensional, filter server-side (`&geo=DE&geo=FR`) to keep the index math trivial. Find dataset codes by searching "eurostat" + the metric name; the code is in the dataset's URL on the Eurostat site.

## Deliverable

The series or comparison as a small table - country × year × value with units and the indicator's full name - plus the exact URL used and the `lastupdated` date, with any null years called out.

## Do NOT use - reach for X instead

- **US Census API (api.census.gov)** - every endpoint now requires a key (free signup, but it cannot be liveness-verified keyless, so it ships as a pointer only: sign up, then `get=NAME,B01001_001E&for=state:*&key=...` on the ACS datasets).
- **data.gov CKAN API (catalog.data.gov/api/3)** - returns 404 on every action endpoint, verified. Use each agency's own API instead of the catalog.
- **Static country facts** (capital, currency, flag, language) - geo-places; this skill is time-series statistics.
- **Live market prices, FX** - finance-fx.
- **OECD/IMF deep dives** - out of pack scope; World Bank mirrors most headline series, name it as the tradeoff.

## Quality bar

- No indicator code was invented; unfamiliar metrics went through the lookup step.
- `[1] === null` and `value: null` handled; the reported year is the year of the data.
- Statistics come from the API response, never from training memory - with `lastupdated` quoted.
- A 502 or hang produced one `mrv` retry or an Eurostat fallback, not a retry loop.

Sourced and liveness-verified from the public-apis project (MIT).
