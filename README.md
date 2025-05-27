# EY Open Science AI and Data Challenge 2025: Cooling Urban Heat Islands
This is the [competition link](https://challenge.ey.com/challenges/the-2025-ey-open-science-ai-and-data-challenge-cooling-urban-heat-islands-external-participants).

The goal of the challenge is to develop a machine learning model to predict heat island hotspots in an urban location. Additionally, the model should be designed to discern and highlight the key factors that contribute significantly to the development of these hotspots within city environments.

Participants will be given near-surface air temperature data in an index format, which was collected on 24 July 2021 using a ground traverse in the Bronx and Manhattan region of New York City. This dataset constitutes traverse points (latitude and longitude) and their corresponding UHI (Urban Heat Island) index values. Participants will use this dataset to build a regression model to predict UHI Index values for a given set of locations.

## Our approach 💡
The team consists of [Wei Siang](https://github.com/WeiSiangLai) and I. The overall architecture of our regression model is:
1. Feature engineering (i.e. feature selection, generation, and data cleaning)
2. Model selection and ensembling (i.e. trying out different model types, choosing the best 3 models and combining into an emsemble for final prediction)

### Part 1: Feature Engineering 🛠️
This is basically three steps: feature selection, generation, and data cleaning

### Part 2: Model Selection and Ensembling 🤖
There is a rule stating that automated machine learning methods are not allowed, so AutoML libraries like AutoGluon are out of the question.

Our highest R<sup>2</sup> score was actually achieved with [TabPFN](https://github.com/PriorLabs/TabPFN), a foundation model for tabular data that outperforms traditional methods while being dramatically faster. However due to not needing to tune hyperparameters, this was also disallowed by the competition.

To select the best models, we try each of the models with the following procedure:
1. Split data into train and validation set (no cross validation at this step to save time)
2. Hyperparameter tuning with Optuna
3. Train final model on best parameters and bigger number of epochs, and compare on cross validation score

We experimented with various traditional ML methods such as Linear Regression, Lasso and Ridge Regression, Polynomial Regression, KNN Regression and Support Vector Regression. However, all of these are consistently outperformed by gradient boosting methods such as Histogram Gradient Boosting, XGBoost, LightGBM and CatBoost. 



## Results 📊
0.9772 R^2 score, Top 20 out of 300+ teams.

