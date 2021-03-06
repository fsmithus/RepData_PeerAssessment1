---
title: "Reproducible Research: Peer Assessment 1"
output: 
html_document:
keep_md: true
---

The following analysis was performed on 20 September, 2015.

A discussion of the dataset and objectives can be found [here in GitHub](https://github.com/fsmithus/RepData_PeerAssessment1).


## Loading and preprocessing the data

Set the working directory (any empty folder will do), and load environment as required.

```r
setwd("~/Documents/Academic/DataScience/Reproduceable_Research/RepData_PeerAssessment1/")
library(lattice)
```

### Questions 1 & 2 - Fetch and load the data.
Download, unzip, and read the activity/step data. No pre-processing is required for this analysis.

```r
download.file("http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip","repdata_data_activity.zip")
unzip("repdata_data_activity.zip")
data <- read.csv("activity.csv")
```


## What is mean total number of steps taken per day?

### Question 1 - Plot histogram of the number of steps per day.
First calculate and plot the total steps for each day.

```r
daily.total <- aggregate(list(steps = data$steps),
                         list(date = cut.Date(as.Date(data$date),breaks="day")),sum)
hist(daily.total$steps,xlab="Steps",main="Histogram of Daily Total Steps")
```

![plot of chunk unnamed-chunk-1](figure/unnamed-chunk-1-1.png) 

### Question 2 - Calculate the mean and median number of steps per day.
Compute the mean steps per day. Ignore missing values.

```r
mean(daily.total$steps,na.rm=TRUE)
```

```
## [1] 10766.19
```

Compute the median steps per day. Ignore missing values.

```r
median(daily.total$steps,na.rm=TRUE)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

### Question 1 - Plot average steps per interval across all days.
Calculate the average steps for each interval across days. Ignore missing values.

```r
interval.mean <- aggregate(list(steps = data$steps),list(interval = data$interval),mean,na.rm=TRUE)
max.interval <- which.max(interval.mean$steps)
plot(interval.mean$interval,interval.mean$steps,type="l",
     xlab="Interval",ylab="Mean Steps",main="Average Daily Activity Pattern")
abline(v=interval.mean[max.interval,1],col="blue",lwd=4)
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

### Question 2 - Which interval contains maximum average number of steps?
Calculate the interval (time-of-day) with the maximum average number of steps.

```r
interval.mean[max.interval,"interval"]
```

```
## [1] 835
```


## Imputing missing values

### Question 1 - Calculate the total number of missing step values in the dataset.

```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

### Question 2 - Define a strategy to fill-in the missing values.
My strategy is to use the interval mean for each missing interval in a given day.

### Question 3 - Create new dataset.
Copy the original data, then fill in missing values with interval means.

```r
data.imputed <- data
for (i in 1:nrow(data.imputed)) {
        if (is.na(data.imputed[i,"steps"])) {
                interval <- data.imputed[i,"interval"]
                data.imputed[i,"steps"] <- interval.mean[interval.mean$interval==interval,"steps"]
        }
}
```

### Question 4 - Repeat earlier analysis to get new histogram, means and medians. Explain the difference.


```r
daily.imputed.total <- aggregate(list(steps = data.imputed$steps),
                                 list(date = cut.Date(as.Date(data.imputed$date),breaks="day")),sum)
hist(daily.imputed.total$steps,xlab="Steps",main="Histogram of Daily Total Steps")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png) 

Compute the mean steps per day. Ignore missing values.

```r
mean(daily.imputed.total$steps,na.rm=TRUE)
```

```
## [1] 10766.19
```

Compute the median steps per day. Ignore missing values.

```r
median(daily.imputed.total$steps,na.rm=TRUE)
```

```
## [1] 10766.19
```

Analysis: The means/medians did not change, so the strategy of filling in missing intervals with the mean number of steps from all other days only raised the frequency of certain interval step counts. There were 8 days with missing step counts, and frequencies for the interval containing the mean value (i.e., the central bar in the histogram) increased by a count of 8.


## Are there differences in activity patterns between weekdays and weekends?

Continuing with the imputed dataset...

### Question 1 - Add a column to categorize each observation as "weekday" or "weekend".
Translate dates to factors.

```r
w <- weekdays(as.Date(data.imputed$date)) %in% c("Saturday","Sunday")
x <- rep("",length(w))
for (i in 1:length(w)) {
        if (w[i])       {x[i] <- "weekend"}
        else            {x[i] <- "weekday"}
}
data.imputed$day.type <- as.factor(x)
```

### Question 2 - Make a panel plot comparing weekday and weekend activity.
Plot weekday/weekend comparison using the lattice package.

```r
xyplot(data.imputed$steps ~ data.imputed$interval | data.imputed$day.type,
       layout=c(1,2), type="l",xlab="Interval",ylab="Number of Steps")
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png) 

With a little more effort, I could reduce the noise in the plots, but it appears that weekdays see the most steps around 8:00am and again around 6:00pm. Weekend days appear to have fewer steps with the peak around 4:00pm.

(Out of time.)

