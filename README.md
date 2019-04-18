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


- As the data that I'll work on is outdated, is my prediction accurate with what happen in reality? In comparison from recent parameters obtained by NOAA. 


The analysis method used will be Time-Series analysis.


---


## TO DO

### Data Preparation

#### Overview:

    What is your dataset about?
    Where/how did you obtain your dataset? It can be either a public dataset or collected with API/web scraping. Provide a link if possible.
    General description of the dataset such as the size, complexity, data types, etc.

### Data Ingestion & Database

    If you downloaded a dataset (either public or private), describe where you downloaded it and include the command to load the dataset.
    If you obtain the data via API/web scraping, provide the scripts.
    Provide a schema of your tables.

### Data Wrangling and Cleaning

Your full process of data wrangling and cleaning.

    Document your workflow and thinking process.

### Data Analysis

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


