## Google Data Analytics Case Study: Bellabeat Fitness Trackers

### Project Overview

This analysis focuses on user behavior for 35 users who used fitness trackers during the period from March 12, 2016 to May 12, 2016. Users tracked various metrics, and some metrics were only recorded by a subset of the total user population. Metrics tracked included steps, calories, sleep, heartrate, and weight.
The goal for this project is to explore user behavior with fitness tracking devices.

### Preparation, Data Integrity, ETL, and Transformations

The dataset has 29 tables, organized by time units: day, hour, minute, second. My analysis combines the metrics by user and in time to determine relationships between the measurements and what users actually tracked. 
There are 35 unique entries for “user_id” in total among these tables. In the tables for exercise intensity, calories, METs, and steps, all 35 user IDs are present. Only subsets of users measured weight, sleep, and heart rate. 4 of the 35 users measured every metric that was in the dataset.

</br>
<img width="585" height="371" alt="horizontal bar chart comparing the number of participants for each measurement" src="https://github.com/user-attachments/assets/f3efc46b-7eb7-4dae-8d49-98f4d1c1c2d9" /></br>
</br>

I converted the time intervals to hours and stitched all the tables together to compare the measurements side-by-side. I aggregated the tables that were organized by seconds and minutes, and used UNION and JOIN statements. There was some overlap between the March-April and April-May time series, but the overlap was with identical rows and the duplicates were easily removed.

I loaded a complete table into Tableau featuring the 35 user ID’s and the total date range (March 12, 2016 at 12:00 AM through May 12, 2016 at 3 PM). This table allows comparisons to be made between all the measurements present in the dataset. With the table loaded into Tableau, it was fairly frictionless to use table calculations for any data transformation that was needed after this point. I calculated percentages of metrics like exercise intensity minutes and sleep value. I also created simple usernames like “User 01” to improve readability. 

### Analysis

The first thing I noticed about the data was that there were four metrics that had all 35 users present: steps, MET’s, calories, and exercise intensity. My suspicion was that these were all derived from the same measurements, perhaps a step counter. In particular, there isn’t really a way to directly measure “calories”, whether that means calories consumed or burned. I charted calories and MET’s together and the resulting relationship was close to linear, and it was basically perfectly linear when broken down by user.

</br>
<img width="521" height="560" alt="three line charts: 2 showing calories and METs plotted by hour of the day, and then a line for the ratio between them, which is perfectly straight" src="https://github.com/user-attachments/assets/c20ecb8a-a5f2-4167-9400-11a776395cdd" /></br>
</br>

This was interesting because the formula for the calorie-to-MET relationship is 

$$Calories = \frac{3.5METs(Body Weight[kg])}{200}$$

and I have both calories and METs for all 35 users- but I only have weight measurements for 13 of them. I used this formula to solve for body weight and generated a body weight measurement for all 35 users. 

I compared the weights for the 13 users who recorded their weight with the weight measurement I derived from the calories-to-METs formula. 

</br>
<img width="419" height="536" alt="vertical dual-axis bar chart showing the weights calculated from the calories to METs ratio overlaid on the actual measured weight for each of 13 users" src="https://github.com/user-attachments/assets/785f1813-a0f8-4724-b911-f631898d4f55" /></br>
</br>

The result showed that of the 13 people who measured their weight, <b>all 13</b> had a lower weight as part of their individual calories-to-METs formula. The difference was also more pronounced for users with higher measured weight (R-Squared of .571):

_All scatter plots shown in this analysis contain relationships with P values below 0.05, unless otherwise noted_

</br>
<img width="595" height="541" alt="scatter plot with the percent ratio of presumed to average weight on the Y axis, and measured weight on the X axis. The trend line shows that the presumed weight was lower as a share of measured weight for heavier users." src="https://github.com/user-attachments/assets/947f40a8-8730-4bae-9633-f9bf5d60f425" /></br>
</br>

My best guess here is that the fitness trackers propmped the users to input their weights, and they systematically underestimated them. 

### Relationships Between Metrics

As the metrics for steps, METs, calories, and daily activity are derived from the same inputs, there was no sense in searching for relationships between them - they were spuriously correlated. The insights here will come from how these activity measurements relate to the other metrics: sleep, heart rate, and weight.

These relationships have some limitations: First, the daily activity/step counter was the only fitness tracker device that had anything close to consistent usage. As mentioned before, the sleep and heart rate trackers were only used by a portion of the cohort, and usage was significantly less consistent among those users as well. Here are the Gantt charts showing when users tracked sleep and heart rate turing the two-month timespan:

</br>
<img width="1030" height="591" alt="horizontal Gantt chart showing which days each user measured their sleep" src="https://github.com/user-attachments/assets/be8f9931-adad-40c9-a953-9db1ff65a18b" />

<img width="1030" height="393" alt="horizontal Gantt chart showing which days each user measured their heart rate" src="https://github.com/user-attachments/assets/9d4282e3-4ac0-40bc-bd85-f6e1807d0eb2" /></br>
</br>

I began plotting the relationships between various metrics for user sleep, heart rate, and activity. This dataset has significant sample size issues: while all 35 unique users in the dataset tracked daily activity, there are only 25 users who measured any sleep, 15 users who measured heart rate at all, 12 users who measured both sleep and heart rate, and just 4 users who measured all three of sleep, heart rate, and weight. Below is the breakdown:

</br>
<img width="595" height="432" alt="diagram showing how many users measured each combination of their sleep, heart rate, and weight at any point in the dataset" src="https://github.com/user-attachments/assets/e329d6a7-4c24-44f6-a775-30abf525968b" /></br>
</br>

Users tracked activity (steps) very consistently (1935/1956 or 99%), but sleep, heart rate, and weight tracking were intermittent. For heart rate, in addition to just 15/35 or 43% of users tracking it at all, for users who did track it, they only did so on 467/836 or 56% of days during the interval they tracked activity.
There was a similar pattern for sleep: 25/35 or 71% of users tracked sleep at all, and of the users who did track sleep, at least one minute was recorded on 891/1485 or 60% of days. 
For weight, 13/35 or 37% of users tracked it at all, and measurements were made on 100/717 or 14% of days.

</br>
<img width="419" height="494" alt="vertical bar chart comparing percentages of days where activity, sleep, heart rate, and weight were recorded" src="https://github.com/user-attachments/assets/02474602-4326-4800-8518-c01d3a578202" /></br>
</br>

Due to these limitations, the insights from this analysis about sleep, heart rate, and weight behavior may be useful, but definitive statements would require more data.

I re-aggregated my table with SQL to organize the metrics by day instead of hour. After plotting many variablies against one another, I was able to yield some potentially useful insights:

</br>
<img width="543" height="541" alt="scatter plot with daily sleep hours on the Y axis and average maximum hourly heartrate on the X axis. There is a trend line showing a negative correlation." src="https://github.com/user-attachments/assets/4cfe1fb0-f667-4258-a4bd-e3298ffbd1e4" /></br>
</br>

The most striking association is between heart rate and sleep. Resting heart rate varies substantially among individuals, so I plotted the maximum hourly heartrate for each user to capture how often their heart rates were elevated. I found a negative correlation between max hourly heart rate and sleep, with a correlation coefficient of -.225, equivalent to a decrease of 2.25 hours of sleep for an increase of 10 beats per minute of average max hourly heartrate. The sample size of 12 is of course tiny, but the R-squared value of .426 made this relationship hard to overlook. The relationship between daily steps and sleep hours was also negative, but fell short of statistical significance.

There was also an interesting relationship between the <b>minimum</b> hourly heart rate (which I believe is a good proxy for resting heart rate) and daily activity. This fell just short of statistical significance with a p value of 0.07, but there is a clear negative relationship in this sample of n=15:

</br>
<img width="595" height="541" alt="scatter plot with daily METs on the Y axis and minimum hourly heart rate on the X axis. There is a trend line showing a negative correlation." src="https://github.com/user-attachments/assets/b621132b-60c5-42c8-90e1-750966bfa64f" /></br>
</br>

The one other interesting insight revealed from this dataset came when I plotted each of the four activity levels (sedentary, light, moderate, intense) against measured weight. The percentage of total minutes spent sedentary was correlated positively with weight, as expected: (R-squared = .34):

</br>
<img width="531" height="545" alt="scatter plot with weight on the Y axis and percentage of total activity minutes spent sedentary on the X axis. There is a trend line showing a positive correlation." src="https://github.com/user-attachments/assets/354ecb5f-c6c9-4d4c-b941-076412fadd9e" /></br>
</br>

However, there was no significant relationship between user weight and their percentage of time spend doing intense of moderate activity. The only significant difference was a decrease in <b>light activity</b>, which had a significant negative relationship with weight (R-squared of .355):

</br>
<img width="576" height="545" alt="scatter plot with weight on the Y axis and percentage of total activity minutes spent performing light activity on the X axis. There is a trend line showing a negative correlation." src="https://github.com/user-attachments/assets/70cde771-9117-40cc-b626-2cb8b7a326f9" /></br>
</br>

The increase in sedentary 'activity' with higher weight seems to come at the expense of light exercise; a finding that could have interesting implications if replicated with a larger sample size.

### Conclusions

I can feel confident in the following observations about the fitness tracker users in this dataset:

- There are 35 unique users in this dataset. 35 unique ID's appear in the daily activity table, and every ID in the sleep, heart rate, and weight tables also appears in the activity table. 

- The "daily activity" measurements were all derived from a single measurement, likely a step counter. METs and calories were derived from steps.

- Users were asked to provide their weight, and they systematically underestimated it. Users estimated their weight at 60%-86% of the actual number, which was hugely inaccurate.

- Measurements from the sleep and heart rate trackers were recorded far less frequently that from the pedometer. They were measured by fewer users, for less time, on fewer days.

- Maximum hourly heart rate was associated with less sleep. While a higher resting heart rate is generally seen as a sign of poor cardiovascular health, a higher maximum rate for each hour seems to indicate that the person was frequently participating in physical activity. The strong negative correlation between this measurement and sleep hours was surprising.

- As expected, sedentary activity was strongly positively correlated with weight. What was interesting was that there was no significant difference in more strenuous activity between lighter and heavier users; rather, the increased share of sedentary time by heavy users was coming at the expense of light activity. Put another way, doing light activity instead of being sedentary was associated with being less heavy in a way that participating in more strenuous activity was not.

### Business Recommendations

Bellabeat can use this analysis to improve the way they design and market their products. Based on the data and my conclusions, I suggest the following:

- **Wrap data collection for activity, sleep, and heart rate into a single wearable** (in this case the Bellabeat Leaf). The biggest limitation of the fitness dataset I analyzed was the lack of overlap between measurements. If there is discomfort from users about having to see heart rate and sleep data displayed on their tracker, they ought to be able to customize it to display what they want (perhaps starting with steps and calories burned). But the other information should be collected unless the user explicitly opts out.

- **Gather accurate information about users' weight.** Weight is a sensitive topic, and it is understandable that users would not want to step on the scale before trying a fitness wearable, and that they would be hesitant to be constantly reminded of a weight measurement that they are uncomfortable with. However, it is important to use accurate measurments to pursue health goals. 

  Users could be gently reminded to input their accurate weight, perhaps with the context that a     higher weight means more calories are being burned from exercise, and they deserve to know how     hard they are working! If Bellabeat wanted to make an addition to their product line, they could   also release a high-tech scale and market it as a "comprehensive wellness platform" or something   of that nature, and have it function as a refresh for their health data as a whole. My             recommendation here is for the scale to not display weight at all, but rather input it into the    Bellabeat app's database and give the user a fun summary of their activity and other metrics       since the last time they stepped on it.

- **Pursue a marketing strategy around the importance of light activity.** My analysis shows that light activity is associated with lower weight. It is not possible to infer causation from this sample, but it is uncontroversial that any activity is better than none, and users are likely to gain satisfaction from knowing that relatively easy light activities are having a positive impact on their health. The marketing campaign could show users video or diagrams of simple and fun activities like walking that they already enjoy doing, and show examples of users performing these activities and being greeted with positive feedback from the Bellabeat app.

- **Make positive feedback central to the user experience.** The best thing that Bellabeat can do for their business is make people feel good about using their products, and excited to recommend them to others. Design of the physical products is a strength of Bellabeat as a company, and it is important to design the user experience with a similar eye for detail. 
A few suggestions:

  -Give weekly updates featuring the most postitive spin on what the user did the past week. This     could include strong numbers for metrics like sleep, steps, calories, frequency of movement,       heart rate, and others. If the metrics aren't amazing that week, the app could make a favorable    comparison to something from the past, or point to a rolling average or other aggregated metric    to give a positive assessment
  
  -Use rewards and encouragement to users to help them include more information when they view        their metrics. For example, if a user decides to add heart rate to a dashboard on the Bellabeat    app, they could be praised simply as being a health-conscious human, and they might get            further positive feedback when metrics come in.
  
  -Allow users to unlock wellness tiers for consistent use. Ideally the product can be worn and       used passively 24/7, but this can be encouraged with a mechanism akin to the Snapchat streaks,     where the user is praised for continuous use, and feels subtle loss aversion to the idea of        discontinuing it. This mechanism can be designed so that there is always another tier that         feels reachable with just a bit more consistency. It should also allow for users to feel           comfortable after they do have an interruption: they could be reminded of their perseverance       and the feedback could imply that the interruption was not their fault, and they deserve credit    for getting back into good habits.

 



