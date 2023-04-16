---
title: "Time Series"
date: 2023-01-09T22:39:20-05:00
draft: true
---

- Stationary time series: constant mean and variance over time
- Unit root: makes the time series non-stationary i.e. there is a long-term trend. This makes parametric analysis difficult.
- Converting non-stationary series to stationary:
    - if there is strong autocorrelation (i.e. consecutive values don't vary wildly, or variance is fairly low), then differencing can be used
    - if there is seasonal autocorrelation (correlation between values is strong and consistent at regular intervals eg: sunscreen sales are higher in summer months of each year), use Seasonal & Trend Decomposition using Loess method
    - if variance is not constant (i.e. suddenly a stock starts to get more volatile than before), taking the log of the series reduces the variance


- Common models for time series:
    - Moving Average: makes the series smoother, trend is more apparent
    - Exponential smoothing: higher weight to more recent values, lower weight to historical values. Useful for smoothing short-term fluctuations, makes long-term trend more apparent. Not the best option around.
    - Autoregressive models: use past values to predict the future values. Lagged values are the predictors here.
    - ARMA: An AR process is a time series in which the current value is a function of past values, and an MA process is a time series in which the current value is a function of random noise or errors. An ARMA model is a combination of both of these processes, and is used to describe time series that exhibit both autocorrelation (a relationship between the current value and past values) and random noise.
    -
