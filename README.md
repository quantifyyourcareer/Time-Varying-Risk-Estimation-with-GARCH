# Time-Varying Risk Estimation with GARCH

A two-part project that builds a complete volatility modeling pipeline on financial return data — from foundational concepts in Excel to a fully estimated Python implementation with formal statistical tests and backtesting.

---

## Project Structure

| File | Description |
|---|---|
| `TimeSeriesProject_Part1_Excel.xlsx` | Concept-building workbook using simulated data |
| `TimeSeriesProject_Part2_PY.ipynb` | Python implementation on real S&P 500 data (2019–2024) |

**Start with Part 1.** For concepts.

---

## Part 1 — Excel (Concept Workbook)

Uses a simulated illustrative price series so the mechanics are visible without market noise.

Covers:
- Log-return construction from raw prices
- The four stylized facts of financial returns — visually verified on the simulated series
- Autocorrelation function (ACF) built from scratch and linked to the ACF plot
- AR(1) model with hardcoded parameters
- GARCH(1,1) with manually set omega, alpha, and beta — see how each parameter shapes the volatility estimate
- Value at Risk under fixed-parameter GARCH
- Kupiec Proportion of Failures backtest

---

## Part 2 — Python (Full Implementation)

Real S&P 500 data, 2019–2024. 

**Libraries:** `yfinance`, `pandas`, `numpy`, `matplotlib`, `seaborn`, `scipy`, `statsmodels`, `arch`

Install:
```bash
pip install yfinance pandas numpy matplotlib seaborn scipy statsmodels arch
```

### What the notebook builds

**Section 1 — Data**
Pull real S&P 500 closing prices and compute log-returns. The 2019–2024 range is chosen deliberately: it spans the COVID crash (March 2020), a low-volatility recovery (2021), the 2022 bear market, and the 2023–24 rally — multiple volatility regimes in one window.

**Section 2 — Stylized Facts**
Verify all four stylized facts on real data:
1. Returns look like random noise — 20-day rolling mean stays near zero
2. Fat tails (leptokurtosis) — histogram vs Normal, excess kurtosis
3. Small autocorrelation in raw returns — ACF mostly inside confidence bands
4. Volatility clustering — squared returns show strong, persistent autocorrelation

The contrast between Facts 3 and 4 is the core motivation for GARCH: returns are unpredictable in direction but somewhat predictable in magnitude.

**Section 3 — ADF Test**
Formal stationarity test (Augmented Dickey-Fuller). Prices: fail to reject unit root. Log-returns: reject strongly. Confirms the transformation is correct before fitting any model.

**Section 4 — ACF and PACF**
ACF and PACF plotted side by side. The PACF is introduced here (not covered in Part 1) — it isolates the direct relationship at each lag, stripping out indirect paths. The Box-Jenkins identification rules are applied to determine appropriate model structure.

**Section 5 — ARIMA Order Selection**
AIC and BIC grid search over ARIMA(p,0,q) combinations selects ARIMA(1,0,1). Not included in this notebook to keep it focused on models.

**Section 6 — ARIMA Fit and Residual Diagnostics**
ARIMA(1,0,1) fitted. Residual diagnostics run four ways: residuals over time, histogram vs Normal, ACF of residuals, ACF of squared residuals. Ljung-Box and ARCH-LM tests confirm that ARIMA cleans the mean structure but leaves the variance clustering untouched — the formal justification for GARCH.

**Section 7 — GARCH(1,1)**
$$\sigma_t^2 = \omega + \alpha \varepsilon_{t-1}^2 + \beta \sigma_{t-1}^2$$

Fitted using the `arch` library. Output includes:
- Estimated parameters (omega, alpha, beta) with interpretation
- Persistence (alpha + beta) and implied half-life of a volatility shock
- Unconditional annualized volatility
- Time-varying conditional volatility plot with ±2σ bands over returns
- GARCH residual diagnostics confirming ARCH effects are captured

**Section 8 — Value at Risk and Expected Shortfall**
Time-varying VaR at 95% and 99% confidence from GARCH conditional volatility:

$$\text{VaR}_\alpha(t) = -\Phi^{-1}(\alpha) \cdot \hat{\sigma}_t$$
$$\text{ES}_\alpha(t) = \frac{\phi(\Phi^{-1}(\alpha))}{\alpha} \cdot \hat{\sigma}_t$$

VaR expands during stress (COVID peak: >6% daily) and contracts in calm periods. A constant-sigma model is wrong in both directions.

**Section 9 — Kupiec Backtest**
Kupiec (1995) Proportion of Failures test validates whether the observed breach rate is consistent with the model's claimed confidence level:

$$\text{LR} = 2\left[N\ln\frac{N/T}{\alpha} + (T-N)\ln\frac{1-N/T}{1-\alpha}\right] \sim \chi^2_1$$

Reject the model if LR > 3.841 (5% significance). Results reported for both VaR 95% and VaR 99%, with a rolling breach rate chart.

**Section 10 — Takeaways and Extensions**
Full comparison table of what Part 1 and Part 2 each cover. Honest statement of the Normal-GARCH limitation (fat tails). Natural next steps: Student-t GARCH, GJR-GARCH, EGARCH, rolling-window backtest, DCC-GARCH for multi-asset portfolios.

**Bonus — Student-t GARCH**
Fits GARCH(1,1) with Student-t innovations alongside the Normal model and re-runs the Kupiec test. Compares LR statistics and AIC to quantify how much calibration improves when the innovation distribution accounts for fat tails.

---

## Key Concepts

| Concept | Where introduced |
|---|---|
| Log-returns | Part 1 (Excel), Section 1 (Python) |
| Four stylized facts | Part 1 (Excel), Section 2 (Python) |
| ADF stationarity test | Section 3 |
| ACF | Part 1 (Excel), Section 4 (Python) |
| PACF | Section 4 (Python only) |
| ARIMA order selection (AIC/BIC) | Section 5 |
| GARCH(1,1) | Part 1 (manual params), Section 7 (MLE) |
| Value at Risk | Part 1 (fixed sigma), Section 8 (time-varying) |
| Expected Shortfall | Section 8 |
| Kupiec backtest | Part 1 (Excel), Section 9 (Python) |

---

## Disclaimer

For educational purposes only. Nothing in this project constitutes financial advice, investment advice, or a recommendation to buy or sell any security. Historical S&P 500 data is used solely to illustrate modeling concepts — past performance does not indicate future results. All analysis and interpretations are my own and do not reflect the views of my employer or any affiliated institutions.

AI assisted with structuring explanations. All technical content, formulas, and interpretations verified.

---

*'Quant'ify Your Career*  
[topmate.io/hiteeksha](https://topmate.io/hiteeksha) | quantifyyourcareer@gmail.com | [@QuantifyYourCareer](https://youtube.com/@QuantifyYourCareer)
