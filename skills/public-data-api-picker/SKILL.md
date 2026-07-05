---
name: Public Data API Picker
description: Use when a task needs real, live data but the domain is vague or unstated - "I need real data for X", "is there a free API for this?", "pull actual data into this demo", "no mock data, use a real source", or picking a data source before building. Routes to the right Live Data skill (weather-climate, geo-places, finance-fx, space-earth-science, government-open-data, test-placeholder-data, language-reference, fun-content), every one backed by keyless liveness-verified APIs. Do NOT use when the domain is already explicit - a request naming weather, FX, earthquakes, or fake users goes straight to that skill; do NOT use for designing your own API - use api-design instead.
---

# Public Data API Picker

Route a vague "I need live data" request to the sibling skill whose APIs are liveness-verified - instead of deliberating over a directory or reaching for a key-walled API from training memory. This skill only routes; every URL template, response shape, and gotcha lives in the domain skill.

## Routing table

| The task needs | Route to |
|---|---|
| Forecast, current conditions, historical weather, air quality, US weather alerts | weather-climate |
| Geocoding, reverse geocoding, city→coordinates, postal codes, country facts (capital, currency, flag) | geo-places |
| FX rates (current or historical), currency conversion, crypto prices and market caps | finance-fx |
| Earthquakes, NASA imagery (APOD, Earth full-disk), near-Earth asteroids | space-earth-science |
| Country statistics over time - GDP, population, inflation, emissions; EU-official figures | government-open-data |
| Fake products/users/posts for a demo or tutorial, placeholder images, teaching CRUD | test-placeholder-data |
| Word definitions, pronunciation, synonyms; Wikipedia summaries and search | language-reference |
| Trivia/quiz questions, animal pictures, advice, random facts | fun-content |

## Tie-breakers

- "Weather in Paris" needing coordinates first → still weather-climate (it embeds the one city→lat/lon step); geo-places only when geography *is* the deliverable.
- Money: rates and prices → finance-fx; economic indicators over years → government-open-data.
- Images: layout filler → test-placeholder-data (Picsum); dog/cat content → fun-content; satellite Earth → space-earth-science.
- Facts about a country: static (capital, currency) → geo-places; time-series (GDP, population trend) → government-open-data; prose ("history of Japan") → language-reference.
- Demo needs *realistic-looking* data → test-placeholder-data; demo needs *true* data → the matching domain skill. Ask which one thing if genuinely unclear; never mix fake data into a real-data feature.

## Worked routing example

Request: *"Add a widget to this dashboard showing actual conditions for the user's city - no mock data."*

Bad - deliberating instead of routing: listing OpenWeatherMap, WeatherAPI, and Open-Meteo with pros and cons, asking the user which they prefer, or starting from a key-signup page. The user asked for data, not a survey; two of those three options are key-walled dead ends.

Good - route and act: "Real conditions by city is a weather need with an embedded city→coordinates step - weather-climate." Then that skill's procedure runs: geocode the city via its one-step geocoder, call Open-Meteo's forecast endpoint with `current=` fields, render values with units from `current_units`. One skill fired, zero APIs evaluated, first call worked.

Counter-example the other direction: *"Show each user's distance from HQ"* mentions no weather - geography is the deliverable, so it routes to geo-places even though both skills touch coordinates.

## Not covered - say so instead of improvising

No skill in this set covers: **stock/equity quotes**, **IP geolocation**, **ISS position**, or **US Census microdata** - the free options failed liveness or auth verification. Name the gap and the nearest paid/keyed alternative rather than citing a dead endpoint from memory.

## Deliverable

A one-line route ("this is an FX-history need - finance-fx") and then the routed skill's procedure executed. If two skills genuinely apply, name both and the split.

## Do NOT

- Do not execute API calls from this skill - templates and gotchas live in the domain skills; duplicating them here is how they drift.
- Do not fall back to OpenWeatherMap, Google Maps, Alpha Vantage, or other key-walled defaults from training memory - the routed skill names the verified keyless replacement.
- Do not dump API options for the user to evaluate - route to one default and act.

Sourced and liveness-verified from the public-apis project (MIT).
