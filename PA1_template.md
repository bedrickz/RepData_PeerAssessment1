# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Load the activity.csv to a dataframe.

```r
activity <- read.csv(paste(getwd(), "/activity.csv", sep = ""))
```


## What is mean total number of steps taken per day?
Get the total steps for each day and put that in its own dataframe.

```r
totals <- aggregate(activity[complete.cases(activity$steps), ]$steps, list(period = activity[complete.cases(activity$steps), 
    ]$date), sum)
```


Make a histogram from to show the steps taken per day.

```r
hist(totals$x, main = "Steps Taken Per Day", xlab = "Steps Taken Per Day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 


Print the mean and median number of steps taken per day.

```r
mean(totals$x)
```

```
## [1] 10766
```

```r
median(totals$x)
```

```
## [1] 10765
```


## What is the average daily activity pattern?
Build a dataframe containing the mean number of steps per interval.

```r
interval <- aggregate(activity[complete.cases(activity$steps), ]$steps, list(interval = activity[complete.cases(activity$steps), 
    ]$interval), mean)
```


Plot the number of steps per five minute interval.

```r
plot(interval$interval, interval$x, type = "l", pch = "", ylab = "Mean Steps Taken", 
    xlab = "Five Minute Interval")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 


Find the time interval where the most steps are taken.

```r
interval[interval$x == max(interval$x), ]$interval
```

```
## [1] 835
```


Apparently, most people are walking between 8:35AM - 8:40AM

## Imputing missing values
Merge the original dataset with the mean number of steps per interval.

```r
full <- merge(activity, interval, by = "interval")
```


If the number of steps is NA, replace it with the mean number of steps for that interval.

```r
fixed <- transform(full, steps = ifelse(is.na(steps), x, steps))
```


Follow the same steps to produce the histogram except with the new fill dataframe.

```r
totals2 <- aggregate(fixed[complete.cases(fixed$steps), ]$steps, list(period = fixed[complete.cases(fixed$steps), 
    ]$date), sum)
hist(totals2$x, main = "Steps Taken Per Day", xlab = "Steps Taken Per Day")
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 

```r
mean(totals2$x)
```

```
## [1] 10766
```

```r
median(totals2$x)
```

```
## [1] 10766
```


You can see the median went up slightly and the histogram maintained a similar shape (although the frequencies are up).

## Are there differences in activity patterns between weekdays and weekends?
Add a new column to the dataframe that determines if the record is during a weekday or weekend

```r
fixed <- transform(fixed, weekday = ifelse(weekdays(as.Date(date)) == "Sunday" | 
    weekdays(as.Date(date)) == "Saturday", "Weekend", "Weekday"))
```


Build a dataframe containing the mean number of steps per interval per day type.

```r
interval2 <- aggregate(fixed[complete.cases(fixed$steps), ]$steps, list(interval = fixed[complete.cases(fixed$steps), 
    ]$interval, day = fixed[complete.cases(fixed$steps), ]$weekday), mean)
```


Plot the mean steps per interval during the weekdays compared to the same during the weekends

```r
par(mfrow = c(2, 1))
plot(interval2[interval2$day == "Weekday", ]$interval, interval2[interval2$day == 
    "Weekday", ]$x, type = "l", pch = "", ylab = "Mean Steps Taken", xlab = "Five Minute Interval")
title(main = "Weekday", col.main = "blue", font.main = 2)
plot(interval2[interval2$day == "Weekend", ]$interval, interval2[interval2$day == 
    "Weekend", ]$x, type = "l", pch = "", ylab = "Mean Steps Taken", xlab = "Five Minute Interval")
title(main = "Weekend", col.main = "blue", font.main = 2)
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13.png) 


It can be seen that there is more walking happening during the middle hours of the weekdays.
