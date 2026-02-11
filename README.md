# CAPM Regression (Alpha & Beta) for Fund Returns

This repo contains a small Python/Jupyter workflow for running a CAPM-style regression to estimate a fund’s **beta** (market sensitivity) and **alpha** (intercept) versus a chosen market benchmark.

During my internship at **ALPS Capital Management**, I used this notebook as a quick, repeatable way to sanity-check and summarize hedge fund behavior:
- How much of a fund’s return stream is explained by broad equity market moves (beta / market exposure)?
- How much return remains unexplained by the benchmark (alpha / residual return), recognizing that “alpha” here is *model-dependent*?
- How consistent is the relationship (R²), and does it differ across managers/strategies?

No proprietary ALPS or manager data is included in this repo—the notebook expects you to provide your own fund return series.

## What it does

The notebook (`capm-regression.ipynb`) does the following:
1. Downloads historical benchmark prices from Yahoo Finance via `yfinance` (default: `ACWI`).
2. Computes benchmark returns and compounds them to **monthly** returns.
3. Loads a fund returns CSV and converts percent strings to decimals.
4. Aligns fund and benchmark returns by date.
5. Runs a simple linear regression using `numpy.polyfit`:

   \[
   R_{fund,t} = \\alpha + \\beta \\cdot R_{mkt,t} + \\varepsilon_t
   \]

6. Plots a scatter chart with a fitted line, and prints `beta`, `alpha`, and `R²`.

## Setup

1. Create and activate a virtual environment (optional but recommended).
2. Install dependencies:

   `pip install -r requirements.txt`

## Input data format

The fund CSV is expected to include:
- `Date`: parseable by pandas (e.g., `2022-01-31`)
- `Net Return`: a percentage string like `1.23%` (the notebook converts this to `0.0123`)

If your file is already in decimal returns (e.g., `0.0123`), you can remove the percent-stripping line in the notebook.

## Run

- Open `capm-regression.ipynb` in Jupyter/VS Code.
- Update the fund CSV path in the notebook to point to your file.
- Run all cells.

## Interpreting results (how I used it at ALPS)

- **Beta (β)**: “market exposure.” A beta near 0 suggests low equity-market dependence; >1 suggests amplified equity exposure.
- **Alpha (α)**: regression intercept. In this notebook it’s **monthly alpha in return units**, not risk-free-adjusted excess return alpha.
- **R²**: fraction of fund return variance explained by the benchmark—useful for distinguishing “equity-like” behavior from more idiosyncratic strategies.

In practice, I used these outputs to:
- Compare market dependence across hedge funds and over time,
- Flag funds whose “hedge” profile drifted toward equity beta,
- Create simple, explainable visuals for internal notes and manager monitoring.

## Customization ideas

- Change the benchmark ticker (e.g., `SPY`, `VTI`, `IWM`, `AGG`) depending on what you’re testing.
- Use **excess returns**: subtract a risk-free series from both fund and benchmark returns before regression.
- Swap `numpy.polyfit` for `statsmodels` OLS to get standard errors and p-values.
- Run rolling-window regressions to see how beta changes over time.

## Notes / limitations

- This is intentionally a lightweight CAPM-style regression (one factor). It’s not a full multi-factor model and should not be the sole basis for investment decisions.
- Results depend heavily on the benchmark choice, return frequency, and data quality (fees, smoothing, stale pricing, backfill bias, etc.).

