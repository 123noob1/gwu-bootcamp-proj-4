# gwu-bootcamp-proj-4
By [Khang Ngo](https://github.com/123noob1) and [John Wooten](https://github.com/jwoot90)

## Overview

This project involves a comprehensive analysis of electricity production in the United States. We aim to project electricity production (in Megawattshour) based on the use of various fuel sources using the Vector Auto Regression Moving Average eXogenus (VARMAX), a multi-variate time series machine learning model. Our findings will be visualized in a series of Tableau charts.

## Methodology
1. **Data Collection:**
    - We gathered data using various queries on the [U.S Energy Information Administration API](https://www.eia.gov/opendata/browser/).
    - We created a database using SQLite to store the cleaned data as well as into CSV and JSON files.
2. **Data Preprocessing:**
    - We connected with the SQLite database using SQL Alchemy. Upon establishing a connection, we parsed the data into bins “Fossil Fuels”, “Renewables” and “Others”.
    - We used pandas to create DataFrame (DF) and NumPy, and stats models to make the calculations necessary for our model. Vector Auto Regression uses a sequence of data points that are organized by time (called time series). The Vector Auto Regression model analyzes multiple time series simultaneously and checks for correlations between these time series.
    - To gain insight into potentially correlated features, we plotted all of these features using `Seaborn` and `Matplotlib`.
    - To check for stationarity visually (a necessary condition for accurate predictions in the VAR Model), we used the `Augmented Dickey-Fuller (ADF)` Test. We were looking for a `P-Value` of less than `0.05` for the X Values.
    - We then dropped the column “energy source” to ensure that all of the column values were floats except for the index “Period” which is in DateTime format.

3. **Model Building:**
    - The following functions were created for our Model:
      - `plot_feature` to plot the features in a DF with time being the index value.
      - `print_ADF` to verify which features in the DF are stationary or nonstationary by using the ADF test based on the `P-value`. Additionally, to help make the data more stationary, especially for the `generation`, we've utilized the `diff()` function[^1] from Pandas before running the test.
      - `get_var_lag_summary` to print the summary using the `select_order` function from the Vector Auto Regression (VAR) model which can be used to recommend the number of lags based on the lowest `AIC`[^2].
      - `get_rt_mean_sqr_err` prints the Root Mean Square Error (RMSE) and the percent difference from the mean of the data being tested[^3].
        [^1]: The differencing method calculates the differences between consecutive elements in data or series. It is useful to help make the data more stationary.
        [^2]: AIC or Akaike Information Criterion - evaluates how well a model explains data. This is the go-to determination based on the lowest value found at the specific lag number. There are also the BIC (Bayesian Information Criterion), HQIC (Hannan-Quinn Information Criterion), and FPE (Final Prediction Error). Ideally, having more of the other criteria included in the same number of lag is better, but otherwise, AIC is the preference.
        [^3]: Having a lower percentage of error indicates that the model can accurately predict or forecast the future.
    - Load cleaned data from SQLite database.
    - We visualized a feature in our dataset as a preliminary test to see if data was stationary.
    - Split the dataset by energy source and create DF (`ff_df`, `re_df`, and `oth_df`) for each energy source - Fossil Fuels, Renewables, and Others respectively.
    - Plot all of the features in each of the DF to examine correlations between features. We looked for visualizations that had similar trends as an indication of correlation.
    - **ADF Test**
       - The Dickey-Fuller test is a statistical test used to determine if a time series data set is stationary or not (determined by a P-value less than 0.05)
       - Despite observing a higher p-value than the recommended threshold we proceeded to conduct additional analysis on the variables `consumption-for-eg-btu` and energy `generation`.
       - In an attempt to lower the P Values of our features, we used the pandas differencing method.
    - **Granger Causality Test**
       - Granger Causality Test checks to see if one time series can help predict another time series.
       - We tested to see if `consumption-for-eg-btu` causes `generation` based on the number of p-values and number of lags.
    - We conducted a split train test on the model for all three energy sources.
       - We started with a 75/25 split (132 months for training and 48 months for testing).
       - Use a VAR Lag summary to Determine the number of lags to use for the model (we selected the LAG from the lowest AIC value which was 40 to be able to accommodate for all energy sources without having different configurations of lags for each source).
       - Run the VarMax model for `generation` and `consumption-for-eg-btu` data.
       - Create forecasts and put results in DataFrames.<br>
&nbsp;&nbsp;&nbsp;&nbsp;i.Analyze forecasts<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;i.We used Root Means Square Error since it is widely used on VAR models as a metric for measuring accuracy.<br>
&nbsp;&nbsp;&nbsp;&nbsp; j.Export Results as JSON files so we can create visualizations in Tableau.<br>

## Analysis
Upon building and testing the VARMAX we realized that the RMSE was ranging from 57-65% errors. In an attempt to lower the RMSE, we developed a hypothesis that the limited number of observations negatively impacted the accuracy of predicting using the VARMAX model. This was most likely due to the high number of lags that we incorporated in the model ( 40). Ideally, the LAG should be able to be utilized by each of the energy source data sets without changing the LAG number. To test this new hypothesis we increased the number of observations in the training dataset from 75/25 to 80/20 and then to 89/11 ( we chose 89/11 to ensure that an even number of months was used for our model). As we increased the number of observations in our model, the RMSE  consistently decreased ( from 57% to 52% and finally to 47% after the 89/11 test ), providing evidence that our hypothesis was valid. 

Additionally, we conducted a linear regression to determine if we could forecast our dataset using a univariate model. This model was unsuccessful at providing an accurate forecast, due to the non-stationary nature of the dataset. Since linear regression is a univariate model, other influential variables could not be measured in the prediction, significantly affecting the accuracy of the predictions. The model had an R Score of 0.02 and RMSE  of 52%, indicating that linear regression is not an appropriate model to predict the values of our dataset.

## Next Steps
We concluded that the VARMAX model is an ideal model for predicting the EIA dataset for the generation of electricity. However, to successfully deploy this model, we would need a significant increase of observations (at least 500 for each energy source) that we could use to more accurately forecast electricity generation. Developing an API that collects data on electricity daily could allow us to work with the observations necessary for more accurate predictions. 

## Tableau Dashboard Design
![Home Page preview](/static/img/tableau_dashboard.png)

## Tools/Technologies Used
- Python
  - Data manipulation/analysis: `Pandas` and `NumPy`
  - Machine Learning: `VARMAX` and `Linear Regression`
-	Jupyter Notebook
-	Tableau Public
-	SQLite, JSON, and CSV
-	MS PowerPoint

### Library Requirements for Python
-	`numpy`, `pandas`, `matplotlib`, `hvplot`, `seaborn`, `statsmodels`, `sklearn`, and `sqlalchemy`

## References
-	https://www.eia.gov/opendata/browser/
-	https://www.statsmodels.org/dev/examples/notebooks/generated/statespace_varmax.html 
-	https://www.machinelearningplus.com/time-series/vector-autoregression-examples-python/
-	https://www.youtube.com/watch?v=4jv1NGlAc_0
