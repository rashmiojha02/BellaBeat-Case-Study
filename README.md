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
1. Checking for NULLS
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

   
