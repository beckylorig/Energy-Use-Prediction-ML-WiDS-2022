# WiDS Energy Use Evaluation

### Women in Data Science (WiDS) Datathon 2022 - Climate Change Evaluation

Women in Data Science (WiDS) holds an annual Datathon.  The goal of the competition this year (2022) was to analyze building energy efficiency and create models to predict building energy consumption, known as 'energy use intensity' (EUI). Energy use by buildings is a contibutor to climate change, and this datathon aims to understand building energy expenditures as a means to combat climate change.  

The datasets contain building features, energy star ratings, and provides additional information such as: building age and the weather features where the building is located (anonymized). The data follow the Kaggle competition design. They include a training dataset and a test dataset. The training data has the target field but the test data does not. The goal is to predict the target variable for the test dataset and submit it for scoring in Kaggle. This dataset has a continuous target variable rather than binary variable. The scoring by Kaggle is RMSE, which is used throughout the analysis.

#### The analysis followed the following main steps:
1. Exploratory analysis of the data
2. Preprocessing the data
3. ETL data loading to PostgreSQL
4. Feature reduction and selection using several techniques
5. Machine Learning techniques to predict the continuous target variable in the data
6. Unsupervised methods
    * t-SNE 
    * K Means Clustering
8. Supervised methods (using pipelines and hyperparameter tuning)
    * K Neighbors Regressor
    * Random Forest Regressor
    * XGBoost Regressor
    * CatBoost Regressor

### The project is split into 3 jupyter notebooks:
1. Preprocessing
2. ETL
3. Model Building

## Findings
Preprocessing was vital to this analysis.  There were missing data, invalid data, and several categorical variables.  The target is the energy consumption of the building, which is a continuous variable.  This made it far more difficult than a binary classification target.  One challenge in the predictive power of the algorithm was the long tail on the target variable.  There were high values that were a magnitude higher than the median and mean of the data.  The bulk of the records for the target were around the mean value with little differentiation among the records at this target level.

Once the data were one-hot-encoded there were 90 variables; one categorical feature had 60 types.  
##### New Variable Creation
There were also three temperature values for each month of the year: minimum temperatue, average temperature, and maximum temperature.  I converted these min, avg, and max values into a new feature.  I subtracted the value from the median of each, which standardized each min,avg, and max and then I added those values (differences from the median) together to create one temperature feature per month.  This converted 36 features into 12 features.
##### Feature Reduction

voting
LassoCV, RandomForestRegressor, GradientBoostRegressor



##### One-Hot-Encoding




## Lessons learned and next steps
