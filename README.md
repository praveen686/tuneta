<p align="center">
  <a href="https://github.com/jmrichardson/tuneta">
    <img src="images/logo.png" alt="tuneTA">
  </a>
</p>

TuneTA optimizes a broad set of technical indicators to maximize its correlation to a user defined target variable.  The set of tuned indicators can be reduced by choosing the most correlated with the target with the least correlation with each other. TuneTA maintains its state of tuned indicators which can easily be used to identically add to multiple data sets (train, validation, test).

### Features

* Given financial prices (OHLCV) and return (X and y respectively), optimizes technical indicator parameters to maximize the correlation to return.  Multiple ranges can be defined to target specific periods of time
* Select the top X optimized indicators with most correlation to return, and select X with the least correlation to each other with the goal of improving downstream ML models
* Persists state to create identical tuned indicators on multiple datasets (train, validation, test)
* Supports technical indicators from the following packages
  * [Pandas TA](https://github.com/twopirllc/pandas-ta)
  * [TA-Lib](https://github.com/mrjbq7/ta-lib)
  * [FinTA](https://github.com/peerchemist/finta)
* Parallel processing


### Overview

"A picture is worth a thousand words"

<p align="center">
  <a href="https://github.com/jmrichardson/tuneta">
    <img src="images/ico.jpg" alt="tuneTA" width="600">
  </a>
</p>

<p align="center">
  <a href="https://github.com/jmrichardson/tuneta">
    <img src="images/ico2.jpg" alt="tuneTA" width="600">
  </a>
</p>

### Install

### Install

```python
pip install tuneta
```

### Example Usage

```python
import yfinance as yf
import pandas as pd
from pandas_ta import percent_return
from tuneta.tune_ta import TuneTA
from sklearn.model_selection import train_test_split


if __name__ == "__main__":
    # Download data set from yahoo, calculate next day return and split into train and test
    X = yf.download("SPY", period="10y", interval="1d", auto_adjust=True)
    y = percent_return(X.close)
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=.3)

    indicators = TuneTA(n_jobs=2)
    indicators.fit(X_train, y_train,
                   indicators=["pta.macd", "pta.ao"],  # Indicators to tune/optimize
                   ranges=[(0, 20), (21, 100)],  # Period ranges to tune for each indicator
                   trials=5  # Number of optimization trials per indicator per range
                   )

    # Take top 10 correlated indicators to return, and then select the 3 least correlated with each other
    indicators.prune(top=10, studies=3)

    # Add indicators to X_train
    features = indicators.transform(X_train)
    X_train = pd.concat([X_train, features], axis=1)

    # Add same indicators to X_test
    features = indicators.transform(X_test)
    X_test = pd.concat([X_test, features], axis=1)
```

### Notes

Tested TALib Indicators:
['tta.BBANDS', 'tta.DEMA', 'tta.EMA', 'tta.HT_TRENDLINE', 'tta.KAMA', 'tta.MA',
'tta.MIDPOINT', 'tta.MIDPRICE', 'tta.SAR', 'tta.SAREXT', 'tta.SMA', 'tta.T3', 'tta.TEMA', 'tta.TRIMA',
'tta.WMA', 'tta.ADX', 'tta.ADXR', 'tta.APO', 'tta.AROON:1', 'tta.AROONOSC', 'tta.BOP', 'tta.CCI', 'tta.CMO',
'tta.DX', 'tta.MACD', 'tta.MACDEXT', 'tta.MACDFIX', 'tta.MFI', 'tta.MINUS_DI', 'tta.MINUS_DM', 'tta.MOM',
'tta.PLUS_DI', 'tta.PLUS_DM', 'tta.PPO', 'tta.ROC', 'tta.ROCP', 'tta.ROCR', 'tta.ROCR100', 'tta.RSI', 'tta.STOCH',
'tta.STOCHF', 'tta.STOCHRSI', 'tta.TRIX', 'tta.ULTOSC', 'tta.WILLR', 'tta.AD', 'tta.ADOSC', 'tta.OBV',
'tta.HT_DCPERIOD', 'tta.HT_DCPHASE', 'tta.HT_PHASOR', 'tta.HT_SINE', 'tta.HT_TRENDMODE', 'tta.AVGPRICE', 'tta.MEDPRICE',
'tta.TYPPRICE', 'tta.WCLPRICE', 'tta.ATR', 'tta.NATR', 'tta.TRANGE', 'tta.CDL2CROWS', 'tta.CDL3BLACKCROWS',
'tta.CDL3INSIDE', 'tta.CDL3LINESTRIKE', 'tta.CDL3OUTSIDE', 'tta.CDL3STARSINSOUTH', 'tta.CDL3WHITESOLDIERS',
'tta.CDLABANDONEDBABY', 'tta.CDLADVANCEBLOCK', 'tta.CDLBELTHOLD', 'tta.CDLBREAKAWAY', 'tta.CDLCLOSINGMARUBOZU',
'tta.CDLCONCEALBABYSWALL', 'tta.CDLCOUNTERATTACK', 'tta.CDLDARKCLOUDCOVER', 'tta.CDLDOJI', 'tta.CDLDOJISTAR',
'tta.CDLDRAGONFLYDOJI', 'tta.CDLENGULFING', 'tta.CDLEVENINGDOJISTAR', 'tta.CDLEVENINGSTAR', 'tta.CDLGAPSIDESIDEWHITE',
'tta.CDLGRAVESTONEDOJI', 'tta.CDLHAMMER', 'tta.CDLHANGINGMAN', 'tta.CDLHARAMI', 'tta.CDLHARAMICROSS',
'tta.CDLHIGHWAVE', 'tta.CDLHIKKAKE', 'tta.CDLHIKKAKEMOD', 'tta.CDLHOMINGPIGEON', 'tta.CDLIDENTICAL3CROWS',
'tta.CDLINNECK', 'tta.CDLINVERTEDHAMMER', 'tta.CDLKICKING', 'tta.CDLKICKINGBYLENGTH', 'tta.CDLLADDERBOTTOM',
'tta.CDLLONGLEGGEDDOJI', 'tta.CDLLONGLINE', 'tta.CDLMARUBOZU', 'tta.CDLMATCHINGLOW', 'tta.CDLMATHOLD',
'tta.CDLMORNINGDOJISTAR', 'tta.CDLMORNINGSTAR', 'tta.CDLONNECK', 'tta.CDLPIERCING', 'tta.CDLRICKSHAWMAN',
'tta.CDLRISEFALL3METHODS', 'tta.CDLSEPARATINGLINES', 'tta.CDLSHOOTINGSTAR', 'tta.CDLSHORTLINE', 'tta.CDLSPINNINGTOP',
'tta.CDLSTALLEDPATTERN', 'tta.CDLSTICKSANDWICH', 'tta.CDLTAKURI', 'tta.CDLTASUKIGAP', 'tta.CDLTHRUSTING', 'tta.CDLTRISTAR',
'tta.CDLUNIQUE3RIVER', 'tta.CDLUPSIDEGAP2CROWS', 'tta.CDLXSIDEGAP3METHODS', 'tta.LINEARREG',
'tta.LINEARREG_ANGLE', 'tta.LINEARREG_INTERCEPT', 'tta.LINEARREG_SLOPE', 'tta.STDDEV', 'tta.TSF', 'tta.VAR']

# Tested Pandas TA indicators
['pta.cdl_doji', 'pta.cdl_inside', 'pta.ha', 'pta.ao', 'pta.apo', 'pta.bias', 'pta.bop', 'pta.brar', 'pta.cci', 'pta.cfo', 'pta.cg', 'pta.cmo', 'pta.coppock', 'pta.er', 'pta.eri',
    'pta.fisher', 'pta.inertia', 'pta.kdj', 'pta.kst', 'pta.macd', 'pta.mom', 'pta.pgo', 'pta.ppo', 'pta.psl', 'pta.pvo', 'pta.qqe', 'pta.roc', 'pta.rsi', 'pta.rvgi', 'pta.slope', 'pta.smi', 'pta.squeeze',
    'pta.stoch', 'pta.stochrsi', 'pta.trix', 'pta.tsi', 'pta.uo', 'pta.willr', 'pta.dema', 'pta.ema', 'pta.fwma', 'pta.hilo', 'pta.hl2', 'pta.hlc3', 'pta.hma', 'pta.kama', 'pta.linreg', 'pta.midpoint',
    'pta.midprice', 'pta.ohlc4', 'pta.pwma', 'pta.rma', 'pta.sinwma', 'pta.sma', 'pta.ssf', 'pta.supertrend', 'pta.swma', 'pta.t3', 'pta.tema', 'pta.trima', 'pta.vidya', 'pta.vwap', 'pta.vwma',
    'pta.wcp', 'pta.wma', 'pta.zlma', 'pta.entropy', 'pta.kurtosis', 'pta.mad', 'pta.median', 'pta.quantile', 'pta.skew', 'pta.stdev', 'pta.variance', 'pta.zscore', 'pta.adx', 'pta.amat',
    'pta.aroon', 'pta.chop', 'pta.cksp', 'pta.decay', 'pta.decreasing', 'pta.increasing', 'pta.psar', 'pta.qstick', 'pta.ttm_trend', 'pta.vortex', 'pta.aberration',
    'pta.accbands', 'pta.atr', 'pta.bbands', 'pta.donchian', 'pta.kc', 'pta.massi', 'pta.natr', 'pta.pdist', 'pta.rvi', 'pta.thermo', 'pta.true_range', 'pta.ui', 'pta.ad', 'pta.adosc',
    'pta.aobv', 'pta.cmf', 'pta.efi', 'pta.eom', 'pta.mfi', 'pta.nvi', 'pta.obv', 'pta.pvi', 'pta.pvol', 'pta.pvt',]



