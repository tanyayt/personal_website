---
layout: post
title: "Boston Airbnb Data"
tags: datascience
---

<div class="mapouter"><div class="gmap_canvas"><iframe width="300" height="200" id="gmap_canvas" src="https://maps.google.com/maps?q=boston,%20ma&t=&z=9&ie=UTF8&iwloc=&output=embed" frameborder="0" scrolling="no" marginheight="0" marginwidth="0"></iframe><a href="https://google-map-generator.com">google map generator</a></div><style>.mapouter{position:relative;text-align:right;height:200px;width:300px;}.gmap_canvas {overflow:hidden;background:none!important;height:200px;width:300px;}</style></div>

Airbnb has transformed travel and the vacation rental industry in numerous ways. It provides travelers cost-effective accommodation options beyond traditional hotel rooms, and brings hosts additional income to compensate housing expenses in the city. This project analyzes Airbnb data in Boston area, and provides valuable insights into the availability trend and pricing distribution in different neighborhoods. 

# What is the availability trend in Boston Area?

## How does the availability change throughout the year? 

<img src="https://github.com/tanyayt/udacity_data_scientist/blob/master/boston_airbnb_analysis/availability_trend_daily.png?raw=true" style="width: 900px;"/>



Availability is defined as the % of listings available. Availability reaches its highest point in early December 2016, and its lowest point in mid-September 2016. The total available listings follow a similar trend. 

<img src="https://github.com/tanyayt/udacity_data_scientist/blob/master/boston_airbnb_analysis/available_counts_trend_daily.png?raw=true" style="width: 900px;"/>

Finally, let's see how availability changes throughout the week. 

<img src="https://github.com/tanyayt/udacity_data_scientist/blob/master/boston_airbnb_analysis/availability_weekly.png?raw=true" style="width: 500px;"/>

<img src="https://github.com/tanyayt/udacity_data_scientist/blob/master/boston_airbnb_analysis/available_count_weekly.png?raw=true" width="500px">



# How much does each type of rooms cost in different neighborhoods? 

<img src="https://github.com/tanyayt/udacity_data_scientist/blob/master/boston_airbnb_analysis/average_price_by_neighbourhood_room_type.png?raw=true" width="800px">

# Can we predict the price? 

Each question is answered with a clear visual, table, or statistic that provides how the data supports or disagrees with some hypothesis that could be formed by each question of interest.

# Conclusion 

View all the project files and codes in [Github Repository](https://github.com/tanyayt/udacity_data_scientist)