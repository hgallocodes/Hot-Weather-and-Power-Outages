# Does Hotter Weather Lead to More Power Outages?

by Sarah Borsotto (idk your email sorry) and Hector Gallo (hgallo@ucsd.edu)

---

## Introduction

Major power outages have had severe widespread impacts, disrupting daily life, businesses, communication systems, and critical infrastructures. They can lead to significant economic losses due to damaged equipment and interrupted services. Public safety may also be compromised as emergency response systems, transportation, and healthcare facilities become limited. Not to mention, prolonged outages can provoke health risks and psychological stress among affected populations. It is therefore imperative that we identify the leading predictors of major power outages. We aim to do so by exploring a rich dataset of major power outage observations within the continental U.S. between January 2000 and July 2016.

Our dataset is sourced from Purdue University and was utilized in an article called “A Multi-Hazard Approach to Assess Severe Weather-Induced Major Power Outage Risks in the U.S.”. It includes a substantial amount of information on the location of the outage, date and time information describing when the outage occurred, as well as climatic, economic, and electrical consumption patterns for the region of the outage. As per the Department of Energy, major power outage events are characterized based on the following; the event either impacted more than 50,000 customers or the event led to an unplanned firm load loss of 300 MW. Correspondingly, our dataset only includes major power outage events that meet these requirements. 

We are interested in investigating the major causes of power outage events, such as when and where these events occur the most, and under what specific weather patterns. More specifically, we seek to answer the following: do major power outage events occur more in hotter weather? We focus our attention on time, location, and cause categories, resulting in a dataset of 1534 rows and 9 columns. The column categories we saved are year, month, U.S. State, NERC region, outage start time, outage restoration time, cause category, cause category detail, and outage duration. The descriptions of each column variable are detailed as such:

| Variable | Description |
| ---------|----------|
| YEAR | The year the outage event took place |
| MONTH | The month the outage event took place |
| U.S._STATE | The state where the outage occurred, within the continental U.S. |
| NERC.REGION | The North American Electric Reliability Corporation (NERC) regions for the outage |
| OUTAGE.START | Indicates the date and time that the power outage began |
| OUTAGE.RESTORATION | Indicates the date and time that power was restored for all customers |
| OUTAGE.DURATION | Duration of the outage event in minutes |
| CAUSE.CATEGORY | Categories of all the events causing the major power outages |
| CAUSE.CATEGORY.DETAIL | Detailed description of the event categories causing the major power outages |

Through our analysis, we hope to identify possible indicators for major power outage events. Identifying possible causes can help organizations implement effective preventive measures. For instance, if outages are frequently triggered by severe weather conditions, especially during specific seasons, reinforcing infrastructure or adjusting maintenance schedules in response to these times of need can mitigate power-outage-induced issues.



---

## Cleaning and EDA

### Data Cleaning

First, let's take a look at our dataset:

SHOW HEAD

<iframe src="assets/10-80-enrollment.html" width=800 height=600 frameBorder=0></iframe>

At first glance, we can see that the first two columns and the first row of the dataset include many NaN values. This is because the variable column denotes the units used for each measure in the following columns, as described in the first row. We will remove this unnecessary row and column since we already have information about the units of each variable. 
We also want to combine the OUTAGE.START.TIME and OUTAGE.START.DATE columns into one variable called OUTAGE.START, and then repeat the same thing for a new OUTAGE.RESTORATION column.
Since we are only interested in looking at potential causes for power outage events, especially weather-induced factors, we will only keep some of the columns. The columns we wanted to focus on are year, month, U.S. State, NERC region, climate region, anomaly level, outage start time, outage restoration time, cause category, cause category detail, and outage duration.

Our resulting data frame contains 1534 rows and 11 columns. The data types for this dataframe is shown below:

SHOW DATATYPES

We need to convert the YEAR, MONTH, and OUTAGE.DURATION columns to integer, and ANOMALY.LEVEL to float. After inspecting each column for abnormal values, we noticed that some of the values in CAUSE.CATEGORY.DETAIL have unnecessary capitalization and spacing, as well as repeated categories, such as winter and winter storm. We take care of these concerns and develop the resulting dataframe with the corresponding data types:

SHOW DATATYPES



<iframe src="assets/10-80-enrollment.html" width=800 height=600 frameBorder=0></iframe>

### Univariate Analysis

We are primarily interested in pinpointing the main causes of power outages. Conveniently enough, our dataset contains categorical information on the cause of the event, in a column named “CAUSE.CATEGORY”. We have included a histogram plot of this variable below:

PLOT HERE

Evidently, ‘severe weather’ appears to have the highest count relative to all of the other causes. While ‘intentional attack’ seems to also have a high count, it makes up only half of ‘severe weather’s’ count. As is such, we would like to center our analysis on the most prominent cause category, severe weather. 

Considering that severe weather is a major factor for power outage events, we are curious to discover what specific severe weather conditions can be used to predict these events. Unlike the “CAUSE.CATEGORY” column, which has 0 missing values, our “CAUSE.CATEGORY.DETAIL” column, describing the cause in more detail, does have missing values. 

PLOT HERE

As we can see here, about 25% of the ‘CAUSE.CATEGORY.DETAIL’ values are missing for the severe weather category, whereas islanding, public appeal, and system operability disruption have far higher proportions of missing values, perhaps because there aren’t as many possible sub-categories for these variables. Interestingly, intentional attack seems to have the lowest proportion of missing values in the cause category detail column. Based on the total number of values and the proportion of missing values, though, severe weather still has a greater number of observations than intentional attack, with 370 events and 576 events respectively. We have therefore decided to only investigate severe weather causes, since we have more data available for those factors. We further investigate these missing values in our missingness section.

In response to these findings, we generated a new dataset that only looks at severe weather induced power outages. Our updated dataset still has all of the same columns, but now has 763 rows, which all have “severe weather” in the “CLIMATE.CATEGORY” column. 

PLOT HERE

---

## Assessment of Missingness

Here's what a Markdown table looks like. Note that the code for this table was generated _automatically_ from a DataFrame, using

```py
print(counts[['Quarter', 'Count']].head().to_markdown(index=False))
```

| Quarter     |   Count |
|:------------|--------:|
| Fall 2020   |       3 |
| Winter 2021 |       2 |
| Spring 2021 |       6 |
| Summer 2021 |       4 |
| Fall 2021   |      55 |

---

## Hypothesis Testing


---
