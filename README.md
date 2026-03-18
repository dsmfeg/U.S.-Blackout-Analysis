### U.S.-Blackout-Analysis
Devin Ellison

#Introduction
The [dataset]([https://www.example.com](https://engineering.purdue.edu/LASCI/research-data/outages)), includes major outages across the U.S. from 2000 to 2016. Also included is geographical and economical information about the outages related to the state it occured in.

The columns of the Data we will be using are as follows
| Column | Description |
| ----------- | ----------- |
| 'YEAR' | Year the outage occured |
| 'MONTH' | Month the outage occured (in numerical value: Jan = 1) |
| 'U.S._STATE' | U.S. state the outage occured in |
| 'CLIMATE.REGION' | National Centers for Environmental Information CLIMATE.REGION of the associeated state |
| 'ANOMALY.LEVEL' | Oceanic El Niño/La Niña (ONI) index |
| 'CLIMATE.CATEGORY' | Cause of the outage |
| 'CAUSE.CATEGORY.DETAIL' | More detailed cause, such as Hurricane under Severe Weather |
| 'TOTAL.PRICE' | Average energy cost in the state |

Using this dataset I want to analyze the effect of weather as a cause of outages. First I will clean the dataset and remove unnesessary columns, then look at some interesting connections between the data. Secondly I will examine the missingness of one of the columns using a permutation test. Lastly, I will do a hypothesis test on two distributions, and create a Random Forest Classifier to predict the weather cause of an outage, checking it's fairness against a column.

#
