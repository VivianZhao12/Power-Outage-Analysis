# Power-Outage-Analysis
*This is a project for DSC 80 at UCSD.*

## Introduction

Welcome to the "Power-Outage-Analysis" project repository! This project aims to explore the relationship between fuel supply emergencies and the duration of power outages. 

The main question that drives our analysis is: **"Does fuel supply emergency lead to longer power outage durations?"** We seek to investigate whether power outages caused by fuel supply emergencies tend to have longer durations compared to other causes of outages. By examining this relationship, we can gain insights into the impact of fuel supply emergencies on the reliability and resilience of the power grid.

### Overview of the Dataset
The dataset used in this analysis provides valuable information on power outage events, regional climate characteristics, regional electricity consumption, and regional economic indicators (more information about the data can be obtained at https://www.sciencedirect.com/science/article/pii/S2352340918307182#bib6).

This outage dataset contains (1540 rows, 57 columns). Though the dataset contains a lot of valuable information, we will only be keeping the 13 columns that contain useful information for our analysis. 

- YEAR: Indicates the year when the outage event occurred
- HURRICANE.NAMES: 
- U.S._STATE: Represents all the states in the continental U.S.
- CLIMATE.REGION: U.S. Climate regions as specified by National Centers for Environmental Information (nine climatically consistent regions in continental U.S.A.)
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


## Cleaning and EDA (Exploratory Data Analysis)

### Data Cleaning
This dataset contains many null data and unused columns. In order to make the analysis process easier, we should first clean the data.


|    | Major power outage events in the continental U.S.                                                           | Unnamed: 1   | Unnamed: 2   | Unnamed: 3   | Unnamed: 4   |
|---:|:------------------------------------------------------------------------------------------------------------|:-------------|:-------------|:-------------|:-------------|
|  0 | Time period: January 2000 - July 2016                                                                       | nan          | nan          | nan          | nan          |
|  1 | Regions affected: Outages reported in this data file affected a single U.S. state at the time of occurrence | nan          | nan          | nan          | nan          |
|  2 | nan                                                                                                         | nan          | nan          | nan          | nan          |
|  3 | nan                                                                                                         | nan          | nan          | nan          | nan          |
|  4 | variables                                                                                                   | OBS          | YEAR         | MONTH        | U.S._STATE   |
|  5 | Units                                                                                                       | nan          | nan          | nan          | nan          |



1. **Correct the index and columns (set column names, remove invalid rows, remove original index from excel file)**

Since the dataset is imported from an excel file, there are some formatting issues that need to be fixed. These operations will return a dataframe with the correct index and column names. Specifically, we will remove the first 4 lines of NaNs, remove the NaN row at index=5, remove two useless columns, set the column names to the values in the first row, remove the first row (index=4), and finally reset the index. 

2. **Convert START and RESTORATION times and dates to timestamp.Create new columns "OUTAGE.START" and "OUTAGE.RESTORATION".**

The columns OUTAGE.START.DATE, OUTAGE.START.TIME, OUTAGE.RESTORATION.DATE, and OUTAGE.RESTORATION.TIME are string type variables and contain similar information types. To make these information easier to understand and work with, we should combine and convert them to timestamp objects.

3. **Drop rows that have more than 10 missing values**

Browsing through the dataset, we see some rows with a lot of missing information. These rows assumingly do not contain as much useful information compared to others. I assume that these null values exist because such information was never recorded and does not depend on any other columns or the missing value itself. Thus, I will remove the rows that have more than 10 missing values to make the dataset more clear.

4. **Filter out unneeded columns, keep columns that are relevant the answering the question only**

Since our main focus in this analysis is duration time and its causes, we should delete the columns that will not give us any useful information regarding answering out question. (columns to keep: ["YEAR","HURRICANE.NAMES", 'U.S._STATE','CLIMATE.REGION', 'ANOMALY.LEVEL', 'CLIMATE.CATEGORY', 'CAUSE.CATEGORY', 'CAUSE.CATEGORY.DETAIL', 'OUTAGE.DURATION', 'CUSTOMERS.AFFECTED', 'OUTAGE.START', 'OUTAGE.RESTORATION', "TOTAL.PRICE"])