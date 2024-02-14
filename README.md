# gwu-bootcamp-proj-4

Overview

This project involves a comprehensive analysis of electricity production in the United States. We aim to project electricity production (in megawatts) based on the use of various fuels source using the Vector Auto Regression Moving Average Exogenus, a multi variate time series machine learning model. Our findings will be visualized in a series of Tableau charts.

Methodology
1. Data Collection: We gathered data using various queries on the U.S Energy Information Administration API. We created a database using SQLite to store these queries
2. Data Preprocessing: We connected with the SQLite database using SQL Alchemy. Upon establishing a connection, we used pandas to create DataFrames and NumPy, and stats models to make the calculations necessary for our model. Vector Auto Regression uses a sequence of data points that are organized by time (called time series). The Vector Auto Regression model analyzes multiple time series simultaneously and checks for correlations between these time series. To gain insight into potentially correlated features, we plotted all of these features using seaborn and matplotlib. To check for stationarity ( a necessary condition for accurate predictions in the VAR Model), we used the Dickey Fuller Test. We were looking for a P -Value of less than .05 for the X Values. We parsed the data into bins “Fossil Fuels”, “Renewables” and “Others”. We then dropped the column “energy source” to ensure that all of the column values were floats with the exception of the index “Period” which is in datetime format.
<br>
 3.Model Building
<br>
  &nbsp;&nbsp;&nbsp;&nbsp;a. The following Functions were created for our Model:
<br>

 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; i.“plot_feature” to plot the features in a dataframe with time being the index value
 <br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ii.“print_ADF” to verify which features in the DF is stationary or nonstationary by using the ADF test.
 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; iii.“get_var_lag_summary” to print the summary for select_order with recommended # of lag based on lowest &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AIC.
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; iv.“get_rt_mean_sqr_err” prints the  rmse and the percent difference.
<br>
&nbsp;&nbsp;&nbsp;&nbsp;b).Load cleaned data from SQLite database
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; i.We visualized a feature in our dataset as a preliminary test to see if data was stationary.
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ii.Split the dataset by energy source and create dataframes (ff_df, re_df, and oth_df) for each energy 			source.
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; iii.Plot all of the features in each of the DataFrames to examine correlations between features. We looked for visualizations that had similar trends as indication  of correlation.<br>
&nbsp;&nbsp;&nbsp;&nbsp;c. Dickey Fuller Test<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;i. The Dickey-Fuller test is a statistical test used to determine if a time series data set is stationary or not (determined by p values less than .05) Despite observing a higher p-value than the recommended threshold we proceeded to conduct additional analysis on the variables “consumption for eg -btu and energy generation.In attempt to lower the P Values of our features, we used the pandas differencing method.<br>
&nbsp;&nbsp;&nbsp;&nbsp;d. Granger Causality test<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;i. Granger Causality Test checks to see if one time series can help predict another time series. <br>
&nbsp;&nbsp;&nbsp;&nbsp;e. Conducted a split train test on the model.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;i. We started with a 75/25 split (132 months for training and 48 months for testing)
&nbsp;&nbsp;&nbsp;&nbsp;f. Use a VAR Lag summary to Determine the number of lags to use for model (we selected the LAG from the lowest AIC value which was 40 .<br>
&nbsp;&nbsp;&nbsp;&nbsp;g. Run the VarMax model for generation and consumption for eg datasets<br>
&nbsp;&nbsp;&nbsp;&nbsp;h.Create forecasts and put results in DataFrames.<br>
&nbsp;&nbsp;&nbsp;&nbsp;i.Analyze forecasts<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbspi.We used Root Means Square Error since it is widely used on VAR models as a metric for measuring accuracy.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbspj.Export Results as JSON files so we can create visualizations in Tableau.<br>

Analysis
Upon building and testing the VARMAX we realized that the RMSE was ranging from 57-65% errors. In attempt to lower the RMSE, we developed a hypothesis that the limited number of observations had a negative effect on the accuracy of the VARMAX model. This was most likely due to the high number of lags that we incorporated in the model ( 40). Ideally, the LAG should be able to be utilized by each of the energy source data sets without changing the LAG number. To test this new hypothesis we increased the number of observations in the training dataset from 75/25 to 80/20 and then to 89/11 ( we chose 89/11 to ensure that an even number of months was used for our model). As we incremented the number of observations in or model, the RMSE  consistently decreased ( from 57% to 52% and finally to 47% after the 89/11 test ), providing evidence that our hypothesis was valid. 

Additionally we conducted a linear regression to determine if we could forecast our dataset using a univariate model. This model was unsucessful at providing an accurate forecast, due to the non-stationary nature of the dataset. Since liner regession is a univariate model, other influential variables could not be measured in the prediction, significantly affecting the accuracy of the predictions. The model had an R Score of 0.02 and RMSE  of 52%, indicated that linear regression is not an appropriate model to predict the values of our dataset.

Next Steps
We concluded that the VARMAX model is an ideal model for predicting the EIA dataset for the generation of electricity. However, to successfully deploy this model, we would need a signficant increase of observations (at least 500 for each energy source) that we could use to more accurately forecast electricity generation. Developing an API that collects data on electricity on a daily basis could allow us to work with the observations neccessary for more acccurate predictions. 
