---
name: Weather & Climate Data
description: Use when a task needs real weather data pulled live — "what's the forecast for X", "current temperature/wind here", "historical weather for this date range", "air quality / AQI right now", or "active US weather alerts" — and you should call a free keyless API instead of deliberating. Open-Meteo is the default (global forecast, history, air quality, no key); api.weather.gov covers official US alerts. Do NOT use for turning place names into coordinates, country facts, or postal codes — use geo-places instead; do NOT use for FX or crypto prices — use finance-fx instead; if the request is a vague "I need live data", route through public-data-api-picker.
---

# Weather & Climate Data

Fetch real weather with one HTTP call. The mistake this prevents: reaching for OpenWeatherMap or WeatherAPI from training memory — both are key-walled and the task stalls at "sign up for an API key" — when Open-Meteo answers the same question keyless in under a second.

## Ranked APIs

1. **Open-Meteo** — DEFAULT for everything: current conditions, 16-day forecast, historical archive, air quality. No key, no signup, global coverage. Free tier is non-commercial, ~10,000 calls/day.
2. **api.weather.gov (NWS)** — US only. Use it when the task needs *official* US government alerts or forecast wording. No key, but a `User-Agent` header is required.

## Procedure

1. **Get coordinates first.** Every endpoint below takes `latitude`/`longitude`, not place names. If you only have a city name, resolve it with Open-Meteo's geocoder: `https://geocoding-api.open-meteo.com/v1/search?name={city}&count=1` → `results[0].latitude`, `results[0].longitude`. For any geocoding beyond this one step (addresses, POIs, reverse), switch to geo-places.
2. **Pick the endpoint for the time range** — current/forecast, historical, air quality, or alerts (templates below).
3. **State units in the answer.** Open-Meteo defaults to °C, km/h, mm; every response carries a `*_units` object — read it rather than assuming. Append `&temperature_unit=fahrenheit&wind_speed_unit=mph&precipitation_unit=inch` for US-facing output.

Ask the user only if missing: location (no default), date range (default: now + 7 days), units (default: metric unless the location is in the US).

## URL templates + real response shapes

**Current + forecast** (up to `forecast_days=16`):

```
https://api.open-meteo.com/v1/forecast?latitude={lat}&longitude={lon}
  &current=temperature_2m,wind_speed_10m,relative_humidity_2m,weather_code
  &daily=temperature_2m_max,temperature_2m_min,precipitation_sum&timezone=auto
```

```json
{"current_units":{"temperature_2m":"°C","wind_speed_10m":"km/h"},
 "current":{"time":"...","temperature_2m":15.9,"wind_speed_10m":10.5},
 "daily":{"time":["...","..."],"temperature_2m_max":[19.3,23.8],"precipitation_sum":[2.9,2.9]}}
```

Gotchas: any `daily=` request **requires** `timezone` (use `auto`) or it errors. `weather_code` is a WMO code (0 clear, 61 light rain, 95 thunderstorm) — translate it, don't print the number. Hourly data uses `&hourly=` with the same variable names.

**Historical** (archive starts 1940, lags ~5 days behind today):

```
https://archive-api.open-meteo.com/v1/archive?latitude={lat}&longitude={lon}
  &start_date=YYYY-MM-DD&end_date=YYYY-MM-DD&daily=temperature_2m_max,precipitation_sum&timezone=UTC
```

Same response shape as forecast `daily`. For dates within the last week, use the forecast endpoint with `&past_days=7` instead.

**Air quality** (separate host):

```
https://air-quality-api.open-meteo.com/v1/air-quality?latitude={lat}&longitude={lon}&current=pm2_5,us_aqi,european_aqi
```

```json
{"current":{"pm2_5":6.0,"us_aqi":35}}
```

`pm2_5` is μg/m³; `us_aqi` is the 0–500 EPA index (≤50 good, 101+ unhealthy for sensitive groups).

**US alerts** (NWS):

```
https://api.weather.gov/alerts/active?area={two-letter state}     ← e.g. area=CA
```

GeoJSON: alerts live in `features[].properties` — `event`, `severity`, `headline`, `expires`. Gotchas: send a real `User-Agent` (e.g. `myapp (contact@example.com)`) or NWS may reject you; there is **no `limit` param** on this endpoint (it 400s); for point forecasts it's two steps — `GET /points/{lat},{lon}` → `properties.forecast` URL → fetch that for `properties.periods[]` with `temperature`, `windSpeed`, `detailedForecast`.

## Deliverable

The fetched numbers with their units and timestamps, plus the exact URL used — so the result is reproducible and the user can see which API answered.

## Do NOT use — reach for X instead

- **OpenWeatherMap, WeatherAPI.com, AccuWeather, weatherstack** — all key-walled; Open-Meteo covers the same ground keyless. Do not send a user to sign up for a key.
- **Geocoding beyond one city→lat/lon step** — geo-places.
- **Earthquakes, space weather** — space-earth-science.
- **Climate-economics indicators (emissions, energy stats by country)** — government-open-data.

## Quality bar

- Every reported number carries its unit, read from the response's `*_units` object.
- The request URL used is shown or logged; the call succeeds on the first try because the template above was copied, not reconstructed.
- No API key was requested from the user for anything this skill covers.

Sourced and liveness-verified from the public-apis project (MIT).
