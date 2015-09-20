# Reproducible Research: Peer Assessment 1

The following analysis was performed on 20 September, 2015.

A discussion of the dataset and objectives can be found [here in GitHub](https://github.com/fsmithus/RepData_PeerAssessment1).


## Loading and preprocessing the data

Set the working directory (any empty folder will do), and load required packages.

```r
setwd("~/Documents/Academic/DataScience/Reproduceable_Research/RepData_PeerAssessment1/")
library(xtable)
```

### Questions 1 & 2
Download, unzip, and read the activity/step data. No pre-processing is required for this analysis.

```r
download.file("http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip","repdata_data_activity.zip")
unzip("repdata_data_activity.zip")
data <- read.csv("activity.csv")
```


## What is mean total number of steps taken per day?

### Question 1
First calculate and plot the total steps for each day.

```r
daily.total <- aggregate(list(steps = data$steps),list(date = cut.Date(as.Date(data$date),breaks="day")),sum)
hist(daily.total$steps,xlab="Steps",main="Histogram of Daily Total Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-1-1.png) 

### Question 2
Compute the mean steps per day.

```r
mean(daily.total$steps,na.rm=TRUE)
```

```
## [1] 10766.19
```

Compute the median steps per day.

```r
median(daily.total$steps,na.rm=TRUE)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

Calculate the average steps for each interval across days. Ignore NA values.

```r
interval.mean <- aggregate(list(steps = data$steps),list(interval = data$interval),mean,na.rm=TRUE)
max.interval <- which.max(interval.mean$steps)
```


```r
plot(interval.mean$interval,interval.mean$steps,type="l",
     xlab="Interval ID",ylab="Mean Steps",main="Average Daily Activity Pattern")
abline(v=interval.mean[max.interval,1],col="blue",lwd=4)
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
