---
name: Finance & FX Data
description: Use when a task needs live or historical money data — "convert USD to EUR", "current/past exchange rate", "FX rate on this date / over this range", or "current price of Bitcoin/Ethereum, market cap, 24h change". Frankfurter (ECB reference rates, no key) is the FX default; CoinGecko's free keyless tier covers crypto. Do NOT use for stock quotes or equities — no keyless stock API survives verification, say so instead of guessing; do NOT use for country economic indicators like GDP or inflation series — use government-open-data instead; if the request is a vague "I need live data", route through public-data-api-picker.
---

# Finance & FX Data

Fetch exchange rates and crypto prices with keyless calls. The mistake this prevents: reaching for exchangerate.host, Fixer, or Alpha Vantage from training memory — all key-walled now — or hand-waving a stale rate from training data into a real calculation.

## Ranked APIs

1. **Frankfurter** (api.frankfurter.dev) — DEFAULT for fiat FX. European Central Bank reference rates, ~30 major currencies, history back to 1999, time series, no key, no rate limit worth worrying about.
2. **CoinGecko free tier** (api.coingecko.com) — crypto prices, market caps, 24h change for thousands of coins. No key, but a tight limit: roughly 5–15 calls/min from an anonymous IP. Batch ids into one call; on 429, back off 60s.

## Procedure

1. **Fiat or crypto?** Fiat → Frankfurter. Crypto → CoinGecko. Crypto→fiat conversion is one CoinGecko call (`vs_currencies=eur`), not two hops.
2. **Date the rate.** ECB reference rates update ~16:00 CET on business days; the response's `date` field tells you which day you actually got. Weekend/holiday requests return the previous business day — quote that date, don't present it as today's.
3. **Use CoinGecko ids, not tickers.** The `ids` param wants `bitcoin`, `ethereum`, `solana` — not BTC/ETH. For an unfamiliar coin, resolve once via `/api/v3/search?query={name}` → `coins[0].id`.
4. **Never do FX math from memory.** If the task multiplies money by a rate, the rate comes from one of these calls, with the date attached.

Ask the user only if missing: currency pair or coin (no default), date/range (default: latest), amount (default: 1).

## URL templates + real response shapes

**Latest FX rate:**

```
https://api.frankfurter.dev/v1/latest?base=USD&symbols=EUR,GBP,JPY
```

```json
{"amount":1.0,"base":"USD","date":"<last business day>","rates":{"EUR":0.87352,"GBP":0.74878,"JPY":161.15}}
```

Convert an amount server-side with `&amount=250` — `rates` comes back pre-multiplied.

**Historical / time series:**

```
https://api.frankfurter.dev/v1/2024-01-15?base=USD&symbols=EUR          ← one date
https://api.frankfurter.dev/v1/2024-01-02..2024-01-31?base=USD&symbols=EUR   ← range
https://api.frankfurter.dev/v1/currencies                               ← code → name map
```

Range shape: `{"rates":{"2024-01-02":{"EUR":0.91274},"2024-01-03":{"EUR":0.91583}}}` — keyed by date, business days only (a full year returns ~256 daily points; no weekend keys, so don't treat gaps as missing data). Omit the end date (`/2024-01-02..`) to run to present.

**Crypto spot price:**

```
https://api.coingecko.com/api/v3/simple/price?ids=bitcoin,ethereum&vs_currencies=usd&include_24hr_change=true
```

```json
{"bitcoin":{"usd":63289,"usd_24h_change":1.19},"ethereum":{"usd":1790.89,"usd_24h_change":1.90}}
```

Unknown ids are silently dropped from the response — a missing key means a wrong id, not a null price.

**Crypto market table (rank, cap, supply):**

```
https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=20&page=1
```

Array of `{"id","symbol","name","current_price","market_cap","market_cap_rank","price_change_percentage_24h","circulating_supply","ath"}` — one call replaces twenty `/simple/price` calls; prefer it for any list.

## Deliverable

The rate or price with its `date`/timestamp and source named, plus the exact URL used. Any conversion shows the arithmetic: `250 USD × 0.87352 (ECB, <rate date>) = 218.38 EUR`.

## Do NOT use — reach for X instead

- **exchangerate.host** — every request now requires an `access_key`; verified failing keyless. Frankfurter replaces it.
- **Fixer, Alpha Vantage, Finnhub, Polygon, OpenExchangeRates** — key-walled; do not send a user to sign up when Frankfurter/CoinGecko cover the need.
- **Stock/equity quotes** — nothing keyless survives verification. Say that plainly and name the paid options rather than fabricating a price.
- **GDP, inflation, unemployment, trade series** — government-open-data.
- **Financial advice** — this skill fetches numbers; buy/sell/allocation judgments are out of scope. Present data, not recommendations.

## Quality bar

- Every number carries its date and source; no rate or price originates from training memory.
- Frankfurter's ~30-currency ceiling respected — exotic pairs (e.g. NGN, VND) are declared unsupported, not approximated.
- CoinGecko calls are batched; a 429 produces a backoff, not a retry loop.

Sourced and liveness-verified from the public-apis project (MIT).
