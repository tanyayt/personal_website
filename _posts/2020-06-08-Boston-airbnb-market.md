---
layout: post
title: "Boston Airbnb Rental Market: Availability Trend and Pricing Model"
tags: "data science"
---

<div class="mapouter"><div class="gmap_canvas"><iframe width="300" height="200" id="gmap_canvas" src="https://maps.google.com/maps?q=boston,%20ma&t=&z=9&ie=UTF8&iwloc=&output=embed" frameborder="0" scrolling="no" marginheight="0" marginwidth="0"></iframe><a href="https://google-map-generator.com">google map generator</a></div><style>.mapouter{position:relative;text-align:right;height:200px;width:300px;}.gmap_canvas {overflow:hidden;background:none!important;height:200px;width:300px;}</style></div>

Airbnb has transformed travel and the vacation rental industry in numerous ways. It provides travelers cost-effective accommodation options beyond traditional hotel rooms, and brings hosts additional income to compensate housing expenses in the city. This project analyzes Airbnb data in Boston area, and provides valuable insights into the availability trend and pricing distribution in different neighborhoods. 

# Trend of Airbnb Rental Availability  in Boston

## How does the availability change throughout the year? 

<img src="https://github.com/tanyayt/boston_airbnb_analysis/blob/master/availability_trend_daily.png?raw=true" style="width: 900px;"/>

Availability is defined as the % of listings available. Availability reaches its highest point in early December 2016, and its lowest point in mid-September 2016. The total available listings follow a similar trend. 

<img src="https://github.com/tanyayt/boston_airbnb_analysis/blob/master/available_counts_trend_daily.png?raw=true" style="width: 900px;"/>

Finally, let's see how availability changes throughout the week. Friday and Saturday have the lowest availabilities and the smallest numbers of available units. Monday and Tuesday are most available, in terms of availability and total available units respectively. 

<img src="https://github.com/tanyayt/boston_airbnb_analysis/blob/master/availability_weekly.png?raw=true" style="width: 500px;"/>

<img src="https://github.com/tanyayt/boston_airbnb_analysis/blob/master/available_count_weekly.png?raw=true" width="500px">



# What's the average nightly rental rate in each neighborhood? 

Entire home/apartment rental (blue bars) is most expensive in South Boston Waterfront and least expensive in Mattapan. Private room (orange bars) is most expensive in downtown and least expensive in Hyde Park. Not every neighbourhood has shared rooms for rental but South End still sees the highest rate for this room type. 

<img src="https://github.com/tanyayt/boston_airbnb_analysis/blob/master/average_price_by_neighbourhood_room_type.png?raw=true" width="800px">

# Can room type,numbers of bedrooms and bathrooms, neighborhood and review ratings predict rental rate? 

We performed a multiple linear regression to predict nightly rental rate, using the following predictors: 

*   Room type
*   Numbers of bedrooms
*   Number of bathrooms
*   Number of beds
*   Neighborhood
*   Review ratings

The result is satisfactory with an R squared value of 0.637 in training data and score of 0.596 on test data. The prediction deviates from the actual rate in high pricing range. 

<img src="https://github.com/tanyayt/boston_airbnb_analysis//blob/master/predicted_rate_vs_actual_rate.png?raw=true" width="500px"> 

# Conclusion 

In summary, the results suggest in Boston, Airbnb rentals are most available in early December, and least available in mid-September; in terms of days of week, Airbnb rentals are most available on Mondays and Tuesdays, and least available on Fridays and Saturdays. Rental rates vary by neighborhood. South Boston, downtown and south ends are the most expensive neighbourhood of entire apartment rental, private room rental, and shared room rental respectively. 

Our initial attempt to build a prediction model is satisfactory. Using room type, neighbourhood, bedrooms, bathrooms, beds, and review ratings as predictors, we are able to capture about 60% of the variance in the data. 

*View all the project files and codes in [Github Repository](https://github.com/tanyayt/udacity_data_scientist/tree/master/boston_airbnb_analysis)*