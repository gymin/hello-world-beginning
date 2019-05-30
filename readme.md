# Ford GoBike Data Exploration

## Dataset

The data consists of information regarding Ford GoBike bike sharing system until April 2019. 
There are 239,111 trip data with 16 features (trip duration in sec, start time and date, end time and date, start station ID,start station name, start station latitude, start station longitude, end station id, end station name, end station latitude,end station longitude, bike ID, user type, member year of birth, member gender, bike share for all trip).
Most variables are categorical in nature, and although some variables numeric, probably one most meaningul numeric variable would be trip duraiton. 
The dataset can be found in https://www.fordgobike.com/system-data
with feature documentation available in https://www.fordgobike.com/system-data


## Summary of Findings

In the exploration, I found that the majority of users are male and subscribers, and bikes tended not to be used for the return journey.
Overall it seems that the bike was used for average 10 minutes although the maximum limit that the payment allowed was 30-45 minutes. 
From the bivariate explaraton, I found that customers, female users, and people who returned the bike to the start point tend to use the service for longer duration than subscribers, male users, and people who did not return the bike to the start point. 
However, the multivariate exploration shows that customers (targetted for visitors and tourists) tend to keep the bike longer than subscribers (e.g. local residents). This is perhaps becasue subscribers have unlimited use of bikes up to 45 minutes, while a customer had only 30 minutes limited ride, and therefore, perhaps customers tended to keep it until its limit. 
It also shows that female customers tend to keep the bike longer than male cusotmers, while for subscribers female tend to use the bike for shorter duration than male. Moreover, the distribution of duration show that for a bike that returned to the start point, subscribers have two high points for the low duration and high duration, while customers have one high point towards the longer duration. 
This is perhaps caused by the fact that a subscriber has unlimited 45-minute rides where they could use it as many times as possible as long as it's under 45 mintues, while for customers they paid for a limited use of a bike, and therefore, they tend to use it for a longer time (probably until the maximum limit, which is 30 minutes), rather than a short time.    


## Key Insights for Presentation

For the presentation, I focus on showing the different characteristics of users based on their membership, gender and journey type,and its influence on trip duration. I start by introducing each variable alone, followed by their interrelationships with each other. 
First trip duration was plotted against three categorical variables, then categorical variables were compared with other categorical variables. Those bivariate plots led to the further investigation of sub groups based on journey type. 
Two subsets were created based on their journey type (i.e. return or no return journey), and detailed distribution of trip duration by user gender and user type was plotted. This showed different patterns of how the service was used based on user charateristics. 
