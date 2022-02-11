# Diabetes’ Rates in US Counties


Aaron Freid, Justin Lau, Shalmali Patil, Alex Rudolph
I. Abstract
	Diabetes prevalence is regularly increasing over the years and one proposed reason for this is a lack of accessible and healthy food. In order to model this relationship, we created several regression models designed to observe the relationship between food deserts, income, race, age and the diabetes rates among counties in the United States. After extensive data cleaning and splitting of the dataset, we used the ML Features package to create pipelines to train the data for decision tree, random forest, and linear regression models. Of these models, we found that the best model was our cross-validated linear regression model which found that the most significant factors in for higher rates of diabetes was prevalence of group housing and SNAP benefits while diabetes was lower in counties with a large number of housing units. However, further exploration and improvements to the models can be made by finding more comprehensive measures of income or exploring interactions between relevant variables.

II. Data and Methods
Background of Data:
The diabetes data utilized in our project came from The Institute for Health Metrics and Evaluation (IHME) and included information about the prevalence of diagnosed diabetes in the United States from 1999- 2012. The original data set included the age-standardized prevalence of diabetes per county for males and females separately as well as combined for the years. Furthermore, the diabetes data included different tabs - one for diagnosed cases, another for undiagnosed cases, and a third that combined the previous two (denoted Total).  The undiagnosed case tab included undiagnosed adults with high fasting plasma glucose (FPG) / A1C levels. However, since the American Diabetes Association states that high FPG / A1C levels are themselves the criteria for a diabetes diagnosis, we determined that despite the absence of a formal diagnosis, these individuals should be included as well. Hence, we used the Total tab in our analysis.  
Our food access data comes from the U.S. Department of Agriculture (USDA) that combined 2010 census data with supermarket locations. This dataset describes access to supermarkets and grocery stores for different races, age groups and income levels. Finally, we included state level median family income data from the U.S. Department of Housing and Urban Development (HUD). The three datasets were combined into one data frame object for use in our analysis.

Data Cleaning and Preprocessing: 
To provide an overview of the data, we calculated the amount of missingness in each column of our data.frame object and looked for potential outliers in its rows. Because there were not many outliers in our data.frame, we kept all rows. We also did not use missingness in the columns to remove certain features.
The IHME diabetes dataset includes columns corresponding to diabetes prevalence for each year from 1999-2012, among both sexes combined as well as delineated separately by sex (male and female).  It also includes additional columns corresponding to percent change in diabetes from 1999-2012, again among both sexes as well as separately. Due to a high correlation between the male and female values for each year as seen below in Figure 1, we chose to drop the individual sex columns. The following figure shows the correlation between the remaining columns for a percentage of diabetes prevalence in both sexes.

Figure 1: Correlation matrix between yearly county-level population percentage of diabetes. 

Figure 1 shows very high correlations between diabetes prevalence between neighboring years from 1999 to 2012. With this in mind, we calculated the five-year moving average centered around 2010, the year of the census data, to use in our later analysis.
	The USDA food access dataset contained census tract level data for different races and age groups according to their access to supermarkets at varying distances. To match the granularity of the diabetes dataset, we aggregated the census block groups to a county level. Where it was appropriate, we used a weighted average by census tract population in the county in order to modify some of the features. This was done on columns such as the median family income. Other columns are count data which used the total number of people for whom the situation applied, such as the total count of low income population. In these situations, we computed the county wide percentage. 
We opted to keep demographic data of the counties, including percentages of different races and age groups. We also kept variables describing the county percentage with low access to supermarkets at different distances. The full list is given in table 1 below.
	The HUD median family income data was included in order to reproduce a food desert label on a county-wide basis. The original dataset included a mapping of 1 if a census tract was a food desert and 0 if it was not.  
Since we anticipated that food desert status may influence diabetes prevalence, we determined it was important to preserve this categorization, albeit for counties instead of census tracts.  We used USDA criteria to categorize counties as food deserts.  While these criteria are typically applied to census tracts, they can easily enough be applied to counties as well. We designated counties as food deserts if they met both low-income and low-access thresholds, as defined below: 

Low Income: A county is considered low-income if its poverty rate is at least 20% or if its median family income (MFI) is 80% or less of the statewide median family income.  Note: We estimated the true county-wide MFI with an average of the MFIs for each census tract within the county, weighted by population.

Low Access: A county is considered low-access if at least 33% of its population lives more than 1 mile (for urban areas) from a supermarket or large grocery store or more than 10 miles away (for rural areas).  Note below that after aggregation, the variable LAPOP1_10 provides exactly this percentage.

Variable
Description
Source
Diabetes
5 year moving average centered around 2010
IHME
Pop
2010 census population
USDA
OHU2010
2010 census occupied housing unit count
USDA
PCTGQTRS
% living in group quarters
USDA
PovertyRate
% population living at or below federal poverty threshold
USDA
MedianFamilyIncome
Median family income
USDA
LAPOP1_10
% living in low access area (access: 1 mile urban, 10 mile rural)
USDA
LAPOP05_10
% living in low access area (access: 0.5 mile urban, 10 mile rural)
USDA
LAPOP1_20
% living in low access area (access: 1 mile urban, 20 mile rural)
USDA
LALOWI1_10
% low access and low income (access: 1 mile urban, 10 mile rural)
USDA
LALOWI05_10
% low access and low income (access: 0.5 mile urban, 10 mile rural)
USDA
LALOWI1_20
% low access and low income (access: 1 mile urban, 20 mile rural)
USDA
TractLOWI
% Low income population
USDA
TractKids
% children
USDA
TractSeniors
% seniors
USDA
TractWhite
% white population
USDA
TractBlack
% black or African American population
USDA
TractAsian
% Asian population
USDA
TractNHOPI
% Native Hawaiian and other Pacific Islander population
USDA
TractAIAN
% American Indian and Alaska Native population
USDA
TractOMultir
% other/multiple race population
USDA
TractHispanic
% Hispanic or Latino population
USDA
TractHUNV
% housing unit without a vehicle
USDA
TractSNAP
% housing units that receive SNAP benefits
USDA
Food Desert


USHUD

Table 1: Model Variables.

Exploratory Data Analysis:
	After cleaning our diabetes data, we were interested in seeing where diabetes was most prevalent in the US. 


Figure 2: Distribution of county level population percentage of diabetes.

From Figure 2, we can see a higher population proportion of diabetes in Texas along its southern border with Mexico, along the Mississippi river and following the southern part of the Appalachian mountains (in the South East). We can see low proportions of diabetes in the Pacific Northwest regions and as well as the Northeast. There are some pockets of counties with a high proportion of diabetes in South Dakota, Arizona, Montana, etc.
We also wanted to plot counties labeled as a food desert and compare it with Figure 2. Food deserts don’t have nutritious food that is affordable and we hypothesize that it could be a strong predictor of diabetes.

Figure 3: The counties colored in red are classified as being a food desert.

Looking at Figure 3, we see many counties that are classified as food deserts in the West and portions of the South, such as Texas. Even though this is not closely aligned with our suspicions that food deserts and diabetes are highly related, we predict the counties’ other features such as income, population, age and race, in combination with classification of their food deserts, could still be useful predictors of diabetes prevalence.

Data Preprocessing and Pipelines:
	After our initial data preprocessing and creation of the merged data.frame object, we built a few pipelines in order to test possible models for our data. All pipelines used the same 
VectorAssembler() and StandardScaler() for the standard deviation for all models.

Random Forest / Decision Tree:
Using a [0.7,0.3] split for the training and testing data, we scaled the data to run PCA on the scaled dataframe. We then created a pipeline utilizing the StandardScaler(), PCA, featureIndexer, and Decision Tree as our testing model. We were using the Decision Tree with PCA as the benchmark model so we omitted any kind of parameter grid as changing the maxCategories in the model would not have changed the output at all. Excluding running a PCA, we used a similar pipeline to run our random forest model.
To test the models, we ran both R^2 to find the variance explained, as well as the RMSE just to make sure that the model was a good fit for our dataset.

Linear Regression:
	After splitting the data with a [0.7, 0.3] split into a training and testing set, we created a pipeline using the StandardScalar() and LinearRegression() functions. In order to ensure that we have used the most accurate parameters possible, we also used a parameter grid using the ParamGridBuilder() function and the CrossValidator() in combination.
	Once we ran the cross validation, we found that the best parameters for our linear regression model are as follows: a regression parameter of 0.01, an elastic parameter of 0.2, including an intercept. This model was used on the testing data and an adjusted R^2 value was calculated in order to objectively measure the fit of the model to our data. 
III. Results
Models and Results:
We decided that the most relevant models would be supervised learning models since we have information about the output that we are interested in. Of these supervised models, we decided to test our data using regression models since these models are best for quantitative data, and the variable we sought to predict is quantitative.  Specifically, we aimed to predict the rate of diabetes (a continuous variable), as predicted by the different food accessibility features. While the diabetes rate variable could have been modified for low, medium, and high rates so as to pursue a classification model, we found that results are more meaningful and relevant to the given audience if we continued to use the variable in its original continuous form. 
We chose a train_test split of 0.7/0.3 because our dataset was rather large, allowing us to have a training dataset smaller than 0.8 without significantly weakening model performance. 

Champion Model:
	Because the linear regression model contains several parameters that require tuning in order to ensure optimal model performance, ideal values for the parameters were chosen through cross validation, shortly after training the model on the training set. For each of the three parameters in our linear regression model, we created a ParamMap, or a parameter grid, which included several possible values for us to test in our model.  The possible parameter values are listed below:

regParam: 0.3, 0.1, 0.01
elasticNetParam: 0, 0.2, 0.5, 0.8, 1
Intercept: TRUE, FALSE

	Via cross-validation, we were able to determine that the optimal parameters for our linear regression model were a regParam of 0.01, an elasticNetParam of 0 and the inclusion of an intercept. The elasticNetParam being set to 0 indicates a full ridge regression. Our use of cross-validation improved our model performance for the initial hypothesized parameters. We report the coefficients obtained from the model in Table 2 below.

Table 2: Linear Regression Coefficients.

Model Comparison:
Model
Adjusted R^2
Decision Tree w/ PCA
0.744
Random Forest 
0.816
Linear Regression
0.863


IV. Conclusion 
From Table 2, we can see that the most important predictors are PCTGQTRS (percentage of population living in group quarters), OHU2010 (occupied housing units), TractSNAP (houses that receive SNAP benefits) and TractNHOPI (percentage of Native Hawaiian and Pacific Islander). 
Group homes include correctional facilities, assisted living and treatment centers. We suspect that individuals that live in group homes are often elderly or low income individuals who are in need of government subsidized treatment facilities or shelters. Accordingly, it is possible that group homes are a strong indicator because people who are in poor health and who often have little access to resources tend to live in these environments.
The occupied housing unit variable was the most significant negative coefficient, meaning that in areas with higher rates of occupied housing units, we see a smaller proportion of people with diabetes. This makes sense, since if most apartments and houses in a certain area are inhabited, it suggests that the same people who can afford the rent or mortgage payment can likely also afford a healthy lifestyle.
SNAP is the Supplemental Nutrition Assistance Program (commonly known as food stamps) that provides funding for food to low income individuals. Our linear regression model has a high positive coefficient for the percentage of people receiving SNAP benefits, which means this is associated with a higher proportion of diabetes. 
Since TractNHOPI is a significant positive coefficient, we recommend increased government support and attention to counties with a large Native Hawaiian and Pacific Islander population.
We also notice that the Food Desert coefficient is close to zero, indicating that it is of low significance. We noticed this in our exploratory analysis.  When comparing the two maps of the United States (Figure 2 and Figure 3), we observed no obvious patterns of overlap between counties with high diabetes and counties that are food deserts.
One surprising result of our model is that neither PovertyRate nor MedianFamilyIncome are significant variables. This is likely because small changes in poverty rate and income do not drastically affect overall diabetes rates.  However, even when applying a different scale, such as per thousand dollars, the relationship remains rather minimal. A measure of income level is still contained within the TractSNAP and PCTGCTRS variables, but we would nonetheless expect our income and poverty measures to be directly related. Future modeling and analysis could consider other measures of income and explore interactions between these measures and other variables in the model.

