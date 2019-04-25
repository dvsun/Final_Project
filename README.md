# ENSO Prediction Analysis

### Overview

The aim of this project is to predict, by Machile-Learning techniques, the future outcome of El Niño-Sourthern Oscillation (ENSO).

Taken from Wikipedia:


" El Niño–Southern Oscillation (ENSO) is an irregularly periodic variation in winds and sea surface temperatures over the tropical eastern Pacific Ocean, affecting the climate of much of the tropics and subtropics. The warming phase of the sea temperature is known as El Niño and the cooling phase as La Niña. The Southern Oscillation is the accompanying atmospheric component, coupled with the sea temperature change: El Niño is accompanied by high air surface pressure in the tropical western Pacific and La Niña with low air surface pressure there. The two periods last several months each and typically occur every few years with varying intensity per period. "


#### Dataset

The [dataset](https://archive.ics.uci.edu/ml/datasets/El+Nino) contains a set of oceanographic and surface meteorological readings taken from a series of buoys positioned throughout the equatorial Pacific from March 1980 to June 1998. The origin of the dataset is NOAA (National Oceanic and Atmospheric Administration), USA.

The main parameters are temporal and spatial location, humidity, wind speed (N-S and W-E) and sea and air temperatures.
	 

#### Main hypothesis

- Does the oscillation follow a pattern?

     H0 : Don't follow a pattern, it's totally random.

     H1 : Follow a pattern.

If so, 
- When will happen the next ENSO variation? 


- As the data that we'll work on is outdated, is my prediction accurate with what happen in reality? In comparison from recent parameters obtained by NOAA. 


The analysis method used will be Time-Series analysis.

Steps:

- Analysis of the time-series with SARIMA.
- Predict future oscillations. 
- Check the predicted oscillations with the reality.

Extra:
- Cluster the buoys with DBSCAN.


## Organization of the reposetory.

#### Analysis folder
Contain all analysis files. There are 3 main different files:

- Import and Clean and Mapping
Contain the first steps of the analysis about importing, clean and mapping the data.

- Time-Series-Analysis
Each file contain a Time-Series-Analysis from different dataset and different variable selected. 

- Clustering files
Contain the clustering analysis of some groups of observations. We use the unsupervised method DBSCAN to cluster them. 

#### Data folder
Contains all data source and the intermediate .csv files generated in the analysis.

#### Images & Graphs folder
Contains the images and graphs used in the readme and the presentation.




## Summary of the main aspects of the analysis.

### Data Preparation

#### Overview:

The dataset of this project contains 178080 observations. Each observation include the parameters that one buoy located in the central Pacific Ocean have registered in one day. The dataset extends from March 1980 to June 1998 for nearly 70 different locations between 7.5N - 7.5S and 135E - 95W. However, as each buoy had different characteristics, the dataset is incomplete and lacks information from many spacetime points. 

The full dataset have a size of 178080 rows x 12 columns, including 5 integers (id & date), 2 floats (location) and 5 object (the main 5 parameters). 

### Data Ingestion & Database

We download the [dataset](https://archive.ics.uci.edu/ml/datasets/El+Nino) from UC repository. Afterwards we open the .csv file with Python 3.7 - Jupyter Notebook. Note that the name of the columns are stored in another .csv.

``` python
import pandas as pd

column = pd.read_csv('../Data/tao-all2.col', names = 'c')
data = pd.read_csv('../Data/tao-all2.dat', sep=' ', names = column['c'])
```


### Data Wrangling and Cleaning

First, we replace the '.' values (meaning missing information) to NaN values. This change allow us to count how many NaN values we have in the dataset. 
Then, we change the format of the columns that contain the parameters. All parameters are numeric, thus, we transform the columns to float.
Afterwards, we create a new column, named 'date', were we summarize the columns that are related to the date of the observation.

Finally, we store the cleaned dataset in a database located in Google Cloud as well as in a .csv. 


### Main Data Analysis

Several years ago, the scientist have establish the Oceanic Niño Index (ONI) to parameterize the effect of el Niño-la Niña events. This index evaluate the sea surface temperature (SST) anomalies in the region 5N-5S, 120-170W. When the SST cross the threshold of +/- 0.5ºC for 5 consecutive overlapping seasons, it is considered that el Niño (for positive anomalies) or la Niña (for negative anomalies) has happened. 

Therefore, in this project we focus our analysis in how changes the Sea Surface Temperature against time. 
For that, we use Time-Series analysis to answer our hypothesis. 

First, from all the data we have, we take only those values related to the Sea Surface Temperature that are located inside the ONI region of 5N-5S, 120-170W. The selection of the observations is done in the Clustering the ONI buoys file.

To simplify our analysis (1st aproximation), we consider that only the average of all the SST measures from the different buoys represent faithfully the average temperature of that region of the sea. 
Moreover (2nd aproximation), in order to reduce the computational requirements, we resample the daily SST measures to a monthly basis. 
Plus, we cut the first months of the time series because there were many missing values. (3rd aproximation).
Finally, we interpolate two more missing values using linear regression between the value below and above. (4th aproximation).

As we have short data and we can test our predictions with values outside the dataset time period, we are not going to split our data into test and train and, therefore, we take all values to train the model.

After these aproximations, we plot the Sea Surface Temperature vs Time:

![image_1](https://raw.githubusercontent.com/dvsun/Final_Project/master/Images_%26_graphs/ONI_SS_temp.png)

We start our analysis decomposing the Time-serie:

``` python
decomposition = sm.tsa.seasonal_decompose(y_rev, model='additive')
fig = decomposition.plot()
```
![image_2](https://github.com/dvsun/Final_Project/blob/master/Images_%26_graphs/SST_ONI_Decom.png)

In the trend plot, we observe a positive trend. We should not see it because (without considering the impact of the Climate Change), the sea surface temperature is constant among the years.
The seasonality is correctly captured by its plot but the residual error plot tends too much to -1, indicating that the other two factors cannot explain completely the behaviour of the dataset. 


Then, we proceed to analyze the time-series by SARIMAX method. In order to apply this method to the dataset, I follow the following SARIMAX guide: "https://www.digitalocean.com/community/tutorials/a-guide-to-time-series-forecasting-with-arima-in-python-3".

First, we select for the p, d ,q ,P, D, Q, S parameters that fits best por our values. 

``` python
mod = sm.tsa.statespace.SARIMAX(y_rev,
                                            order=param,
                                            seasonal_order=param_seasonal,
                                            enforce_stationarity=False,
                                            enforce_invertibility=False)

```

Second, we fit the selected parameters (1, 0, 1)x(0, 1, 1, 12)12, as they are the parameters that result in a lower AIC (a mesure that quantifies the overall fitness of the model).
Next, we fit the SARIMAX model with out dataset.

``` python
sarimax_model = sm.tsa.statespace.SARIMAX(y_rev,
                                order=(1, 0, 1),
                                seasonal_order=(0, 1, 1, 12),
                                enforce_stationarity=False,
                                enforce_invertibility=False)

results = sarimax_model.fit()
```
We check the fitness of our model running the diagnostics.

![image_3](https://github.com/dvsun/Final_Project/blob/master/Images_%26_graphs/SST_ONI_diagnostic.png)

This four graphs explain the fitness of the model with our data. The histogram shows that our the errors of our model follow a normal distribution, a sign of a good model. On the other hand, the standarized residual plot and the correlogram indicates that some errors are not equally distributed and exceed the confidential interval. Finally, the qq-plot warn us that the ordered distribution of residuals don't follow totally a normal distribution. 

Having a fit model, we predict the last values of our dataset to check the desviation from the real values. 
First with the parameter dynamic = False that ensures that the forecast at each point is generated using the full history of the dataset.


``` python

pred = results.get_prediction(start=pd.to_datetime('1995-01-01'), dynamic=False)
pred_ci = pred.conf_int()

```

![image_4](https://github.com/dvsun/Final_Project/blob/master/Images_%26_graphs/SST_ONI_pred_dyn_false.png)

The prediction fits quite well with the values from the dataset. However, we have to take into account that we are biasing the model using the full history of the dataset. 

We calculate the MSE for this prediction:

``` python
mse = ((y_forecasted - y_truth) ** 2).mean()
print('The Mean Squared Error of our forecasts is {}'.format(round(mse, 2)))

```
We obtain a low error in line with what we see in the plot. 

Then, we change the parameter dynamic to True. In this configuration the model only uses the values from the dataset until certain point and then only uses the data generated by the model itself. 

``` python

pred = results.get_prediction(start=pd.to_datetime('1995-01-01'), dynamic=True)
pred_ci = pred.conf_int()

```

![image_5](https://github.com/dvsun/Final_Project/blob/master/Images_%26_graphs/SST_ONI_pred_dyn_true.png)

In this plot, we can see the poor prediction that our model really generates, with a high confidence interval. 


And, again, we calculate the MSE for this prediction:

``` python
mse = ((y_forecasted - y_truth) ** 2).mean()
print('The Mean Squared Error of our forecasts is {}'.format(round(mse, 2)))
```
And, also logical, our error is higher than before. 


Finally, we forecast 100 more months from the last month of the data set: 

```
pred_uc = results.get_forecast(steps=100)
pred_ci = pred_uc.conf_int()

```

![image_6](https://github.com/dvsun/Final_Project/blob/master/Images_%26_graphs/SST_ONI_forecast.png)

As we though, the model give us a poor forecast that prevent to make any prediction with some confidence. However, the model does predict the seasonal behaviour of the sea surface temperature.


### Model Training and Evaluation

We follow the same workflow for different parameters and more simplified datasets (taking the data from only 4 buoys, or taking only the periods when there are no anomalies. However, any analysis provide us a better result than the analysis already described.


### Conclusion

Our model does predict the seasonal changes in the sea surface temperature, and thus, predicting the normal behaviour. Unfortunately, the high error we obtain with our model prevent us to compare between normal and anormal sea surface temperature in the central Pacific. Therefore, the results we obtain from this analysis are not good enough to predict any future el Niño o la Niña event and so, we cannot answer our initial question with this combination of dataset and analysis. 



## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License
[MIT](https://choosealicense.com/licenses/mit/)


