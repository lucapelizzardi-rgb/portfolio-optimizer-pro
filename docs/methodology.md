# Methodology

## Overview

This tool implements a full institutional-grade portfolio construction pipeline in a single HTML file with no backend required. All computation runs client-side via [Pyodide](https://pyodide.org) (Python in WebAssembly).

---

## 1. Data Input

- **Ticker mode**: fetches price data via `yfinance` directly in the browser
- **CSV mode**: accepts user-uploaded price files (one column per asset + `Date` index)
- **Benchmark CSV**: separate file with benchmark index prices for comparison

### Expected CSV Format

```
Date,TICKER1,TICKER2,...
YYYY-MM-DD,price1,price2,...
```

---

## 2. Return Estimation

Log returns are computed as:

```
r_t = ln(P_t / P_{t-1})
```

Annualized mean and covariance are estimated using **Ledoit-Wolf shrinkage** to reduce estimation error in high-dimensional portfolios.

---

## 3. Portfolio Construction

Five portfolios are constructed with different objectives:

| Portfolio | Objective | Method |
|---|---|---|
| Max Sharpe | Maximize (μ - rf) / σ | SciPy `minimize` with SLSQP |
| Min Volatility | Minimize σ_p | Quadratic programming |
| Max Sortino | Maximize μ / σ_downside | Custom optimizer |
| Risk Parity | Equal risk contribution | Iterative scaling |
| Min CVaR | Minimize Expected Shortfall at α% | Linear programming |

All portfolios respect user-defined constraints: min/max weight per asset, long-only.

---

## 4. Covariance Estimation — Ledoit-Wolf

The Ledoit-Wolf estimator shrinks the sample covariance matrix S toward a structured target T:

```
Σ_LW = (1 - α) · S + α · T
```

where α is the optimal shrinkage coefficient analytically derived to minimize Mean Squared Error.

---

## 5. Volatility Forecasting — GARCH(1,1)

For each asset, a GARCH(1,1) model is fitted:

```
σ²_t = ω + α · ε²_{t-1} + β · σ²_{t-1}
```

Parameters ω, α, β are estimated via Maximum Likelihood. The model produces a volatility forecast term structure used to stress-test portfolio risk.

---

## 6. Black-Litterman Model

The Black-Litterman model blends market equilibrium returns π with investor views Q:

```
μ_BL = [(τΣ)⁻¹ + P'Ω⁻¹P]⁻¹ · [(τΣ)⁻¹π + P'Ω⁻¹Q]
```

Where:
- π = implied equilibrium excess returns (reverse-optimized from market caps)
- P = pick matrix (which assets each view refers to)
- Q = vector of expected returns per view
- Ω = diagonal uncertainty matrix for views
- τ = scalar controlling prior confidence (default: 0.05)

Users can input custom views through the UI.

---

## 7. Regime Detection — Hidden Markov Model

A 2-state Gaussian HMM is fitted on portfolio log-returns:

- **State 0**: Low volatility regime (Bull market)
- **State 1**: High volatility regime (Bear/Crisis)

The Viterbi algorithm decodes the most likely regime sequence. The tool plots regime coloring on the cumulative return chart.

---

## 8. Risk Metrics — CVaR / Expected Shortfall

For each portfolio, at confidence level α (e.g., 95%):

```
VaR_α = quantile(r, 1 - α)
CVaR_α = E[r | r ≤ VaR_α]
```

CVaR is computed historically (empirical distribution) and parametrically (assuming normality).

Additional metrics reported:
- Annualized Return
- Annualized Volatility
- Sharpe Ratio
- Sortino Ratio
- Maximum Drawdown
- Calmar Ratio

---

## References

- Ledoit, O., & Wolf, M. (2004). *A well-conditioned estimator for large-dimensional covariance matrices*
- Black, F., & Litterman, R. (1992). *Global Portfolio Optimization*
- Bollerslev, T. (1986). *Generalized Autoregressive Conditional Heteroskedasticity*
- Baum et al. (1970). *A Maximization Technique in Statistical Estimation — HMM*
- Rockafellar, R.T., & Uryasev, S. (2000). *Optimization of Conditional Value-at-Risk*
