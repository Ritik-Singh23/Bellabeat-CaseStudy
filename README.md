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
```r
install.packages("tidyverse")
install.packages("readr")
install.packages("dplyr")
library(tidyverse)
library(readr)
library(dplyr)
library(lubridate)
```
```r
> library(tidyverse)
-- Attaching packages ---------------------------------------------------------------------------------------------------- tidyverse 1.3.1 --
v ggplot2 3.3.5     v purrr   0.3.4
v tibble  3.1.4     v dplyr   1.0.7
v tidyr   1.1.3     v stringr 1.4.0
v readr   2.0.1     v forcats 0.5.1
-- Conflicts ------------------------------------------------------------------------------------------------------- tidyverse_conflicts() --
x dplyr::filter() masks stats::filter()
x dplyr::lag()    masks stats::lag()
> library(readr)
> library(dplyr)
> library(lubridate)
```

```r
> Hourly_intensity <- read.csv("hourlyIntensities_merged.csv")
> daily_activity <- read.csv("dailyActivity_merged.csv")
> sleep_day <- read.csv("sleepDay_merged.csv")
> weight_log <- read.csv("weightLogInfo_merged.csv")
```
*	Now checking the number of observation and distinct ids from each of the data sets
```r
> n_distinct(daily_activity$Id)
[1] 33
> n_distinct(sleep_day$Id)
[1] 24
> n_distinct(weight_log$Id)
[1] 8
> nrow(daily_activity)
[1] 940
> nrow(sleep_day)
[1] 413
> nrow(weight_log)
[1] 67
```
*	By checking the date column in each Data Set therefore using “as.date()” , “as.posixct()” to correct the DataType
```r
> daily_activity$Date <- as.Date(daily_activity$ActivityDate,format="%m/%d/%Y")
> daily_activity <- daily_activity %>% 
+   select(-ActivityDate)
> sleep_day$Date <- as.Date(sleep_day$SleepDay,format="%m/%d/%Y")
> sleep_day <- sleep_day %>% 
+   select(-SleepDay)
> weight_log$Date <- as.POSIXct(weight_log$Date,format="%m/%d/%Y %H:%M:%S")
```
*	Creating two addition columns called “weekdays” and “TotalHours” 
```r
daily_activity$Weekday <- weekdays(daily_activity$ActivityDate)
daily_activity$TotalHours <-round((daily_activity$FairlyActiveMinutes+daily_activity$LightlyActiveMinutes+daily_activity$VeryActiveMinutes)/60)
```
## ANALYZE

*	Performing common summarize functions to look at analyze the data further


```r
> daily_activity %>%  
+   select(TotalSteps,
+          TotalDistance,
+          TotalHours) %>%
+   summary()
   TotalSteps    TotalDistance      TotalHours   
 Min.   :    0   Min.   : 0.000   Min.   :0.000  
 1st Qu.: 3790   1st Qu.: 2.620   1st Qu.:2.000  
 Median : 7406   Median : 5.245   Median :4.000  
 Mean   : 7638   Mean   : 5.490   Mean   :3.776  
 3rd Qu.:10727   3rd Qu.: 7.713   3rd Qu.:5.000  
 Max.   :36019   Max.   :28.030   Max.   :9.000  
> sleep_day %>%  
+   select(TotalSleepRecords,
+          TotalMinutesAsleep,
+          TotalTimeInBed) %>%
+   summary()
 TotalSleepRecords TotalMinutesAsleep TotalTimeInBed 
 Min.   :1.000     Min.   : 58.0      Min.   : 61.0  
 1st Qu.:1.000     1st Qu.:361.0      1st Qu.:403.0  
 Median :1.000     Median :433.0      Median :463.0  
 Mean   :1.119     Mean   :419.5      Mean   :458.6  
 3rd Qu.:1.000     3rd Qu.:490.0      3rd Qu.:526.0  
 Max.   :3.000     Max.   :796.0      Max.   :961.0  
> weight_log %>%  
+   select(WeightPounds,
+          BMI) %>%
+   summary()
  WeightPounds        BMI       
 Min.   :116.0   Min.   :21.45  
 1st Qu.:135.4   1st Qu.:23.96  
 Median :137.8   Median :24.39  
 Mean   :158.8   Mean   :25.19  
 3rd Qu.:187.5   3rd Qu.:25.56  
 Max.   :294.3   Max.   :47.54 
 ```
*	Creating some charts using “ggplot2” functions for further Analysis 
```r
install.packages("ggplot2")
library(ggplot2)
```
```r
> ggplot(data=daily_activity, aes(x=TotalSteps, y=SedentaryMinutes)) + geom_point(color="darkblue")+ geom_smooth(method=lm,color="black")+
+   labs(title = "Sedentary Minutes vs Total Steps")
`geom_smooth()` using formula 'y ~ x'
> ggplot(data=daily_activity, aes(x=TotalSteps,y=Calories)) + geom_point(color="darkblue")+geom_smooth(method=lm,color="black")+
+   labs(title = "Calories vs Total Steps")
`geom_smooth()` using formula 'y ~ x'
> ggplot(data=daily_activity, aes(x=TotalHours, y=Calories))+geom_point(color="darkblue")+geom_smooth(method=lm,color="black")+
+   labs(title = "Calories vs Toatl Activity Time")
`geom_smooth()` using formula 'y ~ x'
> ggplot(data=sleep_day, aes(x=TotalMinutesAsleep, y=TotalTimeInBed,color=TotalSleepRecords)) + geom_point(color="darkblue")+geom_smooth(method=lm,color="black")+
+   labs(title = "Total Time in Bed vs Total Time Asleep")
`geom_smooth()` using formula 'y ~ x'
```
*	Grouping and summarizing Columns in table “hourly_intensities” and “daily_activity” to create more plots

```r
> Daily <- daily_activity %>%
+   group_by(Weekday) %>%
+   summarise(MeanTotalSteps = round(mean(TotalSteps),digits = 2))
> Daily <-Daily %>%
+   mutate(Weekday=factor(Weekday,levels = c("Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"))) %>% 
+   arrange(Weekday)
> ggplot(data=Daily,aes(x=Weekday,y=MeanTotalSteps)) + geom_bar(stat = "identity",fill="Darkblue",color="black")+
+   geom_text(aes(label=MeanTotalSteps,x=Weekday,y=MeanTotalSteps),size=5,vjust=-0.2)+
+   labs(title = "Mean Total Steps vs Weekdays")
```

```r
intensity <- Hourly_intensity %>%
+   group_by(ActivityHour) %>%
+   drop_na() %>%
+   summarise(AvgTotalIntensity =round(mean(TotalIntensity),1))
> ggplot(data=intensity, aes(x=ActivityHour, y=AvgTotalIntensity)) + geom_histogram(stat = "identity", fill='darkblue',color="black") +
+   theme(axis.text.x = element_text(angle = 90)) +geom_text(aes(label=AvgTotalIntensity,x=ActivityHour,y=AvgTotalIntensity),vjust=-0.3)+
+   labs(title="Average Total Intensity vs. Time")
```

*	Merging the datasets “sleep_day” and “daily_activity” by “id” & “date” to create a new table “merged_data”

```r
> merged_data <- merge(sleep_day, daily_activity, by=c("Id", "Date"))
> n_distinct(merged_data$Id)
[1] 24
> ggplot(data = merged_data, aes(x = TotalMinutesAsleep, y = SedentaryMinutes)) +
+   geom_point(color="darkblue")+ geom_smooth(method=lm,color="black") + labs(title = "Total Minutes Asleep Vs. Sedentary Minutes")
`geom_smooth()` using formula 'y ~ x'
```
*  Creating a pie chart using "pie()" function and creating "lables" and "piepercent" values using "daily_activity" table
```r
> sact<- sum(daily_activity$SedentaryMinutes)
> vact <- sum(daily_activity$VeryActiveMinutes)
> fact<- sum(daily_activity$FairlyActiveMinutes)
> lact<- sum(daily_activity$LightlyActiveMinutes)
> time <- c(vact,lact,fact,sact)
> labels <- c("Very Active Minutes","Lightly Active Minutes","Fairly Active Minutes","Sedentary Minutes")
> piepercent <- round(100*time/sum(time),1)
> pie(time, labels = paste0(piepercent,"%"),col = rainbow(length(time)),cex=1,main="Time Distribution for Different Activities")
> legend("topright",labels, cex = 0.55,fill = rainbow(length(time)))
```

## SHARE
*	For interactive dashboard Check Tableau Public on this [link]( https://public.tableau.com/app/profile/ritik8507/viz/BellaBeatDashboardandAdditionalGraphs/BellaBeatDashBoard)
*	The Scatter-Plot between Sedentary Time and Total steps shows a negative co-relation
*	 
![](https://github.com/Ritik-Singh23/images/blob/main/Rplot.png)

*	 The Scatter-Plot between Calories and Total Steps and Total Activity Shows a positive co-relation 
	 
![](https://github.com/Ritik-Singh23/images/blob/main/Rplot01.png)	


*	The Scatter-Plot between Total Minutes Asleep and Total Time In Bed shows a significantly small gap 
	 
![](https://github.com/Ritik-Singh23/images/blob/main/Rplot03.png)	   

*	The Graph between Avg. total steps & weekdays depicts that there is significantly less activity on Sundays compared to other weekdays 

![](https://github.com/Ritik-Singh23/images/blob/main/Rplot04.png)	 

*	Majority (81.3%) of users track sedentary activities as compared to their other activity habits

![](https://github.com/Ritik-Singh23/images/blob/main/Rplot05.png)	 

*	The Graph between Average Total Intensity and Time shows more activity in the evening than in morning or afternoon.

![](https://github.com/Ritik-Singh23/images/blob/main/Rplot06.png)	 

*	The Scatter-Plot between Sedentary Time and Total Sleep Time shows a negative co-relation 

	 


## ACT
#### Following are some suggestions for the Stakeholders that they can consider moving forward:
*	As Fitbit does not collect hydration data therefore the company can heavily market their product “Spring” as tracking can facilitate sufficient water intake which is highly beneficial.
*	On weekends, Bellabeat app can also prompt notification to encourage users to exercise as on weekends the avg. activity is significantly less.
*	The team can create daily fitness and wellness goals in which the users are recommended to stay active, take at least 7,500 steps & get at least 7 hrs of sleep furthermore the app can offer some additional features to set custom goals based on the users need.
*	The co-relation between calories and total steps is  positive but small therefore the Bellabeat app can suggest some high intensity exercises to achieve the daily goal and Add calories intake and burnt rate information on the app as well.
*	The app can also notify the user when his/her sedentary or inactivity time is precedes the recommended amount as it can affect proper sleep and prevent the user from achieving the set goal


