---
title: 'Cyclistic: Bike share analysis'
author: "Susannah Moses"
date: "2024-01-09"
output: html_document
---

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

## Cyclistic: Bike-Share analysis using R

### Background

In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geo-tracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as **casual** riders. Customers who purchase annual memberships are Cyclistic **members**.

Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. Although the pricing flexibility helps Cyclistic attract more customers, Lily Moreno, the director of marketing believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, Moreno believes there is a very good
chance to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their mobility needs.

Moreno has set a clear goal: Design marketing strategies aimed at converting casual riders into annual members. In order to do that, however, the marketing analyst team   needs to better understand how annual members and casual riders differ, why casual
riders would buy a membership, and how digital media could affect their marketing tactics. 

**My role:** I am a junior data analyst, working in the marketing analyst team at Cyclistic.

**Overall goal for the entire team:** Design marketing strategies aimed at converting casual riders into annual members.

I have been asked to do a specific business task (mentioned below).

The business task is done by following through all the steps of data analysis.

1.Ask
2.Prepare
3.Process
4.Analyze
5.Share
6.Act

### 1. Ask

**Business task:** ***How do annual members and casual riders use Cyclistic bike differently?***

### 2. Prepare

**Data Source**: The data used for solving the above business task is downloaded from [the Cyclistic trip data.](https://divvy-tripdata.s3.amazonaws.com/index.html). I used data from the period Nov 2022 - Oct 2023.

I downloaded the data and stored it locally in my computer. It is organized by month and year and stored as zip files.

**Data credibility** 
The data is reliable and original.
It is comprehensive and has the required information to solve the business problem
It is current and cited from this site [Click here](https://divvy-tripdata.s3.amazonaws.com/index.html)

**Data License**

The data has been made available by ***Motivate International Inc*** under this [license](https://divvybikes.com/data-license-agreement)

Data-privacy issues prohibits me  from using riders’ personally identifiable information and I won't be able to connect pass purchases to credit card numbers to determine if casual riders live in the Cyclistic service area or
if they have purchased multiple single passes.

### 3.Process

I have used the R programming language for processing and analyzing data and making it ready for creating visualizations.

#### 3.1 Loading the required packages

```{r}
library(tidyverse) #helps wrangle data
library(janitor) #helps with data cleaning
```

#### 3.2 Setting up the working directory and loading data

```{r}
setwd("~/Data analysis/Module 8/Case study_cyclistic/CSV files")
Nov_22 <- read_csv("202211-divvy-tripdata.csv")
Dec_22 <- read_csv("202212-divvy-tripdata.csv")
Jan_23 <- read_csv("202301-divvy-tripdata.csv")
Feb_23 <- read_csv("202302-divvy-tripdata.csv")
Mar_23 <- read_csv("202303-divvy-tripdata.csv")
Apr_23 <- read_csv("202304-divvy-tripdata.csv")
May_23 <- read_csv("202305-divvy-tripdata.csv")
Jun_23 <- read_csv("202306-divvy-tripdata.csv")
Jul_23 <- read_csv("202307-divvy-tripdata.csv")
Aug_23 <- read_csv("202308-divvy-tripdata.csv")
Sep_23 <- read_csv("202309-divvy-tripdata.csv")
Oct_23 <- read_csv("202310-divvy-tripdata.csv")
```


```{r}
knitr::include_graphics("Sample_csv.png")
```

#### 3.3 Getting an overview of each of the dataframe 

```{r}
summary(Nov_22)
summary(Dec_22)
summary(Jan_23)
summary(Feb_23)
summary(Mar_23)
summary(Apr_23)
summary(May_23)
summary(Jun_23)
summary(Jul_23)
summary(Aug_23)
summary(Sep_23)
summary(Oct_23)
```

#### 3.4 Comparing the columns of each data frame before combining them into one dataframe

This is a crucial step as we need to make sure that the column names of each individual data frames and their data types match. Only then we can combine them together without any issues.This is done using the compare_df_cols() function from the janitor package.

```{r}
compare_df_cols(Nov_22,Dec_22,Jan_23,Feb_23,Mar_23,Apr_23,May_23,Jun_23,Jul_23,Aug_23,Sep_23,Oct_23, return = 'mismatch')

```

This shows that all the column names and their data types match.

#### 3.5 Combining individual dataframes into one big data frame

```{r}
all_rides <- bind_rows(Nov_22,Dec_22,Jan_23,Feb_23,Mar_23,Apr_23,May_23,Jun_23,Jul_23,Aug_23,Sep_23,Oct_23)

```

Now let's take an initial overall look at our combined data frame

```{r}
head(all_rides)
colnames(all_rides)
nrow(all_rides)
summary(all_rides)
```

#### 3.6 Data cleaning

##### 3.6.1 The columns **started_at** and **ended_at** represent the date and time when the rides started and ended. We can see that their data type is character. So we need to change that and also have to treat the date and time separately as it will be really helpful for our further analysis.

```{r}
all_rides$started_at <- strptime(all_rides$started_at, "%m/%d/%Y %H:%M")
all_rides$ended_at <- strptime(all_rides$ended_at, "%m/%d/%Y %H:%M")
```

##### 3.6.2 Add few new columns to represent the **month** , **day** and **year** when the rides occurred.

```{r}
all_rides$date <- as.Date(all_rides$started_at, "%m/%d/%Y")
all_rides$month <- format(as.Date(all_rides$date), "%m")
all_rides$day <- format(as.Date(all_rides$date), "%d")
all_rides$year <- format(as.Date(all_rides$date), "%Y")

```

##### 3.6.3 Add another column to represent the **day of the week** when the ride occurred.

```{r}
all_rides$day_of_week <- format(as.Date(all_rides$date), "%A")
```

##### 3.6.4 Calculate the **length of the ride** by subtracting the time portion of the ended_at and started_at columns

```{r}
all_rides$ride_length <- difftime(all_rides$ended_at,all_rides$started_at,units="min")

```

##### 3.6.5 Check if the data type of ride length is numeric. If not, convert it to numeric as we want it numeric for further analysis and calculations

```{r}
is.numeric(all_rides$ride_length)
all_rides$ride_length <- as.numeric(all_rides$ride_length)
is.numeric(all_rides$ride_length)
```

Now let's get a summary of our combined data frame

```{r}
summary(all_rides)
head(all_rides)

```

##### 3.6.7 The ride length column should have only positive values and no negative or zero values. Let's create a temporary table that will show us how many positive, negative and zero values does the ride length column has.

```{r}
table(sign(all_rides$ride_length))

```

In our final data frame we have to filter and just use the positive ride length values. Before we get to that, we need to do some more analysis.

##### 3.6.8 Check if there are any "NA" values in the start_station_name and end_station_name columns and if so, how many are there

```{r}
sum(is.na(all_rides$start_station_name))
sum(is.na(all_rides$end_station_name))
```

##### 3.6.9 Check if the column member_casual has only "member" and "casual" entries. We use the unique() function which returns a list of unique entries in a column

```{r}
unique(all_rides$member_casual)

```

##### 3.6.10 Use the same unique() function on the rideable_type column to get a unique list of the type of bikes used

```{r}
unique(all_rides$rideable_type)

```

##### 3.6.11 We need to make sure that there are no duplicated ride id's in the data frame. This is done using the duplicated() function.

```{r}
sum(duplicated(all_rides$ride_id))

```

##### 3.6.12 Removing bad data and creating our final dataframe

```{r}
all_rides_v2 <- all_rides %>% filter(all_rides$ride_length>0, !duplicated(all_rides$ride_id))
all_rides_final <- drop_na(all_rides_v2)

```

Now let's have an overall look at our final data frame that we just created.

```{r}
str(all_rides_final)
View(all_rides_final)
```

*Below is a screenshot of the first few rows of* **all_rides_final** *data frame*
```{r}
knitr::include_graphics("all_rides_final.png")
```


### 4. Analyze

##### 4.1 Perform descriptive analysis

##### 4.1.1. Run a descriptive analysis on ride length.

This means that we need to know the mean,median, min and max values of ride length. All of these values can be obtained in one shot using the summary() function.

```{r}
summary(all_rides_final$ride_length)
```

##### 4.1.2. Analyse how all these measures of ride length differ for casual and member riders

```{r}
#this calculates the overall average ride length for casual and member riders
aggregate(all_rides_final$ride_length ~ all_rides_final$member_casual, FUN = mean)
#this calculates the minimum ride length for casual and member riders
aggregate(all_rides_final$ride_length ~ all_rides_final$member_casual, FUN = min)
#this calculates the maximum ride length for casual and member riders
aggregate(all_rides_final$ride_length ~ all_rides_final$member_casual, FUN =max)
# this calculates the median value of ride length for casual and member riders
aggregate(all_rides_final$ride_length ~ all_rides_final$member_casual, FUN = median)

```


##### 4.1.3. Look at the average ride length by day of the week for member vs casual riders

```{r}
aggregate(all_rides_final$ride_length ~ all_rides_final$member_casual + all_rides_final$day_of_week, FUN = mean)

```

We can see that the days of the week are out of order. Let's fix that

```{r}
all_rides_final$day_of_week <- ordered(all_rides_final$day_of_week, levels = c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))

```

Now let's run the code for the average ride length by day of the week for casual vs member riders

```{r}
aggregate(all_rides_final$ride_length ~ all_rides_final$member_casual + all_rides_final$day_of_week, FUN = mean)

```

##### 4.1.4. Analyze ridership data by rider type and weekday

```{r}
all_rides_final %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% # creates weekday field using wday() function
  group_by(member_casual,weekday) %>% # groups by rider type and weekday
  summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>% # calculates the number of rides and average duration 
  arrange(member_casual,weekday) #  sorts
```

##### 4.1.5. Create a visualization that shows the number of rides by rider type throughout a week

```{r}
all_rides_final %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual,weekday) %>% 
  summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>% 
  arrange(member_casual,weekday) %>% 
  ggplot(aes(x=weekday,y=number_of_rides,fill=member_casual)) + geom_col(position="dodge")
```

**The graph shows us that member riders ride more number of bikes than casual riders in a week**

##### 4.1.6. Create a visualization that shows the average ride length for each rider type throughout a week

```{r}
all_rides_final %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual,weekday) %>% 
  summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>% 
  arrange(member_casual,weekday) %>% 
  ggplot(aes(x=weekday,y=average_duration,fill=member_casual)) + geom_col(position="dodge")
```

**This graph shows that though member riders ride more number of bikes, it is the casual riders that ride their bikes for a long time.**

### 5.Share

 **Export the final file for further analysis**

I created a final csv file and exported it for further analysis and visualization using Tableau

```{r}
write.csv(all_rides_final, "C:\\Users\\susan\\OneDrive\\Documents\\Data analysis\\Module 8\\Case study_cyclistic\\Insights\\all_rides_export.csv", row.names=FALSE)

```
