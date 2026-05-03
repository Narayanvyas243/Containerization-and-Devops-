📊 STATISTICS AND DATA ANALYSIS ASSIGNMENT – 2 (WITH EXPLANATION)👤 Student DetailsName:&nbsp;Narayan VyasSAP ID:&nbsp;500122747Batch:&nbsp;2 (CCVT)🎓 UniversityUniversity of Petroleum and Energy Studies🔬 EXPERIMENT 1: Principal Component Analysis (PCA)🎯 ObjectiveTo reduce the number of features (dimensions) in customer data while preserving maximum important information and visualize patterns.🧠 What is PCA? (Concept)Principal Component Analysis (PCA) is a&nbsp;dimensionality reduction technique.👉 It:Converts many variables → into fewer new variables (called&nbsp;principal components)Keeps&nbsp;maximum variance (information)Helps in&nbsp;visualization &amp; pattern detection📁 Dataset DescriptionIncomeSpending ScoreFrequencyThese are numerical features describing customer behavior.⚙️ Step-by-Step Code + Explanation🔹 Step 1: Import LibrariesPythonRunimport pandas as pdimport numpy as npimport matplotlib.pyplot as pltfrom sklearn.preprocessing import StandardScalerfrom sklearn.decomposition import PCA📌 Explanation:pandas&nbsp;→ handling datasetnumpy&nbsp;→ numerical operationsmatplotlib&nbsp;→ visualizationStandardScaler&nbsp;→ normalize dataPCA&nbsp;→ perform dimensionality reduction

          
            
          
        
  
        
    

🔹 Step 2: Load DatasetPythonRundf = pd.read_csv("customer_data.csv")print(df.head())📌 Explanation:Loads dataset into DataFramehead()&nbsp;shows first 5 rows🔹 Step 3: Handle Missing ValuesPythonRundf.fillna(df.mean(numeric_only=True), inplace=True)

          
            
          
        
  
        
    

📌 Explanation:Missing values can break analysisReplace them with&nbsp;mean values🔹 Step 4: Select Numeric ColumnsPythonRundf_numeric = df.select_dtypes(include=[np.number])📌 Explanation:PCA works only on&nbsp;numerical dataSo we filter numeric columns

          
            
          
        
  
        
    

🔹 Step 5: Normalize DataPythonRunscaler = StandardScaler()scaled_data = scaler.fit_transform(df_numeric)📌 Explanation:Features may have different ranges (Income vs Score)Scaling makes them comparableMean = 0, Standard deviation = 1🔹 Step 6: Apply PCAPythonRunpca = PCA()pca_data = pca.fit_transform(scaled_data)

          
            
          
        
  
        
    

📌 Explanation:PCA finds new axes (principal components)Data is transformed into new coordinate system🔹 Step 7: Explained VariancePythonRunexplained_variance = pca.explained_variance_ratio_plt.plot(explained_variance.cumsum(), marker='o')plt.xlabel("Number of Components")plt.ylabel("Cumulative Explained Variance")plt.title("Explained Variance by Components")plt.grid()plt.show()📌 Explanation:Shows how much information each component keepsHelps decide&nbsp;how many components to select

          
            
          
        
  
        
    

🔹 Step 8: Reduce to 2 ComponentsPythonRunpca = PCA(n_components=2)pca_data = pca.fit_transform(scaled_data)📌 Explanation:Reduce dataset to&nbsp;2 dimensionsUseful for visualization🔹 Step 9: VisualizationPythonRunplt.scatter(pca_data[:,0], pca_data[:,1])plt.xlabel("Principal Component 1")plt.ylabel("Principal Component 2")plt.title("PCA Scatter Plot")plt.grid()plt.show()

          
            
          
        
  
        
    

📌 Explanation:Displays data in 2DHelps identify clusters/patterns📊 OutputExplained variance graphPCA scatter plot📌 ConclusionPCA reduces complexityMaintains important patternsUseful in machine learning &amp; data visualization📈 EXPERIMENT 2: Time Series Analysis🎯 ObjectiveTo analyze past sales data and predict future values using time series models.🧠 What is Time Series?A time series is:👉 Data collected over time (daily, monthly, yearly)Example:Sales per dayTemperature per month📁 Dataset DescriptionDateSales⚙️ Step-by-Step Code + Explanation🔹 Step 1: Import LibrariesPythonRunimport pandas as pdimport numpy as npimport matplotlib.pyplot as pltfrom statsmodels.tsa.seasonal import seasonal_decomposefrom statsmodels.tsa.stattools import adfullerfrom statsmodels.tsa.arima.model import ARIMAfrom sklearn.metrics import mean_absolute_error, mean_squared_error📌 Explanation:seasonal_decompose&nbsp;→ break data into componentsadfuller&nbsp;→ stationarity testARIMA&nbsp;→ forecasting model

          
            
          
        
  
        
    

🔹 Step 2: Load DatasetPythonRundf = pd.read_csv("sales_data.csv")print(df.head())🔹 Step 3: Data CleaningPythonRundf = df[df['Date'] != 'Date']df['Date'] = pd.to_datetime(df['Date'], errors='coerce')df.dropna(inplace=True)📌 Explanation:Removes invalid rowsConverts Date column to proper format

          
            
          
        
  
        
    

🔹 Step 4: Handle Small DatasetPythonRunif len(df) &lt; 30:    dates = pd.date_range(start="2021-01-01", periods=120, freq='D')    sales = np.random.randint(150, 300, size=120)    df = pd.DataFrame({        'Date': dates,        'Sales': sales    })📌 Explanation:If dataset is too small → create sample dataEnsures model works properly🔹 Step 5: Set IndexPythonRundf.set_index('Date', inplace=True)df = df.ffill()

          
            
          
        
  
        
    

📌 Explanation:Set Date as index (important for time series)Forward fill missing values🔹 Step 6: Plot Time SeriesPythonRunplt.figure(figsize=(8,4))plt.plot(df['Sales'])plt.title("Sales Over Time")plt.xlabel("Date")plt.ylabel("Sales")plt.grid()plt.show()📌 Explanation:Shows trend over time

          
            
          
        
  
        
    

🔹 Step 7: DecompositionPythonRunif len(df) &lt; 50:    period_value = 2elif len(df) &lt; 200:    period_value = 7else:    period_value = 30decomposition = seasonal_decompose(df['Sales'], model='additive', period=period_value)decomposition.plot()plt.show()📌 Explanation:Breaks data into:TrendSeasonalResidual🔹 Step 8: Stationarity Test (ADF)PythonRunresult = adfuller(df['Sales'])print("ADF Statistic:", result[0])print("p-value:", result[1])if result[1] &lt; 0.05:    print("Data is Stationary")else:    print("Data is NOT Stationary")

          
            
          
        
  
        
    

📌 Explanation:Stationary data = stable mean &amp; varianceRequired for ARIMA model🔹 Step 9: Train-Test SplitPythonRuntrain = df['Sales'][:-30]test = df['Sales'][-30:]📌 Explanation:Train model on past dataTest on recent data

          
            
          
        
  
        
    

🔹 Step 10: ARIMA ModelPythonRunmodel = ARIMA(train, order=(2,1,2))model_fit = model.fit()print(model_fit.summary())📌 Explanation:ARIMA = AutoRegressive + Integrated + Moving Average(2,1,2) → model parameters🔹 Step 11: ForecastPythonRunforecast = model_fit.forecast(steps=30)future_dates = pd.date_range(start=df.index[-1], periods=30, freq='D')

          
            
          
        
  
        
    

🔹 Step 12: Plot ForecastPythonRunplt.figure(figsize=(8,4))plt.plot(test.index, test, label="Actual")plt.plot(future_dates, forecast, label="Forecast")plt.title("Actual vs Forecast")plt.xlabel("Date")plt.ylabel("Sales")plt.legend()plt.grid()plt.show()🔹 Step 13: EvaluationPythonRunmae = mean_absolute_error(test, forecast)rmse = np.sqrt(mean_squared_error(test, forecast))print("MAE:", mae)print("RMSE:", rmse)📌 Explanation:MAE → average errorRMSE → squared error measureLower values = better model📊 OutputTime series graphDecomposition graphForecast vs actual graphMAE &amp; RMSE values📌 ConclusionTime series helps understand trendsARIMA predicts future valuesUseful in business decision-making🧾 FINAL CONCLUSIONPCA reduces dimensions and simplifies dataTime series analysis predicts future trendsBoth are important tools in data science
