# The Battle of Neighborhoods Report
## 1. Introduction
China, as a developing country, often meet a lot of problems on environment pollution: water, air, plastic, etc.

As a Chinese, especially who live in Beijing, always suffers from air pollution. The reasons of air pollution are various, car exhaust, industry exhaust, fume, construction dust, etc.

![Air pollution in Beijing](https://github.com/lc3t/IBMDataScience/blob/master/pic/pollution.jpg "Air pollution in Beijing")

It will be awesome if we can predict its happening in the future, then we can get prepared before we go outdoor.

Fourtunately, today's weather forecast already included air pollution forecast. But, is it reliable?

According to the comparison result of air pollution index recorded by US Embassy in Beijing and air pollution report recorded by Beijing government, the result are not quite the same.

How about we create a tool, to do the forecast by ourselves?

This tool is based on reliable data source (US Embassy in Beijing), and can do forecast for the future. It is very useful for ordinary people to create a air pollution forecast for themselves!

## 2. Data
### 2.1 Data source
Beijing historical air pollution data can be found from UCI (University of California, Irvine):

[Data Source Page of UCI](https://archive.ics.uci.edu/ml/datasets/Beijing+PM2.5+Data)

This hourly data set contains the PM2.5 data of US Embassy in Beijing. Meanwhile, meteorological data from Beijing Capital International Airport are also included.
Time period is year 2010 to 2014

There are 13 columns in this raw data:
* No: row number 
* year: year of data in this row 
* month: month of data in this row 
* day: day of data in this row 
* hour: hour of data in this row 
* pm2.5: PM2.5 concentration (ug/m^3) 
* DEWP: Dew Point 
* TEMP: Temperature
* PRES: Pressure (hPa) 
* cbwd: Combined wind direction 
* Iws: Cumulated wind speed (m/s) 
* Is: Cumulated hours of snow 
* Ir: Cumulated hours of rain 

We can use this data and frame a forecasting problem where, given the weather conditions and pollution for prior hours, we forecast the pollution at the next hour

### 2.2 Raw data clean
let's take a snapshot of the raw data set:

![raw dataset](https://github.com/lc3t/IBMDataScience/blob/master/pic/raw_dataset.png "raw dataset")

We can find there are missing data ("NaN"), several data types, separated datetime stamps and no frame index. We need clean them one by one.

First, we need to combine datetime related columns together and get a standard pandas datetime. Create a function can make it easier. apply the function to each row to get the combine datatime. Set this datatime as index, then we can get a dataframe of time series.

Second, Reshape the dataframe, drop unnecessary columns and rename the column names much more easier for understanding.

Third, fill the missing values with number 0

Now, we get a cleaned raw data set, which is ready for data analysis:
![cleaned raw dataset](https://github.com/lc3t/IBMDataScience/blob/master/pic/cleaned_raw_data.png "cleaned raw data")

Plot of main variables in 5 years:

![all features plot](https://github.com/lc3t/IBMDataScience/blob/master/pic/all_feature_plot.png "all features plot")


### 2.3 Model data set preparation
As shown above, raw data contains a lot different type of data, which is not suitable to be used in a machine learning model, we need adjust to satisfy the model requirement.

We can see in the column _"wind_dir"_, the values are string, which can not be calcluated. We can find useful tool in __scikit_learn__ library: __LabelEncoder__. This encoder allow us to encode a string to one-hot code.

After that, we need to make sure all data types are float32 type.

Then, we can do normalization to all features, which will increase the gradient descent efficency. We can also find useful tool in __scikit_learn__ library: __MinMaxScaler__ to do this.

To train a model, in one training unit, we need a input X and output y. Here, we can treat time _t-1_ features as input and use time _t_ "pm2.5" value as output. In this purpose, we need do one step diff to the dataframe. Make it easier to understand, we need to create _t-1_ dataframe and _t_ dataframe, then merge them into one data frame, drop all _t_ features exclude _"pm2.5"_. 

After that, we get the refreamed data set.
![reframed dataset](https://github.com/lc3t/IBMDataScience/blob/master/pic/reframed.png "reframed dataset")

### 2.4 Data set split
We need to split the dataset into train set and test set, then splits the train set and test set into input and output variables. That means we need two steps:

1. Split data set into train set and test set
2. Split train set and test set into training input, training output, test input, test output

As we have 4 years * 365 days * 24 hours data,  this data set is big enough for us to train the model, so we choose 1 year data as training set, 3 years data as test set.

Finally, the inputs (X) are reshaped into the 3D format expected by the model. _[samples, timesteps, features]_

## 3. Methodology
### 3.1 LSTM model
Long short-term memory (LSTM) is an artificial recurrent neural network (RNN) architecture, used in the field of deep learning. Unlike standard feedforward neural networks, LSTM has feedback connections that make it a "general purpose computer" (that is, it can compute anything that a Turing machine can). It can not only process single data points (such as images), but also entire sequences of data (such as speech or video). For example, LSTM is applicable to tasks such as unsegmented, connected handwriting recognition or speech recognition.

A common LSTM unit is composed of a cell, an input gate, an output gate and a forget gate. The cell remembers values over arbitrary time intervals and the three gates regulate the flow of information into and out of the cell.

LSTM networks are well-suited to classifying, processing and making predictions based on time series data, since there can be lags of unknown duration between important events in a time series. LSTMs were developed to deal with the exploding and vanishing gradient problems that can be encountered when training traditional RNNs. Relative insensitivity to gap length is an advantage of LSTM over RNNs, hidden Markov models and other sequence learning methods in numerous applications.

### 3.2 Model define
We will define the LSTM with __50__ neurons in the first hidden layer and __1__ neuron in the output layer for predicting pm2.5. The input shape will be __1__ time step with __8__ features.

We will use the __Mean Absolute Error (MAE)__ loss function and the efficient __Adam__ version of stochastic gradient descent.

The model will be fit for __50__ training epochs with a batch size of __72__.

## 4. Results
### 4.1 In-sample prediction
To verify the model, let's plot pm2.5 time series of test model and the predictions.
![prediction vs test](https://github.com/lc3t/IBMDataScience/blob/master/pic/forecastvstest.png "prediction vs test")

The graph looks same.

To further verify it, let's test with __Root Mean Squared Error__

### 4.2 RMSE test
use following fuction to get RMSE result
```python
np.sqrt(mean_squared_error(test, prediction))
```
The result is 3.8358, a quite small number which is acceptable.

## 5. Discussion
Overall, LSTM is a good model for multivariable time series prediction. With a small sample (365*24), we can also get a good prediction in a long term.

With this model, people can do air pollution forecast by themselves.

## 6. Conclusion
I hope through this report, people can learn how to build a time series forecast model with LSTM. Time series are currently applied in many feild, such as stock, sales, weather, etc. a good predection can help peole make better decision in the future.

Hope this report can help.


