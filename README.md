# BellaBeat-Case-Study
## Overview
BellaBeat, a small smart device, founded in 2013, has grown rapidly and quickly positioned itself as a tech-driven wellness company for women. The task here is to to focus on one of Bellabeat’s products and analyze smart device data to gain insight into how consumers are using their smart devices. In order to answer the key business questions, the task is divided in the following steps of the data analysis process: **ask**, **prepare**, **process**, **analyze**, **share**, and **act**. 

[Tableau Dashboard](https://public.tableau.com/views/BellaBeatCaseStudy_17002247690100/Dashboard1?:language=en-GB&:display_count=n&:origin=viz_share_link)

[Tableau Storyboard](https://public.tableau.com/views/BellaBeatAnalysisStoryboard/Story1?:language=en-GB&:display_count=n&:origin=viz_share_link)

## ❓ASK
Analyze smart device usage data in order to gain insight into how consumers use non-Bellabeat smart devices.
##  💻 PREPARE
**Source of data:** This Kaggle data set contains personal fitness tracker from 30 fitbit users. (https://www.kaggle.com/arashnic/fitbit)

The dataset consists of 18 csv files, Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. 

**LIMITATIONS 🚨**

1. There are just 30 user records accessible.
2. Upon using COUNT(DISTINCT id) function to look for a unique user ID reveals that the collection contains 33 user data related to daily activity and hourly activity, 24 related to sleep, and only 8 related to weight.
3. Some users failed to record their data for monitoring their daily activities and sleep.

## 👩‍💻 PROCESS
1. Checking for DUPLICATES
```
SELECT
  id,
  COUNT(ActivityDate) AS count
FROM
  `gda1-397615.Fitbit.dailyActivity_merged`
GROUP BY
  Id,
  ActivityDate
HAVING (count > 1);
```
2. Removing the duplicates
```
CREATE OR REPLACE TABLE
  `gda1-397615.Fitbit.sleepDay_merged_cleaned` AS
SELECT
  *
FROM (
  SELECT
    *,
    ROW_NUMBER() OVER (PARTITION BY Id, SleepDay ORDER BY SleepDay DESC) AS rn
  FROM
    `gda1-397615.Fitbit.sleepDay_merged` )
WHERE
  rn = 1; 
```
3. Checking for NULLS
```
SELECT
  *
FROM
  `gda1-397615.Fitbit.dailyActivity_merged`
WHERE
  ActivityDate IS NULL;
```
4. Merge the hourly data tables into one using MS EXCEL POWER QUERY.
5. Ensure that the datatypes are suitable for further analysis.

## 📈 ANALYZE
1. **Average Steps Weekly:*** Users are most active on saturdays walking and average of 8153 steps while least active on sundays walking an average of 6933 steps.
```
SELECT 
  EXTRACT(DAYOFWEEK FROM ActivityDate) AS day_of_week,
  AVG(TotalSteps) AS average_steps,
  AVG(Calories) AS average_calories
FROM 
  `gda1-397615.Fitbit.dailyActivity_merged`
GROUP BY 
  day_of_week
ORDER BY 
  day_of_week;
```
2. **Total Minutes Asleep VS Active Minutes:** Users seem to spend to spend the most minutes in Sedentary Mode on Monday, i.e, 849.5 minutes. Sunday has the least Very Active minutes, i.e, 19.9 minutes. Thursday accounts for the least Fairly Active minutes, i.e, 14.6 minutes and the most minutes Asleep, i.e, 420.4 minutes.
```
SELECT 
  EXTRACT(DAYOFWEEK FROM ActivityDate) AS day_of_week,
  AVG(TotalMinutesAsleep) AS average_minutes_asleep,
  ROUND(AVG(VeryActiveMinutes),3) AS average_veryactiveminutes,
  ROUND(AVG(FairlyActiveMinutes),3) AS average_fairlyactiveminutes,
  ROUND(AVG(LightlyActiveMinutes),3) AS average_lightlyactiveminutes,
  ROUND(AVG(SedentaryMinutes),3) AS average_sedentaryminutes
FROM 
  `gda1-397615.Fitbit.dailyActivity_merged` AS active_data
JOIN `gda1-397615.Fitbit.sleepDay_merged_cleaned` AS sleep_data
ON active_data.Id=sleep_data.Id
GROUP BY 
  day_of_week
ORDER BY 
  day_of_week;
```
3. **Active Minutes by Percentage:** 81.3% of the time users are in sedentary mode. ONLY 1.1% of the time users are fairly active.
```
WITH total AS (SELECT 
  SUM(VeryActiveMinutes) AS total_veryactiveminutes,
  SUM(FairlyActiveMinutes) AS total_fairlyactiveminutes,
  SUM(LightlyActiveMinutes) AS total_lightlyactiveminutes,
  SUM(SedentaryMinutes) AS total_sedentaryminutes
FROM
  `gda1-397615.Fitbit.dailyActivity_merged`)
SELECT
   total_veryactiveminutes*100/(total_veryactiveminutes+total_fairlyactiveminutes+total_lightlyactiveminutes+total_sedentaryminutes) AS veryactive_percent,
   total_fairlyactiveminutes*100/(total_veryactiveminutes+total_fairlyactiveminutes+total_lightlyactiveminutes+total_sedentaryminutes) AS fairlyactive_percent,
   total_lightlyactiveminutes*100/(total_veryactiveminutes+total_fairlyactiveminutes+total_lightlyactiveminutes+total_sedentaryminutes) AS lightlyactive_percent,
   total_sedentaryminutes*100/(total_veryactiveminutes+total_fairlyactiveminutes+total_lightlyactiveminutes+total_sedentaryminutes) AS sedentaryactive_percent
FROM total;
```
4. **Average Sleep Weekly:** Users have the Highest sleeping minutes on SUNDAYS. On an average, users have 420 minutes of sleep, i.e, 7 hours.
```
SELECT 
  EXTRACT(DAYOFWEEK FROM SleepDay) AS day_of_week,
  AVG(TotalMinutesAsleep) AS average_sleep,
  AVG(TotalTimeInBed) AS average_tib,
FROM 
  `gda1-397615.Fitbit.sleepDay_merged_cleaned`
GROUP BY 
  day_of_week
ORDER BY 
  day_of_week
```
5. **Average Steps Walked Hourly:** Users are MOST active at 10:00 AM. Users are LEAST active at 00:00 AM.
```
SELECT 
 ActivityHour,
 AVG(StepTotal) AS avg_steps_hourly,
FROM 
 `gda1-397615.Fitbit.hourly_activity`
GROUP BY ActivityHour
ORDER BY ActivityHour;
```
ORDER BY ActivityHour;
