# Air Quality Dashboard

![Hopsworks Logo](../titanic/assets/img/logo.png)

## Comparison of old and new model
The old model uses default features while the new model uses the default features and lag1, lag2, lag3, and rolling average of the lagged values. The new model is used for all predictions below

![Comparison](./assets/img/pm25_hindcast_comparison.png)


# Predictions
{% include air-quality.html %}

## Norrland county
Predictions for sensors in Norrland county will be added here as soon as historical daily average measurements of PM2.5 are available. In the meantime, have a look at predictions for Nordland and Troms county in Norway where all sensors with available historical daily average measurements in the two regions have been added.

## Nordland county (Norway)

![Forecast](./assets/img/pm25_forecast_moirana.png)
![Forecast](./assets/img/pm25_forecast_bodo.png)

## Troms county (Norway)

![Forecast](./assets/img/pm25_forecast_harstad.png)
![Forecast](./assets/img/pm25_forecast_tromso.png)



# Model Performance Monitoring

1-Day Hindcast: Predictions vs Outcomes

![Hindcast](./assets/img/pm25_hindcast_1day_moirana.png)
![Hindcast](./assets/img/pm25_hindcast_1day_bodo.png)
![Hindcast](./assets/img/pm25_hindcast_1day_harstad.png)
![Hindcast](./assets/img/pm25_hindcast_1day_tromso.png)
