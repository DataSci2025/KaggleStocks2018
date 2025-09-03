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


## Results

The results are somewhat problematic: MAPE is frequently higher than 100 and Test R² is sometimes negative. Multicollinearity is not a major issue, shown by low correlations between multiple predictors (Appendix C). Evaluating Table 2 broadly shows a failure for the Price/Book split dataset to reveal significant results. Slightly more useful is the P/E split dataset, with the top split showing positive correlation with Price/Sales. However, the combined dataset, with more datapoints, produces better regressions – scatter plots show a weak but clear correlation between Price/Sales and performance. Price/Book had reasonable p-values, but its scatter plots showed clear heteroscedasticity
This appears to firmly reject the ‘Value’ hypothesis for this dataset and supports a ‘glamour’ hypothesis. As risk measures are excluded from the data, it is possible that high ‘Value’ stocks were discounted as being too risky (Lahonishok, 1994). More likely, this is a S&P 500 peculiarity: if ‘glamour’ investing and bubbles are irrational, these can persist for a long time, and investing news appears to suggest the persistence of ‘growth/glamour’ performance in recent years (Gray, 2018). Most interesting is the negative influence of 52_Week_Trend on Year 1 returns, as this suggests that stocks near 52-week maximum and minimum will generally ‘revert’ towards a middle value; this is rarely observed with ‘glamour/growth’ stocks as these are associated with positive momentum, yet the combined Year 1 regression works alongside a positive coefficient for Price/Sales, a growth indicator. It would be beneficial to investigate this further, as this tentatively suggests S&P 500 pricing is neither ‘semi-strong’ nor ‘weak’ efficient and that behavioural overreactions are at work. Possibly, weighted combinations between the parameters and more complex regressions than OLS might prove more accurate; however, expanding the dataset beyond one year of ratio data, allowing reassessment of the ratios at each year, and expanding the number of ratio variables (liquidity, ROE etc.) would be the primary action to improve regressions.

## References

Asness, C.S. et al., 2013. Value and Momentum Everywhere. The Journal of Finance, 68(3), pp. 929-985.

Carhart, M.M., 1997. On Persistence in Mutual Fund Performance. The Journal of Finance, 52(1), pp. 57-82.

Fama, E.F. and French, K.R., 1992. The Cross-Section of Expected Stock Returns. The Journal of Finance, 47, pp. 427-465. 

Gray, W., 2018. Factor Investing Fact Check: Are Value and Momentum Dead? [online] Available at: https://alphaarchitect.com/factor-investing-fact-check-are-value-and-momentum-dead/ [Accessed 31 August 2025].

He, W. & Wei, P.P., 2003. Is overreaction an explanation for the value effect? A study using implied volatility from option prices. Department of Economics and Finance Working Papers, 1991-2006, Paper 8.

Hou, K., Mo, H., Xue, C. & Zhang, L., 2017. The Economics of Value Investing. NBER Working Paper No. w23563. 

Lakonishok, J., Shleifer, A. and Vishny, R.W., 1994. Contrarian Investment, Extrapolation, and Risk. The Journal of Finance, 49, pp. 1541-1578.

Lehmann, B., 1990. Fads, martingales and market efficiency. Quarterly Journal of Economics, 105, pp. 1-28.

Li, H. & Fang, Z. & Zhao, D., 2007. GBP/USD Currency Exchange Rate Time Series Forecasting Using Regularized Least-Squares Regression Method. Lecture Notes in Engineering and Computer Science, 2166.

Navas, R. & Bentes, S., 2023. Value investing: a new SCORE model. Review of Business Management, 25, pp. 166-185. DOI: 10.7819/rbgn.v25i2.4224.

PaytonFisher, 2018. S&P 500 Companies with Financial Information [online] Available at: https://www.kaggle.com/datasets/paytonfisher/sp-500-companies-with-financial-information/data [Accessed 01 August 2025].
Piotroski, J.D., 2000. Value Investing: The Use of Historical Financial Statement Information to Separate Winners from Losers. Journal of Accounting Research, 38, pp. 1–41. JSTOR, Available at: https://doi.org/10.2307/2672906 [Accessed 3 Sept. 2025].
Roll, R., 1988. R². The Journal of Finance, 43(3), pp. 541-566.
Six, L., 2021. Value Investing with Big Data. [online] Available at: https://lionsix.ch/home/static/media/paper.ca88398a.pdf [Accessed 10 July 2025].
Tramer, M et al., 2020. Multiple Linear Regression (2nd Edition). Cathie Marsh Institute Working Paper, 2020-01.
Woo, K.-Y., Mai, C., McAleer, M. & Wong, W.-K., 2020. Review on Efficiency and Anomalies in Stock Markets. Economies, 8(1). DOI: https://doi.org/10.3390/economies8010020.
Ying, Q., Yousaf, T., Ain, Q. u., Akhtar, Y. & Rasheed, M.S., 2019. Stock Investment and Excess Returns: A Critical Review in the Light of the Efficient Market Hypothesis. Journal of Risk and Financial Management, 12(2). DOI: https://doi.org/10.3390/jrfm12020097.

