# Bellabeat-CaseStudy






## Introduction

Bellabeat - a high-tech company that manufactures health-focused smart products. Since it was founded in 2013, Bellabeat has grown rapidly and quickly positioned itself as a tech-driven wellness company for women. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower women with knowledge about their own health and habits. 

Urška Sršen, cofounder and Chief Creative Officer of Bellabeat, believes that analyzing smart device fitness data could help unlock new growth opportunities for the company. She has asked the marketing analytics team to focus on a Bellabeat product and analyze smart device usage data in order to gain insight into how people are already using their smart devices. Then, using this information, she would like high-level recommendations for how these trends can inform Bellabeat marketing strategy.

## ASK
#### Business Task:

Analyze a non-Bellabeat smart device (FitBit Fitness Tracker) data to gain insights on customer usage, discover trends for Bellabeat marketing strategy.
#### Business Objectives:
*	What are the trends identified? 
*	How could these trends apply to Bellabeat customers? 
*	How could these trends help influence Bellabeat marketing strategy?
#### Key Stakeholders: 
*	Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer
*	Sando Mur: Mathematician, Bellabeat’s cofounder and key member of the Bellabeat executive team
*	Bellabeat marketing analytics team: A team of data analysts guiding Bellabeat's marketing strategy.

## PREPARE
*	The data used is publicly available on Kaggle: [FitBit Fitness Tracker Data](https://www.kaggle.com/arashnic/fitbit) 
*	The size of this Data is 338MB
*	There is 18 CSV files and the data was last data was recorded on 05.12.2016
*	These datasets were generated by 30 FitBit users who consented to the submission of personal tracker data to a distributed survey via Amazon Mechanical Turk

#### Limitations of Data Set: 
*	The Data was collected in year 2016 therefore the data may not be timely or relevant today.
*	Sample size of 30 FitBit users is not a big enough sample size to represent the entire population using FitBit.
*	As data is collected through a third party survey, hence the data might not be completely accurate. 

 #### The following data frames are selected for analysis
*	Daily Activity
*	Daily Calories
*	Daily Intensities
*	Daily Sleep
*	Weight Log Info
*	Hourly Intensity

## PROCESS

#### Tools
*	SQL (MySql) for cleaning and format data.
*	R studio to Manipulate, Analyze and make data visualization.
*	Tableau to make interactive dashboard
#### Data Cleaning Process
*	Import all the .csv files in mysql tables
*	To facilitate the cleaning process check the column names and data type of each data set

```sql
SELECT COLUMN_NAME,DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'dailyactivity'
  
SELECT COLUMN_NAME,DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'dailycalories'

SELECT COLUMN_NAME,DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'dailyintensities'

SELECT COLUMN_NAME,DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'sleepday'

SELECT COLUMN_NAME,DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'weightloginfo'
```
![](https://github.com/Ritik-Singh23/images/blob/main/Capture.PNG)![](https://github.com/Ritik-Singh23/images/blob/main/Capture1.PNG)
![](https://github.com/Ritik-Singh23/images/blob/main/Capture2.PNG)![](https://github.com/Ritik-Singh23/images/blob/main/Capture3.PNG)
![](https://github.com/Ritik-Singh23/images/blob/main/Capture4.PNG)

*	It appears that “dailyactivity”, “dailycalories”, and “dailyintensities” have the exact same number of observations.
*	Furthermore, it seems the “dailyactivity” table might have a log of calories and intensities already, so we should confirm that the values actually match for any given ‘ID’ number.
*	To confirm the function INNER JOIN is used to check if there are any values in “dailycalories”, matches “dailyactivity”, however the two data sets should have the same number of columns Therefore a temporary data frame called “daily_activity2” is created from “dailyactivity”,

```sql
CREATE TABLE case_study.daily_activity2 
AS (SELECT Id, ActivityDate, Calories 
FROM case_study.dailyactivity)


CREATE TABLE case_study.check1
AS (
Select DISTINCT a.*
From case_study.daily_activity2 as a
INNER join case_study.dailycalories as b
ON a.id = b.id) 
SELECT COUNT(*) FROM case_study.check1
```
*	There are 940 observations from the sql query same as the “dailyactivity” table therefore  the values are the same between the data frames.
*	Similarly, check the table “dailyactivity” and “dailyintensities” as well

```sql
CREATE TABLE case_study.daily_activity3 AS (
SELECT Id, ActivityDate, SedentaryMinutes, LightlyActiveMinutes, FairlyActiveMinutes, VeryActiveMinutes,
SedentaryActiveDistance, LightActiveDistance, ModeratelyActiveDistance, VeryActiveDistance
 FROM case_study.dailyactivity)

CREATE TABLE case_study.check2
AS (SELECT DISTINCT a.*
From case_study.daily_activity3 as a
INNER join case_study.dailyintensities as b
ON a.id = b.id) 
SELECT COUNT(*) FROM case_study.check2
```
![](https://github.com/Ritik-Singh23/images/blob/main/Capture5.PNG)

*	As there are again 940 observations , tables “dailyintensities”  and “dailycalories” can be safely dropped as the “dailyactivity” table contains these values 
*	Now Checking the “dailyactivity” table for null values

```sql
SELECT
    SUM(CASE WHEN Id IS NULL THEN 1 ELSE 0 END) AS id_null,
     SUM(CASE WHEN ActivityDate IS NULL THEN 1 ELSE 0 END) AS ActivityDate_null,
     SUM(CASE WHEN TotalSteps IS NULL THEN 1 ELSE 0 END) AS TotalSteps_null,
     SUM(CASE WHEN TotalDistance IS NULL THEN 1 ELSE 0 END) AS TotalDistance_null,
     SUM(CASE WHEN TrackerDistance IS NULL THEN 1 ELSE 0 END) AS TrackerDistance_null,
     SUM(CASE WHEN LoggedActivitiesDistance IS NULL THEN 1 ELSE 0 END) AS LoggedActivitiesDistance_null,
     SUM(CASE WHEN VeryActiveDistance IS NULL THEN 1 ELSE 0 END) AS VeryActiveDistance_null,
     SUM(CASE WHEN ModeratelyActiveDistance IS NULL THEN 1 ELSE 0 END) AS LightActiveDistance_null,
     SUM(CASE WHEN SedentaryActiveDistance IS NULL THEN 1 ELSE 0 END) AS SedentaryActiveDistance_null,
     SUM(CASE WHEN VeryActiveMinutes IS NULL THEN 1 ELSE 0 END) AS VeryActiveMinutes_null,
     SUM(CASE WHEN FairlyActiveMinutes IS NULL THEN 1 ELSE 0 END) AS FairlyActiveMinutes_null,
     SUM(CASE WHEN LightlyActiveMinutes IS NULL THEN 1 ELSE 0 END) AS LightlyActiveMinutes_null,
     SUM(CASE WHEN SedentaryMinutes  IS NULL THEN 1 ELSE 0 END) AS SedentaryMinutes_null,
     SUM(CASE WHEN Calories  IS NULL THEN 1 ELSE 0 END) AS Calories_null
FROM case_study.dailyactivity
```
![](https://github.com/Ritik-Singh23/images/blob/main/Capture6.PNG)

*	Upload the cleaned data sets to R-Studio for further data manipulation and import required packages “tidyverse” , “dplyr” and “readr”
*	Now checking the number of observation and distinct ids from each of the data sets
*	By checking the date column in each Data Set therefore using “as.date()” , “as.posixct()” to correct the DataType
*	Creating two addition columns called “weekdays” and “TotalHours” 
