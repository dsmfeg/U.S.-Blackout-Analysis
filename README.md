### U.S.-Blackout-Analysis
Devin Ellison

## Introduction 
The [dataset](https://engineering.purdue.edu/LASCI/research-data/outages), includes major outages across the U.S. from 2000 to 2016. Also included is geographical and economical information about the outages related to the state it occured in.

The columns of the Data we will be using are as follows
| Column | Description |
| ----------- | ----------- |
| 'YEAR' | Year the outage occured |
| 'MONTH' | Month the outage occured (in numerical value: Jan = 1) |
| 'U.S._STATE' | U.S. state the outage occured in |
| 'CLIMATE.REGION' | National Centers for Environmental Information CLIMATE.REGION of the associeated state |
| 'OUTAGE.START.DATE' | Date the outage started on |
| 'OUTAGE.START.TIME' | Time the outage started at |
| 'OUTAGE.RESTORATION.DATE' | Date the outage was restored on |
| 'OUTAGE.RESTORATION.TIME' | Time the outage was restored at |
| 'ANOMALY.LEVEL' | Oceanic El Niño/La Niña (ONI) index |
| 'CLIMATE.CATEGORY' | Cause of the outage |
| 'CAUSE.CATEGORY.DETAIL' | More detailed cause, such as Hurricane under Severe Weather |
| 'TOTAL.PRICE' | Average energy cost in the state |

Using this dataset I want to analyze the effect of weather as a cause of outages. First I will clean the dataset and remove unnesessary columns, then look at some interesting connections between the data. Secondly I will examine the missingness of one of the columns using a permutation test. Lastly, I will do a hypothesis test on two distributions, and create a Random Forest Classifier to predict the weather cause of an outage, checking it's fairness against a column.


## Data Cleaning and Exploratory Data Analysis

# Data Cleaning
1. Firstly, I dropped any unnecessary columns, which were not included above. All of columns dropped are as follows: 'OBS', 'POSTAL.CODE', 'NERC.REGION', 'HURRICANE.NAMES', 'DEMAND.LOSS.MW', 'RES.PRICE', 'COM.PRICE', 'IND.PRICE', 'RES.SALES',  'COM.SALES', 'IND.SALES', 'TOTAL.SALES', 'RES.PERCEN', 'COM.PERCEN',  'IND.PERCEN', 'RES.CUSTOMERS' ,'COM.CUSTOMERS', 'IND.CUSTOMERS', 'TOTAL.CUSTOMERS', 'RES.CUST.PCT', 'COM.CUST.PCT', 'IND.CUST.PCT', 'PC.REALGSP.STATE', 'PC.REALGSP.USA', 'PC.REALGSP.REL', 'PC.REALGSP.CHANGE', 'UTIL.REALGSP', 'TOTAL.REALGSP', 'UTIL.CONTRI', 'PI.UTIL.OFUSA', 'POPULATION', 'POPPCT_URBAN', 'POPPCT_UC', 'POPDEN_URBAN', 'POPDEN_UC', 'POPDEN_RURAL', 'AREAPCT_URBAN', 'AREAPCT_UC', 'PCT_LAND', 'PCT_WATER_TOT', 'PCT_WATER_INLAND'

2. Second, I combined 'OUTAGE.START.DATE' and 'OUTAGE.START.Time' into a pd.DateTime object, and similarly with 'OUTAGE.RESTORATION.DATE' and 'OUTAGE.RESTORATION.Time'

My dataframe ends up looking like this:
|   YEAR |   MONTH | U.S._STATE   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   TOTAL.PRICE | START               | END                 |
|-------:|--------:|:-------------|:-------------------|----------------:|:-------------------|:-------------------|:------------------------|------------------:|---------------------:|--------------:|:--------------------|:--------------------|
|   2011 |       7 | Minnesota    | East North Central |            -0.3 | normal             | severe weather     | nan                     |              3060 |                70000 |          9.28 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00 |
|   2014 |       5 | Minnesota    | East North Central |            -0.1 | normal             | intentional attack | vandalism               |                 1 |                  nan |          9.28 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00 |
|   2010 |      10 | Minnesota    | East North Central |            -1.5 | cold               | severe weather     | heavy wind              |              3000 |                70000 |          8.15 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00 |
|   2012 |       6 | Minnesota    | East North Central |            -0.1 | normal             | severe weather     | thunderstorm            |              2550 |                68200 |          9.19 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00 |
|   2015 |       7 | Minnesota    | East North Central |             1.2 | warm               | severe weather     | nan                     |              1740 |               250000 |         10.43 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00 |

# Univariate Analysis
I wanted to look at the number of blackouts over time in months and in years to see if there are any standouts. You can see that 2011 stands out a lot, and seems to have an effect in a few of the years that followed as well before it got back down to normal levels.

<iframe
  src="assets/2.1"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I also wanted to do the same but for months instead. There is some pattern of the hotter and colder months in Summer and Winter having more blackouts, likely due to severe weather. But the difference isn't too great.

<iframe
  src="assets/2.2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Bivariate Analysis
I wanted to see both months and severe weather together to see if my assumption based on the previous month graph is correct. Most of the causes are pretty linear, but there is definitely a pickup in severe weather at least over the summer months.

<iframe
  src="assets/2.3.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I also wanted to see now the main cause category detail over the months, based on this, because I have a feeling the reason it spikes in June is because hurricane season is happening. I'm pretty surprised to see that it's actually thunderstorm with 63 outages. In hindsight it does make sense, and I wonder if this large amount from thunderstorm is coming from the midwest Tornade belt region

<iframe
  src="assets/2.4.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Interesting Aggregates
To check the previous thunderstorm discovery, I wanted to group CLIMATE.REGION and the details of severe weather to see where the thunderstorms are mainly coming from. Looking at thunderstorm, there is a large amount coming mainly from the two Central regions and ther North East, which largely matches with my assumption.

|   earthquake |   flooding |   fog |   hailstorm |   heatwave |   heavy wind |   hurricanes |   lightning |   public appeal |   snow/ice  |   snow/ice storm |   storm |   thunderstorm |   thunderstorm; islanding |   tornadoes |   uncontrolled loss |   wildfire |   wind |   wind storm |   wind/rain |   winter |   winter storm |
|-------------:|-----------:|------:|------------:|-----------:|-------------:|-------------:|------------:|----------------:|------------:|-----------------:|--------:|---------------:|--------------------------:|------------:|--------------------:|-----------:|-------:|-------------:|------------:|---------:|---------------:|
|          nan |        nan |   nan |         nan |          1 |           11 |            4 |         nan |             nan |         nan |              nan |       8 |             48 |                       nan |           2 |                 nan |          2 |    nan |            2 |         nan |        7 |             17 |
|          nan |          2 |   nan |           2 |        nan |           12 |          nan |         nan |             nan |           2 |                1 |       7 |             33 |                       nan |           2 |                 nan |        nan |    nan |          nan |           3 |      nan |             12 |
|          nan |          1 |   nan |         nan |          1 |            8 |           20 |         nan |             nan |           6 |              nan |      11 |             42 |                         1 |         nan |                   1 |          2 |    nan |            1 |           6 |        7 |             28 |
|          nan |        nan |   nan |         nan |        nan |           14 |          nan |         nan |             nan |         nan |              nan |     nan |            nan |                       nan |         nan |                 nan |          1 |      1 |          nan |         nan |      nan |              4 |
|          nan |          1 |   nan |           1 |        nan |            5 |           22 |           2 |               1 |           1 |              nan |       3 |             23 |                       nan |           2 |                   1 |          1 |      1 |          nan |         nan |        3 |             13 |

## Assessment of Missingness
A few different columns have missing values in the dataset, but all of the price and percent columns are N/A together which I find interesting. I imagine it might have to do with the company related to the outage, so it is likely MNAR. I would really like to see the data of how they got the price values, and what companies they are associated with. However there was a corellation between the missingness of 'TOTAL.PRICE' and anomaly level. After running a permutation test, I got the values:

Observed KS statistic: 0.4683
Permutation p-value: 0.0000

This shows that the column may actually be MAR, and the price information was less likely reported for outages of standout anomaly. This can be observed on the graph:

<iframe
  src="assets/3.1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing
The U.S. is a unique country for many reasons, one of these being it's size and diverse enviorenments across many states. In general though, seperating the states to West and East, I imagined there wouldn't be a significant different between the number outages between the two. My first thought was hurricanes, which I believed would skew it a lot to the East, but looking at the previous shows that California alone might carry the Western outages back to a neutral standpoint due to how many we get. 

Null Hypothesis: Severe weather outages are equally common in the East and the West.
Alternate Hypothesis: Severe weather outages are more common in one region than the other (East vs. West).

I seperated the states to East and West based on their 'CLIMATE.REGION' as follows:
- "East North Central": "East",
- "Central": "East",
- "South": "East",
- "Northeast": "East",
- "Southeast": "East",
- "Northwest": "West",
- "Southwest": "West",
- "West North Central": "West",
- "West": "West"

I then ran a permutation test using the difference in proportions of outages of east and west, which gave me a p-value of 0.000. This makes sense as the observed value was about 0.35, showing that the East does get a lot more severe weather outages than the west, despite California having a lot itself.

## Prediction Problem
Based somehwat on the hypothesis test, but mainly on the pivot table in the data analysis, there is likely a corellation bewteen the type of weather and the location of the blackout. I imagine if you told me that it was a hurricane, along with some other features, I could tell you it was in the south and what states it was. Similarly if it was a Tornado it would likely be the Midwest. Based on this, I want to try and predict the state the blackout happened in using other columns. To do this I will use a multiclass Random Forest Classifier. 

## Baseline Model
I made the basic Random Forest Classifier using the columns; 
- 'CUSTOMERS.AFFECTED' (Quantitative)
- 'ANOMALY.LEVEL' (Quantitative)
- 'OUTAGE.DURATION' (Quantitative)
- 'YEAR' (Quantitative)
- 'MONTH' (Quantitative)
to predict the 'US._STATE'. Despite the idea, the accuracy was pretty terrible at a 0.1699. Obviously I do not think the model is good.

## Final Model
I realised I could try adding the start time as well, as a lot of the blackouts I've experienced were more towards nighttime. I figured events like severe weather or other causes may break that and occur at more unique times, which could help the model at understanding the occurence of the outage better. Also since if it occured at 6 pm Eastern Time when a lot of lights are starting to turn on and increase the power being drawn, it's still going to be 3 pm on the West coast and likely not where the outage occured. 

To do this i just had to transform the pd.DateTime objects to get the hour it happened at. I figured the difference in minute or date wouldn't mean as much, since I already had the 'YEAR' and 'MONTH' column, and since minutes probably doesn't affect much.

I also realized that after thinking so much about how the weather could be affecting the outages, It doesn't understand what's actually causing it since I didn't include the cause category as a column. Due to this I decided to OneHotEncode the 'CAUSE.CATEGORY.DETAIL' column, so it knows whether it was a hurricane, intentional attack, or other.

I used GridSearchCV to optimize the max_depth of the classifier, which it determined to only be a max depth of 5, which I found interesting, since I would've thought something more like 10 or 20 would be better without it being too overfit.

While it didn't do too much, it did increase the accuracy to a 0.26666666666666666, just about 0.1 better than before.


## Fairness Analysis
I decided to do my groups for the fairness analysis as day and night. This is classified as day being 9am to 7pm inclusive, and night as everything else. I'm using the weighted F1-score of the classifier’s predictions (predicted) compared to the true labels (observed) within each group to check the fairness. Using the absolute difference in weighted F1-scores between day and night as my test statistic at a significance level of a=0.05. 

Null Hypothesis: Any observed difference in weighted F1-score between the two groups is due to random chance.
Alternate Hypothesis: There is a real difference in weighted F1-scores between the two groups.

After doing the permutation test, I got:
Observed F1 difference (day vs night): 0.0199
Permutation test p-value: 0.0000

With this, we can see that there is strong evidence that the model’s performance differs between day and night outages. The observed difference in weighted F1-score is unlikely to have occurred by chance alone, suggesting a potential fairness or performance disparity across these groups.
