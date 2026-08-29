# Stock Market Prediction: A Comparative Analysis

A comparative time-series forecasting study for predicting the closing price of Bajaj Finance using historical market data.

The project evaluates a deep learning based LSTM model against traditional statistical forecasting methods and a tree-based machine learning model. The goal is not only to build a forecasting model, but also to understand how different modelling approaches behave on the same financial time-series problem.

## Overview

Financial time-series prediction is challenging because stock prices are affected by noise, volatility, trends, and many external factors.

In this project, historical market data for **Bajaj Finance (BAJFINANCE)** is used to forecast future closing prices. A multivariate LSTM model is developed using historical Open, High, Low, Close, and Volume information.

The LSTM model is then compared with four alternative approaches:

- ARIMA
- GARCH
- Exponential Smoothing (ETS)
- XGBoost

This provides a broader comparison between deep learning, statistical forecasting, and traditional machine learning.

## Research Question

> How does a multivariate LSTM model perform compared with traditional statistical forecasting methods and XGBoost when forecasting stock closing prices?

The experiment focuses on both prediction accuracy and the differences between the modelling approaches.

## Dataset

The project uses historical market data for **Bajaj Finance**.

The main variables used in the forecasting process are:

| Feature | Description |
|---------|-------------|
| Open | Opening price |
| High | Highest price during the trading period |
| Low | Lowest price during the trading period |
| Close | Closing price |
| Volume | Trading volume |

The dataset is loaded from a CSV file containing historical BAJFINANCE market data. The Date column is converted to a datetime index and missing observations are removed. :contentReference[oaicite:2]{index=2}

## Methodology

The overall workflow is:

```text
Historical Stock Data
        |
        v
Data Cleaning
        |
        v
Feature Selection
(Open, High, Low, Close, Volume)
        |
        v
Min-Max Scaling
        |
        v
120-Day Lookback Windows
        |
        +-------------------+
        |                   |
        v                   v
     LSTM              Baseline Models
                          |
              +-----------+-----------+
              |           |           |
            ARIMA       GARCH        ETS
                                      |
                                  XGBoost
              |
              v
       Closing Price Forecasts
              |
              v
       MSE / MAE Comparison
              |
              v
       Forecast Visualization
````

## Data Preparation

The market variables are normalized using `MinMaxScaler` before being passed to the LSTM.

A **120-day lookback window** is used to provide the model with a longer historical context.

For every prediction, the model receives the previous 120 observations containing:

```text
Close
Open
High
Low
Volume
```

and predicts the next closing price.

The train/test split is performed sequentially without shuffling, which preserves the chronological structure of the financial time series. 

## LSTM Architecture

The main forecasting model is a multi-layer LSTM network.

```text
Input
120 time steps × 5 features
        |
        v
LSTM (100 units)
        |
        v
Dropout (30%)
        |
        v
LSTM (100 units)
        |
        v
Dropout (30%)
        |
        v
Dense (50)
        |
        v
Dense (1)
        |
        v
Predicted Closing Price
```

The model uses:

* Two LSTM layers with 100 units
* Dropout layers with a rate of 0.3
* Dense layer with 50 units
* Single output neuron
* Adam optimizer
* Mean Squared Error loss
* Mean Absolute Error as an additional metric
* Early stopping with patience of 10 epochs

Training was configured for a maximum of 100 epochs with a batch size of 32. 

## Comparative Models

To put the LSTM results into context, four additional forecasting approaches were evaluated.

### 1. ARIMA

ARIMA is used as a traditional statistical time-series baseline.

The experiment uses:

```text
ARIMA(5, 1, 0)
```

This provides a classical benchmark for comparing the neural network against an established time-series forecasting approach. 

### 2. GARCH

GARCH is included to model financial time-series behaviour and volatility.

A GARCH(1,1) configuration is used in the experiment. 

### 3. Exponential Smoothing

An Exponential Smoothing model is included as another traditional forecasting baseline.

The experiment uses additive trend and seasonality with a seasonal period of 12. 

### 4. XGBoost

XGBoost provides a tree-based machine learning comparison.

The model is trained using the flattened historical lookback windows and configured with:

```text
n_estimators = 100
learning_rate = 0.1
```

This allows the experiment to compare a gradient-boosted tree model with both statistical methods and the recurrent neural network. 

## Evaluation

The models are evaluated using:

### Mean Squared Error (MSE)

MSE measures the average squared difference between the predicted and actual closing prices.

Lower values indicate better predictive performance.

### Mean Absolute Error (MAE)

MAE measures the average absolute difference between predicted and actual prices.

Again, lower values indicate better performance.

The project also visualizes the predicted price trajectories against the actual test-set prices. 

## Results

The experiment produced the following results on the test data:

| Model    |                      MSE |           MAE |
| -------- | -----------------------: | ------------: |
| ARIMA    |   498,285,333,044,072.56 | 22,322,305.58 |
| GARCH    |         1,703,769,879.64 |     41,240.93 |
| ETS      | 2,082,540,517,433,027.75 | 43,895,500.71 |
| XGBoost  |             5,992,256.13 |      1,866.88 |
| **LSTM** |            **71,370.00** |    **191.20** |

The LSTM achieved the lowest MSE and MAE among the evaluated models in this experiment. XGBoost was the second strongest model based on these metrics. 

### Model Comparison

```text
Lower MSE / MAE = Better

LSTM       █
XGBoost    █████████████
GARCH      █████████████████████████
ARIMA      █████████████████████████████████████
ETS        █████████████████████████████████████████
```

The difference in error between the models highlights how strongly the choice of forecasting approach can affect results for this dataset.

## Training Behaviour

The LSTM was trained with validation-based early stopping.

During training, validation loss fluctuated considerably, showing that financial forecasting remains a difficult optimization problem even when the training loss is relatively small.

The model stopped after 28 epochs in the recorded experiment rather than using the full 100-epoch limit. 

## Visualization

The notebook compares the forecasts from each model against the actual closing prices.

The visual analysis includes:

* ARIMA vs LSTM
* GARCH vs LSTM
* ETS vs LSTM
* XGBoost vs LSTM
* Combined comparison of all models

These plots make it possible to inspect how closely each forecasting method follows the actual price trajectory. 

## Key Findings

Several observations emerged from the experiment:

1. **LSTM produced the lowest forecasting error** among the tested approaches.

2. **XGBoost performed considerably better than the traditional statistical baselines** in this experiment.

3. The LSTM benefited from using multiple market variables rather than relying only on historical closing prices.

4. Traditional statistical models provided useful baselines but struggled considerably on the evaluated test period.

5. Financial forecasting remains difficult even when a model achieves relatively low error because historical patterns do not guarantee future market behaviour.

## Limitations

This project should be interpreted as an experimental forecasting study rather than a complete trading system.

Some limitations include:

* The experiment focuses on a single stock.
* Only historical market information is used.
* External factors such as news, economic conditions, company announcements, and investor sentiment are not included.
* The evaluation is based on one chronological train/test split.
* Prediction accuracy does not directly measure trading profitability.
* Transaction costs, slippage, portfolio allocation, and risk management are not considered.

A model that predicts the closing price relatively well does not automatically provide a profitable investment strategy.

## Future Work

Possible extensions include:

* Test the methodology on multiple stocks and indices
* Add technical indicators such as RSI, MACD and moving averages
* Incorporate financial news sentiment
* Compare against GRU and Transformer-based time-series models
* Use walk-forward validation
* Perform hyperparameter optimization
* Predict returns or market direction in addition to price
* Build a backtesting framework
* Evaluate risk-adjusted trading performance
* Develop an interactive forecasting dashboard

## Technologies

* Python
* NumPy
* Pandas
* Matplotlib
* TensorFlow / Keras
* Scikit-learn
* Statsmodels
* ARCH
* XGBoost
* Jupyter Notebook

## Project Structure

```text
stock-market-trend-prediction/
│
├── main.ipynb
├── dataset/
│   └── BAJFINANCE.NS (4).csv
│
├── requirements.txt
├── LICENSE
└── README.md
```

The exact file structure may vary depending on the files included in the repository.

## Running the Project

Clone the repository:

```bash
git clone https://github.com/ahsxndev/stock-market-trend-prediction.git
cd stock-market-trend-prediction
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

Open the notebook:

```bash
jupyter notebook
```

Run the notebook cells sequentially.

The notebook performs the complete workflow from data preparation through model training, forecasting, evaluation, and visualization.

## Research Perspective

This project was developed to study the differences between several forecasting paradigms when applied to financial time-series data.

Instead of evaluating an LSTM in isolation, the experiment compares it against statistical forecasting methods and XGBoost. This makes the analysis more useful for understanding the trade-offs between different modelling approaches.

The results show that, for the evaluated Bajaj Finance dataset and test period, the LSTM produced substantially lower MSE and MAE than the other tested models. However, these results should not be interpreted as evidence that LSTM can reliably predict future stock prices in general.

Further validation across different stocks, time periods, and market conditions would be necessary before drawing broader conclusions.

## Disclaimer

This project is intended for educational and research purposes only.

It is not financial advice and should not be used as a basis for making investment decisions.

## Author

**Ahsan Zaman**

BS Computer Science
University of Engineering and Technology, Lahore

GitHub: [https://github.com/ahsxndev](https://github.com/ahsxndev)

LinkedIn: [https://www.linkedin.com/in/ahxanzaman/](https://www.linkedin.com/in/ahxanzaman/)
