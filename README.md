# Power-Outage-Analysis
> *This is a project for DSC 80 at UCSD.*
<br>
---

## Introduction

Welcome to the "Power-Outage-Analysis" project repository! This project aims to explore the relationship between fuel supply emergencies and the duration of power outages. 

The main question that drives our analysis is: **"Does fuel supply emergency lead to longer power outage durations?"** We seek to investigate whether power outages caused by fuel supply emergencies tend to have longer durations compared to other causes of outages. By examining this relationship, we can gain insights into the impact of fuel supply emergencies on the reliability and resilience of the power grid.
<br>

### Overview of the Dataset
The dataset used in this analysis provides valuable information on power outage events, regional climate characteristics, regional electricity consumption, and regional economic indicators (more information about the data can be obtained at https://www.sciencedirect.com/science/article/pii/S2352340918307182#bib6).

This outage dataset contains (1540 rows, 57 columns). Though the dataset contains a lot of valuable information, we will only be keeping the 13 columns that
contain useful information for our analysis. 

- YEAR: Indicates the year when the outage event occurred
- HURRICANE.NAMES: 
- U.S._STATE: Represents all the states in the continental U.S.
- CLIMATE.REGION: U.S. Climate regions as specified by National Centers for Environmental Information
- ANOMALY.LEVEL: This represents the oceanic ONI index referring to the cold and warm episodes by season.
- CLIMATE.CATEGORY: This represents the climate episodes corresponding to the years. The categories Warm, Cold or Normal episodes of the climate.
- CAUSE.CATEGORY: Categories of all the events causing the major power outages
- CAUSE.CATEGORY.DETAIL: Detailed description of the event categories causing the major power outages
- OUTAGE.DURATION: Duration of outage events (in minutes)
- CUSTOMERS.AFFECTED: Number of customers affected by the power outage event
- OOUTAGE.START.DATE: This variable indicates the day of the year when the outage event started (as reported by the corresponding Utility in the region)
- OUTAGE.START.TIME: This variable indicates the time of the day when the outage event started (as reported by the corresponding Utility in the region)
- OUTAGE.RESTORATION.DATE: This variable indicates the day of the year when power was restored to all the customers
- OUTAGE.RESTORATION.TIME: This variable indicates the time of the day when power was restored to all the customers
- TOTAL.PRICE: Average monthly electricity price in the U.S. state (cents/kilowatt-hour)
<br>

---

## Cleaning and EDA (Exploratory Data Analysis)
<br>

### Data Cleaning
> This dataset contains many null data and unused columns. In order to make the analysis process easier, we should first clean the data. Here is a snippet of the original dataframe (1540 rows, 57 columns).

---

|    | Major power outage events in the continental U.S.                                                           | Unnamed: 1   | Unnamed: 2   | Unnamed: 3   | Unnamed: 4   |
|---:|:------------------------------------------------------------------------------------------------------------|:-------------|:-------------|:-------------|:-------------|
|  0 | Time period: January 2000 - July 2016                                                                       | nan          | nan          | nan          | nan          |
|  1 | Regions affected: Outages reported in this data file affected a single U.S. state at the time of occurrence | nan          | nan          | nan          | nan          |
|  2 | nan                                                                                                         | nan          | nan          | nan          | nan          |
|  3 | nan                                                                                                         | nan          | nan          | nan          | nan          |
|  4 | variables                                                                                                   | OBS          | YEAR         | MONTH        | U.S._STATE   |
|  5 | Units                                                                                                       | nan          | nan          | nan          | nan          |

<br>

---

**1. Correct the index and columns (set column names, remove invalid rows, remove original index from excel file)**

Since the dataset is imported from an excel file, there are some formatting issues that need to be fixed. These operations will return a dataframe with the correct index and column names. Specifically, we will remove the first 4 lines of NaNs, remove the NaN row at index=5, remove two useless columns, set the column names to the values in the first row, remove the first row (index=4), and finally reset the index. 

**2. Convert START and RESTORATION times and dates to timestamp.Create new columns "OUTAGE.START" and "OUTAGE.RESTORATION".**

The columns OUTAGE.START.DATE, OUTAGE.START.TIME, OUTAGE.RESTORATION.DATE, and OUTAGE.RESTORATION.TIME are string type variables and contain similar information types. To make these information easier to understand and work with, we should combine and convert them to timestamp objects.

**3. Drop rows that have more than 10 missing values**

Browsing through the dataset, we see some rows with a lot of missing information. These rows assumingly do not contain as much useful information compared to others. I assume that these null values exist because such information was never recorded and does not depend on any other columns or the missing value itself. Thus, I will remove the rows that have more than 10 missing values to make the dataset more clear.

**4. Filter out unneeded columns, keep columns that are relevant the answering the question only**

Since our main focus in this analysis is duration time and its causes, we should delete the columns that will not give us any useful information regarding answering out question. (columns to keep: ["YEAR","HURRICANE.NAMES", 'U.S._STATE','CLIMATE.REGION', 'ANOMALY.LEVEL', 'CLIMATE.CATEGORY', 'CAUSE.CATEGORY', 'CAUSE.CATEGORY.DETAIL', 'OUTAGE.DURATION', 'CUSTOMERS.AFFECTED', 'OUTAGE.START', 'OUTAGE.RESTORATION', "TOTAL.PRICE"])

**5. Convert datatypes of columns**

When checking the datatypes of columns, we see that many numbers that should be numerical are saved as string objects. In order to have data that are computable and can bring us more information, we should convert them to float.

**6. impute Hurricane Names to "Not Applicable" for ones not cuased by hurricanes**

the Hurrican names column is missing at random. The missingness of hurricane names depends on CAUSE.CATEGORY.DETAIL. This is very straighforward. The power outages that are not caused by hurricanes will not have hurricane names. However, there are instances where it is caused by a hurricane but the name was not recorded. In order to distinguish these missingness, we should set the hurricane names that are not caused by hurricanse to not applicable.

**7. five occurrences of missing region** 

There are five occurences of missing regions, and with closer inspection, we see that they all have Hawaii as there state. This data is missing at random since it depends on the STATE column. Through a quick google search, Hawaii belongs to the "Pacific" Region. So all we have to do to resolve this missingness issue is to impute it with the correct region.

**8.Since we are trying to answer a question related to duration time (which is continuous), it is helpful to bin them into categories to better visualize them.**

So we will create a new column that stores the durations as one of four categories: ['Less than a week', 'One to two weeks', 'Two to three weeks', 'More than three weeks']

<br>

---
<br>

> ### After the cleaning operations, we get a clear dataset: 


|   index |   YEAR | HURRICANE.NAMES   | U.S._STATE   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED | OUTAGE.START        | OUTAGE.RESTORATION   |   TOTAL.PRICE | OUTAGE.DURATION.WEEKS   |
|--------:|-------:|:------------------|:-------------|:-------------------|----------------:|:-------------------|:-------------------|:------------------------|------------------:|---------------------:|:--------------------|:---------------------|--------------:|:------------------------|
|       0 |   2011 | Not Applicable    | Minnesota    | East North Central |            -0.3 | normal             | severe weather     | nan                     |              3060 |                70000 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |          9.28 | Less than a week        |
|       1 |   2014 | Not Applicable    | Minnesota    | East North Central |            -0.1 | normal             | intentional attack | vandalism               |                 1 |                  nan | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |          9.28 | Less than a week        |
|       2 |   2010 | Not Applicable    | Minnesota    | East North Central |            -1.5 | cold               | severe weather     | heavy wind              |              3000 |                70000 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |          8.15 | Less than a week        |
|       3 |   2012 | Not Applicable    | Minnesota    | East North Central |            -0.1 | normal             | severe weather     | thunderstorm            |              2550 |                68200 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |          9.19 | Less than a week        |
|       4 |   2015 | Not Applicable    | Minnesota    | East North Central |             1.2 | warm               | severe weather     | nan                     |              1740 |               250000 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |         10.43 | Less than a week  


---
<br>
<br>
<br>


## Univariate Analysis

1. This graph presents the distribution of customers affected. As expected, there are more occurrences where less customers are affected. It is rare to have a large power outage effecting many people. Since one power station can be responsible for the electricity of many households, the occurrences of some extreme numbers are reasonable. 

<iframe src="Distribution_of_Customers_Affected.html" width=800 height=600 frameBorder=0></iframe>
