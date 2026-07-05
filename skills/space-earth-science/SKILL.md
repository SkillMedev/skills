---
name: Space & Earth Science Data
description: Use when a task needs live space or geophysical data — "recent earthquakes near X / above magnitude Y", "NASA picture of the day", "near-Earth asteroids this week", or "latest full-disk Earth image". USGS FDSN is the default for quakes (no key, GeoJSON); NASA's APIs cover astronomy (DEMO_KEY works, free key is instant). Do NOT use for weather, storms, or air quality — use weather-climate instead; do NOT use for maps/geocoding — use geo-places instead; if the request is a vague "I need live data", route through public-data-api-picker.
---

# Space & Earth Science Data

Fetch earthquakes and NASA astronomy data with verified endpoints. The mistakes this prevents: trusting community space APIs from training memory — the SpaceX API (api.spacexdata.com) and the popular ISS-position endpoints fail TLS or time out, verified — and burning NASA's shared `DEMO_KEY` quota mid-task.

## Ranked APIs

1. **USGS Earthquake FDSN** (earthquake.usgs.gov) — DEFAULT for anything seismic. Global catalog, rich filtering, GeoJSON, no key, fast.
2. **NASA APIs** (api.nasa.gov) — APOD (picture of the day), NeoWs (near-Earth objects). `DEMO_KEY` works but is shared per-IP (~10–30 req/hr); a real key is a 30-second instant email signup at api.nasa.gov and lifts it to 1,000 req/hr. Use `DEMO_KEY` for one-off calls only.
3. **NASA EPIC mirror** (epic.gsfc.nasa.gov) — full-disk Earth imagery, **no key at all** on this host. Prefer it over the api.nasa.gov EPIC route, which spends key quota for identical data.

## Procedure

1. **Quakes: filter server-side.** Time, magnitude, radius, and sort all belong in the query string — never fetch the whole feed and filter locally (templates below).
2. **NASA: budget the key.** One-off → `DEMO_KEY`. Anything in a loop, cron, or shipped app → tell the user to grab the instant free key and read the `X-RateLimit-Remaining` header. A 429 here means quota, not outage — the fix is a key, not a retry.
3. **Convert epoch times.** USGS `time` is **milliseconds** since epoch; NASA dates are `YYYY-MM-DD` strings. Report human-readable UTC.

Ask the user only if missing — quakes: region (default: global), min magnitude (default: 4.5), window (default: past 7 days). NASA: date (default: today).

## URL templates + real response shapes

**Earthquakes:**

```
https://earthquake.usgs.gov/fdsnws/event/1/query?format=geojson
  &starttime={YYYY-MM-DD}&endtime={YYYY-MM-DD}&minmagnitude=5
  &latitude=35.6&longitude=139.7&maxradiuskm=500        ← optional region circle
  &orderby=magnitude&limit=20
```

```json
{"features":[{"properties":{"mag":6.2,"place":"58 km W of Tobelo, Indonesia",
   "time":1783045888846,"tsunami":0,"alert":"green","felt":7,
   "url":"https://earthquake.usgs.gov/earthquakes/eventpage/us6000t9r5"},
  "geometry":{"coordinates":[127.63,1.73,10.0]}}],
 "metadata":{"count":1}}
```

Gotchas: `geometry.coordinates` is `[lon, lat, depth-km]` — longitude first. `orderby=time` is newest-first. `alert` (PAGER: green/yellow/orange/red) and `tsunami` are the impact fields worth surfacing. Hard cap 20,000 events per query — page with `offset` (1-based) if you hit it.

**NASA APOD** (note: this endpoint often takes 2–10s — set a generous timeout):

```
https://api.nasa.gov/planetary/apod?api_key={KEY}&date={YYYY-MM-DD}
```

```json
{"date":"...","title":"...","explanation":"...","media_type":"image",
 "url":"https://apod.nasa.gov/apod/image/...jpg","hdurl":"..."}
```

Check `media_type` — on video days `url` is a YouTube embed, not an image.

**Near-Earth asteroids:**

```
https://api.nasa.gov/neo/rest/v1/feed?start_date={YYYY-MM-DD}&end_date={YYYY-MM-DD}&api_key={KEY}
```

Max 7-day window per call (400 above that). Shape: `element_count`, then `near_earth_objects` keyed **by date**, each object with `name`, `is_potentially_hazardous_asteroid`, `estimated_diameter.meters.estimated_diameter_max`, and `close_approach_data[0].miss_distance.kilometers` (a string — parse it).

**Full-disk Earth image (keyless mirror):**

```
https://epic.gsfc.nasa.gov/api/natural                       ← latest image list
https://epic.gsfc.nasa.gov/archive/natural/{yyyy}/{mm}/{dd}/png/{image}.png
```

List items carry `image` (basename) and `date`; split the date into the `{yyyy}/{mm}/{dd}` path parts and append `{image}.png`.

## Deliverable

The fetched events/objects with human-readable UTC times, magnitudes/diameters with units, impact fields (alert, tsunami, hazardous flags) surfaced, and the exact URL used.

## Do NOT use — reach for X instead

- **SpaceX API (api.spacexdata.com)** — fails TLS (HTTP 525), verified; the project is archived. Don't cite it as a data source.
- **ISS position (api.open-notify.org / wheretheiss.at)** — HTTP-only and timing out respectively, verified. No keyless ISS tracker ships in this pack; say so rather than pointing at a dead endpoint.
- **Weather, storms, air quality** — weather-climate (the USGS/NASA slot is rocks and space, not atmosphere).
- **"Where is this quake's city?"** enrichment — geo-places for reverse geocoding beyond the `place` string USGS already gives you.

## Quality bar

- USGS filters ran server-side; no full-catalog downloads.
- Epoch milliseconds became UTC datetimes; `[lon, lat]` order was respected.
- Any loop against api.nasa.gov uses a real key, never `DEMO_KEY`.

Sourced and liveness-verified from the public-apis project (MIT).
