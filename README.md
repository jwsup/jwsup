# Final Projet - MIE1516
# Applicaitons of LSTM networks in stock market

## Introduction
For years, people have been exploiting their creativity in finding strategies to generate profits from stock market, 
and have invented countless brilliant quantitative models for trading. However, the assumptions/constraints of most of the traditional
models, such as normality asssumption, have limited the use of the models and requires complex data transformation before using the model.
In Comparison, RNN model, LSTM specifically, has no limits on the distributions of data, and is good at handelling time series data. Therefore, it is worth trying 
to test the ability of LSTM to predict stock price, and examine the return of the trading strategies based on the LSTM models.

## Overview
In the first half of this report, we will focus on comparing the choices of different feature subsets as input, and the differnces in prediction time period. Then, we will try to 
predict the return of a stock based on the buy and hold strategy.

## Table of Contact

## 1. Model Structure

### 1.1 Data
The stocks are selected from the S&P500 list to ensure that it is frequently traded everyday and has enough data. Among all available features of a stock, the most relevant ones are respectively open, high, low, close, volume. The time range of the data set is 5 years, from 2013-04-15 to 2018-3-31 with 1250 days in total (weekends excluded).

### 1.2 Train-Test Split
Data before 2017-12-29 is training set (12-30, 12-31 are weekends), the rest is testing set. Testing set is 3 months in length with 61 days in total.

### 1.3 Sequence Length
The time range of input is set to 30 days, which is equivalent to approximately 1.5 months. When time range being too large, e.g. 50days, data from long time ago does little help in determining the price at t+1.

### 1.3.1 Data Normalization


### 1.4 LSTM model
The model has 5 layers in total (2 LSTM Layers + 3 Dense Layers)

When training the model, small number of epochs is sufficient for capturing the features of data as the size of data is small.

1.5 Predicted Price and Return
It is the close price that the model tries to predict. 
For t+1 case, if price at t+1 is greater than current price, position will be 1; otherwise,-1
For t+5 case, if price at t+5 is greater than current price, position will be 1; otherwise,-1





## 2. Predicting price of a single stock
### 2.1 one feature as input
We start with the most basic model, with only closed price of the stock as input. For each sequence of length 30, the target value is the value at t=31.

It is interesting to see that the predicted value is very closed to the real value. 
