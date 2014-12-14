---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---

## Loading and preprocessing the data
#### Check if CSV file exists. If not, unzip the *activity.zip* file included in the repo

```r
if (!file.exists('activity.csv')) {
  unzip("activity.zip")
}
```

#### Reading the CSV file

```r
activity <- read.csv("activity.csv")
```

We leave the data processing and transformation at each of the later steps, if required.

## What is mean total number of steps taken per day?
#### Make a histogram of the total number of steps taken each day

```r
totalStepsPerDay <- aggregate(steps ~ date, data = activity, sum, na.rm = TRUE)
hist(totalStepsPerDay$steps, main= "Histogram of Total Steps per Day", xlab="Total Steps per Day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

#### Calculate and report the mean and median total number of steps taken per day

```r
mean(totalStepsPerDay$steps)
```

```
## [1] 10766.19
```

```r
median(totalStepsPerDay$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
#### Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
intervalAndSteps <- aggregate(steps ~ interval, data = activity, mean, na.rm = TRUE)
plot(steps ~ interval, data = intervalAndSteps, type="l", main="Average Daily Activity", xlab="5 Minute Interval", ylab="Average Steps")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 

#### Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
intervalAndSteps[which.max(intervalAndSteps$steps),]$interval
```

```
## [1] 835
```

## Imputing missing values
#### Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

#### Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
We are going to use the mean for the 5-minute interval to fill in the missing values.

#### Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activity_merged <- activity
activity_merged$steps <- mapply(function(steps, interval) 
                            if (is.na(steps))
                              intervalAndSteps[intervalAndSteps$interval == interval, "steps"]
                            else
                              steps,
                            activity_merged$steps, activity_merged$interval)
```

#### Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of inputing missing data on the estimates of the total daily number of steps?

```r
totalStepsPerDayMerged <- aggregate(steps ~ date, data = activity_merged, sum, na.rm = TRUE)
hist(totalStepsPerDayMerged$steps, main= "Histogram of Total Steps per Day", xlab="Total Steps per Day")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png) 

```r
mean(totalStepsPerDayMerged$steps)
```

```
## [1] 10766.19
```

```r
median(totalStepsPerDayMerged$steps)
```

```
## [1] 10766.19
```

The **mean** value is the same as the first part of the assignment. The **median** value differs slightly with a value of 1.0766189 &times; 10<sup>4</sup> steps vs 10765 steps that we got in the first part of the assignment.

From this observation, we can conclude that the impact of inputting missing values based on using the mean of the existing values is negligible.

## Are there differences in activity patterns between weekdays and weekends?
