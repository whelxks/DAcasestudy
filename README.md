# DA Case Study: Bellabeat

## Introduction ##
* Mission: a high-tech company that manufactures health-focused smart products for women

* About: Bellabeat products became available through a growing number of online retailers in addition to their own e-commerce channel on their website. Their current marketing strategies include: 
  * **Traditional advertising media**, such as radio, out-of-home billboards, print, and television, but focuses on digital marketing extensively
  * **Google Search**, maintaining active Facebook and Instagram pages, and consistently engages consumers on Twitter.
  * **Video ads** on Youtube and **display ads** on the Google Display Network to support campaigns around key marketing dates.

* Business goals: Bellabeat aims to become a larger player in the global **smart device** market. By analyzing smart device fitness data, we could potentially unlock new growth opportunities for the company.

## Data analysis process (6 steps): ##

1. Ask

* Identify the business task
I have been asked to analyze smart device data (data collected on activity, sleep, stress, and reproductive health). 
This helps me to gain insight into how consumers are using their smart devices (consumer's health status and habits) by making predictions and searching for trends and patterns.
Using this information, I will then craft high-level recommendations for how these trends can be applied to Bellabeat's products, customers to make their marketing strategy more effective.

* Consider Key Stakeholders
  * Primary Stakeholders: Urška Sršen (CEO of Bellabeats) & Sando Mur (co-founder of Bellabeats)
  * Secondary Stakeholder: Bellabeat marketing analytics team.
    

2. Prepare 

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


```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
