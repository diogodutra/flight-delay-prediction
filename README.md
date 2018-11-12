![](https://ydq1jlhdhy-flywheel.netdna-ssl.com/wp-content/uploads/2014/05/airport-banner.jpg)

# Flight Delay Prediction
## Machine Learning Engineer Nanodegree - Capstone Proposal
Diogo Dutra
November 11th, 2018

## Proposal

### Domain Background
_Flight delays_

Aerial commute is increasingly important as the globalization advances and the world population grows. However, the air traffic is also becoming a issue, especially for the most used regional hubs. While transportation infrastructure is mainly a role for the governments, predicting the flight delays may be accessible for private initiative and it will benefit those passengers running tight on schedule by allowing them to reorganize their tasks on advance.

The most common causes of flight delays are varied [[reference]](https://en.wikipedia.org/wiki/Flight_cancellation_and_delay). While some are not related to accessble data, some other might be within reach. The inaccessible data will remain as noise caused from security, maintenance and distater issues. The accessible data are weather and congestion that hopefully will shed some light to predict some of the flight delays.

The inspiration for such topic is clear for the author because of a combination of being a frequent airplane traveller and an experienced aeronautical engineer.

### Problem Statement
_How much will be the flight delay?_

Basically, the predictive model shall be able to answer ther following question:
>Given the departure information what will be the flight delay in minutes?

The departure information is all the data available before the take-off, including scheduled time, date, airliner, flight number, air temperature, air pressure, visibility and so on.

### Datasets and Inputs
_ANAC and BDM_

The historical data of flight departures and arrivals will be the world most busy regional aerial commute to maximize the ammount of available data. The Brazilian one-way from São Paulo (Guarulhos airport) to Rio de Janeiro (Sandos Dumont airport) is the most numerous by quantity of departures per day.
The 2018 historical dataset from January to September will be downloaded from the [ANAC website](http://www.anac.gov.br).
The weather data will be the [METAR](https://en.wikipedia.org/wiki/METAR) type and specific to the departure airport extracted directly from the [INPE's BDM website](http://bancodedados.cptec.inpe.br/).
Both departure and weather data will be merged into one single table considering the nearest date and time. Such table will be the sample input to the pipeline.

### Solution Statement
_Supervised learning regression_

The predictive algorithm shall be a supervised learning regression in order to estimate the flight delay in minutes.
The input will be split between train, test and plot data from random choice.

Different options of regression algorithms will be considered by applicability and compared by accuracy. The chosen one will be further polished for even better accuracy.

### Benchmark Model
_Better than naive_

The answer to the problem statement shall be more accurated than the "naive guess", where "naive guess" means estimating null delay for every flight. In order to compare, the same evaluation metrics will be considered for both the predictive model and the "naive guess".


### Evaluation Metrics
_Sum of errors_

The considered metrics are:
- Sum of MSE (mean squared error)
- Sum of absolute errors

Additionally, there shall be a linear plot of percent of successfull flight delay estimatives (y axis) by threshold of error tolerance (x axis) comparing both the predictive model and the "naive guess". This plot will be a support for extra insights.


## Project Design

### Structure
The pipeline will be divided in 4 different files.
The first file will be used to download the data from the sources whenever it is possible. The more files can be downloaded by algorithm the better due to replicability. Should some data be not possible ti download by algorithm then it should be available in the repository for public offline access. 
The second file will be used to prepare the downloaded data as an usefull input table. It means that the missed data will be treated and bot weather and departure tables will be the merged. All columns will also be derived (ie: delay in minutes), removed or changed in order to prepare for later use for plot, model fit and analysis.
The third file will be used to explore the data with different plots for insights. The insights will hopefully be which features are the more relevants, which algorithms are the better candidates for predictive model and which difficulties are expected to be ahead.
The fourth file will be used to split the samples into train and test data, to train the candidate models, to select which model is the final one, to improve its accuracy and finally to evaluate its performance over the benchmark.

### 1st File - Download
The data will be downloaded directly from the source as CSV files.
The source of the departure files are CSV separated by months and are directly accessible by url. For this reason, the algorithm will dowload all of them separatedly and then append them as illustrated below.
![](http://www.digdb.com/excel_add_ins/combine_append_tables_sheets_files/1.gif)

The weather files are also separated by months. However, they are not directly accessible by url as the website requires a login. For this reason, each of the files will be manually downloaded as XLS and then saved as CSV. All the files  will be available at the repository for public offline access. Then, the weather tables will be appended similarly as the departure tables.

### 2nd File - Preparing
The downloaded data will be loaded into Panda's dataframe tables.

The departure table will be filtered to discard all routes except the one's regarding airliners one-way from Guarulhos (SBGR) to Santos Dumont (SBRJ) airports.

The departure and weather tables will be merged using the nearest date and time as the linking column from left to right, meaning that useless weather data will be discarded as illustrated below.
![](https://datacarpentry.org/python-ecology-lesson/fig/inner-join.png)

Then, the table will have its columns modified for scikit-learn type. For instance, data will be converted from strings to datetime and the flight delay in minutes will be calculated from the difference between the schedule and the real arrivals. The final input table will be saved as local file for persistent access. This file will also be available at the repository for public offline access.


### 3rd File - Exploration
The prepared input table will then be used for a series of plots to better understand how is the data. We will focus on how each feature is correlated with the label "Flight Delay", a columns with integer values calculated as explained before.

The series of plots will be of two kinds: scatter with transparency and violin distribution.

These plots will give enough insights to choose some candidates of types of regression algorithms and which features should be considered for the last phase of this pipeline.

### 4th File - Predicting
The last file will be used to create the predictive model.

Firstly we will filter down the input features. Secondly, we will hot-encode the categorical features. Thridly, we will split the samples between train and test samples.

The selected candidates from the previous phase will each be instantiated with standard parameters, trained with the train sample and compared to each other by the evaluation metrics (sum of MSE and sum of absolute errors). The lowest metric will define the best algorithm for further tune. In the case of even results, then the linear plot comparing the percent of predict success (y axis) per threshold of error tolerance (x axis) will also be considered to choose the final algorithm.

Once the final algorithm is chosen, it will be fine tuned for better prediction accuracy. In order to achieve this, a grid of different values of parameters will be rerun to search and find the combination that gives the best accuracy considering again the evaluation metrics.

Finally, the final evaluation metrics over the test samples will be compared with the "naive guess" as benchmark for the predictive model. 
