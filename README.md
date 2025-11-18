[Dashboard for Air Quality Predictions](https://joarp.github.io/mlfs-book/air-quality/)

# Project description
This repo contains the code necessary for running the dashboard above on a schedule. In the dashboard we show predictions for air quality in different cities for the coming day. In the dashboard our improved model is also compared to the default model for one city.

## Hopsworks
We use hopsworks as our feature store and for model storing.

## Daily run workflow
The air-quality-daily.yml file provide the logic for fetching daily data and running inference (prediction) of the coming days for all sensors. To be able to run the daily workflow (air-quality-daily.yml) there need to exist a feature group and a trained model for the particular sensor. This can be done by running aq-features and aq-train in a .yml file or running the notebooks manually. Do this when you add a new sensor or want to retrain the model on new data.

## Improved model
We have improved the model by adding the following features: lag1, lag2, lag3, and rolling mean of the lagged values. This gives a better prediction since PM2.5 today is highly correlated with PM2.5 yesterday. Pollution accumulates over time and PM2.5 does not suddenly disappear.

## How predictions work

## Parameters
To keep everything clean and since we never have multiple sensors in each city for the areas we investigate we use city as the unique id for each station and loop through the city values in air-quality-daily.yml by passing the city parameter to the aq-inference runs of the notebooks. To better keep track of all variables for each city we insert url, longitude, and latitude to a specific csv file instead of passing these as parameters. This can however easily be modified.

## Problems with predicting the next day
Feature importance for the model trained on Tromso.
![Feature importance](notebooks/airquality/air_quality_model/images/feature_importance_tromso.png)

As can be seen in the image above, using the PM2.5 measurement from the previous day combined with weather features are the most important features for predicting PM2.5 the next day. Therefore the quality of the prediction for tomorrow will be dependant on the measure of PM2.5 today. A prediction late in the day will therefore be preferred since the API will provide a more reliable average PM2.5 for the day. Predicting the coming day in the morning can be problematic since the daily average will most likely be a large underestimate of the actual average for that day. As an example, PM2.5 levels usually rise during commuting times if the sensor is located close to a road. To mitigate this issue you might want to run the daily workflow late in the day and if you need a prediction for the coming day earlier than that, you can use the prediction from the day before (which spans multiple days).

## Autoregressive model for predictions > 1 day
For predicting air quality for multiple days ahead we utilize an autoregressive framework where the prediction for day t is dependendant on the prediction for day t-1.

Since we are using XGBoost we won't exptrapolate upward trends in the way an ARIMA model or NN would. This is because it learns rules and it will not have learnt rules that pushes values higher indefinitely. Weather features will also push the forecast down to plausible ranges.

## Preprocessing to remove measurements due to sensor fault
For all sensors except one, the measurements where relatively clean, but, for Tromso we identified suspect outliers abnormal for the scandinavian region. The image below show the suspect outlier:

![Outliers](notebooks/airquality/air_quality_model/images/outliers.png)

To understand if the measurements were a consequence of a faulting sensor or actual abnormal air quality e.g. due to a wildfire, we used a filter to identify and remove outliers which likely had been caused by a faulting sensor. For the Tromso station we had access to PM10 measurments and could compare if abnormalities also were found in the PM10 measurements, if not, the outliers were most probably due to a faulting sensor. Here we can see the corresponding PM10 measurements for the same outlier periods. This technique is of course not guaranteed to work but provide some idea if the outlier should be remove without manually investigating every outlier.

![Outliers PM10](notebooks/airquality/air_quality_model/images/outliers_pm10.png)

When measuring a PM2.5 value larger than 100 while not measuring any deviation from the median of the corresponding PM10 value for that date, it is highly likely not an observation of the actual PM2.5 value. Using a filter similar to above description we identify the following suspects for Tromso and also remove these before running the ML pipeline. This preprocessing step is done in the notebook preprocess_historical_data.ipynb

![Found outliers with the filter](notebooks/airquality/air_quality_model/images/after_outlier_filter.png)

















# mlfs-book
O'Reilly book - Building Machine Learning Systems with a feature store: batch, real-time, and LLMs


## ML System Examples


[Dashboards for Example ML Systems](https://featurestorebook.github.io/mlfs-book/)




# Run Air Quality Tutorial

See [tutorial instructions here](https://docs.google.com/document/d/1YXfM1_rpo1-jM-lYyb1HpbV9EJPN6i1u6h2rhdPduNE/edit?usp=sharing)
    # Create a conda or virtual environment for your project
    conda create -n book 
    conda activate book

    # Install 'uv' and 'invoke'
    pip install invoke dotenv

    # 'invoke install' installs python dependencies using uv and requirements.txt
    invoke install


## PyInvoke

    invoke aq-backfill
    invoke aq-features
    invoke aq-train
    invoke aq-inference
    invoke aq-clean



## Feldera


pip install feldera ipython-secrets
sudo apt-get install python3-secretstorage
sudo apt-get install gnome-keyring 

mkdir -p /tmp/c.app.hopsworks.ai
ln -s  /tmp/c.app.hopsworks.ai ~/hopsworks
docker run -p 8080:8080 \
  -v ~/hopsworks:/tmp/c.app.hopsworks.ai \
  --tty --rm -it ghcr.io/feldera/pipeline-manager:latest

