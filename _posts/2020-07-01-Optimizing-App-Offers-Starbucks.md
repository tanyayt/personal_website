---
layout: post
title: "Optimizing Starbucks App Offers with Machine Learning Algorithm "
tags: python machine-learning data-science sales-analytics
---

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/header_starbucks_app.jpg?raw=true" width="500px">

# The Project 

## Project Overview 

Customer Loyalty program is one of the most widely used marketing strategies to improve customer engagement. Starbucks sends their promotional offers to customers via email, social media channels or to their mobile Apps. It is important to understand customer behaviors in response to these offers to optimize the offer selections for them. In this project, I took a deep dive into Starbucks App data and built a machine learning model to predict whether a user will complete a promotional offer. 

The raw data are in json format, providing information about promotional offers (e.g. offer type and duration), customers (e.g. demographic and membership), and their transaction history in the app (e.g. when offers are received, viewed and completed). 

## Problem Statement 

The goal of this project is to identify target customers for a successful marketing campaign based on the customers' demographic information and the offers' settings. 

There are some challenges that need to be tackled in the datasets. For example, a customer who receives an offer, can complete the offer without even viewing it. From a business perspective, these customers will make purchases without the offer so they are not the best audience for the offer. Another example that I discovered is that, informational offer has no tracking mechanism so none of the informational offer is recorded as completed by customers. We have to assume that, customers who viewed an informational offer, and made a purchase, as being influenced by the offer. This estimation, however, may affect the accuracy of our predictive model. 

## Metrics 

The metrics I used in this project is weighted accuracy of the Random Forest Classification model. 

# Analysis 

This section outlines the key steps included in data exploration, cleaning, pre-processing, model implementation and refinement. Selected visualizations are included. For more information, please visit my [repository on Github](https://github.com/tanyayt/starbucks_optimizing_app_offers)

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

​    

*Cleaning Steps*

*   Removing odd ages greater than 115. In fact, these rows are exactly the rows with missing values. So after the removal, the missing value problem is resolved 
*   Re-code `became_member_on` to `membership_days`. Since I do not know what the reference date is when the data was collected, I chose the latest day in the dataset (a.k.a membership_days is 0), and all other customers can have a positive value for `membership_days`. 
*   Renamed column names from id to offer_id 
*   Re-code gender column with dummy 0,1 codes in is_female and is_male variables

*After Cleaning*: 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/clean_customers.PNG?raw=true" title ="clean customer data table view" width="600px">

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/customers_distribution_grid.png?raw=true" title ="age,gender,membership days distribution"> 

The final data seems to have a balanced distribution in terms of age, gender and income. 

### Transaction Data

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

`df_view_events`: dataframe containing events when offers are viewed 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/df_view_events_clean.PNG?raw=true" width="500px"> 



Since customers can have received multiple offers, it is expected they have multiple view events. For one offer_id, customers could have viewed them for more than once. 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/multiple_view_each_offer.png?raw=true" width="400px"> 

`df_complete_events` : dataframe containing events when offers are completed 

Customers can receive and complete one offer multiple times so we will use complete time later to filter out complete events that happened after the expiry time of offers. The complete events dataframe looks like this: 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/df_complete_events_clean.png?raw=true" title="df_complete_events" width="600px"> 

`df_purchase_events` : dataframe containing events when customers make purchases. With the codes below, I have removed the odd purchase amounts outside the 1.5IQR range

```python
purchase_events_clean = purchase_events # initialize the clean dataset 
    # calculate IQR scores
    Q1 = purchase_events.amount.quantile(0.25)
    Q3 = purchase_events.amount.quantile(0.75)
    IQR = Q3 - Q1
               
    # remove outliners
    purchase_events_clean = purchase_events[(purchase_events.amount> Q1-1.5*IQR) & (purchase_events.amount < Q3+1.5*IQR)]
```

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/boxplot_purchase_amount_clean.png?raw=true" title="after removing purchase amt outliers" width="400px"> 
    

The clean purchase events data looks like this 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/clean_purchase_events.PNG?raw=true" title="df_purchase_events"> 
        

## Data Pre-Processing

This section outlines how I combined customers' data (df_customers), offers data (df_offers), offer-receiving event data (df_receive_events), offer viewing data(df_view_events), offer completion data (df_complete_events), and purchase event data (df_purchase_events) together, and filter out duplicated rows resulted from multiple receiving,viewing, and purchase events. 

### Left join receive events data with customer data

```python
df_receive_customers = pd.merge(df_receive_events,df_customers,how='left',on='customer_id')
```

I first left joined the receive events data with customer data and built a heatmap (below) to check whether there are any correlations between the number of offers customers received, and their demographic characteristics. The correlations are weak. 

```python
#calculate number of offers each customer received
offer_counts_per_customer = df_receive_customers.groupby(['customer_id','age','is_female','membership_days'])['offer_id'].count().reset_index().rename(columns={'offer_id':'offer_count'})

#create heatmap
g= sns.heatmap(offer_counts_per_customer.corr(),annot=True,cmap='coolwarm',vmin=-1)
g.set(title="Heatmap showing weak correlations between number of offers received and customer demogrpahics")
```

![heatmap](https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/heatmap_offer_receive_counts2.png?raw=true)

### Left join with  view events and offers data

I continue the left join the dataframe from the previous step, with events and offers data. I also computed the expiry time of the offer. We'll then remove the rows where view time is before the receive time or after the expiry time. These rows are produced from the join results because one customer can receive the same offer_id multiple times. 

```python
df_receive_view_customers = pd.merge(df_receive_customers,df_view_events,how='left'
                                       ,on=['customer_id','offer_id'])

df_receive_view_customers_offers = pd.merge(df_receive_view_customers,df_offers
                                           ,on='offer_id',how='left')

#compute expiry time
df_receive_view_customers_offers['expiry_time']=df_receive_view_customers_offers['receive_time']
                                              +df_receive_view_customers_offers['duration_hours']

#filter out views that are outside the receive and expiry time
df_receive_view_customers_offers_clean = df_receive_view_customers_offers.drop(df_receive_view_customers_offers[(df_receive_view_customers_offers.view_time <= df_receive_view_customers_offers.receive_time) | (df_receive_view_customers_offers.view_time >= df_receive_view_customers_offers.expiry_time)]
                                         .index,axis=0)
```

### Left Join with Completion Data and Filter  

Following the same logic, I left join the resulting dataframe from above, with completion event data, and then remove the rows where completion time is after the expiry time or before the receive time. 

```python
df_combined = pd.merge(df_receive_view_customers_offers_clean,df_complete_events, how ='left',on=['customer_id','offer_id'])

#filter
df_combined = df_combined.drop(df_combined[(df_combined.complete_time <= df_combined.receive_time) | (df_combined.complete_time >= df_combined.expiry_time)]
                                .index,axis=0)
```

### Consolidate Multiple View Events and Completion Events

To create a unique identifier using: customer_id, offer_id, and receive_time, I then aggregate the rows, by consolidating the rows associated with multiple view or multiple completion events of the same offer, received at the same time. 

The process is rather complex so please visit the [Python Notebook](https://github.com/tanyayt/starbucks_optimizing_app_offers/blob/master/Starbucks_Capstone_notebook.ipynb) on Github to see how NaNs are handled in GroupBy calculations.The end result is that we have 1 and only 1 row in the dataframe df_combined, with each combination of customer_id, offer_id, and receive_time

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/df_combined_unique.PNG?raw=true" title="unique df_combined" width="600px"> 

### Left Join with Purchase Events and Filter 

As mentioned before, an informational offer does not have a valid "offer completion" event so we have to assume that  if a customer makes a purchase between receiving the offer and the offer's expiry time, we consider that the customer has completed the offer. Again, we use the same logic to remove rows that the purchase events happened before receive time or after expiry time. Lastly, since customer can still make multiple purchases in between these times, we use groupby again to consolidate all purchases, and keep 1 single row for each unique combination of customer_id, offer_id, and receive_time. 

Lastly, I assigned completion status to 1, if an informational offer was received, and followed by purchase events before its expiry time.  

```python
df_combined_purchase = pd.merge(df_combined,df_purchase_events,how='left',on='customer_id')
# only keep the purchase when the purchase is between receive_time and expiry_time, and when offer is informational
df_combined_purchase = df_combined_purchase[(df_combined_purchase.receive_time <= df_combined_purchase.purchase_time) \
                                            & (df_combined_purchase.purchase_time >= df_combined_purchase.expiry_time)]

#consolidate multiple purchases
purchase_counts_amount = df_combined_purchase.groupby(['customer_id','offer_id','receive_time'])['purchase_amount']\
                                             .agg(['count','sum']).reset_index()\
                                             .rename(columns={'count':'purchase_count','sum':'purchase_amount'})
#join back to the main dataset 
df_combined  = pd.merge(df_combined,purchase_counts_amount,how='left',on=['customer_id','offer_id','receive_time'])

#assign completion status to informational offers
df_combined['is_complete'] = [1 if ((a==1) & (b>0)) else c for (a,b,c) in \                  zip(df_combined.is_informational,df_combined.purchase_count,df_combined.is_complete)]
```

### Calculate Completion Rate 

Because of our assumption on informational offers, the completion rate could be inflated. We want to get an idea to make sure there are balanced number of rows with complete and incomplete status. 

To do so, I create a function to calculate completion rate: 

```python
def completion_rate(df_combined,column):
    '''
    Args: df_combined - the consolidated dataframe as above 
          column -string column name
    Returns: None
    Calculate and prints completion rate when the selected column's value is 1 
    '''
    df_subset = df_combined[df_combined[column]==1]
    total_completion =  df_subset['is_complete'].sum()
    total_cases = df_subset['is_complete'].notnull().sum()
    completion_rate = total_completion/total_cases
    print("When column {} value is 1, the offer completion rate is {}".format(column,completion_rate))
# check completion rates 
completion_rate(df_combined,'is_bogo')
completion_rate(df_combined,'is_informational')
completion_rate(df_combined,'is_discount')
```

The results indicate that BOGO, informational, and discount offers have a completion rate of 51%, 81%, and 60% respectively. 

We then checked the completion rates for: 

*   Female users: 74%

*   Male users: 59%

*   Offers sent by email: 60%

*   Offers sent by social media channel: 62% 

*   Offers sent by web: 59% 

*   Offers sent by mobile: 62% 

Gender seems to play a role on offer completion rate however, how offers are sent out seem to have no noticeable impact on the completion rate. 



## Model Implementation and Refinement

### A Simplistic Logistic Regression  Model 

Logistic regression is a regression model that uses a logistic function to model a binary outcome. Here I attempted to build a simplistic logistic regression model  to predict whether or not a customer completes an offer ('is_complete') using user-related variables: income, age, gender, and membership days 

Before running regression model, we removed unintended completions, those are completions without view events. We also removed rows with missing values so the regression model can be built. 

```python
# remove unintended completion 
df_combined_excl_unintended_completion = df_combined.drop(df_combined[(df_combined.is_viewed==0) & (df_combined.is_complete==1)].index,axis=0 )

#remove missing values 
df = df_combined_excl_unintended_completion.dropna(how='any',subset=['income','age','is_female','is_complete','membership_days'])

#split data to train and test 
X = df[['income','age','is_female','membership_days']]
y = df['is_complete']
X_train,X_test,y_train,y_test = train_test_split(X,y,test_size=0.2,random_state=41)

#build logistic regression model
logreg = LogisticRegression()
logreg.fit(X_train,y_train)

#predict and evaluate 
y_pred = logreg.predict(X_test)
print(classification_report(y_test, y_pred,zero_division=0))
```

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/2020-07/log_reg_accuracy.PNG?raw=true)" title="accuracy log reg" width="400px"> 

The accuracy of this model is only 0.61, considering the completion rate of the data used for the model is 59.0%. The prediction accuracy is just slightly better than a random guess. I intentionally reduced the features included in the logistic regression model, to see whether a prediction can be made with the minimum set of variables with decent accuracy. The answer is no, but as expected 

### Model Refinement : Random Forest Model with Hyperparameter Tuning

Finally, I built a Random Forest Model, with both customer and offer characteristics, to predict whether customers have completed and offer. In addition, a grid search of parameters are performed, to optimize the model. 

```python
# include more offer-related features 
df= df_combined_excl_unintended_completion[['income','membership_days','age','is_female','is_bogo','is_discount',\
                           'reward','difficulty','has_email','has_web'\
                           ,'has_mobile','has_social','is_complete']].dropna(how='any')
X = df.drop('is_complete',axis=1)
y = df['is_complete']

#split data to train and test 
X_train,X_test,y_train,y_test = train_test_split(X,y,test_size=0.2,random_state=2)

#set up parameters for tunning 
param_grid = {
    'criterion' : ['gini', 'entropy'],
    'max_depth' : [2,5,10],
    'min_samples_split' : [10,50,100],
    'min_samples_leaf' : [2,5,10],
     'bootstrap': [False,True],
    'n_estimators': [50, 100, 200]
}

model = GridSearchCV(estimator=RandomForestClassifier(random_state=42), 
                              param_grid=param_grid, cv=2, scoring='f1')

# Train model
model.fit(X_train, y_train)
```

*Note: I removed is_informational from the feature since when is_bogo=0, is_discount=0, it already suggests that the offer is an informational offer. We do not need an extra column to identify informational offer. *

# Results and Conclusions 

## Model Evaluation and Validation 

The best set of parameters are with Bootstrap and 200 estimators. 

```python
{'bootstrap': True,
 'criterion': 'entropy',
 'max_depth': 10,
 'min_samples_leaf': 10,
 'min_samples_split': 50,
 'n_estimators': 200}
```

The classification report of the optimized model is as below, producing a 0.73 weighted accuracy. 

```python
            precision    recall  f1-score   support
           0       0.70      0.62      0.66      3498
           1       0.76      0.81      0.78      5069
    accuracy                           0.73      8567
   macro avg       0.73      0.72      0.72      8567
weighted avg       0.73      0.73      0.73      8567
```

And we also identified the feature importance, ordered from most important to least important. The result suggests that, membership days is the most important variable, followed by income, the reward offers provide. Distribution channels seems to have little to none importance to the outcome (whether customers complete offers)

```python
Variable: membership_days      Importance: 0.38
Variable: income               Importance: 0.19
Variable: reward               Importance: 0.09
Variable: age                  Importance: 0.08
Variable: has_social           Importance: 0.07
Variable: difficulty           Importance: 0.06
Variable: is_female            Importance: 0.05
Variable: is_bogo              Importance: 0.03
Variable: has_mobile           Importance: 0.03
Variable: is_discount          Importance: 0.02
Variable: has_web              Importance: 0.01
Variable: has_email            Importance: 0.0
```

## Justifications 

Random Forest model with bootstrapping, and other parameter tuning works better than the simplistic logistic regression model. Random Forest is a classification method that creates as many decision trees on a randomly selected subset of features, and integrate the results of all trees produced by these subsets. It reduces overfitting problem that could exist in decision trees and also reduces the variances, and improves accuracy. It is robust and can be used for both classification and regression problems. In addition, I added a hyperparameter tuning grid. It works by searching exhaustively through a specified subset of hyperparameters, and find the optimal combination of parameters. And hence, the final model has shown some improvement in accuracy, compared to the simplistic model. 

## Conclusion and Reflection 

Till now, I have successfully built a predictive model predicting whether a customer may respond to an promotional offer sent by Starbucks. The two most important predictors are how long they have been a Starbucks member, and their income. An interesting matter of fact to me, is that the offer type has little impact on whether customers respond to offers. 

## Future Improvement

There are some known reasons that may have negatively affected the model's performance, that we can improve on with additional data: 

*   Since there is 0 offer complete event for informational offers in the dataset, we faced a challenge of having no data predicting the outcome for informational offer An assumption has to be made, that when customers view and make purchase during the active period of an informational offer, we consider them complete the offer. However, this assumption can be better refined, if we know what product the informational offer is about. For example, if the informational offer is about latte, and the customer purchases a cup of Americano, we should not consider the customer as influenced by, or completed the offer. Unfortunately, targeted product of each offer, is not provided in the dataset. 
*   Another deficiency in the data is the granularity of time recording. Many events can happen within an hour so a real timestamp may provide more accurate filtration of, for instance, offer views after completion or expiry times. 
*   *Edit 2021-06-10*: in outlier treatment, a better approach is to consider windsorizing extreme values to the upper or lower bound of the normal range for the benefit of 1) retaining the statistical power of the same sample size 2) reducing the change of the covariance compared to simply just remoing all outliers. 

The dataset however, does offer some information that I haven't used yet in the model. For instance, the total purchase amounts of each customer, before receiving an offer. This will serve as a good metric for customer engagement. More work can be done to reshape the data and incorporate this variable. 

**END**

- 

-- 


*Header image* from [Starbucks](https://stories.starbucks.com/stories/2020/a-how-to-guide-for-digital-ordering-at-starbucks/) 









