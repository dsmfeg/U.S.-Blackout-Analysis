### U.S.-Blackout-Analysis
Devin Ellison

## Introduction 
The [dataset]([https://www.example.com](https://engineering.purdue.edu/LASCI/research-data/outages)), includes major outages across the U.S. from 2000 to 2016. Also included is geographical and economical information about the outages related to the state it occured in.

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
  src="/assets/2.1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I also wanted to do the same but for months instead. There is some pattern of the hotter and colder months in Summer and Winter having more blackouts, likely due to severe weather. But the difference isn't too great.

<iframe
  src="/assets/2.2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Bivariate Analysis
I wanted to see both months and severe weather together to see if my assumption based on the previous month graph is correct. Most of the causes are pretty linear, but there is definitely a pickup in severe weather at least over the summer months.

<iframe
  src="/assets/2.3.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I also wanted to see now the main cause category detail over the months, based on this, because I have a feeling the reason it spikes in June is because
#hurricane season is happening. I'm pretty surprised to see that it's actually thunderstorm with 63 outages. In hindsight it does make sense, and I wonder if this large amount from thunderstorm is coming from the midwest Tornade belt region

<iframe
  src="/assets/2.4.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#Interesting Aggregates
