# Australian Bank Risk Analytics & Portfolio Optimization: A 25-Year Empirical Study

## Overview

This project analyses market risk, systematic risk, and portfolio-level risk-adjusted performance for the four major Australian banks — Commonwealth Bank (CBA), National Australia Bank (NAB), Westpac (WBC) and ANZ — plus Macquarie Group (MQG), using 25 years of daily price data (30 July 2001 to 28 July 2026) benchmarked against the ASX200 Accumulation (total-return) Index.

The pipeline moves from descriptive risk statistics through CAPM/econometrics to mean-variance portfolio optimisation, and treats the "optimal" portfolio as a hypothesis to be checked rather than a final answer: alongside the full-sample (2001–2026) portfolios, a second set of weights is estimated using only 2001–2020 data, frozen, and evaluated on 2021–2026 data never used in estimation — a genuine out-of-sample test, not a re-evaluation of the same weights on a subset of their own training data. Portfolios are also stress-tested against a bank-specific shock and re-estimated across three overlapping historical windows to test how stable the recommended weights really are.

The full write-up, with all tables and figures referenced below, is in [`report/Australian_Bank_Risk_Analytics_Report.docx`](report/).

## Research Questions

1. How do Australia's major banks differ in market risk, tail risk, and systematic risk over a 25-year period spanning multiple crises?
2. Can mean-variance portfolio optimisation improve risk-adjusted outcomes relative to a naive equal-weight allocation?
3. How robust are any such gains to the choice of estimation window, and do they survive genuine out-of-sample testing and stress?

## Data

| | |
|---|---|
| Banks | CBA, NAB, WBC, ANZ (four major Australian banks), plus Macquarie Group (MQG) — ASX-listed |
| Period | 30 Jul 2001 – 28 Jul 2026 (~25 years, 6,349 raw daily prices per bank) |
| Frequency | Daily |
| Price source | Yahoo Finance (`yfinance`); adjusted close prices (dividend- and split-adjusted) |
| Benchmark | ASX200 Accumulation (Total Return) Index |
| Risk-free proxy | RBA official cash rate, converted to a daily compounding-equivalent return; sample mean 3.38% p.a. |

## Methodology

Python (NumPy, Pandas, SciPy, statsmodels, Matplotlib, Seaborn). Descriptive return statistics; historical and parametric Value-at-Risk and Expected Shortfall (95%/99%); CAPM via OLS with Breusch-Pagan, Durbin-Watson and Jarque-Bera diagnostics; crisis-period beta comparison (GFC, COVID-19, 2022–24 rate hikes); mean-variance portfolio optimisation (equal-weight, minimum-variance, maximum-Sharpe under a 40% single-asset cap, solved via SLSQP and cross-checked against 10,000 Monte Carlo portfolios); a genuine out-of-sample test (weights trained on 2001–2020 only, frozen, evaluated on 2021–2026); uniform and differentiated stress testing; robustness testing across three overlapping estimation windows.

## Key Findings

- All five banks carry statistically significant CAPM betas above 1.0 (1.03–1.40); none has a statistically significant CAPM alpha over the full sample — the evidence cannot reject zero abnormal return for any bank, which is not the same as proving alpha is exactly zero.
- **MQG is a structural outlier**: highest return, highest volatility (32.5% p.a.), highest beta (1.40), and by far the deepest drawdown (‑81.7%), with far fatter tails than the other four banks.
- Systematic risk is crisis-dependent: beta rose for **every** bank during COVID-19, while during the 2022–24 rate-hiking cycle it fell for four of five banks and stayed flat only for MQG.
- Using weights estimated from the full 25-year sample (a hindsight exercise), the maximum-Sharpe portfolio (Sharpe 0.524) beats minimum-variance (0.457) and equal-weight (0.454) — but does so by pushing CBA and MQG to a 40% cap each, 80% of the portfolio in two banks.
- That concentration is not free: maximum-Sharpe loses the most (‑16.0%) under a Macquarie-specific stress scenario. In a genuinely disjoint test — separate weights trained only on 2001–2020, frozen, and evaluated fresh on 2021–2026 — minimum-variance **overtakes** maximum-Sharpe (Sharpe 0.875 vs 0.848).
- Maximum-Sharpe portfolio weights are also the least stable across three re-estimation windows (MQG's weight alone swings from 0% to 40% depending on the window used), while minimum-variance is comparatively more stable.
- Net conclusion: mean-variance optimisation delivers real diversification gains over naive equal-weighting via minimum-variance, but the higher-Sharpe maximum-Sharpe solution is also the most concentrated, fragile, and estimation-sensitive of the three portfolios studied.

## Project Structure

```
Australian-Bank-Risk-Analytics/
├── README.md
├── data/
│   ├── raw/             # Source prices (Yahoo Finance) and benchmark exports
│   └── processed/        # Cleaned returns, risk metrics, CAPM/portfolio results
├── notebooks/
│   ├── 01_data_collection.ipynb
│   ├── 02_data_cleaning.ipynb
│   ├── 03_exploratory_analysis.ipynb
│   ├── 04_risk_metrics.ipynb
│   ├── 05_capm_regression.ipynb
│   ├── 06_regression_diagnostics.ipynb
│   └── 07_portfolio_fundamentals.ipynb
├── figures/
└── report/
    └── Australian_Bank_Risk_Analytics_Report.docx
```

> **Note on structure:** notebook numbering was updated since an earlier draft of this README (previously 01–08 with a gap at 04 and "_FIXED_FINAL"-style suffixes on two files) — this reflects what's actually in the project as of this synthesis. A `src/` package of reusable functions has not yet been extracted from the notebooks — see the code-cleanup recommendations shared alongside this README for a suggested `data_processing.py` / `risk_metrics.py` / `capm.py` / `portfolio.py` split.

## Reproduction Instructions

1. Clone the repository and create a Python environment (3.10+ recommended).
2. Install dependencies: `pip install -r requirements.txt`. A verified `requirements.txt` is provided alongside this README (checked against every `import` statement across all seven notebooks: matplotlib, numpy, openpyxl, pandas, scipy, seaborn, statsmodels, yfinance).
3. Run the notebooks in numeric order (`01` → `07`); each writes its outputs to `data/processed/` for the next notebook to consume.
4. All figures, including the portfolio-optimisation, out-of-sample, and stress-testing charts from notebook `07`, are saved to `figures/` via `plt.savefig(...)` calls in each notebook.

## Limitations

Historical performance does not guarantee future results — though this study's own out-of-sample test (trained 2001–2020, evaluated 2021–2026) is a genuinely disjoint check, not a re-use of the same estimation window; CAPM is a single-factor model explaining roughly half of daily return variance; portfolio optimisation is sensitive to estimated inputs, especially expected returns; historical VaR assumes the past is informative about future tail risk; stress scenarios here are hypothetical rather than modelled; the analysis covers market risk only, not credit, liquidity, or operational risk; the robustness sub-windows (Section 7.3 of the report) partially overlap the out-of-sample test period and should be read as testing sensitivity of the full-sample approach, not as a second validation of the out-of-sample result. See the full report's Limitations section for the complete list.