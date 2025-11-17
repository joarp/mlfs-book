# Air Quality Dashboard

![Hopsworks Logo](../titanic/assets/img/logo.png)

## Comparison of old and new model
The old model uses default features while the new model uses the default features and lag1, lag2, lag3, and rolling average of the lagged values.

![Comparison](./assets/img/pm25_hindcast_comparison.png)


## Predictions

{% include air-quality.html %}

![Forecast](./assets/img/pm25_forecast_moirana.png)
![Forecast](./assets/img/pm25_forecast_bodo.png)


There is also a Python program to interact with the air quality ML system using language (text, voice),
powered by a [function-calling LLM](https://www.hopsworks.ai/dictionary/function-calling-with-llms).

# Model Performance Monitoring

1-Day Hindcast: Predictions vs Outcomes

![Hindcast](./assets/img/pm25_hindcast_1day_moirana.png)
![Hindcast](./assets/img/pm25_hindcast_1day_bodo.png)
