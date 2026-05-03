]# STATISTICS AND DATA ANALYSIS ASSIGNMENT – 2

**Student Details**
*   **Name:** Narayan Vyas
*   **SAP ID:** 500122747
*   **Batch:** 2 (CCVT)
*   **University:** University of Petroleum and Energy Studies (UPES)

---



---

## EXPERIMENT 1: Principal Component Analysis (PCA) {#experiment-1}

### Objective
To reduce the number of features (dimensions) in customer data while preserving maximum important information and visualize patterns.

### What is PCA? (Concept)
Principal Component Analysis (PCA) is a dimensionality reduction technique that:
*   Converts many variables into fewer new variables (called principal components).
*   Keeps maximum variance (information).
*   Helps in visualization & pattern detection.

### Dataset Description
| Feature | Description |
| :--- | :--- |
| Income | Annual income |
| Spending Score | Spending behavior score (0-100) |
| Frequency | Purchase frequency |

### Implementation
```python
# ======================================================
# EXPERIMENT 1: PRINCIPAL COMPONENT ANALYSIS (PCA)
# ======================================================

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.preprocessing import StandardScaler
from sklearn.decomposition import PCA
import warnings
warnings.filterwarnings('ignore')

# Dataset Generation
np.random.seed(42)
n_samples = 250
income = np.random.normal(50000, 15000, n_samples)
spending_score = 0.65 * (income/1000) + np.random.normal(0, 12, n_samples)
frequency = np.random.poisson(15, n_samples) + 0.25 * (spending_score/10)

df = pd.DataFrame({'Income': income, 'Spending_Score': np.clip(spending_score, 0, 100), 'Frequency': frequency})

# Preprocessing
df.fillna(df.mean(numeric_only=True), inplace=True)
scaler = StandardScaler()
scaled_data = scaler.fit_transform(df.select_dtypes(include=[np.number]))

# Apply PCA
pca_full = PCA()
pca_full_data = pca_full.fit_transform(scaled_data)

# Explained Variance Analysis
explained_variance = pca_full.explained_variance_ratio_
cumulative_variance = np.cumsum(explained_variance)

# Visualization code for variance and clusters omitted for brevity
# ... (See full implementation in source)
```

---

## EXPERIMENT 2: Time Series Analysis {#experiment-2}

### Objective
To analyze past sales data and predict future values using time series models.

### What is Time Series?
Data collected over time (daily, monthly, yearly). Examples: Sales per day, Temperature per month.

### Dataset Description
| Column | Description |
| :--- | :--- |
| Date | Transaction date |
| Sales | Daily sales amount |

### Implementation
```python
# ======================================================
# EXPERIMENT 2: TIME SERIES ANALYSIS & FORECASTING
# ======================================================

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from statsmodels.tsa.seasonal import seasonal_decompose
from statsmodels.tsa.arima.model import ARIMA
from sklearn.metrics import mean_absolute_error, mean_squared_error

# Dataset Generation
np.random.seed(42)
dates = pd.date_range(start="2024-01-01", periods=180, freq='D')
df = pd.DataFrame({
    'Date': dates,
    'Sales': np.linspace(220, 380, 180) + 25 * np.sin(2 * np.pi * np.arange(180) / 7) + np.random.normal(0, 18, 180)
})
df.set_index('Date', inplace=True)

# Modeling (ARIMA)
train_size = int(len(df) * 0.8)
train, test = df['Sales'][:train_size], df['Sales'][train_size:]
model = ARIMA(train, order=(2,1,2))
model_fit = model.fit()
forecast = model_fit.forecast(steps=len(test))

# ... (Evaluation and visualization code omitted for brevity)
```

---

## FINAL CONCLUSION {#final-conclusion}

| Technique | Purpose | Key Benefits |
| :--- | :--- | :--- |
| **PCA** | Dimensionality Reduction | Reduces features while preserving 85-95% information; enables 2D visualization. |
| **Time Series (ARIMA)** | Forecasting | Captures trends + seasonality; provides future predictions with error metrics. |

Both techniques are essential for modern data analysis and business applications.
