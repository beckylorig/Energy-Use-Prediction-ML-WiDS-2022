# WiDS Energy Use Evaluation

### Women in Data Science (WiDS) Datathon 2022 - Climate Change Evaluation

Women in Data Science (WiDS) holds an annual Datathon.  The goal of the competition this year (2022) was to analyze building energy efficiency and create models to predict building energy consumption, known as 'energy use intensity' (EUI). Energy use by buildings is a contibutor to climate change, and this datathon aims to understand building energy expenditures as a means to combat climate change.  

The datasets contain building features, energy star ratings, and provides additional information such as: building age and the weather features where the building is located (anonymized). The data follow the Kaggle competition design. They include a training dataset and a test dataset. The training data has the target field but the test data does not. The goal is to predict the target variable for the test dataset and submit it for scoring in Kaggle. This dataset has a continuous target variable rather than binary variable. The scoring by Kaggle is RMSE, which is used throughout the analysis.

#### The analysis followed the following main steps:
1. Exploratory analysis of the data
2. Preprocessing the data
3. ETL data loading to PostgreSQL
4. Feature reduction and selection using several techniques
   * Recursive Feature Elimination (RFE) using a combination of Lasso, Random Forest and Gradient Boost (and voting on best features).
5. Machine Learning techniques to predict the continuous target variable in the data   
   * Unsupervised methods
      * t-SNE 
      * K Means Clustering
   * Supervised methods (using pipelines and hyperparameter tuning)
      * K Neighbors Regressor
      * Random Forest Regressor
      * XGBoost Regressor
      * CatBoost Regressor

#### The project is split into 3 jupyter notebooks:
1. Preprocessing
2. ETL
3. Model Building

## Processing
Preprocessing was vital to this analysis.  There were missing data, invalid data, and several categorical variables.  The target is the energy consumption of the building, which is a continuous variable.  This made it far more difficult than a binary classification target.  One challenge in the predictive power of the algorithm was the long tail on the target variable.  There were high values that were a magnitude higher than the median and mean of the data.  The bulk of the records for the target were around the mean value with little differentiation among the records at this target level.
  
#### New Variable Creation
There were also three temperature values for each month of the year: minimum temperatue, average temperature, and maximum temperature.  I converted these min, avg, and max values into a new feature.  I subtracted the value from the median of each, which standardized each min,avg, and max and then I added those values (differences from the median) together to create one temperature feature per month.  This converted 36 features into 12 features.

#### Feature Reduction

Using a combination of RandomForestRegressor and GradientBoostRegressor with Recursive Feature Elimination, and LassoCV, I was able to score the features and then vote on the best features (selecting either 2 or 3, meaning the feature had to have either two or three of the models select it) in order for it to be considered in the final dataset.  I analyzed these using numerical data only, excluding any categorical features (even if one-hot-encoded) using a StandardScaler, then evaluated the entire dataset (with one-hot-encoded features) using two different scalers, StandardScaler and then Normalize, from the sklearn libraries. 

##### One-Hot-Encoding

Once the data were one-hot-encoded there were 90 variables; one categorical feature had 60 types.  This was addressed with feature reduction techniques.


## Results

The CatBoost algorithm was most performant, with RMSE score of 28.95. KNN Regressor was the least performant, with RMSE scores between 45 - 46, depending on tuning and cross-validation settings. Random Forest was more performant than KNN with scores around 45. XGBoost with some tuning scored a RMSE around 39. The CatBoost performed better than XGBoost.

Based on the initial analysis using unsupervised learning, both the t-SNE and K Means showed that the clusters were not cleanly separating, even though there was eventual separation in the clusters, the clusters were not as tightly packed. This suggests that the data, even when reduced to two factors does not explain the majority of the variation in the data. The pre-processed dataset that was used for unsupervised learning already had feature reduction techniques applied, but due to the distribution of clusters from both analyses, I further processed the data and dropped additional fields. I believe the variance is due to the one-hot-encoded categorical variable for 'facility-type'. There were 60 facility types listed, making for a large number of features added once it was one-hot-encoded. Furthermore, there were additional categorical fields that were also one-hot-encoded, but their features were not as numerous.

Therefore, the initial pre-processed dataset (called train) was analyzed using the various techniques mentioned, but a second dataset was also used that had more features dropped (called energy_df), as a comparison and also as a means to reduce error (lower RMSE) in the model and make better predictions.

Over and under-fitting of the models was analyzed in each model run, and overall, the KNN and Random Forest had less variation between train and test than the XGBoost. Although XGBoost was more performant, the model overfit the data by a substantial amount. As a result, I decided that CatBoost might be helpful. Since the large number of encoded categorical variables nearly doubled the variable count, I thought that CatBoost could handle the categorical variables better (they don't need to be encoded to go into the model). Indeed, CatBoost was more performant, I achieved an RMSE of 28.95 but it came at a cost of over-fitting (train was more performant than test).

In conclusion, the original dataset from WiDS, by itself, does not have enough explanatory power and results in models with higher error or overfitted models. Even with pre-processing, the data still create a model with what I consider mediocre predictive power. However, on the leaderboard for the competition the best performing teams, which are professional data scientists from international universities scored an RMSE around 18. I have reviewed the documentation from previous WiDS competitions and it seems that the competitors usually create many new variables from a combined set of variables from the data. I'm unclear of what the best approach is when combining variables, but I did attempt to do so when I created temperatures from the median for the monthly reported min, max and averages, then summed these values. This was one of several feature reduction techniques, which also helped in the reduction of colinearity, and creating new information. I've seen models where they will combine categorical data with numeric, but I'm not clear on the technique they use to make the best judgements about these combinations. However, I see that they test many of the new variables and drop any that do not provide additional gains for the model. These will be next steps for this analysis and for other datathons/competitions for me in the future. I am also looking forward to the final results of the competition to see how others carried out this specific analysis and to learn from their methodology.
