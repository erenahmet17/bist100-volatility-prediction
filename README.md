# BIST100 Volatility Prediction

Predicting future volatility of the BIST100 index using historical price data and machine learning.

## Overview

This project predicts the realized volatility of the BIST100 index over the next 10 trading days, using engineered features derived from historical price and volume data. Rather than attempting to forecast exact price movements (a notoriously difficult and often unrealistic target), the project focuses on **volatility** — a more tractable and practically useful signal for risk management and trading decisions.

## Motivation

Volatility forecasting has direct applications in risk management, portfolio hedging, and position sizing. This project explores whether simple, interpretable models can capture short-term volatility dynamics in an emerging market index (BIST100), and compares linear vs. non-linear approaches.

## Data

- **Source:** Yahoo Finance (`yfinance`)
- **Ticker:** `XU100.IS` (BIST100)
- **Period:** 2019–2026 (daily OHLCV data)

## Methodology

1. **Target variable:** Realized volatility over the next 10 trading days (rolling standard deviation of log returns), shifted forward to avoid data leakage.
2. **Features:**
   - Lagged volatility (1-day, 5-day)
   - 5-day moving average of price
   - 5-day moving average of volume
   - RSI (14-day)
3. **Train/test split:** Chronological 80/20 split (no shuffling, to respect time-series structure).
4. **Models compared:** Linear Regression vs. Random Forest Regressor.

## Key Findings

- **Linear Regression outperformed Random Forest** (lower RMSE and MAE), suggesting that volatility dynamics in this dataset are largely linear — consistent with the well-documented phenomenon of *volatility clustering* (periods of high volatility tend to persist).
- Initial feature importance analysis revealed **severe multicollinearity** between the 5-day and 20-day moving averages (correlation ≈ 0.999). After removing the redundant feature, coefficients became interpretable and confirmed that **past volatility (1-day lag) is the strongest predictor** of future volatility — aligning with financial theory.
- The final model was used to generate a live forecast: as of the latest available data, the model predicts volatility above both the recent 10-day realized level and the 1-year average, suggesting an expected increase in market turbulence.

## Results

| Model | RMSE | MAE |
|---|---|---|
| Linear Regression | 0.00729 | 0.00591 |
| Random Forest | 0.00831 | 0.00669 |

## Limitations

- The model relies on historical patterns and cannot anticipate unforeseen macroeconomic shocks or news events.
- Turkey's high-inflation environment may inflate nominal volatility figures; this is not explicitly adjusted for in the current version.
- Single train/test split rather than walk-forward cross-validation; results may vary across different time windows.

## Tech Stack

- Python (pandas, NumPy, scikit-learn)
- yfinance for data acquisition
- Jupyter Notebook

## How to Run

```bash
pip install -r requirements.txt
jupyter notebook volatility_prediction.ipynb
```

## Future Work

- Walk-forward cross-validation for more robust evaluation
- Additional features (macro indicators, sector-level volatility)
- Comparison with GARCH-family models as a domain-specific baseline
