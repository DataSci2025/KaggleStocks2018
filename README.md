# Kaggle 2018 S&P 500 Stock Market Analysis
Utilises paytonfisher's S&P 500 Companies with Financial Information (2021) Kaggle dataset https://www.kaggle.com/datasets/paytonfisher/sp-500-companies-with-financial-information/data
# Kaggle 2018 S&P 500 Stock Market Analysis

## Executive Summary
This project utilises public data to explain stock returns. A Kaggle snapshot of S&P 500 data with a limited number of financial ratios from 2018 was employed alongside *yfinance* price data. OLS regressions predicted 5 snapshots of annual price returns. Overall results were weak, although positive correlations appeared with **Price/Sales**. For Year 1, negative correlations were also shown with **momentum**, an unusual combination worthy of further investigation.

---

## Project Workflow - refer to Code

### Step 1 – Exporting Price Data  

Two datasets were used: the Kaggle dataset and price data from yahoo finance (yfinance) python library. First, the Kaggle Dataset was downloaded into a .csv. The 52 Week High and 52 Week Low fields were incorrectly named and swapped. To obtain price performance, the distinct Tickers (‘Symbol’) were used to query yfinance. To simulate real-life investment and ensure Consistency, the Kaggle snapshot date had to be determined. 


### Step 2 – Matching Kaggle Snapshot Dates  

A loop matched prices in `yfinance` with the Kaggle `Price` field. Dates within ±14 days were accepted as valid matches, ensuring consistency across tickers. 

A ‘For’ loop queries each ticker on a rolling basis and logs dates from yfinance on which the Kaggle price falls between the High and Low yfinance price. The first ticker appends all matching dates to a master list, OldDateList. Each successive ticker’s matching dates are compared with OldDateList, and if any date is within 14 days of any OldDateList date, these are appended to a new DateList that replaces OldDateList at the end of each Ticker’s iteration. Companies with no price data are skipped; those with price data but no matches are skipped but also dropped (Appendix B). OldDateList, therefore, should retain dates that are within +- 14 days of potential matches. The resulting data produced matches for 478 tickers, with dates ranging from late December 2017 – February 2018. Data was cleaned following the UK Government Data Quality Framework (2020), with actions noted In Table 1.

### Step 3 – Pulling Future Price Snapshots  

Next, prices were pulled for each Jan/Feb of the following 5 years and joined with the original dataset. This imitates realistic investment horizons where investments are periodically revisited (Six, 2021). Yfinance is only populated for live stocks. To ensure completeness, and avoid survivorship bias, historical price data was pulled manually from Investing.com where mergers, bankruptcies or acquisitions had rendered a 2018 stock no longer ‘live’. The money or money equivalent of stocks for takeovers was used to fill the future stock price. Complex corporate actions where several split companies were created were excluded.


### Step 4 - Regression
OLS Regression was chosen to analyse the cross-sectional data; time series, an alternative, is more suited to price data on assets without detailed ratio information, such as Li’s (2007) Foreign Exchange analysis. The null Hypothesis is that there is no relationship between fundamentals and the annual price returns. This hypothesis is considered broken by statistically likely (p < 0.05) relationships. In finance, R², used for train and test sets, remains valuable at low levels. Academically, most stock movement ‘noise’ is random, and each firm has idiosyncratic performance (Piotroski, 2000). Adjusted R-squared, even for the usually strong beta relationship, is often 0.1-0.2 and rarely exceeds 0.4 (Roll, 1988).   
As loss-making (negative PE) or severely distressed (negative PB) companies might perform erratically, these are dropped. By multiplying dividend yield by the 2018 Price, stocks’ annual dividend was estimated and appended to the price for each year. This assumes dividends don’t not change – unlikely for all stocks – but this is the best approximation given data limitations. Prices are converted into 5 years of Gain/Loss (GL) to enable standard comparisons. This is winsorised by 1%, removing outliers, following similar practice by Fama and French (1992). 52_Week_Trend was calculated to enable comparisons, assigning a decimal for the 2018 price relative to its 52-Week Low (0) and 52-Week High (1). This proxies momentum before the snapshot.


Despite the apparently strong correlation in Appendix F, absolute values such as EBITDA were excluded as these are not easily comparable between companies. The initial regression parameters were: ['Price/Book', 'Price/Sales', 'Price/Earnings', 'Market Cap' (a proxy for size), '52_Week_Trend']. The regression was run on an 80/20 train/test split using statsmodels.api.ols. Parameters were removed and appended via stepwise methodology until p-values were suitably reduced. Results are in Table 2.
