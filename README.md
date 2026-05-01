# portfolio-optimizer-pro

> **Advanced portfolio optimizer** — single-file HTML tool with no backend required.

Built entirely in the browser using [Pyodide](https://pyodide.org) (Python in WebAssembly). Implements institutional-grade portfolio construction techniques accessible from any device.

---

## Features

### Portfolio Construction
Five portfolios optimized with different objectives, all with customizable weight constraints:

| Portfolio | Objective |
|---|---|
| **Max Sharpe** | Maximize risk-adjusted return |
| **Min Volatility** | Minimize portfolio variance |
| **Max Sortino** | Maximize downside-adjusted return |
| **Risk Parity** | Equal risk contribution per asset |
| **Min CVaR** | Minimize tail risk at user-defined α% |

### Quantitative Models
- **Ledoit-Wolf shrinkage** — robust covariance matrix estimation
- **GARCH(1,1)** — volatility forecasting per asset
- **Black-Litterman** — incorporate custom investor views
- **Hidden Markov Model** — regime detection (Bull/Bear switching)
- **CVaR / Expected Shortfall** — tail risk for each portfolio

### Data Input
- Live prices via Yahoo Finance ticker symbols
- CSV upload for portfolio prices and benchmark indexes
- Configurable: risk-free rate, min/max weights, CVaR confidence level

### Output
- Per-portfolio metrics: Return, Volatility, Sharpe, Sortino, CVaR, Max Drawdown
- Cumulative return chart vs. benchmarks
- GARCH volatility forecast chart
- HMM regime chart
- Black-Litterman posterior returns
- Efficient frontier visualization

---

## Usage

### Option A — GitHub Pages (recommended)

1. Fork this repository
2. Go to **Settings → Pages → Source: main branch / root**
3. Access the live tool at `https://<your-username>.github.io/portfolio-optimizer-pro`

### Option B — Local

```bash
git clone https://github.com/lucapelizzardi-rgb/portfolio-optimizer-pro
cd portfolio-optimizer-pro
# Open index.html in any modern browser
open index.html
```

> No installation, no Python environment, no server needed.

---

## CSV Format

See [`docs/csv-format.md`](docs/csv-format.md) for full format specification and Python scripts to generate compatible files.

Sample files are available in [`examples/`](examples/).

---

## Methodology

Full technical documentation in [`docs/methodology.md`](docs/methodology.md), including formulas for:
- Ledoit-Wolf shrinkage estimator
- GARCH(1,1) model
- Black-Litterman posterior
- CVaR / Expected Shortfall
- HMM regime switching

---

## Tech Stack

| Layer | Technology |
|---|---|
| Runtime | [Pyodide](https://pyodide.org) — Python in WebAssembly |
| Optimization | `scipy.optimize`, `numpy` |
| Covariance | `sklearn.covariance.LedoitWolf` |
| Volatility | `arch` (GARCH) |
| Regime | `hmmlearn` |
| Visualization | `Plotly.js` |
| Data | `yfinance`, `pandas` |

---

## Related Projects

- [`risk-analytics-engine`](https://github.com/lucapelizzardi-rgb/risk-analytics-engine) — Python library for modular risk analytics (VaR, CVaR, backtesting, Monte Carlo)

---

## License

MIT License — see [LICENSE](LICENSE) for details.
