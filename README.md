# Calculating Cancer Rates with Boosted Decision Trees


## Motivation

My Galvanize capstone project is to investigate environmental factors and cancer rates in California counties from 2001 to 2012 using supervised learning techniques, specifically looking into types of gradient boosting. Boosting models are a go to base layer along with neural nets for most ensemble models in Kaggle competitions by Grand Masters. [6] This project will use different boosting models with different parameters to reduce root mean square error in test predictions. 2017 saw a new  boosting model hailing from Russian company Yandex called Catboost (Cat stands for Categorical); this model is supposed to compete well and even surpass XGBoost, the current all star.

Our environment plays a very important role in our life and thus our health. According to the WHO, as much as 24% of all disease is caused by environmental exposures that could have been averted. [5] New research keeps getting published about cancer and environmental pollution and some of this data is now being collected by government departments, especially in California. The Surveillance, Epidemiology, and End Results program (SEER) has about 10 million cases of cancer in their database ranging over 11 states, some starting in the 1970s. The last California counties to join this program (mandated by the state) happened in 2001 and since that year they have logged 2.5 million cancer incidents. This might seem like a lot for a state close to 40 million people, but the American Cancer Society states that 40% of American people will receive a diagnosis of cancer in their lifetime (½ of all men and ⅓ of all women). [4] Cancer rate is typically measured as incidents (new cases) per 100,000 and so will this project. This measure should not be confused with prevalence, total current cases. 

The first goal was to find the average yearly California incident rate per county. The histogram below is a plot of the different incidents and the average is 542 cases per 100,000, ranging from 200 to 1,000.

![Cancer Distribution Rate](https://github.com/DataDanD/CancerCapstone/blob/master/Graphs/Updated/CancerIncidents.png)
(Make graph bigger and better looking, delete cancer rate from X, maybe add y label)





## Questions

-Can a supervised learning algorithm predict cancer rates at a county level?

-What model technique will give the lowest root mean squared error (RMSE)?

-What environmental factors will be important for prediction accuracy?

-Why do cancer rates differ in California counties? 

This following is a box plot demonstrating the variety of county cancer incidents within California.

![Cancer Box Plot](https://github.com/DataDanD/CancerCapstone/blob/master/Graphs/Updated/CountyCancer.png)

County Fips code is used in this project to keep county names simple. FIPS (Federal Information Processing Standard) Code is used by the government and is 5 digits when you concatenate the 2 digit code for the state ID and 3 digit code for the county. This numbering systems helps identify counties with the same name that are in different states by using a unique ID system. The term County Fips (and in some files C Fips) will be used in this project, as we are only looking at one state and the California code of 06 was delete from all data for the merging process. Link to [California Fips dictionary](https://www.weather.gov/hnx/cafips), if interested in certain location.





## Data

Most datasets were collected from open source government data at data.gov. [1] The government data is from surveys, reported by companies, or collected from different agencies. The columns and rows in the table below are recorded after filtering for California and for years 2001-2012. 

![Data Table](https://github.com/DataDanD/Cancer-Capstone-Portfolio/blob/master/Graphs/DataTable.png)

Accessing the SEER cancer data requires a signed wavier, therefore I am not allowed to share the file. However, Cancer.csv is the final file and is on the home page. It has the cancer incident vales the models later on are trying to predict as well as all the features used to make these predictions. Final data frame is 627 rows with 28 columns (the y value being predicted is 'Cancer Rate' and drop 'Count' to prevent data leakage). There are 58 counties in California and 11 years of data, there should be 638 rows, but one county was missing Radon data and was dropped from this experiment.

Unfortunately some of the main data used stopped in 2012, while other data is updated regularly. The data is grouped by county because, the SEER data (the Y value the models are predicting) is at the county level. The Fracking Wells and Superfund Sites data that researchers scrapped from the web and uploaded to Kaggle has GPS coordinates, but it is impossible to tell which SEER patients in those counties lived closer to these locations.

For more information on each datasets used, including a link to each source, check out [Data.md](https://github.com/DataDanD/Cancer-Capstone-Portfolio/blob/master/Data.md) on the home page.





## Pipeline

### Preprocessing 

1) Find useful environmental and socioeconomic public datasets, in any format
2) Turn all downloaded files into data frames with Pandas, an awesome Python package
3) Exploratory Data Analysis (EDA) to see if file has usable features to use in modeling
4) Remove any inappropriate states and/or years, but keep all columns for now
5) Save info as CSV file for a checkpoint incase of future problems or to reference
6) Decide on appropriate columns for modeling process, drop features that will not be used 
7) Group data by county and by year when possible to get rows ready for merge
8) Group spare features with count or sum when to many exist (group chems by carcinogen, sum pounds)
9) Pivot columns that have multiple features into separate columns, drop new features that are not needed)
10) Match County Fips, delete 6000 to lose state ID from ints or change strings to match Fips file and merge #
11) Merge final (14) data frames to SEER data to create Cancer.csv (merge to the left each time of C Fips)
12) Feature engineering and more filtering to prevent multicollinearity and data leakage for good modeling

### Modeling

1) Get base measure with a grid searched (3 boolean parameters) linear regression model 
2) Look into performance of Sk-Learn regression models & other boosting packages (XGBoost, CatBoost, LightGBM)
3) Study different models to see how they using the data to make cancer incident predictions
4) Grid search on models with low RMSE to find best parameters and finally the best model 
5) Get feature importance with recursive feature selection to see what data is most useful





## EDA

(Currently updating section along with hyperlinked jupyter notebooks)

[Air Quality](https://github.com/DataDanD/Cancer-Capstone-Portfolio/tree/master/Jupyters) / [Fracking](https://github.com/DataDanD/Cancer-Capstone-Portfolio/tree/master/Jupyters) / [Superfunds](https://github.com/DataDanD/Cancer-Capstone-Portfolio/tree/master/Jupyters) / [Radon](https://github.com/DataDanD/Cancer-Capstone-Portfolio/tree/master/Jupyters) / [TRI](https://github.com/DataDanD/Cancer-Capstone-Portfolio/tree/master/Jupyters)
[Cancer Rates and Graphs](https://github.com/DataDanD/Cancer-Capstone-Portfolio/tree/master/Jupyters)

We can see some nice correlations between cancer incidents and features as we look at a heatmap and we can look at some of these variables closer with a pairplot. We can also see some of the features correlate, this is call multicolinearity and is not a good thing for machine learning algorithms. Lighter and darker boxes on the heatmap are not a good thing, except for the diagalon going across the middle where features intersect with eachother. Explain why its bad. 

![Heatmap of All Features](https://github.com/DataDanD/CancerCapstone/blob/master/Graphs/heatmap.png)

Explain some similarity.
Pair plot explaination
high and low rates explained

![Pair Plot](https://github.com/DataDanD/CancerCapstone/blob/master/Graphs/Updated/Pair4.png)

Major depression and cancer incidents





## Models

I will start with a normal linear regression gridsearched, as baseline. The gridsearch foung that setting fit_intercept=True, normalize=False, and copy_X=True will yeild the best RMSE score.

![Lin Predicting](https://github.com/DataDanD/CancerCapstone/blob/master/Graphs/Updated/Lin.png)

Not bad, but lets see how well boosting can perform on these predictions. I will try Random forest, gradient boosting, addaboost, and catboost with default parameters and I am curious to see feature importance. Feature importance in these tree models refers to where splits of the tree happen. Splits are decided based on how well the model can best gain information.

feature relevance on boosting with default parameters

![Boosting Models Rel](https://github.com/DataDanD/Cancer-Capstone-Portfolio/blob/master/Graphs/PosterBoosting.png)





## Results

I picked gradient boosting because catboost takes way longer to gridsearch.

gridsearch parameters Gradient Boosting
parameters = {

     'max_depth':[3], #[2,3,4,5,6,7,8]
     
     'max_features':['auto'], #['sqrt','log2','auto']
     
     'min_samples_leaf':[4], #[1,2,3,4,5]
     
     'min_samples_split':[2], #[2,3,4,5]
     
     'n_estimators': [1600] #[600,900,1100,1200,1300,1400,1500,1600]
}

![distribution predictions (Boosting)](https://github.com/DataDanD/Cancer-Capstone-Portfolio/blob/master/Graphs/PosterPredicting.png)

![Feature importance](https://github.com/DataDanD/Cancer-Capstone-Portfolio/blob/master/Graphs/PosterImportance.png)





## Discussion

This project was supposed to look at specific chemicals from the Toxic Release Inventory data from the EPA and specific cancers, but the data was sparse and not all toxins released into our environment have to be reported. Adding socioeconomic data made the model more accurate and reliable. The rows with the smaller counties could have been dropped, then it would have be possible to add more features: BMI, blood pressure, smoking. Fracking wells and superfund sites information would have performed better, if the cancer data was mapped to an area smaller than the county level. There may be some data leakage with major depression, as cancer can lead to depression. CatBoost / XGBoost / LightGB could have been good alternatives to gradient boosting, but they take longer to gridsearch for optimal parameters and implement.

![Cancer and Medicare](https://github.com/DataDanD/CancerCapstone/blob/master/Graphs/Updated/CanInMedPop.png)





## Conclusion 

Boosting works with this regression problem. The final model has variance of +/- 5 RMSE. Medicare population and county FIPS are the features that matter the most in predicting cancer incidence rates for these models. More data is needed to determine relationships between diseases and the environment. 





### Future Steps

EDA in Jupyter Notebooks explained

Partial Dependency Plots to EDA section (script written)

Bayesian Probabilistic Modeling with PyMC3

Clean graph folder





## References
[1] https://www.data.gov/

[2] https://seer.cancer.gov/data/

[3] https://www.epa.gov/

[4] https://www.cancer.org/cancer/cancer-causes.html

[5] http://www.who.int/mediacentre/news/releases/2006/pr32/en/ 

[6]http://blog.kaggle.com/2017/01/23/a-kaggle-master-explains-gradient-boosting/ 

