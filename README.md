
# Wind Forecast Bias Correction with Gradient Boosting

This project aims to build a machine learning model to correct biases in wind speed and direction forecasts using historical forecast and archive (actual) data. The goal is to improve the accuracy of wind predictions, which is crucial for applications like autonomous drone navigation.

## Project Overview

The core idea is to train a model that learns the systemic errors (biases) of a weather forecasting model by comparing its historical forecasts with actual observed conditions. Once the bias model is trained, it can be used to adjust future forecasts, making them more accurate.

## Data Acquisition and Preparation

1.  **Data Sources**: Hourly wind speed and direction data for 10 geographical locations were fetched for the year 2025 from:
    *   `historical-forecast-api.open-meteo.com` (Forecast data)
    *   `archive-api.open-meteo.com` (Archive/Actual data)
2.  **Data Consolidation**: Both datasets were combined into a single DataFrame, `all_data`, and labeled as 'forecast' or 'archive'.
3.  **Feature Engineering**: Temporal features (hour, day_of_week, day_of_year, month) were extracted from the 'date' column to capture time-dependent patterns in bias.
4.  **Bias Calculation**: The data was pivoted to align forecast and archive values, and biases were calculated as `Forecast - Actual` for both wind speed and direction.
5.  **Missing Value Handling**: Rows with missing bias values were removed.

## Model Development

1.  **Model Choice**: Gradient Boosting Regressor (from scikit-learn) was selected for its ability to handle non-linear relationships, mixed data types, and its strong predictive performance. Two separate models were trained: one for wind speed bias and another for wind direction bias.
2.  **Features**: The models used latitude, longitude, hour, day_of_week, day_of_year, month, and the original wind speed/direction forecast as input features.
3.  **Data Splitting**: Data for January to October 2025 was used for training, while November and December 2025 data was reserved for testing the model's performance on unseen future data.

## Evaluation and Results

### Bias Prediction Performance (on test set)

*   **Wind Speed Bias Model**:
    *   Mean Absolute Error (MAE): 0.6081 m/s
    *   Root Mean Squared Error (RMSE): 0.8146 m/s
*   **Wind Direction Bias Model**:
    *   Mean Absolute Error (MAE): 39.2458 degrees
    *   Root Mean Squared Error (RMSE): 67.9908 degrees

### Corrected Forecast Performance (on test set)

After applying the predicted biases to the original forecasts (`Corrected Forecast = Original Forecast - Predicted Bias`), the evaluation metrics for the corrected forecasts against actual values were identical to the bias prediction errors, confirming that the bias correction mechanism is working as expected.

## Analysis of Bias Dependence

*   **Terrain Dependence**: The current dataset lacks specific terrain information, preventing quantitative analysis of terrain's impact on bias. Integrating high-resolution terrain data was identified as a critical next step.
*   **Temporal Dependence**: Clear hourly and monthly patterns were observed in both wind speed and direction biases. Quantitatively, the average hourly wind speed bias ranged from approximately -0.18 m/s to 0.24 m/s, and average hourly wind direction bias ranged from about -10.15 degrees to 5.11 degrees. Monthly average wind speed bias varied from -0.31 m/s to 0.03 m/s, and monthly average wind direction bias ranged from -7.26 degrees to 3.58 degrees. These statistics demonstrate the significant temporal influence on forecast bias.

## Operational Deployment Considerations (for autonomous drones)

Deploying such a model on an autonomous drone would require:

*   **Computational Efficiency**: Adapting the model for onboard, real-time processing (e.g., lightweight models or edge AI).
*   **High-Resolution Spatial Data**: Incorporating detailed terrain and land cover information along the drone's route.
*   **Real-time Sensor Integration**: Using the drone's own sensor data (anemometers, barometers, GPS) as dynamic inputs for localized corrections.
*   **Adaptive Learning**: Implementing mechanisms for the model to continually learn and adjust to encountered conditions during flight.
*   **Uncertainty Quantification**: Providing estimates of correction uncertainty for safer drone operation.

## Files Generated

*   `predictions.csv`: Contains the bias-corrected wind speed and direction forecasts for the test period (November-December 2025).
*   `requirements.txt`: Lists all Python packages and their versions used in this project.

## How to Run the Notebook

1.  **Open in Google Colab**: Upload the `.ipynb` file to Google Colab.
2.  **Install Dependencies**: Run the first code cell to install necessary libraries (`openmeteo-requests`, `requests-cache`, `retry-requests`, `numpy`, `pandas`, `scikit-learn`, `matplotlib`, `seaborn`).
3.  **Execute Cells**: Run all subsequent code cells in order. The notebook will:
    *   Fetch historical forecast and archive weather data.
    *   Perform data cleaning and feature engineering.
    *   Train Gradient Boosting Regressor models for wind speed and direction bias.
    *   Evaluate model performance.
    *   Visualize temporal dependencies of bias.
    *   Generate `predictions.csv` with corrected forecasts and a `requirements.txt` file.
