# Fraudulent-Click-Detection

Kaggle Competition: [TalkingData AdTracking Fraud Detection Challenge](https://www.kaggle.com/c/talkingdata-adtracking-fraud-detection#description)

[Google Slide](https://docs.google.com/presentation/d/1MQPqgvEI_hoCk9ZzuIwVrM0uqW1MSgdktnZV1K0qtyg/edit?usp=sharing) 

[Data sample](https://github.com/helloyuhan/Fraudulent-Click-Detection/tree/master/data)   
[Overall walk though](https://github.com/helloyuhan/Fraudulent-Click-Detection/blob/master/Fradulent_detection_walkthrough.ipynb)   
[Split data](https://github.com/helloyuhan/Fraudulent-Click-Detection/blob/master/data/csv_split.ipynb)   
[EDA vis & Raw model vis](https://github.com/helloyuhan/Fraudulent-Click-Detection/blob/master/visualization/EDA%26Raw_Model_vis.ipynb)   
[Featured Engineered vis](https://github.com/helloyuhan/Fraudulent-Click-Detection/blob/master/visualization/feature_engineering_vis.ipynb)   
[Raw model selection](https://github.com/helloyuhan/Fraudulent-Click-Detection/blob/master/model_fitting%26param_tuning/feature_engineering_vis.ipynb)   
[Raw model parameter tuning](https://github.com/helloyuhan/Fraudulent-Click-Detection/blob/master/model_fitting%26param_tuning/raw_param_tuning.ipynb)   
[Featured enigneered model selection](https://github.com/helloyuhan/Fraudulent-Click-Detection/blob/master/model_fitting%26param_tuning/feature_engineered_model_selection.ipynb)   
[Featured enigneered paramter tuning](https://github.com/helloyuhan/Fraudulent-Click-Detection/blob/master/model_fitting%26param_tuning/feature_engineered_param_tuning.ipynb)   


## Problem Description

Fraud risk is everywhere, but for companies that advertise online, click fraud can happen at an overwhelming volume, resulting in misleading click data and wasted money. Ad channels can drive up costs by simply clicking on the ad at a large scale. With over 1 billion smart mobile devices in active use every month, China is the largest mobile market in the world and therefore suffers from huge volumes of fradulent traffic.

TalkingData, China’s largest independent big data service platform, covers over 70% of active mobile devices nationwide. They handle 3 billion clicks per day, of which 90% are potentially fraudulent. Their current approach to prevent click fraud for app developers is to measure the journey of a user’s click across their portfolio, and flag IP addresses who produce lots of clicks, but never end up installing apps. With this information, they've built an IP blacklist and device blacklist.

While successful, they want to always be one step ahead of fraudsters and have turned to the Kaggle community for help in further developing their solution. In their 2nd competition with Kaggle, you’re challenged to build an algorithm that predicts whether a user will download an app after clicking a mobile app ad. To support your modeling, they have provided a generous dataset covering approximately 200 million clicks over 4 days!

## Objective

Detect fraudulent click traffic for mobile app ads and predict whether a user will download an app after clicking a mobile app advertisement.

## What We Did

We explored the original dataset and did some EDA for insights. Due to the large size of the dataset (around 200m lines), we sampled three exclusive subsets for different uses: 2m lines for model selection, 18m lines for parameter tuning, and 2m lines for final test. The first 2m- and 18m- datasets are further downsampled to 10k and 90k lines, because we found the data was extremely imbalanced with only 0.25% of the clicks led to app downloading. Therefore, after downsampling, there were 10k lines for model selection, 90k lines for parameter tuning, and 2m lines (imbalanced) for final test.

Before any feature engineering, we preprocessed the raw features and fit them with 10 models with default parameters, and then chose 4 out of 10 according to roc_auc_score. Then we grid searched these 4 models for best parameters and the improvement was summarized below:

|type|roc_auc_before|roc_auc_after|
|:-:|:-:|:-:|
|RandomForestClassfier|0.909480|0.915570|
|GradientBoostingClassifer|0.909877|0.916020|
|XGBClassifer|0.909404|0.920014|
|LGBMClassifer|0.914530|0.920279|

But maybe we could furtehr improve the performance of our models by doing some groupby-and-aggregation feature engineering and feature selection. Here're some examples:

• Grouping by ['ip', 'app', 'channel'], and aggregating day with var

• Grouping by ['app'], and aggregating channel with count

• Grouping by ['channel'], and aggregating app with count

• Grouping by ['ip'], and aggregating channel with nunique

......

Using these full features, we grid searched the four best models from the last step. Then we did feature selection for each model to reduce complexity and grid searched again. The performance comparison before and after feature selection is summarized below:

|type|roc_auc_full|roc_auc_reduced|
|:-:|:-:|:-:|
|RandomForestClassfier|0.921463|0.879052|
|GradientBoostingClassifer|0.922486|0.922440|
|XGBClassifer|0.922467|0.918519|
|LGBMClassifer|0.926037|0.910807|

The best model so far is Gradient Boosting, and with features reducing from 30 to 7, the roc-auc-score only decreased by 0.000046. Therefore, our final model was chosen as the Gradient Boosting with reduced features. This way, we have essentially produced a model as good as the model with full features and reduced the computational cost of grid search by having relatively fewer features.

Finally, we trained the selected model on the 90K samples downsampled from the 18 million dataset, and tested with the original 2 million dataset. The final roc_auc scores were calculated on this dataset:

|Models|roc_auc score|
|:-:|:-:|
|Baseline|0.909213|
|Featured engineered full|0.803564|
|Featured engineered reduced| 0.817060|

Due to the relative small size of the dataset and limited computational power, we were unable to train and test the new model with added features. By evaluating on a smaller dataset, the new model was deemed to be overly complex and prone to overfitting. If we could have gathered more data and have higher computational power, we should be able to have a much improved roc_auc score with the feature-engineered model.

We have concluded that the features need to be independent and more data are always preferred. It would be amazing if the data are un-encoded so we could study the data more and generate more useful insights.



