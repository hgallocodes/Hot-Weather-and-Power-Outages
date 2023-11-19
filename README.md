# Does Colder Weather Lead to Longer Power Outages?

by Sarah Borsotto (sborsott@ucsd.edu) and Hector Gallo (hgallo@ucsd.edu)

---

## Introduction

Major power outages have had severe widespread impacts, disrupting daily life, businesses, communication systems, and critical infrastructures. They can lead to significant economic losses due to damaged equipment and interrupted services. Public safety may also be compromised as emergency response systems, transportation, and healthcare facilities become limited. Not to mention, prolonged outages can provoke health risks and psychological stress among affected populations. It is therefore imperative that we identify the leading predictors of major power outages. We aim to do so by exploring a rich dataset of major power outage observations within the continental U.S. between January 2000 and July 2016.

Our dataset is sourced from Purdue University and was utilized in an article called “A Multi-Hazard Approach to Assess Severe Weather-Induced Major Power Outage Risks in the U.S.”. It includes a substantial amount of information on the location of the outage, date and time information describing when the outage occurred, as well as climatic, economic, and electrical consumption patterns for the region of the outage. As per the Department of Energy, major power outage events are characterized based on the following; the event either impacted more than 50,000 customers or the event led to an unplanned firm load loss of 300 MW. Correspondingly, our dataset only includes major power outage events that meet these requirements.

We are interested in investigating the major causes of power outage events, such as when these events occur the most and under what specific weather patterns. More specifically, we seek to answer the following: do power outages have longer duration periods in colder weather? We focus our attention on time, climate, and cause categories, resulting in a dataset of 1534 rows and 10 columns. The column categories we saved are year, month, anomaly level, outage start time, outage restoration time, cause category, cause category detail, outage duration, customers affected, and our own season category. The descriptions of each column variable are detailed as such:

| Variable | Description |
| ---------|----------|
| YEAR | The year the outage event took place |
| MONTH | The month the outage event took place |
| OUTAGE.START | Indicates the date and time that the power outage began |
| OUTAGE.RESTORATION | Indicates the date and time that power was restored for all customers |
| CAUSE.CATEGORY | Categories of all the events causing the major power outages |
| CAUSE.CATEGORY.DETAIL | Detailed description of the event categories causing the major power outages |
| OUTAGE.DURATION | Duration of the outage event in minutes |
| CUSTOMERS.AFFECTED | Number of customers affected by the power outage event |
| SEASON | The season that the power outage occurred in |

Through our analysis, we hope to identify possible indicators for major power outage events. Identifying possible causes can help organizations implement effective preventive measures. For instance, if outages are frequently triggered by severe weather conditions, especially during specific seasons, reinforcing infrastructure or adjusting maintenance schedules in response to these times of need can mitigate power-outage-induced issues.

---

## Cleaning and EDA

### Data Cleaning

At first glance, we can see that the first two columns and the first row of the dataset include many NaN values. This is because the variable column denotes the units used for each measure in the following columns, as described in the first row. We will remove this row since we already have information about the units of each variable.

We also want to combine the OUTAGE.START.TIME and OUTAGE.START.DATE columns into one variable called OUTAGE.START, and then repeat the same thing for a new OUTAGE.RESTORATION column. We will do so using some helper functions.

Since we are only interested in looking at potential causes for power outage events, especially weather-induced factors, we will only keep some of the columns. The columns we wanted to focus on are year, month, anomaly level, outage start time, outage restoration time, cause category, cause category detail, outage duration, and customers affected.

For our hypothesis testing later on, we also need information on the season. We have created a helper function that takes in the month of the power outage and returns the season that the power outage occurred in. This was saved to a new column called SEASON. For our hypothesis testing later on, we also need information on the season. We have created a helper function that takes in the month of the power outage and returns the season that the power outage occurred in. This was saved to a new column called SEASON.

We also need to convert the YEAR and MONTH columns to integer and the ANOMALY.LEVEL and OUTAGE.DURATION columns to float. This is necessary for us to be able to calculate means of the durations and anomaly level variables when we perform data analysis later on. After inspecting each column for abnormal values, we noticed that some of the values in CAUSE.CATEGORY.DETAIL have unnecessary capitalization and spacing, as well as repeating categories, such as winter and winter storm. These can be grouped together so we can investigate weather patterns in a more general sense. We take care of these concerns and develop the resulting dataframe:

|   YEAR |   MONTH |   ANOMALY.LEVEL | OUTAGE.START        | OUTAGE.RESTORATION   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED | SEASON   |
|-------:|--------:|----------------:|:--------------------|:---------------------|:-------------------|:------------------------|------------------:|---------------------:|:---------|
|   2011 |       7 |            -0.3 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  | severe weather     | nan                     |              3060 |                70000 | summer   |
|   2014 |       5 |            -0.1 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  | intentional attack | vandalism               |                 1 |                  nan | spring   |
|   2010 |      10 |            -1.5 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  | severe weather     | wind                    |              3000 |                70000 | fall     |
|   2012 |       6 |            -0.1 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  | severe weather     | thunderstorm            |              2550 |                68200 | summer   |
|   2015 |       7 |             1.2 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  | severe weather     | nan                     |              1740 |               250000 | summer   |


### Univariate Analysis

We are primarily interested in pinpointing the main causes of power outages. Conveniently enough, our dataset contains categorical information on the cause of the event, in a column named “CAUSE.CATEGORY”. We have included a histogram plot of this variable below:

<iframe src="assets/cause_major_power.html" width=800 height=600 frameBorder=0></iframe>

Evidently, ‘severe weather’ appears to have the highest count relative to all of the other causes. While ‘intentional attack’ seems to also have a high count, it makes up only half of ‘severe weather’s’ count. As is such, we would like to center our analysis on the most prominent cause category, severe weather.

Considering that severe weather is a major factor for power outage events, we are curious to discover what specific severe weather conditions can be used to predict these events. Unlike the “CAUSE.CATEGORY” column, which has 0 missing values, our “CAUSE.CATEGORY.DETAIL” column, describing the cause in more detail, does have missing values.

<iframe src="assets/fig2.html" width=800 height=600 frameBorder=0></iframe>

Here is the percent of missing values for each cause detail category. We can see that islanding, public appeal, and system operability disruption have high proportions of missing values. This may be due to there not being many categories to describe the cause in detail, as the cause is already somewhat specific.

Severe weather appears to have some missing values, about 25%, but it still prevails as the category with the most observations. As is such, we are interested in identifying possible relationships between weather patterns and power outages. To do so, we have created a sub-dataframe that only includes observations that have 'severe weather' as the main cause category.


|   YEAR |   MONTH |   ANOMALY.LEVEL | OUTAGE.START        | OUTAGE.RESTORATION   | CAUSE.CATEGORY   | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED | SEASON   |
|-------:|--------:|----------------:|:--------------------|:---------------------|:-----------------|:------------------------|------------------:|---------------------:|:---------|
|   2011 |       7 |            -0.3 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  | severe weather   | nan                     |              3060 |                70000 | summer   |
|   2010 |      10 |            -1.5 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  | severe weather   | wind                    |              3000 |                70000 | fall     |
|   2012 |       6 |            -0.1 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  | severe weather   | thunderstorm            |              2550 |                68200 | summer   |
|   2015 |       7 |             1.2 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  | severe weather   | nan                     |              1740 |               250000 | summer   |
|   2010 |      11 |            -1.4 | 2010-11-13 15:00:00 | 2010-11-14 22:00:00  | severe weather   | winter                  |              1860 |                60000 | fall     |

Great! Now we have a clean dataframe that only includes severe-weather induced power outages. By excluding other causes, we can identify possible weather patterns that cause power outages. We can now take a look at some possible relationships between power outages and time, location, and weather conditions.


### Bivariate Analysis

First let's take a look at power outages over time. We can do so by looking at number of customers affected and outage duration times over the months. Do power outages occur more in the summer time due to hotter weather? Let's see how mean outage durations change over time based on monthly categories.

<iframe src="assets/fig5.html" width=800 height=600 frameBorder=0></iframe>

Surprisingly enough, the longest mean outage duration times appear to be in the fall months. This may be due to heavier storms, as hotter seasons may have less severe weather conditions.

This can be further determined by looking at anomaly level. Anomaly level represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season, estimated as a 3-month running mean of ERSST.v4 SST anomalies in the Niño 3.4 region. Larger positive values indicate that the region where the power outage occurred had a hotter than usual weather pattern, compared to a mean of weather conditions within the past 30 years. On the other hand, larger negative values indicate that the region where the power outage occurred had an abnormally cold weather pattern.

<iframe src="assets/fig6.html" width=800 height=600 frameBorder=0></iframe>

We can see that the highest mean outage duration values are around -0.5 and 0.5, which is within the normal anomaly level range. There also seems to be more negative anomaly level values.

Accordingly, our dataset of severe-weather induced power outages seems to have more cold-weather patterns than hot-weather patterns. Yet, our data revolves around very small anomaly level values, meaning that the weather may still be considered "normal" based on this variable.

Unfortunately, anomaly level doesn't appear to be a great representation for weather conditions during the outage. This may be due to the fact that anomaly level is a metric that measures deviation in 3-month long weather conditions with normal regular conditions. This 3-month period may be too long to provide a useful representation of the weather at the time of the outage. Instead, we can use the cause of the outage, as well as the season of the outage, to assume possible weather conditions and how they influenced the number of power outages that occurred.

### Interesting Aggregates

|   ('CUSTOMERS.AFFECTED', 'count') |   ('CUSTOMERS.AFFECTED', 'mean') |   ('ANOMALY.LEVEL', 'count') |   ('ANOMALY.LEVEL', 'mean') |
|----------------------------------:|---------------------------------:|-----------------------------:|----------------------------:|
|                                13 |                           109254 |                           10 |                  -0.83      |
|                                 1 |                            36073 |                            1 |                  -0.1       |
|                                13 |                           469430 |                           12 |                   0.816667  |
|                                30 |                           179991 |                           30 |                   0.203333  |
|                                51 |                           255258 |                           56 |                   0.473214  |
|                                45 |                           265103 |                           47 |                   0.0574468 |
|                                50 |                           183338 |                           54 |                   0.224074  |
|                                36 |                           159309 |                           40 |                  -0.4425    |
|                                75 |                           241344 |                           76 |                  -0.594737  |
|                                43 |                           145498 |                           45 |                   0.0755556 |
|                                59 |                           163006 |                           62 |                  -0.329032  |
|                               104 |                           141015 |                          107 |                  -0.625234  |
|                                63 |                           198161 |                           65 |                   0.0738462 |
|                                47 |                           137655 |                           49 |                  -0.277551  |
|                                30 |                           266473 |                           45 |                  -0.335556  |
|                                45 |                           117705 |                           48 |                   1.38125   |
|                                12 |                           126214 |                           12 |                   0.725     |

We were curious to group power outages by YEAR and analyze the aggregate statistics that we would obtain from CUSTOMERS.AFFECTED and ANOMALY.LEVEL, so we created a pivot table. This pivot table contains the aggregated statistics, mean and count, for both CUSTOMERS.AFFECTED and ANOMALY.LEVEL grouped by YEAR. A high negative anomaly level represents colder weather in general and a high positive anomaly level represents hotter weather. Taking this into account, we wanted to observe what happened on average when the anomaly level was lower or higher and how the average of customers was affected as well as the count of power outages. This is significant to our analysis since we think that a higher anomaly level could be accompanied by a higher average of customers affected, as well as a higher count of power outages for that specific year. It is not until we perform hypothesis testing that we will be able to determine if this might be true or not, but for now performing this form of exploratory analysis will give us a clearer picture of our data.

Something that is worth taking note of is that there doesn't seem to be a higher average of customers affected when the anomaly is higher or lower. It seems like on average, the number of customers affected is independent of the anomaly level. The same thing can be said about the count statistic. A higher anomaly level, or in other words a hotter weather, doesn’t seem to be a variable that influences customers affected on average. The same trend can be observed in the opposite direction; that is, a higher negative anomaly level doesn’t seem indicative of a higher count of power outages and a higher average of customers affected.

Although we are ultimately subsetting the data frame to only include power outages caused by severe weather and dividing the data into summer and winter based on the MONTH variable, we believe that it was still important to observe the trends that would take place annually in terms of anomaly level and customers affected. This is due to the fact that it could help us later when finding missingness dependency, specially because the count of power outages by YEAR seems to vary a lot from year to year.

---

## Assessment of Missingness

### NMAR Analysis

As we can see in the “Counts of Major Power Outages Cases” visualization, there are very few power outages caused by system operability disruption and equipment failure when we look at the CAUSE.CATEGORY column and the frequency of each value. The majority of the power outages are due to severe weather and intentional attacks which are the values in this column with the most occurrences. 

A value is NMAR (Not Missing At Random) when the chance that a value is missing depends on the actual missing value. In this case, we believe that the CAUSE.CATEGORY column is NMAR. We suspect that the low number of power outages being reported due to system operability and equipment failure are caused by the electricity providers’ choice to keep a good reputation and to seem reliable to the government and others by not reporting these power outages often. Although we are not saying that companies are purposely altering the number of reported outages for each cause, we do believe that since they might not be in favor of reporting outages due to system operability disruption and equipment failure, they might put more focus on collecting data when power outages are caused by other reasons, which in this case might be severe weather. Therefore, there might be a lower number of outages being reported to be caused by system operability disruption and equipment failure.

In this case, an additional set of data that we might want to obtain to make this variable MAR is one where we have a column that contains a column with boolean values that are True when the company reported the power outage themselves and False when the government had to ask them for the cause of the power outage. This column of booleans would allow us to observe in conjunction with the CAUSE.CATEGORY what the cause is for each power outage, and if the government had to ask for this data or if the company voluntarily reported it. By taking a look at the datasets that were used to acquire the data used in this dataset, we noticed that all of them were created by governmental agencies. This is why we think that it is plausible that we could obtain a dataset that would help us learn whether each power outage cause was reported by the company or asked by the government to be reported. A failure to report voluntarily would suggest that the electricity providing company prefers not to admit that the cause of the power failure was in fact due to system operability disruption and equipment failure.

### Missing Dependency

We opted to analyze the CAUSE.CATEGORY.DETAIL column with non-trivial missingness to analyze the dependency of the missingness of this column on other columns. After running permutation tests, we were able to observe that the missingness of CAUSE.CATEGORY.DETAIL depends on the column MONTH and that it does not depend on the CUSTOMERS.AFFECTED column.

We chose the Kolmogorov-Smirnov test statistic since we wanted to measure the similarity between the two distributions, when CAUSE.CATEGORY.DETAIL is missing and when it’s not. After creating a function that does this for us, we proceeded to create a function that would iterate 1000 times and for each iteration compute the Kolmogorov-Smirnov statistic for the distribution of MONTH when the CAUSE.CATEGORY.DETAIL was missing. If our observed statistic seemed to not belong to the empirical distribution of the test statistic, then we would be able to determine that the missingness of the CAUSE.CATEGORY.DETAIL does depend on the MONTH column. Below are the results of these permutation tests:

<iframe src="assets/figm1.html" width=800 height=600 frameBorder=0></iframe>

In respect to our question, these permutation tests show that the missingess on CAUSE.CATEGORY.DETAIL depends on MONTH. Just as the missigness of CAUSE.CATEGORY.DETAIL depends on MONTH, we can expect other variables like OUTAGE.DURATION to also depend on MONTH. Using the permutation tests and the Kolmogorov-Smirnov test statistic, we can conclude that the CAUSE.CATEGORY.DETAIL is MAR since our observed statistic has a p-value of 0.0001, using a significance level of 0.01.

Like we mentioned before, after running the permutation tests, we were able to observe that the missingness of CAUSE.CATEGORY.DETAIL depends on the column MONTH and that it does not depend on the CUSTOMERS.AFFECTED column.

Similarly, to when we performed the permutation tests to find a dependency of CAUSE.CATEGORY.DETAIL on MONTH, we chose the Kolmogorov-Smirnov test statistic since we wanted to measure the similarity between the two distributions, when CAUSE.CATEGORY.DETAIL is missing and when it’s not. Using the same function that we used previously, we iterated 1000 times and for each iteration computed the Kolmogorov-Smirnov statistic for the distribution of CUSTOMERS.AFFECTED when the CAUSE.CATEGORY.DETAIL was missing and when it wasn’t. If our observed statistic seemed to not belong to the empirical distribution of the test statistic, then we would be able to determine that the missingness of the CAUSE.CATEGORY.DETAIL does depend on the MONTH column. However, in this case, we suspected that the observed statistic would belong to the empirical distribution of the test statistic, and that we’d determine that the missingness of the CAUSE.CATEGORY.DETAIL does not depend on the CUSTOMERS.AFFECTED column. Below are the results of these permutation tests:

<iframe src="assets/figm2.html" width=800 height=600 frameBorder=0></iframe>

In respect to our question, these permutation tests show that the missingness of CAUSE.CATEGORY.DETAIL does not depend on CUSTOMERS.AFFECTED. We can connect this to our question in the sense that the variable CUSTOMERS.AFFECTED doesn't have a direct correlation to CAUSE.CATEGORY.DETAIL, since CAUSE.CATEGROY.DETAIL describes weather and CUSTOMERS.AFFETCED is a measure of the population in each city. Since, CUSTOMERS.AFFECTED does not seem to be a variable that the missingness of CAUSE.CATEGORY.DETAIL depends on, then we think that it is not relevant to take CUSTOMERS.AFFECTED into account when answering our question. Our question aims to analyze whether there will be a longer duration of power outages during a colder weather. CUSTOMERS.AFFECTED is a variable that far from helping us drive our study forward, could distract us from obtaining an answer. On the other hand, knowing that the missingness of CAUSE.CATEGORY.DETAIL depends on MONTH, tells us that we should definitely take into consideration dividing our data into summer and winter based on the months. Using the permutation tests and the Kolmogorov-Smirnov test statistic, we conclude that the CAUSE.CATEGORY.DETAIL is not dependent on CUSTOMERS.AFFECTED since our observed statistic has a p-value of 0.0117, using a significance level of 0.01.

---

## Hypothesis Testing

As we saw in the bivariate analysis, anomaly level may not be an accurate representation of the weather conditions at the time of the power outage. For a more accurate representation we can consider the detailed cause of the power outage, as well as the season the event occurred in. We are interested in exploring how weather impacts power outages. More specifically, we want to determine if colder weather leads to longer mean power outage durations. We focus on power outage durations as a numerical indicator of the status of the power outage. We assume that longer power outages are more negatively impactful than shorter power outages. In order to identify hot and cold weather without using anomaly levels, we will only look at summer and winter months, as well as their corresponding causes. Summer and winter tend to be viewed as the two extremes for weather, with summer typically consisting of hot weather and winter generally consisting of cold weather.

There are various categories for the cause detail column, and some typically only occur within a certain season or weather condition. For example, thunderstorms and hurricanes typically occur in warmer weather, and snow/ice storms occur in colder weather. Additionally, some categories are not directly correlated with weather, such as earthquakes and public appeal, so we have decided to ignore these when we consider power outages within each season. Since we are only looking at two seasons, we will exclude causes that are not typical for that season, like removing snow/ice as a possible category in the summer season. While these storms can still occur in the seasons you wouldn't expect, we are focusing on hot and cold weather, not the season itself, the season is meerily an indicator.

Accordingly, we created a dataframe that only includes summer and winter seasons, as well as an additional column that denotes the weather condition during the power outage, defined as either hot, cold, or nan.

We are interested in seeing how weather patterns impact the severity of a power outage. Our focus is: do power outages have longer duration periods in colder weather? We hypothesize that colder weather leads to longer mean duration times than hotter weather. Therefore, our null hypothesis is that cold weather and hot weather have the same mean power outage duration time. On the other hand, our alternative hypothesis is that colder weather has higher mean duration times than hotter weather. We believe colder weather is correlated with higher duration times since our data has more cold weather data, as we saw in our anomaly level bivariate analysis, and because severe weather conditions typically occur more often in colder weather.

To test this hypothesis, we found the mean duration times for hot and cold weather. We then subtracted hot mean duration time from cold mean duration time to calculate our observed statistic, since we are comparing two numerical distributions. Our observed statistic is 1004 minutes.

This seems to be a fairly large number considering that the means of the hot and cold weathers are in the thousands. But, how do we know if this is significantly different from what is truly expected?

We can perform a permutation test to find out! We will shuffle the HOT.OR.COLD column and find the difference in means for outage duration times in cold weather and hot weather. We will repeat this process 1000 times so we can approximate the distribution of the test statistic. We will then compare our observed statistic to these null statistics. We will be using a significance level of 0.05.

<iframe src="assets/figp.html" width=800 height=600 frameBorder=0></iframe>

The p-value after the permutation test is 0.024, which is less than our significance level of 0.05, so we reject our null hypothesis that cold weather and hot weather have the same mean outage duration times. Accordingly, we have sufficient evidence to suggest that power outages have longer duration times in colder weather than hotter weather.

This may be due to the presence of more storms in colder weather, leading to longer mean duration times. Suitably, companies may want to allocate their resources to places with colder weather, and during colder seasons. Additional analysis may be performed to further investigate this question by looking at the weather forecast for the day of the power outage. Since we did not have this data, we believe our definition of hot and cold weather based on season and cause is a good representation of the weather at the time of the power outage.

---
