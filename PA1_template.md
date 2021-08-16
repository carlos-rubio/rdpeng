Loading and preprocessing the data
----------------------------------

Read and show summary of the data:

    data <- read.csv(file = 'activity.csv')
    summary(data)

    ##      steps                date          interval     
    ##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
    ##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
    ##  Median :  0.00   2012-10-03:  288   Median :1177.5  
    ##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
    ##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
    ##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
    ##  NA's   :2304     (Other)   :15840

Histogram of number of steps per day

    groupedPerDay <- aggregate(data$steps, by=list(date=data$date), FUN=sum)
    hist(groupedPerDay$x, main="Histogram of steps per day", xlab="Steps")

![](scriptProject1_files/figure-markdown_strict/unnamed-chunk-2-1.png)

What is mean total number of steps taken per day?
-------------------------------------------------

Mean and median of the total number of steps per day

    meanPerDay <- mean(groupedPerDay$x, na.rm=TRUE)
    medianPerDay <- median(groupedPerDay$x, na.rm=TRUE)
    cat("Mean steps per day: ", meanPerDay," steps\n")

    ## Mean steps per day:  10766.19  steps

    cat("Median steps per day: ", medianPerDay," steps\n")

    ## Median steps per day:  10765  steps

What is the average daily activity pattern?
-------------------------------------------

Group steps by interval and plot time series

    groupedByInterval <- aggregate(data$steps, by=list(interval=data$interval), FUN=mean, na.rm=TRUE)
    plot(groupedByInterval$x, xlab="Interval index", ylab="Mean steps", main="Mean steps by 5-minute interval")

![](scriptProject1_files/figure-markdown_strict/unnamed-chunk-4-1.png)

The 5-minute interval with maximum number of steps on average is:

    intervalMaxAverageSteps = groupedByInterval[which.max(groupedByInterval$x),]$interval
    cat("Interval with max average steps: ", intervalMaxAverageSteps,"\n")

    ## Interval with max average steps:  835

Imputing missing values
-----------------------

Fill NA values with the mean for that 5-minute interval

    dataComplete <- data
    incompleteIndexes = is.na(data$steps)
    dataComplete$steps[incompleteIndexes] = groupedByInterval[groupedByInterval$interval == dataComplete$interval[incompleteIndexes],]$x
    summary(dataComplete)

    ##      steps                date          interval     
    ##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
    ##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
    ##  Median :  0.00   2012-10-03:  288   Median :1177.5  
    ##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
    ##  3rd Qu.: 15.00   2012-10-05:  288   3rd Qu.:1766.2  
    ##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
    ##  NA's   :2016     (Other)   :15840

Histogram of number of steps per day in the complete dataset

    groupedPerDayComplete <- aggregate(dataComplete$steps, by=list(date=data$date), FUN=sum)
    hist(groupedPerDayComplete$x, main="Histogram of steps per day (complete dataset)", xlab="Steps")

![](scriptProject1_files/figure-markdown_strict/unnamed-chunk-7-1.png)

Mean and median of the total number of steps per day (complete dataset)

    meanPerDayComplete <- mean(groupedPerDayComplete$x, na.rm=TRUE)
    medianPerDayComplete <- median(groupedPerDayComplete$x, na.rm=TRUE)
    cat("Mean steps per day: ", meanPerDayComplete," steps\n")

    ## Mean steps per day:  10766.19  steps

    cat("Median steps per day: ", medianPerDayComplete," steps\n")

    ## Median steps per day:  10765.59  steps

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

Encode week and week-end day in a factor variable

    dataComplete$day = factor(as.POSIXlt(dataComplete$date)$wday <= 5, c(TRUE, FALSE),c("weekday","weekend"))

Plot 5-minute interval time series

    par(mfrow=c(1,2))
    dataWeekday = dataComplete[dataComplete$day == "weekday",]
    groupedByIntervalWeekday <- aggregate(dataWeekday$steps, by=list(interval=dataWeekday$interval), FUN=mean, na.rm=TRUE)
    plot(groupedByIntervalWeekday$x, xlab="Interval index", ylab="Mean steps", main="Mean steps by 5-minute interval, weekdays")
    dataWeekend = dataComplete[dataComplete$day == "weekend",]
    groupedByIntervalWeekend <- aggregate(dataWeekend$steps, by=list(interval=dataWeekend$interval), FUN=mean, na.rm=TRUE)
    plot(groupedByIntervalWeekend$x, xlab="Interval index", ylab="Mean steps", main="Mean steps by 5-minute interval, weekend")

![](scriptProject1_files/figure-markdown_strict/unnamed-chunk-10-1.png)
