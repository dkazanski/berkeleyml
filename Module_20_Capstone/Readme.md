# Read Me - Capstone - Dmitri Kazanski

For this project, I used 5 million actual records of Ad impressions (shown here as "wins") and the resulting Clicks on Ads.
The original data set contained 77 columns. The Clicks were shown in the Click TimeStamp. Records (ad impressions) without clicks would have no click timestamp.

The goal of the exercise is to predict the probability of a click given the combination of 77 parameters contained in the columns. Using the probability of clicks, the system would then optimize the company's media buying activity to maximize revenue.

I organized the work into four stages:
1. The initial data cleanup
2. Exploratory Data Analysis (EDA) and more cleanup
3. Data transformation
4. Modeling

## 1.Initial Data Cleanup:
- Reviewed data types
- Reviewed unique value counts
- Reviewed the absolute counts and % of NaNs for each column
- Deleted empty fields
- Deleted columns where every (or almost every) value was a unique value
- Converted the Click Timestamp into the "Target" variable: 1 for a click, 0 for no click.

Out of 5,000,000 times the ads were shown, there were only 4,804 clicks, which is a massive class imbalance. So, I under-sampled the non-clicks by 1 to 1,000, thus reducing the number of non-click events to 5,000

## 2. Exploratory Data Analysis (EDA) and More Cleanup:
- Converted "object" data type to "category."
- Several columns that were numerical (integer or float) type were actually category IDs. Converted them to "category" type.
- The country column had only two values: US and Peru, with only a handful of records belonging to Peru. Deleted the column
- Some columns had string values like ['2', None, None, None]. Simplified the category IDs
- Mapped "prem" and "RON" Inventory type to "Premium = 1/0 variable.
- Transformed the impression (win) timestamp into two variables: Day of Week and UTC time. Because all of the events were in US or Peru, I converted the time to UC Central Time. Defined two variables based on date and time:
   - Weekday = 1/0
   - TOD: Morning, Afternoon, Evening, Night
   Based on my experience, ad performance differed during this period. However, during the subsequent analysis, these variables were not predictive of the outcome.
- Build Cramer's V HeatMap to analyze the association between each Categorical variable and Target as well as between the independent variables. Based on this analysis:
    - Deleted variables that have no impact on Target
    - Deleted some of the variables that were essentially duplicative of one another and thus would not add any new information while also having strong Association (to prevent Multicollinearity)
- Build a Correlation Matrix and Heatmap to analyse the Correlation between each Numerical variable and Target. Based on the analysis:
    - Deleted variables that have no impact on Target
    - Deleted some of the variables that were essentially duplicative of another and thus would not add any new information while also having a strong Correlation (to prevent Multicollinearity)
- Reviewed the distribution of variables and determined that they needed a log transformation
- Conducted bi-variate analysis to confirm that the remaining variable had a significant impact on the target (were predictive of target)

## 3. Transformaiton:
- Treated the NaNs:
    - Imputed with Mode for Categorical variables
    - Imputed with Median for Numerical variables
- Conducted Log transformation for the numerical variables
- Simplified the column names
- Using the Variance Inflation Factor (VIF) calculations, got rid of two columns, which were similar to two other columns and thus had super high VIF.
- One-hot encoded the categorical variables. The resulting Dataframe now had a total of 246 columns.

## 4. Modeling:
- Created the baseline (dumb classifier) model
- Created the following baseline models:
   - Decision Tree
   - Random Forest
   - SVM
   - Logistic Regression
   - KNN
- Using GridSearchCV, conducted hyperparameter optimization for each model. Given the fact that the goal is to predict click-through rate (probability of click), I used LogLoss as the main optimization metric, however, I also tracked the following:
   - Accuracy
   - Recall
   - Precision
   - F1
   - ROC AUC

### Resuling Performance

**Model: Baseline**
- Accuracy: 0.51
- Precision: 0.00
- Recall: 0.00
- F1 Score: 0.00
- LogLoss: 16.93
- ROC AUC: 0.50

**Model: Decision Tree Basic**
- Accuracy: 0.97
- Precision: 0.97
- Recall: 0.97
- F1 Score: 0.97
- LogLoss: 0.90
- ROC AUC: 0.97

**Model: Decision Tree Optimized**
- Accuracy: 0.96
- Precision: 0.97
- Recall: 0.96
- F1 Score: 0.96
- LogLoss: 0.15
- ROC AUC: 0.99

**Model: Random Forest Basic**
- Accuracy: 0.97
- Precision: 0.98
- Recall: 0.97
- F1 Score: 0.97
- LogLoss: 0.20
- ROC AUC: 0.99

**Model: Random Forest Optimized**
- Accuracy: 0.98
- Precision: 0.98
- Recall: 0.97
- F1 Score: 0.97
- LogLoss: 0.09
- ROC AUC: 0.99

**Model: SVM Basic**
- Accuracy: 0.97
- Precision: 0.99
- Recall: 0.94
- F1 Score: 0.97
- LogLoss: 0.11
- ROC AUC: 0.99

**Model: SVM Optimized**
- Accuracy: 0.97
- Precision: 0.99
- Recall: 0.95
- F1 Score: 0.97
- LogLoss: 0.10
- ROC AUC: 0.99

**Model: Logistic Regression Basic**
- Accuracy: 0.97
- Precision: 0.99
- Recall: 0.94
- F1 Score: 0.97
- LogLoss: 0.09
- ROC AUC: 0.99

**Model: Logistic Regression Optimized**
- Accuracy: 0.97
- Precision: 0.99
- Recall: 0.95
- F1 Score: 0.97
- LogLoss: 0.09
- ROC AUC: 0.99

**Model: KNN Basic**
- Accuracy: 0.94
- Precision: 0.95
- Recall: 0.93
- F1 Score: 0.94
- LogLoss: 0.57
- ROC AUC: 0.98

**Model: KNN Optimized**
- Accuracy: 0.94
- Precision: 0.97
- Recall: 0.91
- F1 Score: 0.94
- LogLoss: 0.27
- ROC AUC: 0.99

# Conclusion

This real-world data contained a lot of garbage, such as empty fields, duplicative fields, records with wrong types, 
et. 
Some fields had little impact on the target variable (clicks). Many others had very high Correlations and Associations with other independent variables. Thus, they had to be treated.

After a lot of clean-up and transformation efforts, I was able to produce quite a few decent models. The best, in terms of the key metric (LogLoss), were Logistic Regression and Random Forest.

Predictions will happen in real time when the system decides what ads to show to users. Thus, simpler models, such as Logistic Regression, could be advantageous. Simpler models will allow for quick calculation of the click probability with minimal latency.



```python

```
