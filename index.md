---
layout: default
altair-loader:
  altair-chart-2: "charts/Philly_mode_income.json"
  altair-chart-3: "charts/Philly_mode_age.json"
hv-loader:
  hv-chart-2: ["charts/tabs.html", "500"]
  hv-chart-3: ["charts/tabs0.html", "500"]
  hv-chart-4: ["charts/importance_plot.html", "500"]
  hv-chart-5: ["charts/MAE_comparison.html", "500"]
  hv-chart-6: ["charts/MAE.html", "500"]
  
---

# Welcome!

We are **Yiping Ying**, **Ying Xu**, **Troy Yang**, a group from [CIS 5450](https://sites.google.com/seas.upenn.edu/cis545/home), Spring 2023.

This website is used to display our final project for this course.

# I. Motivation

Bike sharing is a new type of micro-transportation that has emerged in urban areas in recent years. Its appearance provides a new way to solve the “last mile” problem. However, while bringing us convenience, shared bicycles also have some difficult problems to solve, among which the re-balancing problem is more prominent.

The problem of re-balancing arises because the number of shared bicycles is not evenly distributed among various sites. In some remote sites, people ride bikes out of the site, but no one returns the shared bicycles. A common solution is that the shared bicycle companies will send some trucks to transport the shared bicycles to these sites to complete the supplement of shared bicycles. The question with this type of solution is how to distribute the volume of these trucks to maximize efficiency?

In general, there are two steps in truck-based rebalancing approaches, i.e., demand prediction and station rebalancing. First, it is crucial to accurately predict the demand at each station to foresee the bike and dock availability in the future. Second, it is important to design effective strategies for truck operators to reposition bikes among stations.

This project will focus on the first step of the problem by taking [Indego](https://www.rideindego.com/), a bike-sharing system in the Philadelphia area, as a research sample, on which we will use different models (OLS, Random Forest, ARIMA, Neural Networks, etc.) to make accurate demand forecasts. Indego started operation on April 23, 2015, with 125 stations and 1,000 bicycles. After 7 years of development, the function of this system has tended to be comprehensive, which is appropriate for research. In this analysis, we select a **5-week** period from **May 17** to **June 20**, **2021** for temporal/spatial analysis.

This section will show embedding interactive charts produced using [Altair](https://altair-viz.github.io) and [Hvplot](https://hvplot.pyviz.org/).

# II. Exploratory Data Analysis

## 2.1 Temporal Pattern

![fig1]({{ site.url }}{{ site.baseurl }}/assets/img/fig1.png)

There is clearly a daily periodicity and there are lull periods on weekends. Notice that the weekend near the 31st of May (Memorial Day) doesn’t have the same dip in activity.

![fig2]({{ site.url }}{{ site.baseurl }}/assets/img/fig2.png)

From the morning, the number of rides gradually increases, and as we can see, the number peaks in the late afternoon (16-18), this may be attributed to the evening rush in Philadelphia, the number falls quickly. 

![fig3]({{ site.url }}{{ site.baseurl }}/assets/img/fig3.png)

For weekends and weekdays, the trends of rides in the day are almost the same, while the rides in the daytime (5-18) on the weekends are much less than that on the weekdays.

![fig4]({{ site.url }}{{ site.baseurl }}/assets/img/fig4.png)

![fig5]({{ site.url }}{{ site.baseurl }}/assets/img/fig5.png)

As the *Indego* shared bike trip duration density suggests, most of the trips’ have duration less than 180 minutes (3 hours), therefore we discard trips with longer duration (more than 3 hours). (Trips: **97435**)

## 2.2 Spatial Pattern

In this part, we check the spatial characteristics in the following categories:

- **Internal Characteristics**

- **Demographic (Census)**

- **Amenities/Disamenities**

- **Transportation network**

- **Neighboring stations**

### 2.2.1 Internal Characteristics

- Active Station: **139**

- Number of Docks in Each Station, Philadelphia 2021

![fig6]({{ site.url }}{{ site.baseurl }}/assets/img/fig6.png)

Below are the scatter plots of the stations, we can see that they are clustered around the center city. As for the trips, a station tends to have almost the same number of start and end trips, given the high similarity shown in the plots. Correspondingly, in the modeling part, we only select the number of start trips.

<div id="hv-chart-3"></div>

### 2.2.2 Census demographic data

In this part, we download data from [American Community Survey 5-Year Data]( American Community Survey 5-Year Data (2009-2021) (census.gov)) in Philadelphia (2021) to check the relation between the social/economical features and the shared bike demand.

- Choropleth of Social/Economical Features

<div id="hv-chart-2"></div>

From the choropleths, we can see that the existing shared bike stations generally locate at the places where the people tend to be younger, having higher income and no car. The below 2 `hvplot` interactive charts present this point more accurately.

- Chart of Median Income vs Transit Mode Selection in Philadelphia.

<div id="altair-chart-2"></div>

- Chart of Median Age vs Transit Mode Selection in Philadelphia.

<div id="altair-chart-3"></div>

### 2.2.3 Amenities/Disamenities

Add two new features:

1. Distances to the nearest **10 restaurants** from [Open Street Map](https://wiki.openstreetmap.org/wiki/Map_Features);

2. Whether the station is located within Center City.

- ***Plot:*** Mean Distance to Nearest 10 Restaurants (log transformed)

![fig8]({{ site.url }}{{ site.baseurl }}/assets/img/fig8.png)

### 2.2.4 Transportation Network

Add a feature that calculates the distance to the nearest intersections.

- Intersections

![fig9]({{ site.url }}{{ site.baseurl }}/assets/img/fig9.png)

- ***Plot:*** Mean Distance to Nearest 3 Intersections (log transformed)

![fig10]({{ site.url }}{{ site.baseurl }}/assets/img/fig10.png)

### 2.2.5 Neighboring Stations

- **Spatial Lag**: a feature that encodes the fact that demand for a specific station is likely related to the demand in neighboring stations.

We add two new features:

1. The average distance to the nearest 5 stations

2. The average trip total for the nearest 5 stations

- ***Plot***: Mean Distance to Nearest 5 Stations (log transformed)

![fig11]({{ site.url }}{{ site.baseurl }}/assets/img/fig11.png)

- **Check the correlation between the trip counts and the spatial lag**

![fig12]({{ site.url }}{{ site.baseurl }}/assets/img/fig12.png)

From the figure, we can see that there is a linear correlation between the trip counts and the spatial lag.

### 2.2.6 Correlation Matrix

![fig13]({{ site.url }}{{ site.baseurl }}/assets/img/fig13.png)

# III. Modeling

In this section we will try several models to predict the *Indego* Shared Bike Demand. Following is the plan:

## 3.1 General Demand Prediction

The first part of our modeling is the general demand prediction, in which temporal features like time lags are not included, as we will use them in the latter models.

Aim for this part is to figure out what is(are) the most important features that affect bike share demand. First, we will use `Random Forest` and several `OLS` models to perform the prediction, then find out the best model, and draw the importance plot based on the selected model.

- **OLS** Models (Base line) Acccuracy:

  - Unregularized Linear Regression: 0.07

  - Ridge Regression: 0.17
 
  - Lasso Regression: 0.15

  - Elastic Net Regression: 0.16

- **Random Forests**

  - The best model with accuracy: 0.34

  - Importance Plot:

<div id="hv-chart-4"></div>

Judging from all the five models, we can say that the random forest performs the best, and the most important feature in the model is the lagged distance from nearby stations which is the same as the plot we made in the last part.

## 3.2 Time Series Prediction:

Only select the station: ***Amtrak 30th Street Station***

- **ARIMA**

**ARIMA (Autoregressive Integrated Moving Average)** is a popular statistical method for time series forecasting. It combines three main components: Autoregressive (AR), Moving Average (MA), and Integration (I).

ARIMA models are represented as ARIMA(p, d, q), where p, d, and q are the orders of the AR, I, and MA components, respectively. Seasonal ARIMA models, known as SARIMA or SARIMAX (when including external variables), can also incorporate seasonal components to capture patterns that repeat at regular intervals, such as daily or yearly seasonality.

To make accurate predictions with ARIMA models, the appropriate model order (p, d, q) and, if applicable, seasonal order need to be determined. This is typically done using techniques like grid search, stepwise search, or auto-ARIMA methods, which identify the model with the best performance based on a chosen criterion, such as the Akaike Information Criterion (AIC) or the Bayesian Information Criterion (BIC).

Once the best ARIMA model is selected, it can be used to forecast future values in the time series. The model's performance can be evaluated using metrics like Mean Absolute Error (MAE), which provide insights into the accuracy of the predictions.
  
  - To find the order of differencing(d) in ARIMA model

The order of differencing in ARIMA model depends on whether the time series is stationary or not. The Augmented Dickey Fuller test (`adfuller()`) can be used to check the stationarity of the series. If the p-value of the test is less than the significance level (0.05), then we reject the null hypothesis and conclude that the series is stationary. In this case, no differencing is needed (d=0). However, if the p-value is greater than 0.05, then the series is non-stationary and differencing is required.

p-value we get is smaller than the significance level, we infer that the time series is stationary. Therefore, the order of differencing d is set to 0.

  - To find the order of AR term (p) and MA term (q)

There are basically two ways to determine the optimal value of AR term (p):

1. Manual selection: You can manually select the AR term (p) by plotting the autocorrelation function (ACF) and partial autocorrelation function (PACF). This requires expertise and experience.

2. Automatic selection: You can use the auto_arima function in Python to automatically select the best values for the parameters p and q. The auto_arima function compares the likelihood of the model's effectiveness (which can be related to accuracy in later prediction) to select the best values for p and q.

Therefore, using the auto_arima function can save time in manually selecting parameters and ensure that the selected parameters are optimal.

  - Automatically build SARIMA model in python

The best-fit model is ARIMA(3, 0, 0). This means that the order of the autoregressive (AR) term is 1, the order of the difference (d) is 0, and the order of the moving average (MA) term is 3.

The AIC (Abject Pool Information Criterion) value of the model is 2009.388, which is a measure of the goodness of fit of the model, and a lower AIC value usually implies a better fit.

  - Data forecasting using the model created
 
 *MAE*: **1.75**

![0424_arima]({{ site.url }}{{ site.baseurl }}/assets/img/0424_arima.png)
  
- **GRU**

Recurrent neural networks (RNNs) are ofren leveraged to model the temporal dependency. In particular, we use Gated Recurrent Units (GRU), which is a powerful variant of RNNs overcoming gradient vanishing and gradient exploding problem effectively. Encoder-decoder structure is widely used in spatiotemporal sequence predicting tasks because it has been verified very effective. Therefore, we introduce encoder-decoder structure to build sequence to sequence architecture along with GRU units. Sequence to sequence is effective in multiple steps ahead prediction.

**Gated Recurrent Unit**. Recurrent Neural Networks (RNNs) can model the dependency of time series effectively. However, the traditional RNN models have limitations for long-term prediction due to gradient vanishing and gradient exploding problems. Although Long Short-term Memory (LSTM) can address these challenges, it has the defect of more training time consumption special for complex structures. Hence, we introduce Gated Recurrent Unit (GRU), the variant of RNNs, to model the temporal dependency. Compared with LSTM, GRU model has a relatively simple structure with fewer parameters and faster training speed.

  -	GRU architecture built on PyTorch.
  
  -	Generate a panel of interval 60 minutes. 

  -	Split Train/Test Set ratio: **0.8**.

  -	Use 3 week’s records to predict one hour’s demand.

  -	Metrics: *MAE*: **0.98**

![0424_gru]({{ site.url }}{{ site.baseurl }}/assets/img/0424_gru.png)

- **GCN**

In spatial correlation modeling, current works usually employ grid to divide the urban area, converting urban data to Euclidean domains, and then use Convolutional Neural Networks (CNN) to model spatial correlation. However, in our problem, the spatial distribution of bike stations is irregular and in non-Euclidean domains. Hence, we introduce graph structure to model the spatial distribution of bike stations and exploit Graph Convolutional Networks (GCN) to model spatial correlation.

**Graph Convolutional Network(GCN)**, is a deep learning model used to handle graph-structured data. Compared to traditional neural networks, GCN can directly process graph-structured data such as social networks, knowledge graphs, chemical molecules, etc.

The basic idea of GCN is to combine the representations of nodes and their neighboring nodes and update the node representations through convolutional operations, thereby achieving feature learning and representation of the entire graph. GCN is a method based on spectral graph theory, which decomposes the ***adjacency matrix*** of the graph into eigenvalues to obtain the Laplacian matrix of the graph, and thus converts the convolutional operation into matrix multiplication. This method can effectively capture topological structure information between nodes and achieve graph feature learning and representation.

  -	Metrics: *MAE*: **1.45**

![GCN_result]({{ site.url }}{{ site.baseurl }}/assets/img/GCN_result.png)

# IV. Evaluation

In this section, we compare the 2 neural network models. The metric we take here is **MAE**. We have calculated it for the following two methods separatel:

1. **MAE** of all stations by time interval(1h):

  - To explore the temporal pattern of MAE;
  
  <div id="hv-chart-5"></div>
  
From the figures, we can see that for **GRU**, **MAE** changes periodically, generally having the lowest point in the morning, while in the afternoon, the number becomes bigger, which may be resulted from the fact that people ride more bikes in the afternoon.

For **GCN**, the magnitude of the value change is not very large, basically around 0.8-1.0. However, this fact also indicates the weakness of the ***GCN*** model, even though it is stable.

Both advantages and shortcomings of the two models are obvious. Perhaps the next step is to combine the two to see if better results can be achieved.

2. **MAE** by each station.

  - To explore the spatial pattern of MAE.

  <div id="hv-chart-6"></div>

The scatter patterns of the two models also suggest what we have discovered in the above interval MAE check. 

One thing the two have in common is that they tended to have larger **MAE** in center city, where the stations generally have more docks and more rides which is correspondent with the above. For the accuracy, **GRU** performs better on most stations than **GCN**.

# V. Conclusion

In this study, we select 35 days' data of Indego Bike trips in 2021 summer to predict the demand of the shared bike demand in Philadelphia.

Based on models we have made, we found out for general demand, the distance to nearby shared bike stations and the number of trips in nearby stations serve as a significant role in the demand. Besides, for time series prediction, we use **MAE** as the final metric to evaluate our 3 models: **ARIMA**, **GRU**, **GCN**. 

As the **GRU** model has the lowest **MAE** (0.98), it outperforms the other two. We would recommend this model to the Department of Transportation for further planning, and also as a support to solve the re-balancing problem.








