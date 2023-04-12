---
layout: default
altair-loader:
  altair-chart-1: "charts/measlesAltair.json"
  altair-chart-2: "charts/Philly_mode_income.json"
  altair-chart-3: "charts/Philly_mode_age.json"
hv-loader:
  hv-chart-1: ["charts/measlesHvplot.html", "500"] # second argument is the desired height
  hv-chart-2: ["charts/tabs.html", "500"]
  hv-chart-3: ["charts/tabs0.html", "500"]
folium-loader:
  folium-chart-1: ["charts/foliumChart.html", "400"] # second argument is the desired height
  folium-chart-2: ["charts/percent_no_internet.html", "400"] # second argument is the desired height
---

# Welcome!

We are **Yiping Ying**, **Ying Xu**, **Troy Yang**, a group from [CIS 5450](https://sites.google.com/seas.upenn.edu/cis545/home), Spring 2023.

This website is used to display our final project for this course.

# I. Motivation

Bike sharing is a new type of micro-transportation that has emerged in urban areas in recent years. Its appearance provides a new way to solve the “last mile” problem. However, while bringing us convenience, shared bicycles also have some difficult problems to solve, among which the re-balancing problem is more prominent.

The problem of re-balancing arises because the number of shared bicycles is not evenly distributed among various sites. In some remote sites, people ride bikes out of the site, but no one returns the shared bicycles. A common solution is that the shared bicycle companies will send some trucks to transport the shared bicycles to these sites to complete the supplement of shared bicycles. The question with this type of solution is how to distribute the volume of these trucks to maximize efficiency?

In general, there are two steps in truck-based rebalancing approaches, i.e., demand prediction and station rebalancing. First, it is crucial to accurately predict the demand at each station to foresee the bike and dock availability in the future. Second, it is important to design effective strategies for truck operators to reposition bikes among stations.

This project will focus on the first step of the problem by taking [***Indego***](https://www.rideindego.com/), a bike-sharing system in the Philadelphia area, as a research sample, on which we will use different models (OLS, ARIMA, Neural Networks, etc.) to make accurate demand forecasts. Indego started operation on April 23, 2015, with 125 stations and 1,000 bicycles. After 7 years of development, the function of this system has tended to be comprehensive, which is appropriate for research. In this analysis, we select a **5-week** period from **May 17** to **June 20**, **2021** for temporal/spatial analysis.

This section will show examples of embedding interactive charts produced using [Altair](https://altair-viz.github.io) and [Hvplot](https://hvplot.pyviz.org/).

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

## 2.2 Spatial Pattern

In this part, we check the spatial characteristics in the following categories:

- **Internal**

  - e.g., the number of docks per station;

- **Demographic (Census)**

  - e.g., population, income, percent commuting by car, percent with bachelor's degree or higher, etc.

- **Amenities/Disamenities**

  - e.g., distance to nearest restaurants, parks, within Center City, etc

- **Transportation network**

  - e.g., distance to nearest bus stop, interesection nodes, nearest subway stop

- **Neighboring stations**

  - e.g., average trips of nearest stations, distance to nearest stations

### 2.2.1 Internal Characteristics

- Number of Docks in Each Station, Philadelphia 2021

![fig6]({{ site.url }}{{ site.baseurl }}/assets/img/fig6.png)

Below are the scatter plots of the stations, we can see that they are clustered around the center city. As for the trips, a station tend to have almost the same number of start and end trips, given the high similarity shown in the plots.

<div id="hv-chart-3"></div>

### 2.2.2 Census demographic data

- Choropleth of Social/Economical Features

<div id="hv-chart-2"></div>

- Chart of Median Income vs Transit Mode Selection in Philadelphia.

<div id="altair-chart-2"></div>

- Chart of Median Age vs Transit Mode Selection in Philadelphia.

<div id="altair-chart-3"></div>

### 2.2.3 Amenities/Disamenities

Add two new features:

1. Distances to the nearest **10 restaurants** from [Open Street Map](https://wiki.openstreetmap.org/wiki/Map_Features);

2. Whether the station is located within Center City.

- Mean Distance to Nearest 10 Restaurants (log transformed)

![fig8]({{ site.url }}{{ site.baseurl }}/assets/img/fig8.png)

### 2.2.4 Transportation Network

Add a feature that calculates the distance to the nearest intersections.

- Intersections

![fig9]({{ site.url }}{{ site.baseurl }}/assets/img/fig9.png)

- Mean Distance to Nearest 3 Intersections (log transformed)

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

### 2.2.6 Correlation Matrix

![fig13]({{ site.url }}{{ site.baseurl }}/assets/img/fig13.png)




