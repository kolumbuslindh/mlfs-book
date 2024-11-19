# Air Quality Prediction Using Weather Features

This project aims to predict air quality (PM2.5) levels using weather data and historical air quality measurements. The solution integrates with Hopsworks for feature and model management and includes pipelines for feature engineering, model training, and batch inference.

## Project Structure

The project is divided into four main components, each implemented as a separate Jupyter notebook:

### 1. **Feature Backfill Pipeline (`1_air_quality_feature_backfill.ipynb`)**

- **Objective**: Populate Hopsworks Feature Groups with historical weather and air quality data.
- **Process**:
  - Fetch historical air quality data using the [AQICN API](https://aqicn.org) and save it as a CSV file.
  - Retrieve historical weather data for the chosen location using the [Open Meteo API](https://open-meteo.com/).
  - Store the processed data into two Feature Groups in Hopsworks: one for weather and another for air quality.

### 2. **Daily Feature Pipeline (`2_air_quality_feature_pipeline.ipynb`)**

- **Objective**: Update Feature Groups daily with new weather and air quality data.
- **Process**:
  - Download today's weather and air quality data.
  - Generate new features, including lagged PM2.5 values for the previous three days.
  - Fetch weather forecasts for the next 7–10 days.
  - Update the corresponding Feature Groups in Hopsworks.

### 3. **Training Pipeline (`3_air_quality_training_pipeline.ipynb`)**

- **Objective**: Train a machine learning model to predict air quality.
- **Process**:
  - Select features from the Feature Groups to create a Feature View in Hopsworks.
  - Prepare training data from the Feature View.
  - Train an XGBoost model (without hyperparameter tuning) to predict PM2.5 levels.
  - Register the trained model in the Hopsworks Model Registry.

### 4. **Batch Inference Pipeline (`4_air_quality_batch_inference.ipynb`)**

- **Objective**: Perform daily predictions and visualize air quality forecasts.
- **Process**:
  - Retrieve the trained model and batch inference data from the Hopsworks Model Registry and Feature View.
  - Generate predictions for the next 7–10 days using lagged PM2.5 features.
  - Sequentially update predictions for each day to account for lagged feature dependencies.
  - Create visualizations:
    - Predicted PM2.5 levels for the upcoming days.
    - Hindcast graph comparing predictions with actual measurements.

## Key Features

- **Data Integration**: Historical and real-time data retrieval from AQICN and Open Meteo APIs.
- **Feature Engineering**: Incorporates lagged PM2.5 values to enhance predictive performance.
- **Model Management**: Uses Hopsworks for managing features, training data, and models.
- **Visualization**: Dashboards for air quality forecasts and accuracy monitoring.

## Pipelines Overview

| Pipeline                  | Frequency | Description                                                            |
| ------------------------- | --------- | ---------------------------------------------------------------------- |
| Feature Backfill Pipeline | Once      | Initializes historical weather and air quality data in Feature Groups. |
| Daily Feature Pipeline    | Daily     | Updates Feature Groups with new weather and air quality data.          |
| Training Pipeline         | As needed | Trains and registers the model for predicting PM2.5 levels.            |
| Batch Inference Pipeline  | Daily     | Generates daily forecasts and visualizations of PM2.5 predictions.     |

## Setup Instructions

1. **Prerequisites**:

   - Hopsworks account and Feature Store setup.
   - API access to AQICN and Open Meteo.
   - Python environment with necessary libraries (`xgboost`, `hopsworks`, etc.).

2. **Run Pipelines**:

   - Create two files in the `data` folder: `aqi-api-key.txt` and `hopsworks-api-key.txt`. These files should contain our API keys from AQICN and Hopsworks. Do not make these public and add them in your .gitignore file, if any.
   - Execute `1_air_quality_feature_backfill.ipynb` to initialize Feature Groups.
   - Schedule `2_air_quality_feature_pipeline.ipynb` for daily updates using GitHub Actions or Modal.
   - Run `3_air_quality_training_pipeline.ipynb` to train and register the model.
   - Schedule `4_air_quality_batch_inference.ipynb` for daily forecasts.

3. **Visualize Results**:
   - Review dashboards for air quality forecasts and hindcast graphs.

## Future Improvements

- Implement hyperparameter tuning for model optimization.
- Experiment with additional features and models.
- Automate hindcast evaluation with alerts for significant prediction errors.
- Implement a LLM to able to query and chat with the predictions.

## License

This project is licensed under the [MIT License](LICENSE).

## Acknowledgments

- [AQICN API](https://aqicn.org) for air quality data.
- [Open Meteo API](https://open-meteo.com/) for weather data.
- Hopsworks for feature and model management.
