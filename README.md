# Final Projet - MIE1516
# Applicaitons of LSTM networks in stock market

## Introduction
For years, people have been exploiting their creativity in finding strategies to generate profits from stock market, 
and have invented countless brilliant quantitative models for trading. However, the assumptions/constraints of most of the traditional models, such as normality asssumption, have limited the use of the models and requires complex data transformation before using the model.
In Comparison, RNN model, LSTM specifically, has no limits on the distributions of data, and is designed to handel time series data. Therefore, it is worth trying 
to test the ability of LSTM networks to predict stock price, and examine the return of the trading strategies based on the LSTM models.

## Overview
In the first half of this report, we will focus on comparing the choices of different feature subsets as input, and the differnces in prediction time period. Then, we will try to 
predict the return of a stock based on the buy and hold strategy.

## Table of Contact

## 1. Model Structure

### 1.1 Data
The stocks are selected from the S&P500 list to ensure that it is frequently traded everyday and has enough data. The 5 most relevant features of a listed stock are respectively open, high, low, close, volume. The time span of the data set is 5 years, from 2013-04-15 to 2018-3-31 with 1250 days in total (weekends excluded).

### 1.2 Sequence Length
The sequence length of input is set to 30 days, which is equivalent to approximately 1.5 months. The rasonale is that, for time series data, autocorrelation decreases when lag increases, which means data from long time ago contributes little to the prediction of value at t+1.

### 1.2.1 Data Normalization
In each sequence of data, value at t(n) will be expressed as the percentage change of value at t(0). Mathematically,

<img src="/pics/Normalization.png" alt="alt text" width="200" height="whatever">

Log retrun is another normaliztion method. It is expressed as the difference between the log value of t and t-1.

However, Log-return is not as good as the above normalization method. One reason might be because the log returns are very small (0.0004xxx) and thus make it hard to use mse to train the model.

<img src="/pics/Log Return Transformation.png" alt="alt text" width="600" height="whatever">

### 1.3 Train-Test Split
Data before 2017-12-29 is training set (12-30, 12-31 are weekends), the rest is testing set. Testing set is 3 months in length with 61 days in total.

### 1.4 LSTM model
Comparing to other neural networks applicaitons such as image captioning, there is much less data for stocks. The model with 5 layers in total is sufficient (2 LSTM Layers + 3 Dense Layers)

<img src="/pics/LSTM Model.png" alt="alt text" width="2000" height="whatever">

Note: The input of the fist LSTM layer is (None, 30, 1) when there is only one feature. It becomes(None,30,5) if all features are fit into the model.

When training the model, small number of epochs is sufficient for capturing the features of data as the data size is small.

1.5 Predicted Value and Return

In the case of predicting stock price, the target value is close price. Since the data is normalized before entering the model, the predicted value should be denormalized to retrieve the predicted price. Let B(t) denotes the position at time t, and R(t) denotes the return between t and t-1. Then, the return of a trade could be expressed as:

The cumulative return is simply the sum of all R(t).




It is the close price that the model tries to predict. 
For t+1 case, if price at t+1 is greater than current price, position will be 1; otherwise,-1
For t+5 case, if price at t+5 is greater than current price, position will be 1; otherwise,-1





## 2. Predicting price of a single stock
### 2.1 one feature as input
We start with the most basic model, with only closed price of the stock as input. Data is Apple stock. In this case, each input sequence is a 30*1 matrix. For each sequence of length 30, the target value is the value at t=31.

<img src="/pics/Apple.norm.png" alt="alt text" width="600" height="whatever">

After converting back to price, 



It is interesting to see that the predicted value is very closed to the real value. Part of the reason is because it is predicting only one step ahead. Even if the prediction for t(n+1)is wrong, the real value of t(n+1) will be entered to predict t(n+2). However, if we are performing trading on a daily basis, predition for t+1 should be sufficient.

Using the pre-defined buy and hold rule, the cumulative return is shown as the green dashed line below.

<img src="/pics/Apple return.png" alt="alt text" width="600" height="whatever">

The cumulative return did not perform well. When the price drop sharply, the return plummet as well. 

### 2.2 Multiple features

#### 2.2.1 Two Features: Close +Volume
To achieve a better ressult, one way is to include more features. First of all, we will try to add volume as an additional feature along with close price.

In this case, the initial input becomes  30*2 matrixs. Both features were normalized in the same way as in the previous case.

<img src="/pics/close+volume.png" alt="alt text" width="600" height="whatever">

Supprisingly, with an additional feature, the predicted value becomes more volatile and has less prediction accuracy.

<img src="/pics/close+volume price.png" alt="alt text" width="600" height="whatever">

Consequently, the return becomes more volatile as well, while cumulative return stay relatively the same compare to the previous case. The nominal return during the 61 days testing period ranges from -20 to 10, which converts to -13% to 5.8% change in profit.

#### 2.2.2 Five Features

With all features added to the model (input: 30*5 matrix)), the prediction result is clearly improved.


However, the cumulative return is the worst among all cases.


## 3. Predicting return on a single stock

Since the only goal for all trading strategies is to achieve positive return, instead of projecting futrue price, predicting log return might result in a better result, because in the previous case, the processs of converting from price to trading position might introduce extra errors.

### 3.1 Data process










