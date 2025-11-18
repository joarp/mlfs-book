[Dashboard for Air Quality Predictions](https://joarp.github.io/mlfs-book/air-quality/)

# Project description
This repository contains the full code and infrastructure required to run a scheduled machine learning pipeline that forecasts air quality and renders a comparative dashboard.
The purpose of this project is to provide daily air quality predictions (specifically for $\text{PM}_{2.5}$) for multiple target cities and to serve these results in a dynamic dashboard. A key feature of the dashboard is the side-by-side comparison of our improved machine learning model against a standard default model for a single city, demonstrating the value of advanced feature engineering.

## Hopsworks
We use hopsworks as our feature store and for model storing.

## Daily run workflow

The pipeline is managed through a central automation file, air-quality-daily.yml.

Trigger: The workflow runs on a daily schedule.

Action: It fetches the latest data and executes the inference process (aq-inference) for all monitored cities/sensors.

Parameterization: To keep runs clean, the city is used as the unique identifier. Instead of passing many parameters, station metadata (URL, longitude, latitude) is loaded from a specific CSV file to simplify the pipeline execution.

Prerequisites for Daily Inference
For the air-quality-daily.yml workflow to succeed, a Feature Group and a Trained Model must already exist for each city/sensor in Hopsworks.

Setup: Run the aq-features and aq-train tasks (or the corresponding notebooks) when adding a new sensor or retraining the model on new data.
## Improved model
We have improved the model by adding the following features: lag1, lag2, lag3, and rolling mean of the lagged values. This gives a better prediction since PM2.5 today is highly correlated with PM2.5 yesterday. Pollution accumulates over time and PM2.5 does not suddenly disappear.

## How predictions work


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

