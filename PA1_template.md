# Reproducible Research: Peer Assessment 1
  
  
  ## Loading and preprocessing the data
  ---
  First of all, we need to unzip and read the input file with R code:
  
  ```r
  unzip("activity.zip")
  ```
  
  ```
  ## Warning in unzip("activity.zip"): error 1 in extracting from zip file
  ```
  
  ```r
  activityRecords<-read.csv("activity.csv")
  ```


## What is mean total number of steps taken per day?
The total number of steps taken per dat is calculate:
  
  ```r
  steps.date<-aggregate(steps~date,data=activityRecords,FUN=sum)
  ```

To understant the data, by following code, the histogram of daily step sums is produced:
  
  ```r
  barplot(steps.date$steps, names.arg=steps.date$date, xlab="Days", ylab="NumOfsteps")
  ```
  
  ![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

Mean and median is calculated by following code:
  
  ```r
  mean(steps.date$steps)
  ```
  
  ```
  ## [1] 10766.19
  ```
  
  ```r
  median(steps.date$steps)
  ```
  
  ```
  ## [1] 10765
  ```

## What is the average daily activity pattern?
With 5-minute intervals, average step numbers are summarized as a plot:
  
  ```r
  steps.interval <- aggregate(steps~interval,data=activityRecords,FUN=mean)
  plot(steps.interval,type="l")
  ```
  
  ![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 
Max step numbers are calculated:
  
  ```r
  steps.interval$interval[which.max(steps.interval$steps)]
  ```
  
  ```
  ## [1] 835
  ```

## Imputing missing values
Total number of missing values are calculated:
  
  ```r
  sum(is.na(activityRecords$steps))
  ```
  
  ```
  ## [1] 2304
  ```

Devise a strategy: Actualy, if there is no data, I prefer to replace with means.

Missing values are replaced:
  
  ```r
  cleanDataSet <- merge(activityRecords, steps.interval, by="interval", suffixes=c("",".y"))
  nas <- is.na(cleanDataSet$steps)
  cleanDataSet$steps[nas] <- cleanDataSet$steps.y[nas]
  cleanDataSet <- cleanDataSet[,c(1:3)]
  ```
You can find the related plots:
  
  ```r
  steps.date<-aggregate(steps~date,data=cleanDataSet,FUN=sum)
  barplot(steps.date$steps,names.arg=steps.date$date,xlab="Days",ylab="NumOfsteps")
  ```
  
  ![](PA1_template_files/figure-html/unnamed-chunk-9-1.png) 
  
  ```r
  mean(steps.date$steps)
  ```
  
  ```
  ## [1] 10766.19
  ```
  
  ```r
  median(steps.date$steps)
  ```
  
  ```
  ## [1] 10766.19
  ```

## Are there differences in activity patterns between weekdays and weekends?
Day type variable is created:
  
  ```r
  dType <- function(date) {
  if (weekdays(as.Date(date)) %in% c("Saturday", "Sunday")) {
    "Weekend"
  } else {
    "Week Day"
  }
  }
  cleanDataSet$dType <- as.factor(sapply(cleanDataSet$date, dType))
  ```

Plots are produced:
  
  ```r
  par(mfrow=c(2,1))
  for (type in c("Weekend", "Week Day")) {
  steps.type <- aggregate(steps ~ interval,data=cleanDataSet,subset=cleanDataSet$dType==type,FUN=mean)
  plot(steps.type, type="l", main=type)
  }
  ```
  
  ![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 
