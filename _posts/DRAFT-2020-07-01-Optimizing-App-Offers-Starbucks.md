---
layout: post
title: "Boston Airbnb Rental Market: Availability Trend and Pricing Model"
---

# Optimizing Starbucks App Offers with Machine Learning Algorithm 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/header_starbucks_app.jpg?raw=true" width="900px">



# [DONE] The Project 

## Project Overview 

Customer Loyalty program is one of the most widely used marketing strategies to improve customer engagement. Starbucks sends their promotional offers to customers via email, social media channels or to their mobile Apps. It is important to understand customer behaviors in response to these offers to optimize the offer selections for them. In this project, I took a deep dive into Starbucks App data and built a machine learning model to predict whether a user will complete a promotional offer. 

The project is part of Udacity's Data Scientist Nanodegree program and the datasets are simulated and provided by Starbucks. The raw data are in json format, providing information about promotional offers (e.g. offer type and duration), customers (e.g. demographic and membership), and their transaction history in the app (e.g. when offers are received, viewed and completed). More discussions on the datasets will be provided in Section: Exploratory Data Analysis. 

## Problem Statement 

The goal of this project is to identify target customers for a successful marketing campaign based on the customers' demographic information and the offers' settings. 

There are some challenges that need to be tackled in the datasets. For example, a customer who receives an offer, can complete the offer without even viewing it. From a business perspective, these customers will make purchases without the offer so they are not the best audience for the offer. Another example that I discovered is that, informational offer has no tracking mechanism so none of the informational offer is recorded as completed by customers. We have to assume that, customers who viewed an informational offer, and made a purchase, as being influenced by the offer. This estimation, however, may affect the accuracy of our predictive model. 

## Metrics 

The metrics I used in this project is weighted accuracy of the Random Forest Classification model. 

# Analysis 

This section outlines the key steps included in data exploration, cleaning, pre-processing, model implementation and refinement. Selected visualizations are included. For more information, please visit my [repository on Github](https://github.com/tanyayt/udacity_data_scientist/tree/master/starbucks_optimizing_app_offers)

## Data Exploration and Cleaning

### Offers Data

*   `portfolio.json`, 10 rows X 6 columns  - containing offer ids and meta data about each offer (duration, type, etc.)
*   id (string) - offer id
    
*   offer_type (string) - type of offer ie BOGO, discount, informational
    
*   difficulty (int) - minimum required spend to complete an offer
    
*   reward (int) - reward given for completing an offer
    
*   duration (int) - time for offer to be open, in days
    
*   channels (list of strings)
    

As shown below, the raw data has promotional channels all together and duration recorded in days. 

*Before*: 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/offers_rawdata.PNG?raw=true" width="700px">

After processing, the channel column is now replaced by 3 dummy-coded columns `has_email`, `has_web`, `has_social` and the duration column is replaced by duration hours. 

*After*:

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/offers_clean_data.PNG?raw=true" width="1200px"> 


### Customer Data

-   `profile.json` , 17000 rows X 5 columns, containing demographic data for each customer
    -   age (int) - age of the customer
    
    -   became_member_on (int) - date when customer created an app account
    
    -   gender (str) - gender of the customer (note some entries contain 'O' for other rather than M or F)
    
    -   id (str) - customer id
    
    -   income (float) - customer's income
    

*Before Cleaning*: The dataset contains missing values and odd ages such as 118. `become_member_on` is an int variable but not suitable for modelling. 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/customer_rawdata.PNG?raw=true" width ="500px">

*   We also see that most members signed up for membership in recent years. 

    <img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/membership_years.png?raw=true" width="400px"> 

*   There are more male users (about 57%) in the dataset

    <img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/gender_distribution.png?raw=true" width="400px">

    

*Cleaning Steps*

*   Removing odd ages greater than 115. In fact, these rows are exactly the rows with missing values. So after the removal, the missing value problem is resolved 
*   Re-code `became_member_on` to `membership_days`. Since I do not know what the reference date is when the data was collected, I chose the latest day in the dataset (a.k.a membership_days is 0), and all other customers can have a positive value for `membership_days`. 
*   Renamed column names from id to offer_id 
*   Re-code gender column with dummy 0,1 codes in is_female and is_male variables

*After Cleaning*: 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/clean_customers.PNG?raw=true" title ="clean customer data table view" width="600px">

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/customers_distribution_grid.png?raw=true" title ="age,gender,membership days distribution"> 

The final data seems to have a balanced distribution in terms of age, gender and income. 

 Transaction Data

-   `transcript.json` - 306534 rows X 5 columns, records for transactions, offers received, offers viewed, and offers completed (no missing values)
    -   event (str) - record description (ie transaction, offer received, offer viewed, etc.)
    -   person (str) - customer id
    -   time (int) - time in hours since start of test. The data begins at time t=0
    -   value - (dict of strings) - either an offer id or transaction amount depending on the record

*Before Cleaning* 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/transactions_raw.PNG?raw=true" width="600px"> 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/event_type_distribution.png?raw=true" title="countplot of event types" width="400px"> 

We have most purchase (transaction) events recorded and least number of offer completion events. 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/boxplot_large_amount.png?raw=true" title= "showing outliers in purchase amount">

It is also noticed that we have outliers in purchase amounts. 

*Cleaning Steps*

*   Breakdown the transaction data into three data frames for offer-receiving events, offer-viewing offer-completing events, and simple  purchase events
*   Extract information from `value` column

    *   Replace `value` column with `offer_id`  for offer-receiving,offer-viewing and offer-completing events 

    *   Replace `value` column with purchase amount for purchase events
*   Renamed `person` to customer_id, so it causes less confusion when joing labels later; renamed `time` to receive_time, purchase_time, and complete_time in each case of offer-receiving, offer-completing, and purchase events 
*   Removed outliers in purchase amounts. I used +/- 1.5 IQR range as the cutoff point and removed the outliers in purchase events

*After Cleaning*

*   `df_receive_events`: dataframe containing events when offers are received 

    <img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/df_receive_events.PNG?raw=true" title="df_receive_events" width="500px"> 

    We also notice that one customer can receive as many as 6 offers (shown below). And one customer can receive the same offer multiple times. We therefore have to use receive_time, along with customer_id, offer_id as unique identifiers. 

    <img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/num_offers_per_customer.png?raw=true" width="400px">

*   `df_view_events`: dataframe containing events when offers are viewed 

    <img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/df_view_events_clean.PNG?raw=true" title="df_view_events" width=500px> 

    <img src="" title ="customers can have multiple views"> 

    Since customers can have received multiple offers, it is expected they have multiple view events. For one offer_id, customers could have viewed them for more than once. 

    <img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/multiple_view_each_offer.png?raw=true" title ="customers can view the same offer multiple times" width="400px"> 

*   `df_complete_events` : dataframe containing events when offers are completed 

    Customers can receive and complete one offer multiple times so we will use complete time later to filter out complete events that happened after the expiry time of offers. The complete events dataframe looks like this: 

    <img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/df_complete_events_clean.png?raw=true" title="df_complete_events" width=600> 

    

*   `df_purchase_events` : dataframe containing events when customers make purchases. With the codes below, I have removed the odd purchase amounts outside the 1.5IQR range

    ```python
purchase_events_clean = purchase_events # initialize the clean dataset 
    # calculate IQR scores
    Q1 = purchase_events.amount.quantile(0.25)
    Q3 = purchase_events.amount.quantile(0.75)
    IQR = Q3 - Q1
               
    # remove outliners
    purchase_events_clean = purchase_events[(purchase_events.amount> Q1-1.5*IQR) & (purchase_events.amount < Q3+1.5*IQR)]
    ```
    
    <img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/boxplot_purchase_amount_clean.png?raw=true" title="after removing purchase amt outliers" width=400px> 
    
    The clean purchase events data looks like this 
    
    <img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/clean_purchase_events.PNG?raw=true" title="df_purchase_events"> 
    
    
## Data Pre-Processing

This section outlines how I combined customers' data (), offers data (), offer-receiving event data (), offer viewing data(), offer completion data, and purchase event data () together, and filter out duplicated rows resulted from multiple receiving,viewing, and purchase events. 

### Combine.....  and Filter 



### Combine..... and filter out 



### Combine.... and filter out ...... 



### Combine ....and filter out.... 

### Completion Rate Calculation

## Model Implementation 

### A Simplistic Logistic Regression  Model 

Logistic regression....is..... A logistic regression is performed in clean, consolidated dataset (  ),  to predict whether or not a customer completes an offer ('is_complete') 




## Model Refinement 

# Results and Conclusions 

## Model Evaluation and Validation 





## Justifications 

why some techniques work better than others 



## Reflection 

problem summary, aspects interesting or difficult 



## Future Improvement

how could improve, future work 


















*Header image* from [Starbucks](https://stories.starbucks.com/stories/2020/a-how-to-guide-for-digital-ordering-at-starbucks/) 









