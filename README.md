# Market Regime Detection on SPY (K-Means + Random Forest)

Identifies distinct market regimes in the S&P 500 ETF (SPY) from daily data
(2010–2025) using unsupervised learning, then tests whether the regimes can be
predicted and used for position sizing.

## Overview
Markets behave differently in calm uptrends, sideways periods and sell-offs.
Instead of labelling these by hand, this project lets the data define them.

## Method
1. **Data:** Daily SPY prices via `yfinance` (adjusted).
2. **Feature engineering:** Returns, SMA/EMA (20, 50), 10-day momentum,
   20-day volatility, RSI(14), MACD, ATR and Bollinger Bands (`ta` library).
3. **Preprocessing:** Six features (Returns, RSI, MACD, ATR, Volatility,
   Momentum) are standardised and reduced to 4 principal components with PCA.
4. **Regime detection:** K-Means (k = 3) clusters trading days into regimes.
5. **Regime interpretation:**
   | Regime | Avg daily return | Volatility | Momentum | Interpretation |
   |---|---|---|---|---|
   | 0 | −0.38% | 1.55% | −8.97 | Bear / high-stress |
   | 1 | +0.27% | 0.92% | +10.11 | Bull / trending |
   | 2 | +0.11% | 0.73% | +1.33 | Consolidation / low volatility |
6. **Classification:** A Random Forest is trained on the first 80% of the data
   (chronological split, no shuffling) and tested on the last 20%.
   **Test accuracy: 94.6%.**
7. **Allocation backtest:** Bear → 0% invested, Bull → 100%, Consolidation → 50%.
   Positions are lagged one day and compared with buy-and-hold.

## Results
- K-Means separates three economically sensible regimes (see table above).
- Random Forest recovers the regime labels with 94.6% out-of-sample accuracy.


## Limitations
- Regimes are fitted on the full sample, so the backtest has look-ahead bias
  and is a demonstration of the idea, not a tradable result.
- The classifier predicts labels built from the same features, so high accuracy
  partly reflects that overlap.
- No transaction costs or slippage are modelled.
- Number of regimes (k = 3) is chosen by judgement, not optimised.

## Possible extensions
- Walk-forward (rolling) regime fitting to remove look-ahead bias
- Hidden Markov Model comparison
- Transaction costs and risk metrics (Sharpe, max drawdown)

## How to run
pip install -r requirements.txt
Open `Regime Final.ipynb` in Jupyter or Google Colab and run all cells.

## Tech
Python, pandas, NumPy, scikit-learn, ta, yfinance, matplotlib
