
<center>
    <h1 style="color: #4684c7">
Bellabeat Project for Google Data Analytics Certificate
    </h1>
</center>
<center>
    <h3>
        Analyzing non-Bellabeat smart devices to gain insights
    </h3>
</center>

<center style="color: #757373">
Tyrese Mathenlall, September 2025
</center>

<h3 style="color: #4684c7">
About the company
    </h3>

Bellabeat is a wellness company founded by Urška Sršen and Sandro Mur in 2014. It is best known for its Leaf smart jewelry wearable line.

They are a pioneer in the fem-tech realm, Bellabeat is a women’s wellness
company that has helped millions of women track their cycle,
pregnancies and live more in sync with their cycles.

<h3 style="color: #4684c7">
Questions that will drive the analysis
    </h3>

1. What are some trends in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat marketing strategy?

<h3 style="color: #4684c7">
Business task
    </h3>

To analyze smart device usage data in order to gain insight into how consumers use non-Bellabeat smart devices. Use newly found insights to unlock new growth opportunities for the company.

<h3 style="color: #4684c7">
Data Source
</h3>

The data used for this analysis was recommended by Google as part of the Google Data Analytics Certificate Capstone course. It can be downloaded from Kaggle, click <a href ="https://www.kaggle.com/datasets/arashnic/fitbit/data"> here </a>  to view or download the dataset - Licensed by CC0: Public Domain and distributed by Mobius.

<h3 style="color: #4684c7">
Data Limitations
</h3>

Although the data used is highly relevant for the this analysis, it does lack a few key factors:

* _Sample size_: There are a total of 33 users with daily entries for 31 days. It is an extremely small dataset to make any marketing-driven decisions.
* _Lack of demographic data_: Detailed demographic and socioeconomic information on the users, including sex, age, career, education, and income were not provided, which limits the available insights.
* _Outdated data_: As of 2025, the dataset is nearly a decade old. Due to its age, it may not reflect current trends.

It is important to understand that this data came from a third party source, we cannot confirm if the tests were done in a controlled environment or it was acquired randomly without verifying. Therefore, I would say it is unlikely we can trust this data to make impactful decisions. The result of this analysis can give us an overall idea of how people use their smart devices.

<h3 style="color: #4684c7">
Loading packages
</h3>

```
library(tidyverse)
library(janitor)
```
<h3 style="color: #4684c7">
Import Data
</h3>

Before importing the data, I went through each file to ensure the data consistency and validity using Excel.

```
d_activities <- read.csv("/dailyActivity_merged.csv")
d_sleep <- read.csv("/sleepDay_merged.csv")

glimpse(d_activities)
glimpse(d_sleep)
```

<h3 style="color: #4684c7">
Cleaning and Fomatting
</h3>

Now that the data has been imported, it is time to clean and format the data.

I picked up irregular naming for column names and found the date column is a character data type for all tables.

```
d_activities <- d_activities %>% 
  clean_names() %>% # To clean column names
  rename(date = activity_date) %>% # To change date name
  mutate(date = mdy(date)) %>% # To fix date column data type
  distinct() %>% # To remove duplicates if any
  subset(calories != 0) # To remove blank entries

d_sleep <- d_sleep %>% 
  clean_names() %>% # To clean column names
  mutate(sleep_day = mdy_hms(sleep_day)) %>% # To fix date column data type
  rename(date = sleep_day) %>%
  distinct() # To remove duplicates if any
```

In the above script for `d_activities`, a subset function was used to filter out blank data entries using the calories burnt. 

To check for NA values:

```
sum(is.na(d_activities))
sum(is.na(d_sleep))
```

<h3 style="color: #4684c7">
Data Analysis and Visualisation
</h3>

Given it is logical that more activity equals more calories burnt, I would like to test the logic of the data to show this.

<img width="436" height="356" alt="image" src="https://github.com/user-attachments/assets/481e5b03-f4ff-4ffb-a7a8-5ca692e6d805" />

As you can see from the abo-apologies, please excuse the cat, I'm not sure how they got in here.

```
ggplot(d_activities, aes(x = total_steps, y = calories)) +
  geom_point(color = "#8a6501") +
  geom_smooth() +
  geom_rug(color = "red") +
  labs(title = "Steps Done vs. Calories Burnt",
       x = "Total Steps",
       y = "Calories Burnt"
       )
```
<img width="436" height="356" alt="image" src="https://github.com/user-attachments/assets/7a3f6d91-0f77-4548-9ebb-ab8562e80cb8" />

That's better. As you can see, there is a positive trend, which passes the logical test. Something to highlight is the widening of the shadow around the end of the trend line, that is the confidence interval. From this, we can take that there is less data and uncertainty. 

Now, let's look at statistical summaries for some key factors, these are daily steps, calories burnt, sleep in minutes, distance walked, sedentary in minutes and active minutes.

```
summary(d_activities [, c("total_steps", 
                          "calories", 
                          "sedentary_minutes", 
                          "total_distance", 
                          "very_active_minutes", 
                          "fairly_active_minutes", 
                          "lightly_active_minutes")])
```
<img width="554" height="341" alt="image" src="https://github.com/user-attachments/assets/9565ce83-6e30-42b6-8c94-50c108c363e7" />

```
summary(d_sleep$total_minutes_asleep)
```
<img width="388" height="57" alt="image" src="https://github.com/user-attachments/assets/03bb9408-920a-4310-bb20-4673ee33a214" />


The mean is greater than the median which means there is a positively skewed distribution for all tables.

We can also see the average steps per day is 7 671 which is above the average daily steps as per article published by <a href = "https://www.medicalnewstoday.com/articles/average-steps-per-day"> MedicalNewsDaily </a>. We can take that these individuals are quite active in their daily lives but it is still slightly below the recommended daily steps.

If you look at he maximum values for `very_active_minutes`, `fairly_active_minutes`, `lightly_active_minutes`, you will see the maximum is higher on the `lightly_active_minutes` column, this indicates most people are lightly active.

Another interesting discovery that catches my attention is the maximum sleep in the `d_sleep` table. The maximum sleep in one day is 796 minutes which converts to 13.2 hours, that is just over 50% of the day. We also find the average sleep is 432.5 minutes, which converts to 7.2 hours. Which is the recommended duration of sleep for adults. 

Looking at the sedentary minutes, the average is 989 minutes which converts to 16.4 hours in a day. This indicates that the average user from this study is idle or inactive for just over 65% of the day.

In the `d_activities` table, we find the maximum steps to be 36 019 in a single day which is quite interesting. Since we do not have any demographical data, it is difficult to get an idea of this user's routine. However, we will be investigating that data entry.

```
d_activities %>% 
  filter(total_steps > 30000)
```

<img width="569" height="81" alt="image" src="https://github.com/user-attachments/assets/9c55d5f6-f2f8-4bf0-b94e-93459665c6db" />

This user has walked 36 019 steps within a distance of 28.03 miles (45,1 km). This does raise the question: is this an outlier or inaccurate? Using <a href="https://www.omnicalculator.com/sports/steps-to-km#km-to-steps-conversion"> this </a> steps calculator on Omnicalculator, below are the estimated distances it would take to reach the users steps count. We are excluding factors such height and stride since we were not provided with this data:

Male: ~17 miles (27.3 km).

Female: ~15 miles (24.1 km).

Let's also take a look at the other entries for this user.

```
d_activities %>% 
  filter(id == 1624580081) %>% 
  select(total_steps, total_distance)

d_activities %>% 
  filter(id == 1624580081) %>% 
  select(total_steps, total_distance) %>% 
  summarise(avg_steps = mean(total_steps), 
            avg_dist = mean(total_distance),
            std_dev = sd(total_steps))
```
<img width="114" height="480" alt="image" src="https://github.com/user-attachments/assets/c8ea6b84-3b04-42c1-a160-d96b967cc55d" />

<img width="207" height="71" alt="image" src="https://github.com/user-attachments/assets/7a6d6c04-963f-4ec1-aa96-7e4f3828c287" />

Results from the above investigation indicates that this entry is inaccurate and an outlier, resulting in its removal.

```
d_activities <- d_activities %>% 
  subset(total_steps != 36019)
```

Let's create a new column in `d_activities` that shows the total active minutes for each day.

```
d_activities <- d_activities %>% 
  mutate(total_active_minutes = very_active_minutes + 
           fairly_active_minutes + 
           lightly_active_minutes)

head(d_activities)
```

<img width="566" height="265" alt="image" src="https://github.com/user-attachments/assets/b84e01c7-afe5-487d-be41-bea070b3cb08" />

I would like to find out if there is a correlation between the users who have a high step count or active minutes and their sleep. We will need to join the `d_activities` and the `d_sleep` tables to find the answer.

```
merge_act_sleep <- merge(x = d_activities, y = d_sleep, by = c("id", "date"))

ggplot(merge_act_sleep, aes(x = total_steps, y = total_minutes_asleep)) +
  geom_point(color = "#5e040d") +
  geom_smooth() +
  geom_rug(color = "red") +
  labs(title = "Sleep vs. Steps",
       x = "Total Steps",
       y = "Total Minutes of Sleep")
```

<img width="436" height="356" alt="image" src="https://github.com/user-attachments/assets/802a4380-6c46-436b-bed4-e2ee1bd0e8fa" />

```
avg_sleep_act <- merge_act_sleep %>% 
  group_by(date) %>% 
  summarise(avg_act = mean(total_active_minutes), 
            avg_sleep = mean(total_minutes_asleep))

ggplot(avg_sleep_act, aes(x = avg_sleep, y = avg_act)) +
  geom_point() +
  geom_smooth() +
  geom_rug(color = "red") +
  labs(title = "Total Active Minutes vs. Sleep",
       x = "Average Sleep in Minutes",
       y = "Average Active Minutes")
```

<img width="436" height="356" alt="image" src="https://github.com/user-attachments/assets/aede1313-71f3-41eb-a805-c1ce7162a3b2" />

From the above, there is no direct correlation between steps and sleep. The relationship is quite linear, there is no steady upward or downward trend. 

We can also see the same for the correlation between the average activity and average sleep.

If you look at the grey shadow around the trend line, we see it is very wide at some points. This indicates a poor confidense interval, meaning there is less data and more uncertainty in those areas.

Next, let's look at the average sleep in minutes per day of the week.

```
# Extract weekday
sleep_with_days <- d_sleep %>% 
  mutate(day = weekdays(date))

# Group and find averages
avg_days_sleep <- sleep_with_days %>% 
  group_by(day) %>%
  summarise(avg_sleep_per_day = mean(total_minutes_asleep)) %>% 
  mutate(total_sleep_hours = (avg_sleep_per_day / 60)) %>% 
  arrange(-avg_sleep_per_day, .by_group = TRUE)

View(avg_days_sleep)

ggplot(avg_days_sleep, aes(x = day, y = total_sleep_hours)) +
  geom_bar(stat = "identity",  fill = "#3c044f") +
  labs(title = "Average Sleep Per Day",
       x = "Day of Week",
       y = "Avg Sleep (hours)") +
  geom_text(aes(label = round(total_sleep_hours, 2)),vjust = -0.5)
```

<img width="315" height="210" alt="image" src="https://github.com/user-attachments/assets/0e288eaa-617f-43a9-97bf-69d3f1fdab5a" />

<img width="436" height="356" alt="image" src="https://github.com/user-attachments/assets/ba2fbfdb-9d02-42ea-ac9e-4c20176f651d" />

From the above, we can gather the users slept more on Sundays and the least on Thursdays.

We will be doing the same as the above for daily steps and weekdays.

```
d_activities <- d_activities %>% 
  mutate(day = weekdays(date))

avg_steps_day <- d_activities %>% 
  group_by(day) %>% 
  summarise(avg_steps_per_day = mean(total_steps)) %>% 
  arrange(-avg_steps_per_day, .by_group = TRUE)

View(avg_steps_day)

ggplot(avg_steps_day, aes(x = day, y = avg_steps_per_day)) +
  geom_bar(stat = "identity", fill = "#26c970") +
  labs(title = "Average Steps Per Day",
       x = "Day of Week",
       y = "Avg Steps") +
  geom_text(aes(label = round(avg_steps_per_day, 1)),vjust = -0.5)
```

<img width="203" height="207" alt="image" src="https://github.com/user-attachments/assets/8222dbb3-35f8-440e-beba-445c3f38c830" />

<img width="436" height="356" alt="image" src="https://github.com/user-attachments/assets/ba568527-a111-4e1f-9fd1-d89b2b872106" />

From this graph, we can find that users walk more on Saturdays and the least on Sundays. 

This finding does complement our previous findings on average sleep per day. The users slept more on Sundays and we now know they also walk the least on the same day.

Next, I will find the averages for steps, distance, total active and step for each user.

```
avg_all <- merge_act_sleep %>% 
  group_by(id) %>% 
  summarise(avg_steps = mean(total_steps), 
            avg_distance = mean(total_distance), 
            avg_activity = mean(total_active_minutes), 
            avg_sleep = mean(total_minutes_asleep)) %>% 
  arrange(id, .by_group = TRUE)

View(avg_all)
```

<img width="320" height="459" alt="image" src="https://github.com/user-attachments/assets/6f9d4637-6345-434b-aeb7-e3f4efca86b6" />

Lastly, I would find it interesting and quite insightful if we group or categorise the the daily steps. To do this, I will use the article published by <a href = "https://www.medicalnewstoday.com/articles/how-many-steps-should-you-take-a-day#for-general-health"> MedicalNewsToday </a> to get the criteria.

Below is the criteria:

* Inactive: Less than 5 000 steps
* Active: Between 5 000 and 8 000
* Very Active: More than 8 000

```
avg_step_by_id <- d_activities %>% 
  group_by(id) %>% 
  summarise(avg_steps = mean(total_steps))

cat_steps <- avg_step_by_id %>% 
  mutate(category = case_when(
    avg_steps >= 8000 ~ "Very Active",
    avg_steps >= 5000 ~ "Active",
    TRUE ~ "Inactive"
         )
    )

ggplot(cat_steps, aes(x = category)) +
  geom_bar(fill = "#05005c") +
  labs(title = "Average Steps Categorised",
       x = "Category",
       y = "No. of Users")
```

<img width="436" height="356" alt="image" src="https://github.com/user-attachments/assets/b94326ed-2c2c-4dee-8f32-6e66751fcd2a" />

We can find that majority of the users are very active, meaning they reach 8 000 steps or more.

<h2 style="color: #4684c7">
Conclusions
</h2>

As mentioned in the beginning of this analysis, the data used has many limitations. I would strongly advise not to make direct market-related decisions based on these findings. However, we can look at the insights they provide.

#### Activity Insights

We gathered that people who do use a smart device are more lightly active but they do above the average steps which is still below the recommended daily steps.

#### Sleep Insights

Another discovery was regarding their sleep. The maximum sleep was just over 13 hours of the day. This finding tells us that a single user slept for over 50% of the day which is high but we do not know the user's demography to determine any other insights. For example, this person could work night shift.

We also found the average sleep is 7.2 hours, which is the recommended sleep for adults. We can take that these users have an overall good sleep cycle.

#### Sleep Per Day

It was derived that users slept more on Sundays and the least on Thursdays. Later on in the analysis, we found that users also walked the least on Sundays which does reinforce that users are more inactive on Sundays.

#### Steps/Activity vs. Sleep

From our findings, there is no strong relationship between the amount of steps someone does and the amount of sleep they get. Similarly, we found the same results for average active minutes and average sleep.

#### Steps Per day

I extracted the average steps per day and found users walked the most on the Saturdays and the least on Sundays.

#### Sedentary Minutes

We found that users are idle for more than 65% of the day. This could indicate that they have office jobs that require them to be at a fixed placed through the day. That percentage is extremely high and it would benefit them to take a break or a short walk to reduce their sedantary time.

#### Categorising Steps

Three categories were created for daily steps, namely: _Inactive_, _Active_ and _Very Active_.

I found that majority of the users were very active, meaning they did more than 8 000 steps per day. Inactive users were in the minority that indicates users from this data are overall active in their daily lives.

<h2 style="color: #4684c7">
Recommendations for Bellabeat
</h2>

#### 1.  Focus on Marketing to an Active Audience

From the data we gathered, majority of the users are quite active by doing more than 8 000 steps. This means Bellabeat should market to those who are already into a healthy lifestyle by introducing new features and accessories that will bring in new customers.

We also discovered that Sunday is considered a rest day to most people as they are least active and sleep the most. Try to promote rest and recovery into their program which is beneficial to active people. You can also add a feature that makes Sunday a relaxing day with minimal activity, depending on the users goals. Endorse stretching, light yoga or even meditation to improve mental health. This will also serve as a reward for them since they were active throughout the week.

#### 2. Use Modern Trends

Gamification is popular trend in modern day to encourage people to be active. People want short term and long term achievements to prove they accomplished their goal. From our analysis we found people are already active, this means they will most likely respond well to new weekly or monthly challenges that creates a routine for them. An example would be, "Complete 3 workouts this week."

Create an online leaderboard where users can compete with each other. A competitive environment is a great way to maintain your current customer base and bring in new customers. 

#### 3. Integrate with third-party apps

Most people want an easy setup with the least restrictions. It is important to ensure the compatibility with other fitness or nutrition apps. This creates an ecosystem where your product could possibly be positioned as the hub or central point.
