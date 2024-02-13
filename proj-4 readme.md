# gwu-bootcamp-proj-4

Overview

This project involves a comprehensive analysis of electricity production in the United States. We aim to project electricity production (in megawatts) based on the use of various fuels source using the Vector Auto Regression Model, a multi variate time series machine learning model. Our findings will be visualized in a series of Tableau charts.

Methodology
1. Data Collection: We gathered data using various queries on the U.S Energy Information Administration API. We created a database using SQLite to store these queries
2. Data Preprocessing: We connected with the SQLite database using SQL Alchemy. Upon establishing a connection, we used pandas to create DataFrames and NumPy, and stats models to make the calculations necessary for our model. Vector Auto Regression uses a sequence of data points that are organized by time (called time series). The Vector Auto Regression model analyzes multiple time series simultaneously and checks for correlations between these time series. To gain insight into potentially correlated features, we plotted all of these features using seaborn and matplotlib. To check for stationarity ( a necessary condition for accurate predictions in the VAR Model), we used the Dickey Fuller Test. We were looking for a P -Value of less than .05 for the X Values. We parsed the data into bins “Fossil Fuels”, “Renewables” and “Others”. We then dropped the column “energy source” to ensure that all of the column values were floats with the exception of the index “Period” which is in datetime format.
3.Model Building
	a. The following Functions were created for our Model:
		i.“plot_feature” to plot the features in a dataframe with time being the index value
		ii.“print_ADF” to verify which features in the DF is stationary or nonstationary by using the ADF test
		iii.“get_var_lag_summary” to print the summary for select_order with recommended # of lag based on lowest AIC
		iv.“get_rt_mean_sqr_err” prints the  rmse and the percent difference
	b.Load cleaned data from SQLite database
		i.We visualized a feature in our dataset as a preliminary test to see if data was stationary
		ii.Split the dataset by energy source and create dataframes (ff_df, re_df, and oth_df) for each energy source
		iii.Plot all of the features in each of the DataFrames to examine correlations between features. We looked for visualizations that had 			similar trends as indication  of correlation.
	c. Dickey Fuller Test
		i. The Dickey-Fuller test is a statistical test used to determine if a time series data set is stationary or not (determined by p values 		less than .05) . Despite observing a higher p-value than the recommended threshold we proceeded to conduct additional analysis on the 			variables “consumption for eg -btu and energy generation.
	d. Granger Causality test
		i. Granger Causality Test checks to see if one time series can help predict another time series. 
	e. Conducted a split train test on the model.
		i. We started with a 75/25 split (132 months for training and 48 months for testing)
	f. Use a VAR Lag summary to Determine the number of lags to use for model (we selected the LAG from the lowest AIC value which was 40) .
	g. Run the VarMax model for generation and consumption for eg datasets
	h.Create forecasts and put results in DataFrames.
	i.Analyze forecasts
		i.We used Root Means Square Error since it is widely used on VAR models as a metric for measuring accuracy.
	j.Export Results as JSON files so we can create visualizations in Tableau.

