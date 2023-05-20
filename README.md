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


### Univariate Analysis

1. This graph presents the distribution of **customers affected**. As expected, there are more occurrences where less customers are affected. It is rare to have a large power outage effecting many people. Since one power station can be responsible for the electricity of many households, the occurrences of some extreme numbers are reasonable. 

<iframe src="Distribution_of_Customers_Affected.html" width=800 height=600 frameBorder=0></iframe>

2. This graph presents the distribution of **outage durations**. This histogram shows that it is more probable to have an outage that lasts for a very short time period. However, there are a few cases of extreme durations. We need to check data to see if these are false entries.

<iframe src="DDistribution_of_outage_duration.html" width=800 height=600 frameBorder=0></iframe>

3. This graph represents the distribution of **anomaly levels** (mean cold and warm episodes by season). This information presents the weather of different regions. By viewing its distirbution, we understand that the weather are almost normally distributed.

<iframe src="Distribution_of_anomoly_level.html" width=800 height=600 frameBorder=0></iframe>

### Bivariate Analysis

1. This graph represents the distribution of **cause categories and durations within each category**. Over all categories, severe weather seems to be the biggest cause of power outages. By looking closely, we see that categories severe weather, intentional attack, system operability disruption, and fuel supply emergency have a visible proportion of outages that took up to three weeks to restore. And categories severe weather, system operability disruption, and fuel supply emergency have a visible proportion of outages lasting over three weeks. This shows that these are the main causes of longer durations in outages.

<iframe src="Distribution_of_cause_categories.html" width=800 height=600 frameBorder=0></iframe>

| OUTAGE.DURATION.WEEKS   |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|:------------------------|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
| Less than a week        |           0.981818  |               0.578947  |           0.994987   |           1 |       0.985507  |       0.913396   |                      0.991667   |
| One to two weeks        |           0         |               0.184211  |           0.00250627 |           0 |       0.0144928 |       0.0703654  |                      0          |
| Two to three weeks      |           0         |               0.157895  |           0.00250627 |           0 |       0         |       0.0108254  |                      0.00833333 |
| More than three weeks   |           0.0181818 |               0.0789474 |           0          |           0 |       0         |       0.00541272 |                      0          |


2. This graph presents the relation between ANOMALY.LEVEL(climate) and OUTAGE.DURATION. It seems like there are **no visible relation** between climate and outage durations. This suggest that climate may not be a main cause of longer outage durations.

<iframe src="relation1.html" width=800 height=600 frameBorder=0></iframe>

3. This graphs presents the relation between outage duration and time (years). It seems like **no impovement** has been made in solving outage issues as there seems to be no relation between years and duration.

<iframe src="relation2.html" width=800 height=600 frameBorder=0></iframe>


## Assessment of Missingness

### NMAR Analysis

I believe that the column CUSTOMERS.AFFECTED is NMAR.

From the missingness summary, we see that there are 437 cases of CUSTOMERS.AFFECTED missing. In this case, it's plausible that the **missingness of the "CUSTOMERS.AFFECTED" variable could depend on the actual missing value**. For example, if a power outage event affects a large number of customers, there might be more attention and effort in accurately recording and reporting the data. On the other hand, **if the outage affects a relatively small number of customers, there might be a lower priority in documenting the specific count.**

Therefore, if there are missing values for "CUSTOMERS.AFFECTED" the chance of the value being missing may depend on the actual number of customers affected. This indicates that the missingness of the variable is not random (NMAR), but rather related to the magnitude or significance of the outage event itself.

If we have data on customer survey response on how they think of the outage resolution, we might be able to explain the missingness in cutomers affected (MAR). This is because, if the outage was small and didn't impact much people, and didn't last for a long time, customers will not have that much complaint compared to larger outages. 

| 4                     |   0 |
|:----------------------|----:|
| YEAR                  |   0 |
| HURRICANE.NAMES       |   2 |
| U.S._STATE            |   0 |
| CLIMATE.REGION        |   5 |
| ANOMALY.LEVEL         |   0 |
| CLIMATE.CATEGORY      |   0 |
| CAUSE.CATEGORY        |   0 |
| CAUSE.CATEGORY.DETAIL | 464 |
| OUTAGE.DURATION       |  48 |
| CUSTOMERS.AFFECTED    | 437 |
| OUTAGE.START          |   0 |
| OUTAGE.RESTORATION    |  48 |
| TOTAL.PRICE           |   0 |
| OUTAGE.DURATION.WEEKS |  48 |

### Missingness Dependency

    We will be analyzing the column OUTAGE.DURATION.   

1. Is the missingness in **outage duration** dependent on **cause category** of the outage?

First, lets look at the distribution of cause categories with missing data and with existing data:

| CAUSE.CATEGORY                |     False |        True |
|:------------------------------|----------:|------------:|
| equipment failure             | 0.0375683 |   0.0416667 |
| fuel supply emergency         | 0.0259563 |   0.25      |
| intentional attack            | 0.272541  |   0.3125    |
| islanding                     | 0.0300546 |   0.0416667 |
| public appeal                 | 0.0471311 | nan         |
| severe weather                | 0.504781  |   0.3125    |
| system operability disruption | 0.0819672 |   0.0416667 |



<iframe src="missing1.html" width=800 height=600 frameBorder=0></iframe>

Looking at the graph, it seems like there is much variation between cause category and duration missingness. Let's preform a permutation test to check missingness dependency. (With a **significance level of 0.05**) And since we are dealing with categorical values, we will use the **total variation distance** as out test statistic.

<iframe src="fig1.html" width=800 height=600 frameBorder=0></iframe>

the p_value is 0.001 which is much smaller than our significance level 0.05. If the p-value is less than or equal to 0.05, we would reject the null hypothesis and conclude that **there is evidence to suggest that the missingness of duration does depend on the cause category**.


2.  Is the missingness in **outage duration** dependent on **total price** of the outage?

Since this time our data is quantitative, we have to create a new method that works with quantitative data. We chose the differences in means as our test statistic and will be using a significance level of 0.05.

irst, lets look at the distribution of total price with missing data and with existing data:

<iframe src="missing2.html" width=800 height=600 frameBorder=0></iframe>

From the graph, we can see that the two distribution have quite different shapes, but the mean is fairly similar, it is hard to see by eye how significant this difference is so we will preform a permutation test. 

<iframe src="fig2.html" width=800 height=600 frameBorder=0></iframe>

The p_value is 0.8, much higher than our significance level. This means that we **do not have enough evidence to conclude that there is dependency relation between outage duration and total price**.


## Hypothesis Testing

Finally after all the preprocessing work, we are ready to answer our main question! **Does fuel supply emergency lead to longer power outage durations?**

To answer the question of whether fuel supply emergencies lead to longer power outage durations, we will perform a hypothesis test using the means of the outage durations. This choice of test statistic is appropriate because the mean provides a representative measure of the central tendency of the duration distribution and is less affected by outliers compared to other parameters such as maximum, range, or minimum.

The null hypothesis states that fuel supply emergencies do not lead to longer durations of power outages. In other words, there is no significant difference in the mean duration between outages caused by fuel supply emergencies and those caused by other factors. The alternative hypothesis suggests that fuel supply emergencies indeed lead to longer durations of power outages.

To test these hypotheses, we will compare the means of two groups: outages caused by fuel supply emergencies and outages caused by other factors. We will calculate the mean duration for each group and assess whether the difference in means is statistically significant. If the observed difference in means is unlikely to occur by chance, we will reject the null hypothesis in favor of the alternative hypothesis, indicating that fuel supply emergencies do lead to longer durations of power outages.

**Null hypothesis**:  fuel supply emergency does not lead to longer durations of power outages.
(if we repeatedly sampled groups of 38 samples from the population and computed their mean duration, it would not be uncommon to see an average duration this low.)

**Alternative hypothesis**:  fuel supply emergency leads to longer durations of power outages.

**Test Statistics**: Means (We want to know if there is significant difference in duration for fuel supply emergency causes and others, mean is a great representative parameter that is not as easily affected by outliers compared to maxmimum, ranges, and minimum)


There is the visual representation of the result: 
<iframe src="fig3.html" width=800 height=600 frameBorder=0></iframe>

Based on the results of our hypothesis test, with a p-value close to 0 (it is hard to know the specific value since it is too small), we have strong evidence to reject the null hypothesis. This indicates that fuel supply emergencies do indeed lead to longer durations of power outages.

The analysis of the dataset supports the conclusion that when a fuel supply emergency occurs, the average duration of power outages tends to be significantly longer compared to outages caused by other factors. This finding highlights the critical role of fuel supply in maintaining uninterrupted power supply and the vulnerability of the power grid during fuel-related emergencies.

Understanding the impact of fuel supply emergencies on power outage durations is crucial for emergency preparedness, infrastructure planning, and policy-making in the energy sector. By recognizing the link between fuel supply emergencies and extended outage durations, stakeholders can develop strategies to enhance fuel resilience, diversify energy sources, and implement contingency plans to mitigate the effects of such emergencies.

Addressing fuel supply challenges and ensuring robust backup systems during emergencies can contribute to the overall reliability and resilience of the power grid, minimizing disruptions and providing consistent electricity supply to communities and critical infrastructure.

In conclusion, our analysis provides compelling evidence that fuel supply emergencies have a significant influence on the duration of power outages. These findings emphasize the importance of proactive measures to mitigate the impact of fuel supply disruptions and strengthen the resilience of the energy infrastructure.