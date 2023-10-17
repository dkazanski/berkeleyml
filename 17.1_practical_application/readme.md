# Phase 1: Exploratory Data Analysis and Data Preparation

The original set contained 20 independent variables and the outcome variable “y”, which we need to predict. 

Luckily, the data set contained no Null values. Many independent variables contained “unknown” values. However, they did not appear to be “unknown” at random. 

I analyzed and treated Categorical and Numerical variables (and their impact on “y”) separately.

## Categorical Variables:

Reviewed distributions and purged some of the values with small counts (like the 3 people with unknown marital status or the 18 “illiterate” people)
- Created split-plot to visualize the impact of the categorical variable on “y”
- Created Crames’V analysis to spot potential multicollinearity and association of each variable with “y”
- Eliminated 3 independent variables with no impact on “y”
- Eliminated 1 independent variable with strong association with another independent variable

## Numerical Variables:

- Reviewed distributions by building Histograms and Box Plots
- Conducted Log transformation for two numerical variables that were highly right-skewed
- Reviewed Correlation Matrix
- Treatet the outliers by imputing them with: 
    - lower_bound = Q1 - 1.5 * IQR
    - upper_bound = Q3 + 1.5 * IQR
- Eliminated multicollinearity by calculating Variance Inflation Factor (VIF) and sequentialy eliminating variables that produced very high VIF.

Since some of the models that I was planning to use are sensitive to outliers and other models are sensitive to Multicollinearity, I created two data sets:
- One without Multicollinearity (to be used for Logistic Regression)
- One without outliers (to be used for KNN, Decision Tree and SVM (note: Decision Tree could handle the outliers via pruning, but imputing them wont’ hurt.  


# Phase 2: Modeling

## Success Metrics

The article and the documentation suggested that the goal of the models was to predict which prospective customers would likely subscribe to a very high-value product: long-term deposit.

Given the high value of the product for the business, I chose Class 1 Recall as my primary metric. This is because each new customer is very valuable to the bank, and a False Negative would be a more costly mistake to avoid. Another reason to choose Class 1 Recall was the high class imbalance (only 12% of records belong to Class 1). Identifying those rare good prospects thus becomes more important. 

The other two (secondary) metrics I would watch would be:
- Overall Accuracy
- ROC AUC

## Baseline (Dummy) Classifier

As a baseline model, I created a dummy classifier. It classified all records as the majority class (0)
As expected, the baselines model had the following performance:
- Class 1 Recall (main metric): 0.00 (all record were classified as Class 0)
- Overall Accuracy: 0.88
- ROC AUC: 0.5

## Basic Logistic Regression

My Basic Logistic Regression used the data set that I treated for Multicollinearity. 
It took 0.16 seconds to fit the model

Performance (on test set) was:
- Class 1 Recall (main metric): 0.39
- Overall Accuracy: 0.90
- ROC AUC: 0.68

## Optimized Logistic Regression

I optimized Logistic Regression by tuning the following hyperparameters:
- 'C': [0.0001, 0.001, 0.01, 0.1, 1, 10, 100],
- 'penalty': ['l1', 'l2'],
- 'solver': ['liblinear', 'saga'],
- 'max_iter': [5000, 10000]


The Grid Search took 14.5 seconds.
- It f 5 folds for each of 56 candidates, totaling 280 fits
- Training time was: 14.5 seconds
- Best Hyperparameters: {'C': 1, 'max_iter': 5000, 'penalty': 'l2', 'solver': 'saga'}

Unfortunately, performance was the same as for the basic model.

## Basic KNN

For my KNN model and the other models below, I used the other dataset, which I treated for outliers but kept the variable with high multic0llinearity.

The Basic KNN took only 0.005 seconds to fit. 

It performed (on test set) as follows:
- Class 1 Recall (main metric): 0.37
- Overall Accuracy: 0.90
- ROC AUC: 0.67

## Optimized KNN

I optimized the following hyperparameters:
- 'n_neighbors': [3, 5, 7, 9, 11, 13], 
- 'weights': ['uniform', 'distance'],
- 'metric': ['euclidean', 'manhattan']

The Grid Search took 223 seconds
- It fit 5 folds for each of 24 candidates, totaling 120 fits
- Best hyperparameters: {'metric': 'euclidean', 'n_neighbors': 3, 'weights': 'distance'}

Performance (on test set) was:
- Class 1 Recall increased to 0.43
- Overall accuracy is 0.89
- ROC AUC went up to 0.69

## Basic Decision Tree 

Took 0.12 seconds to train.

Performance on test set:
- Basic Decision Tree did quite well in terms of Class 1 Recall (0.50)
- The overall Accuracy was 0.89
- ROC AUC was 0.72

## Optimized Decision Tree

I optimized the following Hyperparameters:
- 'criterion': ['gini', 'entropy'],
- 'max_depth': [None, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20],
- 'min_samples_split': [2, 3, 4, 5, 6, 7, 10],
- 'min_samples_leaf': [1, 2, 3, 4, 5]


- The grid search took 123 seconds.
- It fit 5 folds for each of 1190 candidates, totaling 5950 fits
- Best Hyperparameters were: {'criterion': 'gini', 'max_depth': None, 'min_samples_leaf': 1, 'min_samples_split': 2}

It did not produce significant improvements in key metrics. 

## Basic SVM

Basic SVM took 12.6 seconds to complete. This is a lot, but to be expected given the large data set. 

Performance on test set was:
- Class 1 Recall: 0.39
- Overall Accuracy: 0.91
- ROC AUC: 0.68

## Optimized SVM

For Optimized SVM, I tried the following parameters:
- 'C': [0.01, 0.1, 1, 10],
- 'kernel': ['linear', 'rbf', 'poly'],
- 'degree': [2, 3, 4],  
- 'gamma': ['scale', 'auto', 0.1, 1],  

Even with so few parameters (total of 720 fits), the search took over 3 yhours to complete.

# Conclusions

1. Business Understanding is necessary to arrive at the most appropriate Success Metric. In this case, I believe that Class 1 Recall would be the most important metric for the client. This is because the good prospects are both very valuable (they subscribe to highly profitable long-term deposits) and highly rare (only 12% are "good" prospects). 
2. Data Preparation is key to building successful models. Cleaning up the data by removing features that can cause Multicollinearity and also handling the outliners is important for many of the classification models that I used at this time. 
3. SVM was the slowest model because of the very large data set. 
4. The best-performing model for this data set was the Decision Tree. 
    - It produced the best Class 1 Recall metric, the most important metric in our case.  
    - ROC AUC was also the best (0.72) for this model. 
    - The Decision Tree was a relatively fast model, too. The basic model with default parameters took only 0.12 seconds to train. A grid search with as many as 5950 folds took only 2 minutes and 3 seconds to complete. Only Logistic Regression was faster, but it didn't perform as well. 
     - Based on this metric, I recommend the client use the Decision Tree. Decision Trees are also easy to interpret, which is another advantage. 
     - In the future, I'd like to experiment with ensemble tree models, which are based on trees (or tiny trees). These include: 
            - Random Forest
            - Gradient Boosting Machines (GBM), such as XGBoost, LightGBM, and CatBoost.
            - AdaBoost (Adaptive Boosting)
            - Extra Trees (Extremely Randomized Trees)





```python

```


```python

```
