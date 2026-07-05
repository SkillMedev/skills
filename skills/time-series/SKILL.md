---
name: Time Series Analysis
description: Decomposes and forecasts time-indexed data with STL, ARIMA/SARIMA, and Prophet, validated by time-ordered backtests against a seasonal-naive baseline. Use when someone asks "forecast next quarter's demand", "is this series seasonal", "why is my ARIMA forecast flat", "how do I backtest a forecast", or has any metric indexed by time that needs prediction or decomposition. Do NOT use for explaining what a trend means for strategy — use trend-analysis instead; for estimating the causal impact of an intervention on a series use causal-inference; for translating a forecast into an ARR or MRR plan use revenue-modeling.
---

# Time Series Analysis

Time series work fails in two characteristic ways: validating on a random split (which leaks the future into training and produces accuracy numbers that evaporate in production), and shipping a model that never had to beat the dumbest possible baseline. The discipline below exists to prevent both — every forecast is backtested in time order and compared against seasonal-naive before anyone sees it.

## Operating procedure

Order matters: exploration decides the seasonal period, the period decides the decomposition and model structure, and validation only means something after the model choices are frozen.

### Step 1: gather inputs

- The series itself, its frequency, and its business meaning. Reindex to a regular frequency before anything else (`series.asfreq("D")`); missing timestamps must become explicit NaNs, then be imputed or modeled, never silently skipped.
- The forecast horizon and what decision it feeds. A 12-month forecast from 18 months of history is a guess — say so. Rules of thumb: require at least 2 full seasonal cycles of history (prefer 3+) before fitting a seasonal model, and keep the horizon under roughly 20% of history length or widen the caveats.
- Known interventions: launches, price changes, outages. Mark them; a level shift modeled as trend poisons everything downstream. If the question is "what did the intervention cause", stop and use causal-inference.
- The seasonal period, from the data's rhythm: 7 for daily data with weekly cycles, 12 for monthly, 52 for weekly, 24 for hourly-with-daily-cycle. Label a guessed period as a guess and verify it in Step 2.

### Step 2: explore before modeling

1. Plot the raw series. Look for trend, seasonality, level shifts, outliers, and variance that grows with level (a candidate for a log transform).
2. Decompose:

```python
from statsmodels.tsa.seasonal import STL
result = STL(series, period=12).fit()
result.plot()
```

Read the components: if the seasonal panel's amplitude dwarfs the residual panel, seasonality is real; if the residual still shows structure, the period is wrong or a second cycle exists.

3. Test stationarity with ADF:

```python
from statsmodels.tsa.stattools import adfuller
stat, pvalue, *_ = adfuller(series.dropna())
```

p < 0.05 rejects the unit root — treat the series as stationary. Otherwise difference.

### Step 3: make the series stationary (for ARIMA)

Difference once for trend, seasonally difference for seasonality:

```python
diff = series.diff().dropna()
seasonal_diff = series.diff(12).dropna()
```

Almost never difference more than twice total (d + D ≤ 2); over-differencing injects noise and shows up as a strongly negative lag-1 ACF. Read ACF/PACF on the differenced series to choose orders: PACF cutting off after lag p suggests AR(p); ACF cutting off after lag q suggests MA(q).

### Step 4: fit candidate models

SARIMA:

```python
from statsmodels.tsa.statespace.sarimax import SARIMAX
model = SARIMAX(series, order=(1, 1, 1), seasonal_order=(1, 1, 1, 12))
fit = model.fit(disp=False)
forecast = fit.get_forecast(steps=12)
ci = forecast.conf_int()
```

`pmdarima.auto_arima` can search orders by AIC, but treat its winner as a candidate, not an answer — verify residuals (Step 6) before trusting it.

Prophet, for business series with strong multiple seasonality and holiday effects:

```python
from prophet import Prophet
m = Prophet(yearly_seasonality=True, weekly_seasonality=True)
m.add_country_holidays(country_name="US")
m.fit(df)  # columns: ds, y
fc = m.predict(m.make_future_dataframe(periods=90))
```

Apply a log transform first when seasonal amplitude scales with the level (multiplicative seasonality), for either model family.

### Step 5: validate in time order — never a random split

Backtest with rolling or expanding windows that respect time:

```python
from sklearn.model_selection import TimeSeriesSplit
tscv = TimeSeriesSplit(n_splits=5)
```

Report MAPE, MAE, and RMSE on held-out future windows only — in-sample fit is not evidence. Always fit the **seasonal-naive baseline** (predict the value from one season ago) through the same backtest. The acceptance threshold: the model's error must beat seasonal-naive (MASE < 1, or lower MAPE on the same folds). A sophisticated model that loses to naive ships the naive.

### Step 6: diagnose residuals

Residuals from a correct model are white noise:

- Ljung-Box test: p > 0.05 means no remaining autocorrelation. p below that means structure is left — revisit orders or the seasonal period.
- Residual ACF plot: no bars beyond the significance band.
- Residual histogram roughly normal **if** the prediction intervals will be quoted; heavy tails mean the intervals are too narrow and should be widened or bootstrapped.

### Step 7: present with honest uncertainty

Always show the confidence interval, and say plainly that uncertainty grows with horizon. Report the backtest error at the actual decision horizon (the 6-month-ahead MAPE for a 6-month decision), not the flattering 1-step-ahead number.

## Worked artifact: model-choice decision

Monthly revenue, 36 points, clear December spike, variance growing with level. Decision trail: period = 12 (monthly with annual cycle, 3 full cycles available — enough). Log transform (multiplicative seasonality). ADF on log series p = 0.41 → difference; on differenced series p = 0.01 → proceed with d = 1, D = 1. ACF/PACF suggest (1,1,1)(0,1,1,12). Backtest on 5 expanding folds at horizon 6: SARIMA MAPE 7.2% vs seasonal-naive 11.8% — model earns its keep, ship with intervals from the log-scale fit back-transformed.

## Deliverable

Produce a forecast package containing: the decomposition plot with a one-line reading, the stationarity and transform decisions with test p-values, the chosen model and orders with the rejected candidates named, backtest error (MAPE/MAE/RMSE) at the decision horizon side-by-side with seasonal-naive, residual diagnostics (Ljung-Box p-value, residual ACF), and the forecast with confidence intervals.

## Do NOT

- Do not validate with a random train/test split; shuffling leaks the future and the reported accuracy is fiction.
- Do not ship any model that has not been compared against seasonal-naive on the same folds.
- Do not model through a known level shift (launch, pricing change) as if it were trend; segment the series or add the intervention as a regressor.
- Do not quote 1-step-ahead error for a 6-step-ahead decision.
- Do not over-difference; d + D above 2 is almost always a specification error, not a cure.
- Do not extrapolate confidently past ~20% of the history length without flagging it.

## Quality bar

Ship only when: the seasonal period is verified by decomposition, not assumed; all accuracy numbers come from time-ordered held-out windows at the decision horizon; the model beats seasonal-naive; Ljung-Box shows no residual autocorrelation; intervals accompany every point forecast; and every data gap, transform, and intervention is documented.
