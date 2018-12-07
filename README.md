# Fraudulent-Click-Detection

Kaggle Competition: [TalkingData AdTracking Fraud Detection Challenge](https://www.kaggle.com/c/talkingdata-adtracking-fraud-detection#description)

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

But maybe we could furtehr improve the performance of our models by doing some feature engineering and feature selection. Here's the idea:

• Grouping by ['ip', 'app', 'channel'], and aggregating day with var

• Grouping by ['ip', 'app', 'os'], and aggregating hour with var

• Grouping by ['ip', 'day', 'hour'], and aggregating channel with count

• Grouping by ['ip', 'app'], and aggregating channel with count

• Grouping by ['ip', 'app', 'os'], and aggregating channel with count

• Grouping by ['ip', 'app', 'day', 'hour'], and aggregating channel with count

• Grouping by ['ip', 'app', 'channel'], and aggregating hour with mean

• Grouping by ['app'], and aggregating ip with AvgViewPerDistinct

• Grouping by ['app'], and aggregating channel with count

• Grouping by ['channel'], and aggregating app with count

• Grouping by ['ip'], and aggregating channel with nunique

• Grouping by ['ip'], and aggregating app with nunique

• Grouping by ['ip', 'day'], and aggregating hour with nunique

• Grouping by ['ip', 'app'], and aggregating os with nunique

• Grouping by ['ip'], and aggregating device with nunique

• Grouping by ['app'], and aggregating channel with nunique

• Grouping by ['ip', 'device', 'os'], and aggregating app with nunique

• Grouping by ['ip', 'device', 'os'], and aggregating app with cumcount

• Grouping by ['ip'], and aggregating app with cumcount

• Grouping by ['ip'], and aggregating os with cumcount

• Grouping by ['ip', 'day', 'channel'], and aggregating hour with var
