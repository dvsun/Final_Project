# ENSO Prediction Analysis

### Overview

The aim of this project is to predict, by Machile-Learning techniques, the future outcome of El Niño-Sourthern Oscillation (ENSO).

Taken from Wikipedia:


" El Niño–Southern Oscillation (ENSO) is an irregularly periodic variation in winds and sea surface temperatures over the tropical eastern Pacific Ocean, affecting the climate of much of the tropics and subtropics. The warming phase of the sea temperature is known as El Niño and the cooling phase as La Niña. The Southern Oscillation is the accompanying atmospheric component, coupled with the sea temperature change: El Niño is accompanied by high air surface pressure in the tropical western Pacific and La Niña with low air surface pressure there. The two periods last several months each and typically occur every few years with varying intensity per period. "


#### Dataset

The [dataset](https://archive.ics.uci.edu/ml/datasets/El+Nino) contains a set of oceanographic and surface meteorological readings taken from a series of buoys positioned throughout the equatorial Pacific from March 1980 to June 1998. The origin cames from NOAA (National Oceanic and Atmospheric Administration), USA.

The main parameters are temporal and spatial location, humidity, wind speed and direction and sea and air temperatures.
	 

#### Main hypothesis

- Does the oscillation follow a pattern?

     H0 : Don't follow a pattern, it's totally random.

     H1 : Follow a pattern.

If so, 
- When will happen the next ENSO variation? 


- As the data that we'll work on is outdated, is my prediction accurate with what happen in reality? In comparison from recent parameters obtained by NOAA. 


The analysis method used will be Time-Series analysis.



### Next Steps

- Cluster the buoys with DBSCAN.
- Analysis of the time-series with SARIMA.
- Predict future oscillations. 
- Check the predicted oscillations with the reality.



### Data Preparation

#### Overview:

The dataset of this project contains 178080 observations. Each observation include the parameters that one buoy located in the central Pacific Ocean have registered in one day. The dataset extends from March 1980 to June 1998 for nearly 70 different locations between 7.5N - 7.5S and 135E - 95W. However, as each buoy had different characteristics, the dataset is incomplete and lacks information from many spacetime points. 

The full dataset have a size of 178080 rows x 12 columns, including 5 integers (id & date), 2 floats (location) and 5 object (the main 5 parameters). 

### Data Ingestion & Database

    If you downloaded a dataset (either public or private), describe where you downloaded it and include the command to load the dataset.
    
We download the [dataset](https://archive.ics.uci.edu/ml/datasets/El+Nino) from UC


repository. Afterwards we open the .csv file with Python 3.7 - Jupyter Notebook. Note that the name of the columns are stored in another .csv.

``` python
import pandas as pd

column = pd.read_csv('../Data/tao-all2.col', names = 'c')
data = pd.read_csv('../Data/tao-all2.dat', sep=' ', names = column['c'])
```


### Data Wrangling and Cleaning

First, we replace the '.' values (meaning missing information) to NaN values. This change allow us to count how many NaN values we have in the dataset. 
Then, we change the format of the columns that contain the parameters. All parameters are numeric, thus, we transform the columns to float.
Afterwards, we create a new column, named 'date', were we summarize the columns that are related to the date of the observation.

Finally we store the cleaned dataset in a database located in Google Cloud as well as in a .csv. 


### Data Analysis

Several years ago, the scientist have establish the Oceanic Niño Index (ONI) to parameterize the effect of el Niño-la Niña events. This index evaluate the sea surface temperature (SST) anomalies in the region 5N-5S, 120-170W. When the SST cross the threshold of +/- 0.5ºC for 5 consecutive overlapping seasons, it is considered that el Niño (for positive anomalies) or la Niña (for negative anomalies) has happened. 

Therefore, in this project we focus our analysis in how changes the Sea Surface Temperature against time. 
For that, we use Time-Series analysis to answer our hypothesis. 

First, from all the data we have, we take only those values related to the Sea Surface Temperature that are located inside the ONI region of 5N-5S, 120-170W. 
To simplify our analysis (1st aproximation), we consider that only the average of all the SST measures from the different buoys represent faithfully the average temperature of that region of the sea. 
Moreover (2nd aproximation), in order to reduce the computational requirements, we resample the daily SST measures to a monthly basis. 


    Overview the general steps you will go through to analyze your data in order to test your hypothesis.
    Document each step of your data exploration and analysis.
    Print charts to demonstrate the effect of your work. Charts make your presentation look good too.
    If you use ML in your final project, also describe your feature selection process.

### Model Training and Evaluation

    Train your ML model, produce results, and evaluate.
    This is an iterative process. Try your best to improve your model performance by:
        Try different models and select one that is the simplest yet produce the best result.
        Try advanced techniques and see if they improve the result.

### Conclusion

    Summarize your data analysis result.
    State your conclusion of your hypothesis testing.
    Interpret your findings in terms of the human-understandable question you try to answer.




## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)


