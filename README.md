# DA Case Study: Bellabeat :star_struck:

## Introduction ##
* Mission: a high-tech company that manufactures health-focused smart products for women

* About: Bellabeat products became available through a growing number of online retailers in addition to their own e-commerce channel on their website. Their current marketing strategies include: 
  * **Traditional advertising media**, such as radio, out-of-home billboards, print, and television, but focuses on digital marketing extensively
  * **Google Search**, maintaining active Facebook and Instagram pages, and consistently engages consumers on Twitter.
  * **Video ads** on Youtube and **display ads** on the Google Display Network to support campaigns around key marketing dates.

* Business goals: Bellabeat aims to become a larger player in the global **smart device** market. By analyzing smart device fitness data, we could potentially unlock new growth opportunities for the company.

## Data analysis process (6 steps): ##

### 1. Ask ###

* Identify the business task
I have been asked to analyze smart device data (data collected on activity, sleep, stress, and reproductive health). 
This helps me to gain insight into how consumers are using their smart devices (consumer's health status and habits) by making predictions and searching for trends and patterns.
Using this information, I will then craft high-level recommendations for how these trends can be applied to Bellabeat's products, customers to make their marketing strategy more effective.

* Consider Key Stakeholders
  * Primary Stakeholders: Urška Sršen (CEO of Bellabeats) & Sando Mur (co-founder of Bellabeats)
  * Secondary Stakeholder: Bellabeat marketing analytics team.
    

### 2. Prepare ###

I will be using the public data from [FitBit Fitness Tracker Data](https://www.kaggle.com/arashnic/fitbit). There are 15 csv files downloaded.

Data is organised in both long and wide format.
 * wide format: each user's response to each variable is in different columns.
 * long format: each user is represented by many rows according to different date and time.

Let's determine the credibility of the data through an ROCCC Analysis.

**Reliability: Not Reliable**
- Low number of participants for survey (data collected from only 30 FitBit users, not representative population).
- These 30 users may be from the same group/type, which means data is not randomised.

**Original: Not Original**
- The responses were collected based on a distributed survey via Amazon Mechanical Turk, which is a 3rd party provided.

**Comprehensive: Not Comprehensive**
- There is information about daily activity, steps, and heart rate of each respondent, but the background information of each respondent is not provided. Background information about the survey participants's age, gender and location are unknown. Most participants may not be female, hence the trends derived from this dataset may not apply to Bellabeat products, which targets females.
- The FitBit product that is being analysed is also not provided, the 30 FitBit users may be using different FitBit products, which makes the data unreliable and not comparable for deriving trends. 
- Since the FitBit product that is being analysed is also not provided, it is not confirmed that the FitBit products used by the participants have similar functionality to BellaBeat's products. The trends derived from this analysis, applied to BellaBeat's marketing strategy may not be effective. 

**Current: Not Current**
- The survey responses were collected between 03.12.2016-05.12.2016. The trends derived may be outdated and not representative in 2021.

**Cited: Not Cited**
- There is not enough information to determine if Amazon Mechanical Turk is a credible source.

**Conclusion:** 
- Would not use this dataset to advise for business decisions/directions. Instead, analysis using this dataset should be treated as a prediction/intuition of a trend. The trend should be further verified through a more reliable and credible dataset.

### 3. Process ###

I will be using RStudio to perform the analysis and generate visualisations to identify trends.

* install the required R packages: 
```R
install.packages() & library()
```
```R
tidyverse ⇒ collection of R packages, which includes:
- ggplot2 ⇒ Data Visualisation
- tidyr ⇒ Data Cleaning
- readr ⇒ Importing Data
- dplyr ⇒ Data manipulation with functions

lubridate ⇒ Date transformations
skimr ⇒ Summary statistics
here ⇒ Easy file references
janitor ⇒ Data Cleaning
```


* import and load the downloaded dataset
```R
hourly_calories <- read.csv('/cloud/project/Fitabase Data 4.12.16-5.12.16/hourlyCalories_merged.csv')
hourly_intensities <- read.csv('/cloud/project/Fitabase Data 4.12.16-5.12.16/hourlyIntensities_merged.csv')
hourly_steps <- read.csv('/cloud/project/Fitabase Data 4.12.16-5.12.16/hourlySteps_merged.csv')


daily_activity <- read_csv("/cloud/project/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
daily_calories <- read.csv("/cloud/project/Fitabase Data 4.12.16-5.12.16/dailyCalories_merged.csv")
daily_intensities <- read.csv("/cloud/project/Fitabase Data 4.12.16-5.12.16/dailyIntensities_merged.csv")
daily_steps <- read.csv("/cloud/project/Fitabase Data 4.12.16-5.12.16/dailySteps_merged.csv")


sleep_day <- read_csv("/cloud/project/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
weight_log <- read_csv("/cloud/project/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv")
```


* quick glimpse of data: check for errors
```R
head() => view first few rows
colnames() 
all.equal() => Equality Test Between Two Data Tables
is.null() => inspect whether data object is null
anyNA()
```

  * inspect hourly data, daily data, sleep and weight data
```R
all.equal(select(daily_activity,Id,Calories),
  select(daily_calories,Id,Calories), check.attributes=FALSE) #true
all.equal(select(daily_activity,Id,SedentaryMinutes,LightlyActiveMinutes,FairlyActiveMinutes,VeryActiveMinutes,SedentaryActiveDistance,LightActiveDistance,ModeratelyActiveDistance,VeryActiveDistance),
 select(daily_intensities,Id,SedentaryMinutes,LightlyActiveMinutes,FairlyActiveMinutes,VeryActiveMinutes,SedentaryActiveDistance,LightActiveDistance,ModeratelyActiveDistance,VeryActiveDistance), check.attributes=FALSE) #true

#check types of each column
typeof(attr(select(daily_activity,Id,Calories), "row.names")) == typeof(attr(select(daily_calories,Id,Calories), "row.names")) #true
mode(attr(select(daily_activity,Id,Calories), "row.names")) #numeric
mode(attr(select(daily_calories,Id,Calories), "row.names")) #numeric
```

* sort & filter data: merge datasets with similar column names
```R
#merge hourly data
hourly_activity_1 <- merge(hourly_calories,hourly_intensities, by=c("Id","ActivityHour"))
hourly_activity <- merge( hourly_activity_1 ,hourly_steps, by=c("Id","ActivityHour"))

#merge daily data
daily_activity_1 <- merge(daily_activity,daily_calories, by=c("Id","Calories"))
daily_activity_2 <- merge(daily_activity_1, daily_intensities, by=c("Id","ActivityDay","SedentaryMinutes", "LightlyActiveMinutes","FairlyActiveMinutes","VeryActiveMinutes", "SedentaryActiveDistance", "LightActiveDistance", "ModeratelyActiveDistance", "VeryActiveDistance"))
daily_activity_3 <- merge(daily_activity_2, daily_steps, by=c("Id","ActivityDay"))
```

* Now, we have 4 databases, I will standardise the date formats across these 4 databases
1. hourly_activity
2. daily_activity_3
3. sleep_day
4. weight_log

```R
1. hourly_activity ⇒ hourly_activity_final
hourly_activity_final = 
hourly_activity %>%
	mutate(ActivityHour_2=ActivityHour) %>% #duplicate column
	separate(ActivityHour_2,into=c("Date","Time","dayornight"),sep=' ') %>%
	mutate("Day" = wday(as.Date(Date,format='%m/%d/%Y'), label=TRUE ))

#check date column type
mode(attr(select(hourly_activity_final,Date), "row.names")) #numeric

#Add a day column 
#wday(as.Date('4/15/2016',format='%m/%d/%Y'), label=TRUE) => Fri
mutate("Day" = (wday(as.Date(Date,format='%m/%d/%Y'), label=TRUE )


2. daily_activity_3 ⇒ daily_activity_final
daily_activity_final = 
daily_activity_3 %>%
	select(-ActivityDay)%>%
	select(-StepTotal)%>%
	select(-TrackerDistance) %>%
	rename(Date = ActivityDate)
 
 
3. sleep_day ⇒ sleep_day_final
sleep_day_final = 
sleep_day %>%
	separate(SleepDay,into=c("Date","Time","dayornight"),sep=' ')
 
 
 4. weight_log ⇒ weight_log_final
weight_log_final = 
weight_log %>%
	separate(Date,into=c("Date","Time","dayornight"),sep=' ')
```

* Do further inspection of databases to delete duplicate rows 
```R
duplicated(sleep_day_final) #returns 3 true 

sleep_day_final %>% distinct()

unique(sleep_day_final) #same as
distinct(sleep_day_final)

sleep_day_final %>% 
	filter(duplicated(sleep_day_final) == TRUE) #returns 3 results

sleep_day_final %>% 
	filter(Id == "4388161847") #verify duplicated 
 
hourly_activity_final %>% 
     filter(duplicated(hourly_activity_final) == TRUE) #no duplicates

daily_activity_final %>% 
     filter(duplicated(daily_activity_final) == TRUE) #no duplicates

sleep_day_final=
sleep_day_final %>% distinct() #remove the 3 duplicate rows

weight_log_final %>% 
     filter(duplicated(weight_log_final) == TRUE) #no duplicates
```

### 4. Analyse ###
```R
group_by() => before summarize() => group rows 
summarize() => many rows into one

geom_bar() => height of the bar proportional to the number of cases in each group (or if the weight aesthetic is supplied, the sum of the weights)
geom_col() => height of the bars to represent values in the data
geom_col() = geom_bar(stat = “identity”)
```

### **4a. bar graph: average calories VS day of the week** ###
![Screenshot%202021-09-09%20at%203 49 12%20PM](https://user-images.githubusercontent.com/70426064/137296780-fa902342-d0ce-4229-b9cb-b9e73210bf36.png)

We can see that there are different no of dataset for each day, hence we compute average instead of sum.

```R
hourly_activity_final %>%
group_by(Day, Id, Date) %>% #each user = one row for the whole day 
summarize(calories=sum(Calories))%>% #sum calories for the user for the whole day
group_by(Day) %>% 
summarize(AverageCalories=mean(calories)) %>% #sum calories for each day / no of data for days
ggplot()+
geom_col(mapping=aes(x=Day, y=,AverageCalories, fill=Day))+
labs(title="Average Calories against Day of the week", x='Day of the week', y='Average calories')+
geom_label(aes(x=Day,y=AverageCalories,label=as.integer(AverageCalories)))
```
![image](https://user-images.githubusercontent.com/70426064/137297247-b144f63e-f293-4f7a-9fd3-5cb18ecae912.png)

> From this graph, there is no distinct outlier. We can interpret that users are more active and make more use of health gadgets on days except Thursday. 





```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
