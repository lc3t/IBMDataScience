# Data and data clean
## Data source
As mentioned in backgroup part, historical data can be found from UCI (University of California, Irvine):

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

## Data clean
let's take a snapshot of the raw data set:

![raw dataset](https://github.com/lc3t/IBMDataScience/blob/master/pic/raw_dataset.png "raw dataset")

We can find there are missing data ("NaN"), several data types, separated datetime stamps and no frame index. We need clean them one by one.

First, we need to combine datetime related columns together and get a standard pandas datetime.
For each row, we can using following function to get standard datetime stamp
```python
def parse(x):
    return datetime.strptime(" ".join([str(x.year),str(x.month),str(x.day),str(x.hour)]), '%Y %m %d %H')
```
Then apply to each row:
```python
df['date'] = df[['year', 'month', 'day', 'hour']].apply(parse,axis = 1)
```
We should set datatime as index, then we can get a dataframe of time series:
```python
df.index = df['date']
```
Second, Reshape the dataframe, drop unnecessary columns and rename the column names much more easier for understanding
```python
df.drop(columns=['No','year','month','day','hour','date'],inplace=True)
df = df.rename(columns={'DEWP':'dew','TEMP':'temp', 'PRES':'press','cbwd':'wind_dir','Iws':'wind_speed','Is':'snow','Ir':'rain'})
```
Third, fill the missing values with number 0
```python
df['pm2.5'].fillna(0, inplace=True)
```

Now, we get a cleaned raw data set, which is ready for data analysis:
![cleaned raw dataset](https://github.com/lc3t/IBMDataScience/blob/master/pic/cleaned_raw_data.png "cleaned raw data")

But this dataset is not ready to do model training, I will further clean data set and do onehot encoding, data normalization, restructure the frame to fit model requirement, split data set into training set and test set...

More details please refer to my notebook:

[Assignment notebook](https://github.com/lc3t/IBMDataScience/blob/master/The%20Battle%20of%20Neighborhoods.ipynb)

The rest of the content will be shown in the final report.







