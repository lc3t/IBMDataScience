# Introduction/Business Problem

## Background of project
China, as a developing country, often meet a lot of problems on environment pollution: water, air, plastic, etc.

As a Chinese, especially who live in Beijing, always suffers from air pollution. The reasons of air pollution are various, car exhaust, industry exhaust, fume, construction dust, etc.

![Air pollution in Beijing](http://www.chinairn.com/UserFiles/image/20181126/20181126134459_5632.jpg "Air pollution in Beijing")

It will be awesome if we can predict its happening in the future, then we can be prepared before we go outdoor.

Fourtunately, today's weather forecast already included air pollution forecast. But, is it reliable?

According to the comparison result of air pollution index recorded by US Embassy in Beijing and air pollution report recorded by Beijing government, the result are not quite the same.

How about we create a tool, to do the forecast based on the data from US Embassy in Beijing?

This tool is based on reliable data source (US Embassy in Beijing), and can do forecast for the future. It is very useful for ordinary people to create a air pollution forecast for themselves!

## How can we do this
### where to find data
We can find the relative data from internet.
For example, we can find historic data from UCI.

[Data Source Page of UCI](https://archive.ics.uci.edu/ml/datasets/Beijing+PM2.5+Data)

Also, we can write a web crawler to scrape data from US Embassy in Beijing website.
### which model/methodology should be used
Air pollution data, is time series, there are several methodologies to do forecast/prediction:
* Linear regression
* Multi regression
* Polynomial regression
* ARiMA
* RNN
* LSTM

As required by the assignment, we need to choose a machine-learning algorithm, so RNN or LSTM is a good ideas.

We can find the models in Keras library
Let's do it!