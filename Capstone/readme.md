# Predicting Clicks on Ads (a non-technical Readme)

**Dmitri Kazanski**

**Please see the Technical Writeup Notebook (Book 0) for additional technical details and the summary of model performance results.**

## Project Overview and Goals

The goal of the exercise is to predict the probability of a click on ads. The model will be used in real-time when deciding how a Deman-Side Platform (DSP) should respond to ad requests. When receiving an ad request, a DSP should respond with a higher or lower bid, depending on the click probability. Thus, predicting click probability is the key to the platform's success.

The data set contained 5 million actual records of Ad impressions (shown here as "wins") and the resulting Clicks on Ads. Each record was composed of 77 columns, which represent the parameters of the bid request that the platform can use to decide how to respond to the bid request. The Clicks were shown in the Click TimeStamp. Records (ad impressions) without clicks would have no click timestamp.

## Data Preparation and Exploratory Analysis

A bulk of the effort in this project was to clean up the data and prepare it for modeling. Since it was real-world data, it was messy. Many of the columns contained empty values. Some of the columns contained mostly empty values. Other columns were such that almost every value was unique (such as a user ID, for example). 

The exploratory data analysis showed that some columns had a very strong Correlation / Association with the target variables (clicks), whereas others did not. Some columns contained information duplicated from the data in the other column. They would have too high of a correlation/association with the other columns. So, they would not add any value and, instead, would cause problems. I had to eliminate such columns one after the other.


## Data Transformation

As part of the data transformation, in order to prepare it for modeling, I treated the null values by imputing (replacing) them with the Mode (most common values) for categorical variables and Median values for numerical variables.

The Exploratory Data Analysis showed that many inputs had exponential (highly skewed to the right) distribution. Because some of the models I was going to use required the input data to have a Normal (bell-shaped) distribution, I applied Quantile Transformation to the numerical variables. It produced better results than the Logarithmic (Log) transformation.

I also scaled the numerical columns (except the binary columns using Standard Scaler to achieve Standard Normal distributions. Finally, I converted the categorical features into dummy (1 or 0) variables. This resulted in a total of 247 columns in my data set. 

## Modeling

After cleaning and transforming the data, I split the data set into a Training set and a Validation set. The model would be trained on the Training set, but it it would be evaluated on how well it predicted the Validation data set that it has never seen.

I created  7 types of Classifier models:
- Decision Tree
- Random Forest
- Logistic Regression
- K-Nearest Neighbours (KNN)
- Support Vector Machine (SVM)
- XGBoost
- CatBoost
I then completed hyperparameter optimization for each of the above models.

I tracked several model performance parameters, such as Accuracy, Precision, Recall, F1Score, and ROC AUC. However, I chose to optimize towards minimizing LogLoss. This metric assesses the probability estimates provided by the model against the actual class labels, with lower values of LogLoss indicating better model performance. This metric is particularly useful because it heavily penalizes models for being confident and wrong, thus encouraging well-calibrated probability estimates.

## Findings, Results and Conclusions

The factors that decided where the ad impression would result in a click were:
- The Make of the user's device: Samsung, Apple, etc.
- Type of an ad: banner, video, etc.
- Ad Campaign
- Category of the mobile app that showed the ad
- Subcategory of the mobile app that showed the ad
- The fact that the user clicked on any other ads in the past
- The types of mobile apps the user uses most often (gaming, news, etc.)
- Whether the app that shows the ad belongs to the list of premium apps
- The average click-through rate on the previously shown ad within the app in the past 30 days
- The number of times the user saw the ad for this advertiser’s offer in the past 90 days
- How many times the user clicked on ads of this advertiser’s offer in the past 90 days

XGBoost, a gradient-boosting ensemble model, produced the best (lowest) LogLoss of 0.066096. It had an overall accuracy of 0.9782, which means that it correctly predicted whether an ad impression would result in a click or not 97.8% of the time. For an advertising model, that is an astonishingly good result.

However, because the system will have to reply to bid requests in real time, the latency of the inference (prediction) could have a high business cost. Therefore, models that deliver quick performance when deployed would be preferred. In this regard, Logistic Regression would be the first choice. It lagged behind my best XGBoost model only slightly, with a LogLoss of 0.078890 and an overall Accuracy of 0.972128, which is still an excellent result.


**Please see the Technical Writeup Notebook (Book 0) for additional technical details and the summary of model performance results.**


```python

```
