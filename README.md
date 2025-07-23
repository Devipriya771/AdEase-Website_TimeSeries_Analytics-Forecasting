# AdEase Website Time Series Analytics Forecasting

This repository contains a time series forecasting project for AdEase, an ad infrastructure company that analyzes Wikipedia page view data to optimize ad placements across languages and access types. By leveraging data cleaning, exploratory data analysis, and three forecasting models—ARIMA, SARIMAX, and Prophet with external regressors—we forecast daily page views to guide targeted ad campaigns.



---

## Project Overview

AdEase aims to forecast the daily Wikipedia page views by language, access type, and traffic type over a 550-day period (July 1, 2015 – December 31, 2016). Accurate forecasts enable strategic ad placements during high-traffic periods and on priority pages.

## Data Description

* **Input**: CSV of shape `(145,063, 551)`

  * **Page**: Wikipedia URL with language, access, and spider metadata
  * **Dates**: 550 daily columns of page view counts
* **Exogenous Variable**: Binary indicator of marketing campaigns/events per date

## Repository Structure

```text
├── AdEase_Forecasting.ipynb   # Jupyter notebook with full pipeline
├── train_1.csv                # Raw page view data
├── Exog_Campaign_eng.csv      # External campaign indicator
├── README.md                  # Project documentation (this file)
└── requirements.txt           # Python dependencies
```

## Installation & Setup

1. **Clone repository**:

   ```bash
   git clone https://github.com/<username>/adease-forecasting.git
   cd adease-forecasting
   ```
2. **Create virtual environment**:

   ```bash
   python -m venv venv
   source venv/bin/activate  # macOS/Linux
   venv\\Scripts\\activate    # Windows
   ```
3. **Install dependencies**:

   ```bash
   pip install -r requirements.txt
   ```

Requirements include: `pandas`, `numpy`, `matplotlib`, `seaborn`, `statsmodels`, `prophet`, `scikit-learn`.

## Methods

### 1. Data Cleaning & Transformation

* **Missing Data**: Dropped pages with >50% missing values.
* **Interpolation**: Linear interpolation, followed by forward/backward fill to impute remaining gaps.
* **Index Sorting**: Ensured date columns in chronological order.

### 2. Feature Extraction

* **Language**: Regex extraction of language code from URL.
* **Access & Traffic Types**: Parsed `access` (all-access, mobile-web, desktop) and `agents` (all-agents, spider).
* **Total Views**: Sum of daily views per page.
* **Exogenous Regressor**: Campaign indicator per date aligned to forecasting index.

### 3. Exploratory Data Analysis

* **Categorical Distributions**: Pie and bar charts for language, access, and traffic type shares.
* **Top Pages**: Identified top 10 pages by total views and plotted their time series.
* **Temporal Patterns**: Month and year aggregations showed higher traffic in July–December and in 2016.

### 4. Stationarity & Decomposition

* **ADF Test**: Assessed stationarity per language; Spanish, French, and Russian were stationary.
* **Decomposition**: Used 7-day period to isolate trend, seasonal, and residual components.
* **ACF/PACF**: Analyzed autocorrelations pre- and post-detrending to guide ARIMA orders.

### 5. Forecasting Models

#### ARIMA

* **Order**: (p=1, d=1, q=1) chosen via ACF/PACF.
* **Fit & Predict**: Train on first 460 days, test on last 90.

#### SARIMAX with Exogenous Variables

* **Order**: (1,1,1) with seasonal=(1,1,1,7).
* **Exogenous**: Campaign indicator series.

#### Prophet with External Regressor

* **DS/Y**: Renamed date and target for Prophet (`ds`, `y`).
* **Regressor**: Added campaign indicator as extra regressor.

## Evaluation Metrics

* **MAPE** (Mean Absolute Percentage Error)
* **RMSE** (Root Mean Squared Error)

| Model   | MAPE      | RMSE           |
| ------- | --------- | -------------- |
| ARIMA   | 6.80%     | 12,267,890     |
| SARIMAX | 6.59%     | 11,081,170     |
| Prophet | **6.27%** | **10,559,860** |

## Results & Insights

* **Best Model**: Prophet with external regressor.
* **Seasonal Peaks**: July–September 2016 saw traffic spikes—optimal ad scheduling window.
* **Language Priority**: English (16%) & Japanese (14%) hold highest viewership shares.
* **Access Channels**: 51% all-access, 25% mobile\_web, 24% desktop.

## Usage

Open and run `AdEase_Forecasting.ipynb` to reproduce the pipeline:

```bash
jupyter notebook AdEase_Forecasting.ipynb
```

Results include interactive plots and model forecasts.

## Acknowledgements

* AdEase for data access
* Wikipedia pageview API community
* Open-source libraries: Statsmodels, Prophet, Pandas, Seaborn
