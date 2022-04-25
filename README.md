# Challenge 5

## Financial Planner
---
In this challenge analyzed data of cryptocurrencies and stocks/bonds of a portfolio by collecting & using data retrieved by Alpaca API keys. Then used the Monte Carlo simulation to review possible options of investments and their returns in regards to retirement.

### *Necessary library imports:*
- import os
- import requests
- import pandas as pd
- from dotenv import load_dotenv
- import alpaca_trade_api as tradeapi
- from MCForecastTools import MCSimulation
- %matplotlib inline 

### *The Analysis*

Before analysis of the data can begin, it is necessary to confirm that you have a correct api environment. For that you need to create a file named <sample.env> and print inside of it your Alpaca API key and your Alpaca Secret API key. To confirm that the key works
```python
load_dotenv('API.env')
#create alpaca api & secret key variables
alpaca_api_key = os.getenv('APCA_API_KEY_ID')
alpaca_secret_key = os.getenv('APCA_API_SECRET_KEY')
#check it works
type(alpaca_api_key)
```
The returned value for the type must be a str, otherwise you will get an error down the line.

In this challenge we are using data for Bitcoin and Etherium cryptocurrencies and analyzing the amount that is in the portfolio. Using the `requests`, `get` and `json` functions we review the data for each cryptocurrency, calculate the sum and then the overall total of the crypto in the portfolio.

After quantifying the crypto data, we move on to the stocks/bonds portion. The following was used for the data: SPDR S&P 500 ETF Trust (ticker: SPY) and of the iShares Core US Aggregate Bond ETF (ticker: AGG).

For this portion an API object needs to be created, as well as the start and end dates in `.isoformat()`, the tickers and the timeframe. This information will be needed to create a DataFrame for the stocks/bonds in the portfolio. The code for this will look like below
```python
# Get current closing prices for SPY and AGG
spyagg_df = alpaca.get_bars(tickers, timeframe, start=start_date, end=end_date).df
# Reorganize the DataFrame Separate ticker data
SPY_df = spyagg_df[spyagg_df['symbol']=='SPY'].drop('symbol', axis=1)
AGG_df = spyagg_df[spyagg_df['symbol']=='AGG'].drop('symbol', axis=1)
# Concatenate the ticker DataFrames
spyagg_df = pd.concat([SPY_df, AGG_df], axis=1, keys=['SPY', 'AGG'])
```

From the created DataFrame only the closing values for the SPY & AGG shares is needed. The two values are then summed and finally added to the crypto total to have the full value of portfolio.

To see the possible returns in regards to the weight given and the length of time of trading before finally retiring the Monte Carlo simulation is used. 

The simulation is run by creating a DataFrame and using it the code of the simulation. The simulation code looks as follows:
```python
MC_thirty = MCSimulation(portfolio_data=df_stock_data, weights=[.60,.40], num_simulation=500, num_trading_days=7560)
```
In this scenario we are running the simulation 500 times, with a weight of 60% for SPY and 40% for AGG and this is done in a span of 30 years (252 days*30 yrs).

This simulation was done on various spans and weights.
Overall if the owner of the portfolio wishes to retire in ten years instead of 30, they can as long as they put more weight in their SPY amount of shares.