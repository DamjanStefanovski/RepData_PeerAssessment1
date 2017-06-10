---
title: "Assignment PA1_template"
author: "Damjan"
date: "June 10, 2017"
output:
  html_document: default
  github_document: default
keep_md: yes
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library(ggplot2)
```
Loading and preprocessing the data Show any code that is needed to

Load the data (i.e. read.csv()) Process/transform the data (if necessary) into a format suitable for your analysis

```{r}
data<- read.csv("activity.csv")
data$date<- as.Date(data$date)

```

What is mean total number of steps taken per day? For this part of the assignment, you can ignore the missing values in the dataset.

1.Make a histogram of the total number of steps taken each day. 2. Calculate and report the mean and median total number of steps taken per day.

```{r}
 stepsbyday<- tapply(data$steps, data$date, sum, na.rm=TRUE)
qplot(stepsbyday, xlab="Number of Steps Taken Each Day", ylab="Total Frequency",fill = "red", binwidth=500) 
```

```{r}
medianbyday<- median(stepsbyday)
meanbyday<- mean(stepsbyday)

meanbyday
``` 

```{r}
medianbyday
```
What is the average daily activity pattern? Make a time series plot (i.e. type = “l”) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis) Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```{r}
avg<- tapply(data$steps, data$interval, mean, na.rm=TRUE)
plot(names(avg), avg, xlab="5 minutes intervals", type="l", ylab="Average number of steps")

```



```{r}
maxavg<- max(avg)
maxinterval<- as.numeric(names(avg)[which(avg==max(avg))])
maxavg

```

```{r}
maxinterval
```
Imputing missing values Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```{r}
totalna <- sum(is.na(data$steps))
# creating a copy of data set so that the missing value can be imputed in it
imputedata <- data

# Devise a strategy for filling in all of the missing values in the datase.
# In place of NA, using the mean for that 5-minute interval

imputedata$steps[which(is.na(data$steps))]<- as.vector(avg[as.character(data[which(is.na(data$steps)),3])])

```

Create a new dataset that is equal to the original dataset but with the missing data filled in.
Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```{r}
stepseachday <- tapply(imputedata$steps, imputedata$date, sum, na.rm=TRUE)
qplot(stepseachday, xlab="No. of Steps Taken Each Day", ylab="Total Frequency", binwidth=500)
```


```{r}
medianEachDayImputed<- median(stepseachday)
meanEachDayImputed<- mean(stepseachday)
```
Median Total No. of Steps Taken Per Day : 10766 Mean Total No. of Steps Taken Per Day : 10766

Are there differences in activity patterns between weekdays and weekends? For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```{r}
imputedata$dayType<- ifelse(as.POSIXlt(imputedata$date)$wday %in% c(0,6), "weekends","weekdays")
```
Make a panel plot containing a time series plot (i.e. type = “l”) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```{r}
aggregateData<- aggregate(steps ~ interval + dayType, data=imputedata, mean)
ggplot(aggregateData, aes(color = "", interval, steps)) + 
    geom_line() +
    facet_grid(dayType ~ .) +
    xlab("5 Minute Interval") + 
    ylab("Average number of steps")
```


