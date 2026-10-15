# Sales & Demand Forecasting for Businesses

**Future Interns Machine Learning Internship, Task 1 (`FUTURE_ML_01`)**

## Problem statement
Retail businesses need to know how much they will sell in the coming months to plan inventory, cash flow and staffing, and to avoid overstocking or stock-outs. This project builds a model that forecasts the next 12 months of sales from historical data and presents the result in a business-friendly way.

## Dataset
Superstore Sales dataset, about 10,000 orders from January 2014 to December 2017.
Source: https://www.kaggle.com/datasets/vivek468/superstore-dataset-final
Orders were aggregated to **48 monthly totals**.

## Approach
1. **Cleaning:** removed duplicates, missing values and non-positive sales; parsed dates explicitly (month/day/year); inspected but kept large orders.
2. **Time series:** aggregated to monthly sales.
3. **Exploration:** seasonal decomposition, seasonal index by month and year-over-year growth.
4. **Feature engineering:** time index, month (also as sine/cosine) and last year's sales for the same month (`lag_12`). Only features known 12 months ahead were used to avoid data leakage.
5. **Models compared:** Seasonal Naive (baseline), Linear Regression, Random Forest, Holt-Winters, Holt-Winters (damped), SARIMA.
6. **Validation:** chronological split (train on Jan 2014 to Dec 2016, test on the last 12 months) plus a 3-period rolling backtest.

## Results (last 12 months held out)
| Model | MAE | RMSE | MAPE % |
|---|---|---|---|
| **Holt-Winters (damped)** | $10,897 | $14,118 | 18.5% |
| Holt-Winters | $11,454 | $12,542 | 22.6% |
| Seasonal Naive (baseline) | $15,468 | $18,986 | 24.5% |
| Random Forest | $12,953 | $15,794 | 24.8% |
| SARIMA | $13,326 | $15,815 | 26.5% |
| Linear Regression | $17,384 | $20,786 | 40.1% |

**Best model: Holt-Winters (damped)** with a MAPE of 18.5% versus 24.5% for the "same month last year" baseline.

Rolling backtest (MAPE in three different test periods):

| Model | Jul 16-Dec 16 | Jan 17-Jun 17 | Jul 17-Dec 17 | Average |
|---|---|---|---|---|
| Holt-Winters (damped) | 24.2% | 20.1% | 15.6% | 20.0% |
| Seasonal Naive (baseline) | 22.0% | 23.6% | 25.4% | 23.7% |
| Holt-Winters | 29.1% | 28.2% | 17.1% | 24.8% |
| Random Forest | 26.0% | 31.8% | 18.1% | 25.3% |
| SARIMA | 25.6% | 35.2% | 17.7% | 26.1% |
| Linear Regression | 18.7% | 60.6% | 15.8% | 31.7% |

## Key charts
![Monthly sales](images/01_monthly_sales.png)
![Decomposition](images/02_decomposition.png)
![Seasonality and growth](images/03_seasonality_and_growth.png)
![Actual vs predicted](images/05_actual_vs_predicted.png)
![Model comparison](images/06_model_comparison.png)
![Error by month](images/07_error_by_month.png)
![12-month forecast](images/08_forecast.png)
![Year over year](images/09_year_over_year_forecast.png)

## Business insights
- SEASONALITY: Sales peak in September, November, December (September 1.6x, November 1.8x, December 1.7x an average month). November and December alone bring about 30% of each year's sales.
- SLOW MONTHS: January and February are the weakest (January 0.48x, February 0.30x an average month).
- GROWTH: Sales grew from $484K in 2014 to $733K in 2017, about 15% per year.
- OUTLOOK: The model expects about $910K of sales in the next 12 months (+24% vs the last 12 months). The strongest forecast month is December 2018 ($116K) and the weakest is February 2018 ($41K).
- RELIABILITY: The model is typically off by about 18.5% per month (baseline 'same as last year': 24.5%). Treat monthly numbers as planning guides and use the shaded range for safety buffers.

## Recommendations
- INVENTORY: Build stock ahead of the peak. Order for September / November / December 1 to 2 months early, and avoid large purchases in January and February.
- STAFFING: Plan extra seasonal staff and shipping capacity for September / November / December.
- CASH FLOW: Expect cash to be tight in the slow months and strongest after the peak. Arrange credit or delay big expenses accordingly.
- RISK: Keep a safety buffer of roughly 19% on any monthly plan, and use the Low/High range when deciding how much stock to commit to.
- RE-FORECAST: Re-run this notebook every month with the newest sales so the forecast keeps improving.

## Limitations
- Only 48 months of history, so error estimates are noisy.
- Sales are lumpy because of a few very large orders.
- The model has no information about promotions, prices or holidays.
- The forecast range is an approximation from past errors, not a guarantee.

## How to run
```bash
pip install -r requirements.txt
jupyter notebook notebook/sales_forecasting.ipynb
```
Place `Sample - Superstore.csv` (or the Kaggle zip) in the `data/` folder or next to the notebook.

## Repository structure
```
FUTURE_ML_01/
├── data/
├── notebook/sales_forecasting.ipynb
├── images/
├── outputs/      (model comparison and forecast CSVs)
├── README.md
└── requirements.txt
```
