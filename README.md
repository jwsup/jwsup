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

<img src="/pics/Log Return.png" alt="alt text" width="200" height="whatever">

However, Log-return is not as good as the above normalization method. One reason might be because the log returns are very small (0.0004xxx) and thus make it hard to use mse to train the model.

Below is the predicted value when using log-return as the normalization method:
<img src="/pics/Log Return Transformation.png" alt="alt text" width="600" height="whatever">

### 1.3 Train-Test Split
Data before 2017-12-29 is training set (12-30, 12-31 are weekends), the rest is testing set. Testing set is 3 months in length with 61 days in total.

### 1.4 LSTM model
Comparing to other neural networks applicaitons such as image captioning, there is much less data for stocks. The model with 5 layers in total is sufficient (2 LSTM Layers + 3 Dense Layers)

<img src="/pics/LSTM Model.png" alt="alt text" width="2000" height="whatever">

Note: The input of the fist LSTM layer is (None, 30, 1) when there is only one feature. It becomes(None,30,5) if all features are fit into the model.

When training the model, small number of epochs is sufficient for capturing the features of data as the data size is small.

### 1.5 Predicted Value and Return

In the case of predicting stock price, the target value is close price. Since the data is normalized before entering the model, the predicted value should be denormalized to retrieve the predicted price. Let B(t) denotes the position at time t, and R(t) denotes the return between t and t-1. Then, the return of a trade could be expressed as:

<img src="/pics/position and return.png" alt="alt text" width="300" height="whatever">

The cumulative return is simply the sum of all R(t).


When predicting Log-return instead, only the sign of the predicted values matter, because log-return tells directly the trend of the price movement at t+1.
The position at time t, B(t) is then:

<img src="/pics/Log return position.png" alt="alt text" width="300" height="whatever">

The return could be calculated accordingly.



## 2. Predicting price of a single stock
### 2.1 One feature as input
We start with the most basic model, with only closed price of the stock as input. Data is Apple stock. In this case, each input sequence is a 30*1 matrix. For each sequence that starts at n, the target value is the value at n+30.

<img src="/pics/apple-1feature.png" alt="alt text" width="600" height="whatever">

It is interesting to see that the predicted value is very closed to the real value. Part of the reason is because it is predicting only one step ahead. Even if the prediction for t(n+1)is wrong, the real value of t(n+1) will be entered to predict t(n+2). However, if we are performing trading on a daily basis, predition for t+1 should be sufficient.

After converting back to price, using the pre-defined buy and hold rule, the cumulative return is shown as the green dashed line below.

<img src="/pics/apple-1feature-price.png" alt="alt text" width="600" height="whatever">


The nominal return is 13 at the end, which is roughly 7.6% over a 61 day period. However, between day 0-25, it is obvious that the strategy made the oppossite decision and result in a loss, when the stock is actually rising. There are some deviation between the predicted and the real values. Reducing the deviations will help increase the performance.

### 2.2 Multiple features

#### 2.2.1 Two Features: Close +Volume
To achieve a better ressult, the trade volume of a listed stock over a single dat is added as an additional feature along with close price.

In this case, the initial input for the first layer becomes 30*2 matrixs. Both features were normalized in the same way as in the previous case.

<img src="/pics/apple-2features.png" alt="alt text" width="600" height="whatever">

Supprisingly, with an additional feature, the predicted value becomes more volatile and has even larger deviation .

<img src="/pics/apple_2features-price.png" alt="alt text" width="600" height="whatever">

Consequently, the return becomes more volatile as well.The nominal return during the 61 days testing period ranges from -15 to 15, which converts to -8% to 8% change in profit. The cumulative return at then end is close to 0. 

Clearly, adding volume has somehow introduced noise into the model and led to a worse result.

#### 2.2.2 Five Features

With all features added to the model (input: 30*5 matrix)), the prediction result is better than having only 2 features, but is still not as good as the one feature case.

<img src="/pics/apple-5features.png" alt="alt text" width="600" height="whatever">

The cumulative return is the worst among all cases.

<img src="/pics/apple_5features-price.png" alt="alt text" width="600" height="whatever">

At the end of 61 days period, the portfolio loses 20.5% of the total asset. Looking at the shapes of returns and predicted values closely, it seems the other features have introduced lots of noise (predicted price rises sharply when the real price is decreasing slowly).

#### 2.3 Summary: Predicting stock price

Contrary to the original guess, adding more features does not increase the prediction accuracy of the model and instead introduces noises that will misslead the result. Also, as more features being included in the model, the volatility of overall return increases. Therefore, when predicting the price of stocks, using only price as feature will tend to result in a better perforamce.


## 3. Predicting Future Log-Return

Since the ultimate goal for all trading strategies is to achieve positive return, instead of projecting futrue price, predicting log return might lead to better performance. The rationale is that when predicting future price, there will be model error, and converting predicted price to trading position is another source of noises.

Predicting log-return directly will reduces noise in that only the sign of the predicted value matters.
### Data transformation

After stock price transformed to log-return, the distribution becomes more stationary.

<img src="/pics/Log-trans.png" alt="alt text" width="400" height="whatever">

### Result

<img src="/pics/apple-log.png" alt="alt text" width="600" height="whatever">
In the graph above, the predicted log-return value are constatnly changing signs. After denormalization, the result is as follow:
<img src="/pics/apple-log-return.png" alt="alt text" width="600" height="whatever">

The model did not capture fully the magnitute of change in log-returns, however, the signs are generally matched. Over the testing period, the return is constantly rising.










