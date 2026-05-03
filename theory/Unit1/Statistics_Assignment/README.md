Markdown

# STATISTICS AND DATA ANALYSIS ASSIGNMENT – 2

## Student Details
Name: Narayan Vyas  
SAP ID: 500122747  
Batch: 2 (CCVT)  

## University
University of Petroleum and Energy Studies  

---

# EXPERIMENT 1: Principal Component Analysis (PCA)

## Objective
To reduce the number of features (dimensions) in customer data while preserving maximum important information and visualize patterns.

---

## What is PCA
Principal Component Analysis (PCA) is a dimensionality reduction technique.

It:
- Converts many variables into fewer new variables (principal components)
- Preserves maximum variance (information)
- Helps in visualization and pattern detection

---

## Dataset Description
- Income  
- Spending Score  
- Frequency  

These are numerical features describing customer behavior.

---

## Step-by-Step Code

### Step 1: Import Libraries
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.preprocessing import StandardScaler
from sklearn.decomposition import PCA
Step 2: Load Dataset
Python


Run
df = pd.read_csv("customer_data.csv")
print(df.head())
Step 3: Handle Missing Values
Python


Run
df.fillna(df.mean(numeric_only=True), inplace=True)
Step 4: Select Numeric Columns
Python


Run
df_numeric = df.select_dtypes(include=[np.number])
Step 5: Normalize Data
Python


Run
scaler = StandardScaler()
scaled_data = scaler.fit_transform(df_numeric)
Step 6: Apply PCA
Python


Run
pca = PCA()
pca_data = pca.fit_transform(scaled_data)
Step 7: Explained Variance
Python


Run
explained_variance = pca.explained_variance_ratio_

plt.plot(explained_variance.cumsum(), marker='o')
plt.xlabel("Number of Components")
plt.ylabel("Cumulative Explained Variance")
plt.title("Explained Variance by Components")
plt.grid()
plt.show()
Step 8: Reduce to 2 Components
Python


Run
pca = PCA(n_components=2)
pca_data = pca.fit_transform(scaled_data)
Step 9: Visualization
Python


Run
plt.scatter(pca_data[:,0], pca_data[:,1])
plt.xlabel("Principal Component 1")
plt.ylabel("Principal Component 2")
plt.title("PCA Scatter Plot")
plt.grid()
plt.show()
Output
Explained variance graph

PCA scatter plot

Conclusion
PCA reduces complexity while preserving important information and helps in visualization and pattern detection.

EXPERIMENT 2: Time Series Analysis
Objective
To analyze past sales data and predict future values using time series models.

What is Time Series
A time series is data collected over time.

Examples:

Sales per day

Temperature per month

Dataset Description
Date

Sales

Step-by-Step Code
Step 1: Import Libraries
Python


Run
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from statsmodels.tsa.seasonal import seasonal_decompose
from statsmodels.tsa.stattools import adfuller
from statsmodels.tsa.arima.model import ARIMA
from sklearn.metrics import mean_absolute_error, mean_squared_error
Step 2: Load Dataset
Python


Run
df = pd.read_csv("sales_data.csv")
print(df.head())
Step 3: Data Cleaning
Python


Run
df = df[df['Date'] != 'Date']
df['Date'] = pd.to_datetime(df['Date'], errors='coerce')
df.dropna(inplace=True)
Step 4: Handle Small Dataset
Python


Run
if len(df) < 30:
    dates = pd.date_range(start="2021-01-01", periods=120, freq='D')
    sales = np.random.randint(150, 300, size=120)

    df = pd.DataFrame({
        'Date': dates,
        'Sales': sales
    })
Step 5: Set Index
Python


Run
df.set_index('Date', inplace=True)
df = df.ffill()
Step 6: Plot Time Series
Python


Run
plt.figure(figsize=(8,4))
plt.plot(df['Sales'])
plt.title("Sales Over Time")
plt.xlabel("Date")
plt.ylabel("Sales")
plt.grid()
plt.show()
Step 7: Decomposition
Python


Run
if len(df) < 50:
    period_value = 2
elif len(df) < 200:
    period_value = 7
else:
    period_value = 30

decomposition = seasonal_decompose(df['Sales'], model='additive', period=period_value)
decomposition.plot()
plt.show()
Step 8: Stationarity Test (ADF)
Python


Run
result = adfuller(df['Sales'])
print("ADF Statistic:", result[0])
print("p-value:", result[1])

if result[1] < 0.05:
    print("Data is Stationary")
else:
    print("Data is NOT Stationary")
Step 9: Train-Test Split
Python


Run
train = df['Sales'][:-30]
test = df['Sales'][-30:]
Step 10: ARIMA Model
Python


Run
model = ARIMA(train, order=(2,1,2))
model_fit = model.fit()
print(model_fit.summary())
Step 11: Forecast
Python


Run
forecast = model_fit.forecast(steps=30)
future_dates = pd.date_range(start=df.index[-1], periods=30, freq='D')
Step 12: Plot Forecast
Python


Run
plt.figure(figsize=(8,4))
plt.plot(test.index, test, label="Actual")
plt.plot(future_dates, forecast, label="Forecast")
plt.title("Actual vs Forecast")
plt.xlabel("Date")
plt.ylabel("Sales")
plt.legend()
plt.grid()
plt.show()
Step 13: Evaluation
Python


Run
mae = mean_absolute_error(test, forecast)
rmse = np.sqrt(mean_squared_error(test, forecast))

print("MAE:", mae)
print("RMSE:", rmse)
Output
Time series graph

Decomposition graph

Forecast vs actual graph

MAE and RMSE values

Conclusion
Time series analysis helps understand trends and patterns in data. ARIMA models are effective for forecasting future values.

Final Conclusion
PCA reduces dimensions and simplifies data while preserving important information. Time series analysis helps predict future trends. Both techniques are essential in data science.
