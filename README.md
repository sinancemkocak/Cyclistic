# Cyclistic
Google Data Analytics Certificate Capstone Project

# How do annual members and casual riders use Cyclistic bikes differently?

## Introduction

This is my capstone project for the Google Data Analytics certificate from Coursera.

Cyclistic is a fictitious bike-share company that seeks for a new strategy to set standards for its future success. It has more than 5800 bikes and 690 stations and offers reclining bikes, hand tricycles and cargo bikes in addition to traditional bikes. A majority of bike riders opt for traditional two-wheel bicycles, while about 8% of riders use assistive options. Most of its users ride bikes for leisure, with about 30% using bikes to commute to work each day. 

Cyclistic’s current marketing strategy is building general awareness and appealing to broad consumer segments. Cyclistic offers single-ride passes, full-day passes and annual membership for customers. Customers who purchase single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members. 

The finance analyst of the company states that the annual members are much more profitable customers than casual riders. Lily Moreno, the director of marketing, believes that increasing the number of annual memberships is the key to the company's future growth. Therefore; the goal set by her is to design marketing strategies for converting casual customers into annual members. 

## Business Task

Identify how annual members and casual riders differ when using Cyclistic to deliver meaningful findings for the marketing analyst team and director of marketing on deciding how to transform casual riders to annual members.

### Questions to ask:

<ul>
  <li><b>How do annual members and casual riders differ?</b></li> 
  <li>Why would casual riders buy a membership? </li>
  <li>How could digital media affect their marketing tactics? </li>
</ul>

### Key stakeholders

The key stakeholders are: 
<ul>
  <li>Lily Moreno: The director of marketing</li>
  <li>Cyclistic marketing analyst team</li>
  <li>Cyclistic executive team</li>
</ul>

## Data Source

Since Cyclistic is a fictitious company, the data used in this analysis are the historical trip data of a Chicago bike share company named Divvy. I used the Divvy monthly trip data of the previous 12 months (October 2020 to September 2021), in order to notice the rider trends for members and casual riders. 

The data is collected by the company from sensor data from the bike docking stations and it is uptodate. The data has been made available by Motivate International Inc. under this [license](https://www.divvybikes.com/data-license-agreement).

The data is originally stored as comma-separated value(.csv) files. I downloaded 12 .csv files with respect to the last 12 months. The datasets are downloaded from the <a href='https://divvy-tripdata.s3.amazonaws.com/index.html'>company's website</a> and reflect details such as ride id, date and time of the start and end of a trip, locations, and rider type of every ride from October 2020 to September 2021.

I used RStudio to import the datasets and merge them into a table to clean the data. Again, RStudio was my choice for analyzing and visualizing the findings.

### Data Fields (Data Dictionary)
* ride_id -- unique identifier of a particular bike trip
* rideable_type -- type of bike used (classic bikes, electric bikes)
* started_at -- date and time when the bike was removed from a dock
* ended_at -- date and time when the bike was returned to a dock
* start_station_name -- location of the starting docking station
* start_station_id -- alphanumeric identifier of the starting dock
* end_station_name -- location of the ending docking station
* end_station_id -- alphanumeric identifier of the ending dock
* start_lat -- latitude of the starting docking station
* start_lng -- longitude of the starting docking station
* end_lat -- latitude of the ending docking station
* end_lng -- longitude of the ending docking station
* member_casual -- type of rider (casual rider or member)

## Data cleaning process 

The datasets contain a very high number of rows, so I chose RStudio to prepare and clean them for analysis.

However, before I uploaded the files to RStudio, I examined them as single files using Microsoft Excel to identify if there are incomplete or inconsistent data entries. 
I created a column named “ride_length” by subtracting the date and time values in the “started_at” column from the values in the “ended_at” column. After sorting and filtering the “ride_length” column, I noticed there were negative values for several rows. There were also values that were either zero or very close to zero which were accompanied with “WATSON TESTING - DIVVY” values in the “start_station_name” column.

There were also null and N/A values for “start_station_name”, “start_station_id”, “end_station_name” and “end_station_id” columns.

These pre inspections gave me an idea on which fields have complete data in order to choose for analyzing and what to consider when cleaning the data.

Afterwards, I started using RStudio to prepare, clean and analyze the data.

### Prepare data
The first step was to download required packages

```
install.packages("tidyverse")
installed.packages
install.packages("ggplot2")
install.packages("lubridate")
library(lubridate)
library(ggplot2)
library(tidyverse)
```

Then I imported the files to R:

``` 
# Upload Divvy datasets (csv files):
m_202010 <- read.csv("202010-divvy-tripdata.csv")
m_202011 <- read.csv("202011-divvy-tripdata.csv")
m_202012 <- read.csv("202012-divvy-tripdata.csv")
m_202101 <- read.csv("202101-divvy-tripdata.csv")
m_202102 <- read.csv("202102-divvy-tripdata.csv")
m_202103 <- read.csv("202103-divvy-tripdata.csv")
m_202104 <- read.csv("202104-divvy-tripdata.csv")
m_202105 <- read.csv("202105-divvy-tripdata.csv")
m_202106 <- read.csv("202106-divvy-tripdata.csv")
m_202107 <- read.csv("202107-divvy-tripdata.csv")
m_202108 <- read.csv("202108-divvy-tripdata.csv")
m_202109 <- read.csv("202109-divvy-tripdata.csv")
```
### Merge data
I used the colnames() function to view and compare the columns of each file to make sure they match before combining them into a single file. 

```
# Compare column names of each file
colnames(m_202010)
colnames(m_202011)
colnames(m_202012)
colnames(m_202101)
colnames(m_202102)
colnames(m_202103)
colnames(m_202104)
colnames(m_202105)
colnames(m_202106)
colnames(m_202107)
colnames(m_202108)
colnames(m_202109)
```
I noticed the column names of all datasets are matching. Then I used str() function to see if the values on columns are formatted with the correct data type.

```
# Inspect the data frames and look for incongruencies
str(m_202010)
str(m_202011)
str(m_202012)
str(m_202101)
str(m_202102)
str(m_202103)
str(m_202104)
str(m_202105)
str(m_202106)
str(m_202107)
str(m_202108)
str(m_202109)
```

I noticed the start_station_id and end_station_id columns of the tables m_202010 and m_202011 are formatted as integers while they are formatted as characters in all of the other tables. I needed to change them to character format to make these columns consistent when merging the tables into a single file.

```
# Convert start_station_id and end_station_id to character so that they can stack correctly
m_202010 <- mutate(m_202010, 
                   start_station_id = as.character(start_station_id), 
                   end_station_id = as.character(end_station_id))
m_202011 <- mutate(m_202011, 
                   start_station_id = as.character(start_station_id), 
                   end_station_id = as.character(end_station_id))
```

Then, I combined all files into a single file to aggregate last 12 months trip data to conduct my analysis.

```
# Stack individual quarter's data frames into one big data frame
all_trips <- bind_rows(m_202010, m_202011, m_202012, m_202101, m_202102, m_202103, m_202104, m_202105, m_202106, m_202107, m_202108, m_202109)
```

To inspect the new table that has been created, I ran these codes.

```
nrow(all_trips)  #How many rows are in the data frame?
dim(all_trips)  #Dimensions of the data frame?
head(all_trips)  #See the first 6 rows of the data frame. 
str(all_trips)  #See list of columns and data types
summary(all_trips)  #Statistical summary of data. 
```

The new table which I used for my analysis has 13 columns and 5136261 rows.

To ensure that member_casual column has only two types of labels for customers I ran this code.

```
# Identify how many observations fall under each usertype
table(all_trips$member_casual)
```

The output is:
		
```
  casual  member 
  2358287 2777974
```

### Add day, month, year, day_of_week columns

I added some additional columns of data -- such as day, month, year of each ride-- that provide additional opportunities to aggregate the data. 

```
# Add columns that list the date, month, day, and year of each ride
all_trips$date <- as.Date(all_trips$started_at)
all_trips$month <- format(as.Date(all_trips$date), "%b")
all_trips$day <- format(as.Date(all_trips$date), "%d")
all_trips$year <- format(as.Date(all_trips$date), "%Y")
all_trips$day_of_week <- format(as.Date(all_trips$date), "%A")
```

### Add ride_length column

To calculate the ride duration, I added a column named ride_length which is calculated by subtracting started_at from ended_at.

```
# Add a "ride_length" calculation to all_trips (in seconds)
all_trips$ride_length <- difftime(all_trips$ended_at, all_trips$started_at)
```

To run calculations on the data, I converted "ride_length" to numeric data type.

```
# Convert "ride_length" to numeric
all_trips$ride_length <- as.numeric(as.character(all_trips$ride_length))
is.numeric(all_trips$ride_length)
```

### Delete bad data

There were some rides where ride_length showed up as negative, including several hundred rides where Divvy took bikes out of circulation for testing reasons which were recorded as “WATSON TESTING - DIVVY” under the “start_station_name” column. I deleted these rides by creating a new version of the dataframe. (4083 rows were deleted)

```
# Create a new version of the dataframe (v2) since data is being removed
all_trips_v2 <- all_trips[!(all_trips$start_station_name == "WATSON TESTING - DIVVY" | all_trips$ride_length < 0),]
```

## Analysis

### Conduct Descriptive Analysis

Ran summary() function to get the minimum, maximum, average and median of ride_length

```
# Descriptive analysis on ride_length (all figures in seconds)
summary(all_trips_v2$ride_length)
```
The output is:
```
  Min.    1st Qu.   Median       Mean   3rd Qu.    Max. 
  0        426        756        1365    1369     3356649
```
So the average ride length is 1365 seconds. Maximum ride length is 3356649 seconds. 

Then, I calculated the average ride length(in seconds) for each user type.

```
# Average ride length for each user type
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = mean)
```
```
        all_trips_v2$member_casual    all_trips_v2$ride_length
                     casual              	    1971.0341
                     member                	     850.0557
```
The average ride length for casual members is 1971.0341 seconds; while it is 850.0557 seconds for annual members.

I calculated the median of ride length(in seconds) values for each user type.

```
# Median of ride length values for each user type
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = median)
```
```
            all_trips_v2$member_casual   all_trips_v2$ride_length
                     casual              	    1001
                     member                	     605
```
The median of ride length for casual members is 1001 seconds; while it is 605 seconds for annual members.

I calculated the minimum and maximum of ride length values for each user type.

```
# Minimum ride length for each user type
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = min)
```
```
           all_trips_v2$member_casual   all_trips_v2$ride_length
                     casual              	  	  0
                     member                	  	  0
```
```
# Maximum ride length for each user type
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = max)
```  
```
            all_trips_v2$member_casual   all_trips_v2$ride_length
                     casual              	    3356649
                     member                	     573467
```
The minimum ride lengths for both user types are 0 seconds; while the maximum ride length for casual members is 3356649 seconds and 573467 seconds for annual members.

### Average ride length by weekday
I wanted to calculate the average ride length by each day for each user type. First, I made sure the days were correctly ordered.

```
# Order the days of week.
all_trips_v2$day_of_week <- ordered(all_trips_v2$day_of_week, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
```
I calculated the average ride length by each day of week for each user type. 

```
# See the average ride time by each day for members vs casual users
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)
```
The output:
```   
              all_trips_v2$member_casual     all_trips_v2$day_of_week     all_trips_v2$ride_length
1                      casual                        Sunday                         2284.1887
2                      member                        Sunday                          962.6954
3                      casual                        Monday                         1953.9973
4                      member                        Monday                          820.6319
5                      casual                        Tuesday                        1760.6944
6                      member                        Tuesday                         799.9708
7                      casual                        Wednesday                      1718.9082
8                      member                        Wednesday                       804.2511
9                      casual                        Thursday                       1703.8111
10                     member                        Thursday                        797.7635
11                     casual                        Friday                         1884.2719
12                     member                        Friday                          841.0405
13                     casual                        Saturday                       2134.5448
14                     member                        Saturday                        943.9400
```

### Average ride length by month
First, I ordered the months from October 2020 to September 2021 to visualize the findings chronologically.

```
# Order the months by starting from October
all_trips_v2$month <- ordered(all_trips_v2$month, levels=c("Oct", "Nov", "Dec", "Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep"))
```

I calculated the average ride length by each day of week for each user type. 

```
# See the average ride time by each month for members vs casual users
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$month, FUN = mean)
```

The output:
```   
           all_trips_v2$member_casual    all_trips_v2$month     all_trips_v2$ride_length
1                      casual                	  Oct                	  1823.8245
2                      member               	  Oct                	   842.9632
3                      casual               	  Nov               	  1911.3579
4                      member             	  Nov                      815.3079
5                      casual               	  Dec                	  1610.9764
6                      member              	  Dec               	   764.9615
7                      casual               	  Jan               	  1541.0754
8                      member               	  Jan              	   772.3387
9                      casual              	  Feb              	  2962.3937
10                     member                	  Feb             	  1081.3251
11                     casual                	  Mar             	  2289.5511
12                     member                	  Mar               	   838.2031
13                     casual                	  Apr               	  2281.3794
14                     member              	  Apr                      881.3527
15                     casual                	  May              	  2293.8580
16                     member                	  May                	   878.3410
17                     casual               	  Jun               	  2227.3044
18                     member                	  Jun                	   880.6729
19                     casual                	  Jul                	  1967.4460
20                     member                	  Jul               	   854.3704
21                     casual                	  Aug                	  1727.2208
22                     member                	  Aug               	   846.0936
23                     casual                	  Sep               	  1668.9319
24                     member                	  Sep              	   824.1358
```

### Number of rides and ride length for each day of week and user type
Finally, I wanted to see the number of rides and average ride_length by each day and user type. To do this, I created a weekday field using wday(), grouped the output by user type and weekday and used summarise() to match the number of rides and average ride length to these groups. Finally I sorted the data frame by user type and weekday.

```
# analyze ridership data by type and weekday
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration_sec = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
```
The output:

```
# A tibble: 14 x 4
# Groups:   member_casual [2]
   member_casual   weekday      number_of_rides   average_duration_sec
   <chr>            <ord>              <int>            <dbl>
 1 casual            Sun              444948            2284.
 2 casual            Mon              266291            1954.
 3 casual      	     Tue              251312            1761.
 4 casual            Wed              257055            1719.
 5 casual            Thu              274473            1704.
 6 casual            Fri              338975            1884.
 7 casual            Sat              523672            2135.
 8 member            Sun              345060             963.
 9 member            Mon              375679             821.
10 member            Tue              406667             800.
11 member            Wed              422575             804.
12 member            Thu              420318             798.
13 member            Fri              405266             841.
14 member            Sat              399887             944.
```

### Chart #1: Number of rides by weekday
I used a bar chart to visualize the number of rides by weekday for each user type.

```
# number of rides by weekday
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration_sec = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge") + 
  labs(title = "Total Number of Rides by Weekday", subtitle = "From October 2020 to September 2021")
```
![Total Number of Rides by Weekday](https://user-images.githubusercontent.com/93592015/140807407-c6ea4d08-49c2-4d12-8764-9238e98fb34c.png)

As we can see, casual members tend to use cyclistic bikes more on weekends while the number of rides by annual members for each day of week are very close.

### Chart #2: Average ride length by weekday for each user type

I used a bar chart to visualize the average ride length by weekday for each user type.

```
# average ride length by weekday
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration_sec = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = average_duration_sec, fill = member_casual)) +
  geom_col(position = "dodge") + 
  labs(title = "Average Ride Length for Each Weekday", subtitle = "From October 2020 to September 2021")
```
![Average Ride Length by Weekday_2](https://user-images.githubusercontent.com/93592015/140809336-4a0c9e16-a833-4b28-8e5e-745967cb58f9.png)

Casual riders have a tendency of taking longer trips than annual members. Members take consistent rides throughout the week.

### Chart #3: Number of rides by month for each user type

I compared the number of rides by month per each user type over the period of October 2020 to September 2021.

```
# number of rides by month
all_trips_v2 %>% 
  group_by(member_casual, month) %>% 
  summarise(number_of_rides = n()
            ,average_duration_sec = mean(ride_length)) %>% 
  arrange(member_casual, month)  %>% 
  ggplot(aes(x = month, y = number_of_rides, color = member_casual, group = member_casual)) +
  geom_line(size = 1) + 
  labs(title = "Total Number of Rides by Month", subtitle = "From October 2020 to September 2021")
```
![Total Number of Rides by Month](https://user-images.githubusercontent.com/93592015/140809370-bf72f35b-fcfb-428a-a866-9e8fb59cc459.png)

Number of rides per month for each user type follows a similar trend which states a higher number of rides for summer than winter. 

### Chart #4: Average ride length by month

I compared the average ride length by month per each user type over the period of October 2020 to September 2021

```
# average ride length by month
all_trips_v2 %>% 
  group_by(member_casual, month) %>% 
  summarise(number_of_rides = n()
            ,average_duration_sec = mean(ride_length)) %>% 
  arrange(member_casual, month)  %>% 
  ggplot(aes(x = month, y = average_duration_sec, group = member_casual, color = member_casual)) +
  geom_line(size = 1) + 
  labs(title = "Average Ride Length by Month", subtitle = "From October 2020 to September 2021")
```
![Average Ride Length by Month (2)](https://user-images.githubusercontent.com/93592015/140809390-0bb693d5-81e3-4af8-9025-6aa707d12a53.png)

We can see the casual members tend to take longer trips than annual members.

## Recommendations

### Recommendation #1

Based on the second and fourth chart, we can say the casual riders tend to have longer rides than the annual members. To convert them into annual members, offering bonuses and rewards for members with long trips should be considered.

### Recommendation #2

Chart # 3 demonstrates that the demand for Cyclistic bike service rises from the bottom to the top from winter to summer respectively. New member acquisition efforts could be maximized during summer months.

### Recommendation #3

As chart #1 demonstrates that the casual riders have a tendency to use the Cyclistic services more on the weekend, organizing special events such as guided tours and contests for Cyclistic members which would take place on weekends could be a strong incentive for casual riders to become annual members. 

### Recommendation #4

As chart #1 demonstrates that while the casual riders have a tendency to use the Cyclistic services more on the weekend, the annual members are using the bikes on weekdays, probably as a commuting vehicle. There can be marketing activities such as ads about using Cyclistic bikes for commuting in order to give the casual riders an incentive for going to their jobs with cyclistic bikes.
