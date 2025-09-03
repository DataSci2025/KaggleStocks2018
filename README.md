# Kaggle 2018 S&P 500 Stock Market Analysis
Utilises paytonfisher's S&P 500 Companies with Financial Information (2021) Kaggle dataset https://www.kaggle.com/datasets/paytonfisher/sp-500-companies-with-financial-information/data
# Kaggle 2018 S&P 500 Stock Market Analysis

## Executive Summary
This project utilises public data to explain stock returns. A Kaggle snapshot of S&P 500 data with a limited number of financial ratios from 2018 was employed alongside *yfinance* price data. OLS regressions predicted 5 snapshots of annual price returns. Overall results were weak, although positive correlations appeared with **Price/Sales**. For Year 1, negative correlations were also shown with **momentum**, an unusual combination worthy of further investigation.

---

## Project Workflow - refer to Code

### Step 1 – Exporting Price Data  

Ticker symbols were extracted from the Kaggle dataset and queried against Yahoo Finance via the `yfinance` Python library. Prices were exported into `.csv` for consistency checks.  

### Step 2 – Matching Kaggle Snapshot Dates  

A loop matched prices in `yfinance` with the Kaggle `Price` field. Dates within ±14 days were accepted as valid matches, ensuring consistency across tickers.  

### Step 3 – Pulling Future Price Snapshots  

Average stock prices were pulled for Jan/Feb of the next five years and merged into the dataset to simulate real-world investment horizons. Missing prices (due to delistings, mergers, or acquisitions) were backfilled using historical sources (e.g., Investing.com).  

### Step 4 - Regression
