---
name: Geo & Places Data
description: Use when a task needs live geographic lookups — "geocode this address", "what's at these coordinates" (reverse geocoding), "lat/lon for this city", "which country/state is this ZIP or postal code in", or "country facts: capital, currency, population, flag". Nominatim (OpenStreetMap) is the geocoding default; Zippopotam for postal codes; APICountries for country facts. All keyless. Do NOT use for weather at a location — use weather-climate instead; do NOT use for country-level statistics over time (GDP, population trends) — use government-open-data instead; if the request is a vague "I need live data", route through public-data-api-picker.
---

# Geo & Places Data

Turn place names, addresses, coordinates, and postal codes into structured geography with keyless APIs. The mistake this prevents: reaching for the Google Maps/Places API from training memory — key-walled, billing-enabled — when Nominatim answers the same lookup free, or stalling on which of the dozens of directory-listed geocoders actually works.

## Ranked APIs

1. **Nominatim** (nominatim.openstreetmap.org) — DEFAULT for geocoding and reverse geocoding. OSM data, global, no key. Hard etiquette rules below.
2. **Open-Meteo Geocoder** — city/town name → lat/lon only. Faster and rule-free; use it when the input is a simple settlement name rather than an address or POI.
3. **Zippopotam.us** — postal code → place/state/coordinates, 60+ countries, no key.
4. **APICountries** (www.apicountries.com) — country facts: capital, currencies, languages, population, flags, borders. No key. (It serves the old REST Countries response shape; REST Countries itself now returns deprecation errors keyless — see Do NOT.)

## Procedure

1. **Classify the input**: address/POI → Nominatim search; coordinates → Nominatim reverse; bare city name → Open-Meteo geocoder; postal code → Zippopotam; country name/code → APICountries.
2. **Obey Nominatim etiquette** — these are enforced, not suggestions: max **1 request/second**, a real **User-Agent** identifying your app (anonymous UAs get blocked), no bulk geocoding of thousands of rows. For a batch, loop with a 1s+ sleep and say so; for very large batches, tell the user this needs a self-hosted Nominatim or a paid geocoder rather than hammering the public instance.
3. **Read coordinates as strings from Nominatim** — `lat`/`lon` come back as JSON strings, not numbers. Parse before arithmetic.

Ask the user only if missing: the place/coordinate input itself, and country context for ambiguous names ("Springfield" — default: take Nominatim's top-ranked hit and say which one it was).

## URL templates + real response shapes

**Geocode (search)**:

```
https://nominatim.openstreetmap.org/search?q={URL-encoded query}&format=jsonv2&limit=1
```

```json
[{"lat":"48.8582599","lon":"2.2945006","name":"Tour Eiffel",
  "display_name":"Tour Eiffel, 5, Avenue Anatole France, ..., 75007, France",
  "category":"man_made","type":"tower","importance":0.62}]
```

Top-level is an **array**; empty array = no match. Add `&addressdetails=1` for a structured `address` object, `&countrycodes=us` to constrain the country.

**Reverse geocode**:

```
https://nominatim.openstreetmap.org/reverse?lat={lat}&lon={lon}&format=jsonv2
```

Returns a single object (not an array): `display_name`, plus `address.{road,city,state,postcode,country_code}` with `&addressdetails=1` (on by default for reverse).

**City name → lat/lon** (when you don't need full addresses):

```
https://geocoding-api.open-meteo.com/v1/search?name={city}&count=1
```

```json
{"results":[{"name":"Berlin","latitude":52.52437,"longitude":13.41053,
  "country_code":"DE","timezone":"Europe/Berlin","population":3426354}]}
```

No `results` key at all when nothing matches — check for the key, not an empty array.

**Postal code**:

```
https://api.zippopotam.us/{country}/{code}        ← e.g. /us/90210, /de/10115
```

```json
{"country":"United States","places":[{"place name":"Beverly Hills",
  "state":"California","state abbreviation":"CA","latitude":"34.0901","longitude":"-118.4065"}]}
```

Gotchas: keys contain **spaces** (`"place name"`, `"post code"`); coordinates are strings; unknown codes return 404 with an empty body, not JSON.

**Country facts**:

```
https://www.apicountries.com/name/{name}      ← fuzzy, returns array
https://www.apicountries.com/alpha/{code}     ← ISO alpha-2/alpha-3, returns object
```

```json
[{"name":"Japan","capital":"Tokyo","region":"Asia","population":126476461,
  "currencies":[{"code":"JPY","symbol":"¥"}],"languages":[{"name":"Japanese"}],
  "alpha2Code":"JP","flags":{"png":"https://flagcdn.com/w320/jp.png"}}]
```

## Deliverable

The structured result (coordinates as numbers, address parts, or country facts) plus the exact URL used. For batches: the loop actually throttled, with the rate stated.

## Do NOT use — reach for X instead

- **Google Maps / Places, Mapbox, HERE** — key-walled with billing; everything above is free. Do not send a user to sign up.
- **REST Countries (restcountries.com)** — its classic endpoints return deprecation errors and the replacement requires an auth key; APICountries serves the same shape keyless.
- **ip-api.com and most free IP-geolocation tiers** — HTTP-only on the free tier (fails any HTTPS requirement); this pack ships no IP-geolocation API.
- **Weather at the location you just geocoded** — weather-climate.
- **Time-series country statistics** — government-open-data.

## Quality bar

- Nominatim calls carry a real User-Agent and never exceed 1 req/s — including inside loops.
- String coordinates are parsed to numbers before any math.
- Ambiguous place names are resolved out loud ("took Springfield, Illinois — Nominatim's top hit").

Sourced and liveness-verified from the public-apis project (MIT).
