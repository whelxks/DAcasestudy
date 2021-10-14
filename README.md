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
  
<br/>

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

<br/>

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
<br/>

### 4. Analyse ###
```R
group_by() => before summarize() => group rows 
summarize() => many rows into one

geom_bar() => height of the bar proportional to the number of cases in each group (or if the weight aesthetic is supplied, the sum of the weights)
geom_col() => height of the bars to represent values in the data
geom_col() = geom_bar(stat = “identity”)
```
<br/>

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


<br/>

### **4b. line graph: average calories VS time of the day** ###
![image](https://user-images.githubusercontent.com/70426064/137298055-b9582561-678d-406d-9c6d-838d205c075d.png)
![image](https://user-images.githubusercontent.com/70426064/137297997-3eae9f53-d8cb-4c61-b910-1a83b890564d.png)

We can see that there are different no of dataset for each hour in time, hence we compute average instead of sum.

```R
#single value
times = strptime("4/16/2016 10:00:00 PM", "%m/%d/%Y %I:%M:%S %p")
times$hour #22

#create new time column
ActivityHour_Time = 
hourly_activity_final %>%
pull(ActivityHour) %>%
strptime(format= "%m/%d/%Y %I:%M:%S %p") %>%

#extract only hours from new time column, as a new column in hourly_activity_final database
hourly_activity_final$ActivityHour_Time <- format(as.POSIXct(ActivityHour_Time), format = "%H")

hourly_activity_final %>%
group_by(ActivityHour_Time) %>% 
summarize(AverageCalories=mean(Calories)) %>% 
ggplot()+
geom_line(mapping=aes(x=ActivityHour_Time, y=,AverageCalories, group=1), color="purple")+
labs(title="Average Calories against Time", x='Time of the Day (in 24hr)', y='Average Calories')+

#geom_point(mapping=aes(x=ActivityHour_Time, y=,AverageCalories), color="purple")
#geom_label(aes(x=ActivityHour_Time,y=AverageCalories,label=as.integer(AverageCalories))
```
![image](https://user-images.githubusercontent.com/70426064/137298075-69a07dd9-842e-491a-b22e-d7e720a4feb8.png)

> From this graph, we can see that users are most active from 5-7pm daily. Reminders on exercise should be targeted at this time.

<br/>

### 4c. Boxplot: calories burned VS steps/distance taken ###
* Hypothesis: More steps = More distance = More calories burned

```R
#split cases according to median value from summary(daily_activity_final)

daily_activity_final %>%
summarise(
distance = factor(case_when(
    TotalDistance < 5.2 ~ "< 5.2 miles",
    TotalDistance >= 5.2 ~ "> 5.2 miles",
),levels = c("> 5.2 miles", "< 5.2 miles")),
steps = factor(case_when(
    TotalSteps < 7406 ~ "< 7406 Steps",
    TotalSteps >= 7406 ~ "> 7406 Steps",
),levels = c("> 7406 Steps", "< 7406 Steps")),
Calories) %>%
ggplot(aes(x=steps,y=Calories,fill=steps)) +
    geom_boxplot() +
    facet_wrap(~distance)+
    labs(title="Calories burned by Steps and Distance",x=NULL) +
    theme(legend.position="none")
```
![image](https://user-images.githubusercontent.com/70426064/137298107-fb5bc787-3353-471d-a838-718bc040a5d7.png)

> When distance travelled is >5.2 miles, calories burned is higher when user takes less steps (<7406 steps), which proves our previous hypothesis wrong. This means that for the distance range of >5.2 miles, intensity of the steps, such as taking bigger strides matters more than the amount of steps. <br/><br/>Reminders should be set to encourage users to take bigger strides when walking to lose weight optimally.

<br/>

### 4d. Line/Scatter plot: average sleep time VS percentage of sedentary time ###
* Hypothesis: More healthy user type = More sleep

* Clean data tables again
```R
#merge daily_activity_final and sleep_day_final
#keep all rows from both database => merge(all=TRUE)

# %>% select(-SleepDay, -TrackerDistance) #this is to remove rows

daily_activity_sleep <- 
merge(daily_activity_final, sleep_day_final, by = c("Id", "Date"),all=TRUE) %>%
drop_na() #diff user has diff no of rows (some days not logged)
```

* This is not an accurate method to split user types as output has many NA
```R
type = factor(case_when(
    AverageSedentaryMinutes > 712.1695 & AverageLightlyActiveMinutes < 216.8547 & AverageFairlyActiveMinutes < 18.03874 & AverageActiveMinutes < 25.18886 ~ "Sedentary",
    AverageSedentaryMinutes < 712.1695 & AverageLightlyActiveMinutes > 216.8547 & AverageFairlyActiveMinutes < 18.03874 & AverageActiveMinutes < 25.18886 ~ "Lightly Active",
    AverageSedentaryMinutes < 712.1695 & AverageLightlyActiveMinutes < 216.8547 & AverageFairlyActiveMinutes > 18.03874 & AverageActiveMinutes < 25.18886 ~ "Fairly Active",
    AverageSedentaryMinutes < 712.1695 & AverageLightlyActiveMinutes < 216.8547 & AverageFairlyActiveMinutes < 18.03874 & AverageActiveMinutes > 25.18886 ~ "Very Active",
),levels=c("Sedentary", "Lightly Active", "Fairly Active", "Very Active")
```

* Instead, we split like this
```R
#summarise(count=n()) #24 users

#sum for each user all the mins, then average
daily_activity_sleep %>%
group_by(Id) %>% 
summarize(AverageActiveMinutes=mean(VeryActiveMinutes), 
AverageFairlyActiveMinutes=mean(FairlyActiveMinutes),
AverageLightlyActiveMinutes=mean(LightlyActiveMinutes),
AverageSedentaryMinutes=mean(SedentaryMinutes),
PercentSedentary=(AverageSedentaryMinutes/(AverageActiveMinutes+AverageFairlyActiveMinutes+AverageLightlyActiveMinutes+AverageSedentaryMinutes))*100)

#According to research: 
#undersleep <7 hrs:  <420 mins
#normalsleep >=7 & <=8 hrs: >=420 & <=480 mins
#oversleep >8 hrs: >480 mins

daily_activity_sleep %>%
group_by(Id) %>% 
summarize(
AverageSleepTime=mean(TotalMinutesAsleep),
SleeperType = factor(case_when(
    mean(TotalMinutesAsleep) < 420 ~ "Bad Sleeper",
    mean(TotalMinutesAsleep) >= 420 & mean(TotalMinutesAsleep) <= 480 ~ "Normal Sleeper",
    mean(TotalMinutesAsleep) > 480 ~ "Over Sleeper",
),levels=c("Bad Sleeper", "Normal Sleeper", "Over Sleeper")))
```

![image](https://user-images.githubusercontent.com/70426064/137300922-5a4b9f2e-60bc-4a29-bded-8731bb9bf1a9.png)
![image](https://user-images.githubusercontent.com/70426064/137300934-8a35cd23-4e77-418d-8155-9bd58647283e.png)



* In Graph Format,
```R
daily_activity_sleep %>%
group_by(Id) %>% 
summarize(AverageActiveMinutes=mean(VeryActiveMinutes), 
AverageFairlyActiveMinutes=mean(FairlyActiveMinutes),
AverageLightlyActiveMinutes=mean(LightlyActiveMinutes),
AverageSedentaryMinutes=mean(SedentaryMinutes),
PercentSedentary=(AverageSedentaryMinutes/(AverageActiveMinutes+AverageFairlyActiveMinutes+AverageLightlyActiveMinutes+AverageSedentaryMinutes))*100,
AverageSleepTime=mean(TotalMinutesAsleep)) %>%
ggplot(aes(x=PercentSedentary, y=AverageSleepTime)) + geom_point()+geom_smooth(formula = y ~ x, method = "loess")+
geom_rect(aes(xmin = min(as.integer(PercentSedentary)), 
                xmax = max(as.integer(PercentSedentary)), 
                ymin = min(AverageSleepTime), 
                ymax = 420), fill="red", alpha=0.005)+
geom_rect(aes(xmin = min(as.integer(PercentSedentary)), 
                xmax = max(as.integer(PercentSedentary)), 
                ymin = 480, 
                ymax = max(AverageSleepTime)), fill="red", alpha=0.005)
```

![image](https://user-images.githubusercontent.com/70426064/137298167-7ee71337-97d9-45c6-a7c1-ca5ba2d8ad4e.png)


> Red boxes on this graph show under sleep (<420 mins) and over sleep (>480 mins) portions.<br/><br/>From this graph, we can deduce that individuals who are less sedentary (lower percentage of sedentary time) are in normal sleeping (≥=420mins & ≤480mins) range.<br/><br/>Reminders should encourage users to adhere to the normal sleeping range to attain a healthier lifestyle.

<br/>

### 5. Share: Conclusions/Trends derived ###

1. Users are more active on days except Thursday. 
2. Users are most active from 5-7pm daily. 
3. For the distance range of >5.2 miles, intensity of the steps, such as taking bigger strides matters more than the amount of steps. 
4. Individuals who are less sedentary are in normal sleeping (≥=420mins & ≤480mins) range.

<br/>

### 6. Act: Proposed Marketing Strategies ###

1. Marketing Ads should be focused on days except Thursdays.
2. Reminders on health gadgets should be more frequent from 5-7pm.
3. Reminders when users are walking distances greater than 5.2 miles should involve tips on increasing intensity instead of increasing distance.
4. Reminders should encourage users to sleep within ≥=420mins & ≤480mins to become lead a less sedentary lifestyle.



