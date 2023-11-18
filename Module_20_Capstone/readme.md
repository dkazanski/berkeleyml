# Read Me (to accompany the 4 notebooks)

This is based on the 5 million actual records of Ad impressions (shown here as "wins") and the resulting Clicks on Ads. The original data set contained 77 columns. The Clicks were shown in the Click TimeStamp. Records (ad impressions) without clicks would have no click timestamp.

The goal of the exercise is to predict the probability of a click given the combination of 77 parameters contained in the columns. 

Prediction Conversions was outside the scope (but would be more important for Kaden). Perhaps we could use some of the elements from these notebooks as a source of ideas for real model improvement (including the conversion models).

I organized the work into four stages:
1. The initial data cleanup
2. Exploratory Data Analysis (EDA) and more cleanup
3. Data transformation
4. Modeling

*Each of the first 3 books output a .csv file that is loaded and used by the next book. Please change the file lolcation path (it points to my hard drive)*

## 1.Initial Data Cleanup:
- Reviewed data types
- Reviewed unique value counts
- Reviewed the absolute counts and % of NaNs for each column
- Deleted empty fields
- Deleted columns where every (or almost every) value was a unique value
- Converted the Click Timestamp into the "Target" variable: 1 for a click, 0 for no click.

Out of 5,000,000 times the ads were shown, there were only 4,804 clicks, which is a massive class imbalance. So, I under-sampled the non-clicks by 1 to 1,000, thus reducing the number of non-click events to 5,000.

## 2. Exploratory Data Analysis (EDA) and More Cleanup:
- Converted "object" data type to "category."
- Several columns that were numerical (integer or float) type were actually category IDs. Converted them to "category" type.
- The country column had only two values: US and Peru, with only a handful of records belonging to Peru. Deleted the column
- Some columns had string values like ['2', None, None, None]. Simplified the category IDs
- Mapped "prem" and "RON" Inventory type to "Premium = 1/0 variable.
- Transformed the impression (win) timestamp into two variables: Day of Week and UTC time. Because all of the events were in the US or Peru, I converted the time to UC Central Time. Defined two variables based on date and time:
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
- Scaled the numerical columns (except for binary) with Standard Scaler.
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

# Things I am planning to do next:

1. **Check for duplicate records**


2. **Try different ecnoders**, such as listed here:  https://contrib.scikit-learn.org/category_encoders/, especially encoder = ce.JamesSteinEncoder(cols=[...]) and encoder = ce.TargetEncoder(cols=[...]) (should be possible to stich them into a pipeline)


3. **Try different thresholds** for the classifiers that thave tem.


4. **Treat the outliers.** Nearly all numerical features (except for the Boolean features, which I converted into Integer type with the values of 1 or 0) have outliers. Some models attempted here, especially Logistic Regression, KNN, and SVM are sensitive to outliers. So, I am surprised that they even validate reasonably well. Perhaps I should treat them in the next iteration of the model.


5. **Polynomial features & interaction features.** The most transformation I did was Log transformation for the numerical features. By the way, even after that, the distribution was far from normal, and I was not sure about doing a log(log(X)) type of transformation. However, I never attempted Polynomial features or Interaction features. I feel that the interaction features can be super useful, especially when it comes to interaction between specific Advertisers/Offers/Campaigns/Creatives and all other features.


6. **Use more features.** At some point, I eliminated two potentially useful features with a very high Association with the Target (clicks). This was because my dataset already had 250 one-hot encoded features, and I didn’t want to add even more categorical features, which would add another 250-500 one-hot encoded features. Here are the candidates that I will consider for the next iteration of the models:
    - One of the features that describe the specific Mobile App (bundle) that shows the ad
        - request_bundle_id (Unique Count: 2912, Association with Target: 0.70)
        - request_publisher_id (Unique Count: 1375, Association with Target: 0.68)


7. **Sequential Feature Selection / Elemination & Similar Methods.** I spend a lot of time manually weeding out some of the features. That included weeding out the following:
    - empty features
    - features with Correlation/Association with the target
    - features with high association/correlation to other independent (X) variables)
    - Features with too many unique values (like user agent, which is unique to almost every win)
    
    However, I did not employ Sequential Feature Selection, Recursive Feature Elimination, Forward Selection, Backward Elimination, PCA, LASSO, or similar methods. SFS is avalaible in ScikitLearn
    https://rasbt.github.io/mlxtend/api_subpackages/mlxtend.feature_selection/#exhaustivefeatureselector


8. **Try other models.** I would like to one day try:
    - Naive Bayes (could be slow for large data sets)
    - Gradient Boosting Tree-based models, such as CatBoost, XGBoost, Light GBM, AdaBoost
    - Neural Nets (for capstone would be too much), but for work. For Tabular Datasets there is a low of 
    The size of the classical (non)
    XGBoost is very fast (it's a separate impementation). Adaboost ScikitLearn implementation is a bit pulkey.
    XGBoots, Catboost, etc. are separate pages. 


9. **Try an ensemble model that combines predictions from several models.** 
    - Combine several classifier outputs and use them as an input to another classifier
    - The outputs coudl be just the predicted class or the "predict_proba" prediction


```python

```


```python

```
