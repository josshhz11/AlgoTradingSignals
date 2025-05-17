# Portfolio Construction using Backtested AlgoTradingSignals (INCOMPLETE)

![image](https://www.ironfx.com/wp-content/uploads/2023/01/forex-algorithm-trade.jpg)

## 1. Purpose of Project

This project seeks to construct portfolios using quantitative analysis/modelling techniques of technical indicators as features, rebalancing the portfolio of stocks every month.

The technical indicators curated as features for quantitative analysis are as follows:
- Garman-Klass Volatility
- RSI
- Bollinger Bands
- ATR
- MACD
- Fama-French Factors
  - Mkt-RF: Market excess return
  - SMB (Small Minus Big): Size premium (small vs large caps)
  - HML (High Minus Low): Value premium (high vs low book-to-market)
  - RMW (Robust Minus Weak): Profitability premium (profitable vs unprofitable firms)
  - CMA (Conservative Minus Aggressive): Investment premium (low vs high asset growth)

We then used these features to backtest trading signals including but not limited to:
1. RSI-Momentum Trading Signal
2. Cross-Sectional Multi-Factor Composite Rank
3. Rolling-IC-Weighted Composite Signal

This model not only predicts the value of an option, but also incorporates the additional profit & loss (P&L) functionality to determine P&L of these options given their purchase price.

This project was built using Python, Plotly, Streamlit, and CSS, and can be found [here](https://black-scholes-options-pricing-model.streamlit.app/).

## 2. Methodology

### 2.1. Data Extraction
We first perform data extraction of the S&P500 stocks from Wikipedia today, as stocks that will form our dataset for our exploration. This list of S&P500 stocks has survivorship bias where it only looks at the current S&P500 stocks today and does not include stocks that used to be in the S&P500 during our analysis period but are no longer there.

From there, we used the yfinance package to extract the stock price data for these stocks at the end of each day, with data including: Adj Close, Close, High, Low, Open, Volume.

### 2.2. Exploration of Technical Indicators & Features of Stocks
We curated the following technical indicators for each stock as below, explaining their significance:

**Garman-Klass Volatility**
- Garman-Klass volatility uses **open, high, low, and closing prices** to provide a more accurate estimate of a stock's volatility than typical methods that only consider closing prices. It captures **intraday price movements** and is often used to **assess the risk associated with a stock**, especially in relation to its potential for rapid price changes.

It is calculated as follows:
$$
\text{Garman--Klass Volatility}
= \frac{\bigl(\ln(\mathrm{High}) - \ln(\mathrm{Low})\bigr)^2}{2}
\;-\;\bigl(2\ln(2)-1\bigr)\,\bigl(\ln(\mathrm{Adj\ Close}) - \ln(\mathrm{Open})\bigr)^2
$$

**RSI**
- The Relative Strength Index (RSI) is a technical indicator that measures the **speed and magnitude of price changes on a stock**, providing insights into its momentum. It's often used to identify **potential overbought or oversold conditions**.

**Bollinger Bands**
- Bollinger Bands are a technical analysis tool that helps traders assess a stock's price **volatility** and identify potential trading opportunities. They consist of a **middle band (a moving average) and two outer bands (standard deviations away from the moving average)**. By observing how the price moves in relation to these bands, traders can gauge whether a stock is **overbought, oversold, or experiencing a potential trend change**.

**ATR**
- The Average True Range (ATR) is a technical analysis indicator that measures **price volatility over a specific period, typically 14 days**. It helps traders understand how much a stock's price fluctuates and can be used to **set stop-loss levels and manage risk**. 

**MACD**
- The MACD (Moving Average Convergence Divergence) is a technical indicator that helps investors **identify trends, momentum, and potential entry and exit points** for trades in a stock. It does this by **showing the relationship between two exponential moving averages (EMAs) of a stock's price**. 

## 2.3. Aggregate to Monthly Level and Filter for Top 100 Most Liquid Stocks For Each Month
We extracted the **top 100 most liquid companies by 5y rolling average dollar_volume**, to narrow our search and aggregate the features to **monthly level** for just the technical indicators (not open/close/high/low type of stats).

## 2.4. Calculation of Monthly Returns for Different Time Horizons
To capture time series dynamics that reflect, for example, momentum patterns, we compute historical returns using the method .pct_change(lag), that is, returns over monthly periods as identified by lags (num of periods/months back). We computed monthly returns for 1, 2, 3, 6, 9, 12 month periods.

##

## Underlying Assumptions for the Model

The Black-Scholes Model makes the following assumptions:

1. No dividends are paid out during the life of the option.
2. Markets are random because market movements can't be predicted.
3. No transaction costs in buying the option.
4. The risk-free rate and volatility of the underlying asset are known and constant.
5. The returns of the underlying asset are normally distributed.
6. The option is European and can only be exercised at expiration.

## How does the Black-Scholes Equation work?
The mathematics involved in the formula are complicated and not necessary for the average user to understand, but this program simply finds the value of an option (call/put) using the inputs provided above, and also derives the potential P&L for each option value with different inputs used. 

To find the value of the option would first require finding the current stock price multiplied by the cumulative standard normal distribution. Thereafter, we will take this value and minus the exercise price discounted back to the present value, multiplied by the cumulative standard normal distribution again.

Now let's find *d1* and *d2* below: 

### Calculating d1:

$$
d_1 = \frac{\ln\left(\frac{S}{K}\right) + \left(r + \frac{1}{2}\sigma^2\right)T}{\sigma\sqrt{T}}
$$

### Calculating d2:
$$
d_2 = \frac{\ln\left(\frac{S}{K}\right) + \left(r - \frac{1}{2}\sigma^2\right)T}{\sigma\sqrt{T}}
$$

or, the alternate way to calculate d2 is:

$$
d_2 = d_1 - \sigma\sqrt{T}
$$

### Calculating the Call Option Price
The call option price C in the Black-Scholes-Merton model is calculated as:

$$
C = S \cdot N(d_1) - K \cdot e^{-rT} \cdot N(d_2)
$$

where S is the current stock price and K is the exercise price.

### Calculating the Put Option Price
The put option price P in the Black-Scholes-Merton model is calculated as:

$$
P = K \cdot e^{-rT} \cdot N(-d_2) - S \cdot N(-d_1)
$$

where S is the current stock price and K is the exercise price.

## How is the P&L functionality calculated?
To calculate the P&L of each option price (call/put), we take in an additional input of the purchase price of the option.

To get the P&L, we take the value of each option minus the purchase price of the option, to get the profit or loss of each option and plot it on a heatmap, with negative values being in the red and positive values in the green. 
