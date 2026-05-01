# CSV Format Guide

## Portfolio CSV (`maintorta.csv` style)

### Requirements
- First column: `Date` in `YYYY-MM-DD` format
- Remaining columns: one per asset, with the ticker as column header
- Values: **closing prices** (adjusted or unadjusted — be consistent)
- Frequency: daily or monthly (daily recommended for GARCH fitting)
- No missing dates required — the tool forward-fills gaps automatically

### Example

```csv
Date,AAPL,MSFT,GOOGL,BTC-USD
2023-01-02,125.07,239.82,89.70,16625.08
2023-01-03,124.17,237.59,88.73,15727.97
2023-01-04,126.36,239.23,90.54,16851.22
```

### How to generate with Python

```python
import yfinance as yf
import pandas as pd

tickers = ["AAPL", "MSFT", "GOOGL", "BTC-USD"]
df = yf.download(tickers, start="2020-01-01")["Close"]
df.reset_index().to_csv("portfolio.csv", index=False)
```

---

## Benchmark CSV (`benchmarks.csv` style)

### Requirements
- First column: `Date` in `YYYY-MM-DD` format
- Remaining columns: benchmark indexes
- Monthly frequency accepted (the tool aligns dates automatically)
- Partial NaN values at the start are fine (e.g., VWCE.DE starting from 2019)

### Common benchmark tickers

| Ticker | Index |
|---|---|
| `^GSPC` | S&P 500 |
| `^IXIC` | Nasdaq Composite |
| `^STOXX50E` | Euro Stoxx 50 |
| `VWCE.DE` | Vanguard FTSE All-World ETF (EUR) |
| `^FTSE` | FTSE 100 |
| `GLD` | SPDR Gold ETF |

### How to generate with Python

```python
import yfinance as yf

benchmarks = ["^GSPC", "^IXIC", "^STOXX50E", "VWCE.DE"]
df = yf.download(benchmarks, start="2015-01-01", interval="1mo")["Close"]
df.reset_index().to_csv("benchmarks.csv", index=False)
```
