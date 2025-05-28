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

These are the list of provided features:
1. Weather data collected from 2 weather stations in Bronx and Manhattan.
   - Air Temperature at Surface
   - Relative Humidity
   - Wind speed and direction
   - Solar flux
2. Satellite data derived from Sentinel-2 and Landsat sensors
   - Spectral Bands
   - Normalized Difference Vegetation Index (NDVI)
   - Normalized Built-up Index (NDBI)
   - Normalized Difference Water Index (NDWI)
   - Land Surface Temperature (LST)
3. Longitude and latitude in New York as predictor, UHI Index as the target

#### Step 1: Feature selection
We chose not to use weather data at all since it is heavily dependent on time, which is not part of the predictor. 

We tried different combinations of the satellite features, including some and excluding the others in each iteration.

This is the list of original features that we went with in our final model:
- B01 (Coastal aerosol)               
- B8A (Narrow Near Infrared)            
- B11 (Short-Wave Infrared 1)            
- B12 (Short-Wave Infrared 2)          
- LST                  
- NDVI                 
- NDBI                 
- NDWI                 

#### Step 2: Feature generation
We explored OpenStreetMap API ([Overpass](https://wiki.openstreetmap.org/wiki/Map_features)) and added additional features taken from the API.

The helpful features we added:
- Building height (we made a box around the coordinate and counted the number of buildings taller than a certain height)
- Building density (same as above but include short buildings as well)
- Presence(number) of water space (rivers, lakes, ponds, etc.)
- Presence(number) of green space (parks, woods, grassy fields)
- Road density 

For all of the above features, we made a box around the coordinate and counted the number/area of the feature in the bounding box. Building related features seemed to be the most important to predicting UHI index.

We also tried OpenFE to generate combinations of features but found that it yielded negligible returns, so we did not use it in the end.

#### Step 3: Data cleaning
We removed rows with missing targets and duplicate coordinates. 

### Part 2: Model Selection and Ensembling 🤖
There is a rule stating that automated machine learning methods are not allowed, so AutoML libraries like AutoGluon are out of the question.

Our highest R<sup>2</sup> score was actually achieved with [TabPFN](https://github.com/PriorLabs/TabPFN), a foundation model for tabular data that outperforms traditional methods while being dramatically faster. However due to not needing to tune hyperparameters, this was also disallowed by the competition.

To select the best models, we try each of the models with the following procedure:
1. Split data into train and validation set (no cross validation at this step to save time)
2. Hyperparameter tuning with Optuna
3. Train final model on best parameters and bigger number of epochs, and compare on cross validation score

We experimented with various traditional ML methods such as Linear Regression, Lasso and Ridge Regression, Polynomial Regression, KNN Regression and Support Vector Regression. However, all of these are consistently outperformed by gradient boosting methods such as Histogram Gradient Boosting, XGBoost, LightGBM and CatBoost. 

For our final model, we did a weighted ensemble of XGBoost, CatBoost and LightGBM using Optuna to fine tune the weights.

## Results 📊
0.9776 R<sup>2</sup> score, Top 30 out of 300+ teams.

