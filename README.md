# CAPM Regression (Alpha & Beta) for Fund Returns

This repo contains a small Python/Jupyter workflow for running a CAPM-style regression to estimate a fund's **beta** (market sensitivity) and **alpha** (intercept) versus a chosen market benchmark.

During my internship at **ALPS Capital Management**, I used this notebook as a quick, repeatable way to sanity-check and summarize hedge fund behavior:
- How much of a fund's return stream is explained by broad equity market moves (beta / market exposure)?
- How much return remains unexplained by the benchmark (alpha / residual return), recognizing that "alpha" here is model-dependent?
- How consistent is the relationship (R^2), and does it differ across managers/strategies?

No proprietary ALPS or manager data is included in this repo—the notebook expects you to provide your own fund return series.

## What's in the repo

- `capm-regression.ipynb`: main notebook
- `requirements.txt`: Python dependencies
- `fund_returns.csv`: example fund monthly returns (decimal)
- `market_returns.csv`: example market monthly returns (decimal)

## What the notebook does

1. Downloads historical benchmark data from Yahoo Finance via `yfinance` (default ticker in the notebook: `ACWI`).
2. Computes market returns (and compounds daily returns to monthly returns when needed).
3. Loads fund returns from a CSV.
4. Aligns fund and market returns on common dates.
5. Runs a simple CAPM regression (optionally on excess returns using a constant risk-free rate):

   \[
   (R_{fund,t} - r_{f,t}) = \alpha + \beta \cdot (R_{mkt,t} - r_{f,t}) + \varepsilon_t
   \]

6. Produces:
   - Scatter plot of fund vs market **excess** returns with fitted line
   - Bar chart of mean alpha/beta contributions (return units)
   - Bar chart of alpha/beta component weights normalized to 0–100%

## Setup

`pip install -r requirements.txt`

## Input data format

Fund returns CSV should include:
- `Date`: parseable by pandas (e.g., `2022-01-31`)
- `Net Return`: either
  - a decimal return like `0.0123`, or
  - a percentage string like `1.23%` (if you keep percent-string parsing in the notebook)

## Risk-free rate (single yield)

The notebook supports setting a single annual yield (e.g., "10Y = 4.2%") and converting it to an approximate monthly return to compute excess returns.

Note: a 10-year yield is not a standard 1-month risk-free proxy (1M/3M T-bills are more typical), but it's acceptable for a lightweight constant-rate adjustment.

## Ensuring monthly market returns are computed correctly

If you compute monthly returns by compounding daily returns:

`(1 + r_daily).prod() - 1`

that's correct for simple returns. To sanity check, compare it to month-end price-to-price returns (and prefer total-return prices like `Adj Close` when available).

## Notes / limitations

- This is intentionally a lightweight 1-factor CAPM-style regression. It's not a full multi-factor model and should not be the sole basis for investment decisions.
- Results depend heavily on benchmark choice, return frequency, and data quality (fees, smoothing, stale pricing, backfill bias, etc.).

