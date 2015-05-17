# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

```r
data <- read.csv("activity.csv")
```


## What is mean total number of steps taken per day?

```r
totalStepsPerDay <- aggregate(steps ~ date, data, sum)
hist(totalStepsPerDay$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

```r
meanTotalStepsPerDay <- mean(totalStepsPerDay$steps)
medianTotalStepsPerDay <- median(totalStepsPerDay$steps)
```


## What is the average daily activity pattern?

```r
stepsPerInterval <- aggregate(steps ~ interval, data, mean)

plot(stepsPerInterval$interval,stepsPerInterval$steps, type="l", xlab="Interval", ylab="Number of Steps",main="Average Number of Steps per Day by Interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

```r
maxInterval <- stepsPerInterval[which.max(stepsPerInterval$steps),1]
```


## Imputing missing values

```r
inc <- sum(!complete.cases(data))
imputedData <- transform(data, steps = ifelse(is.na(data$steps), stepsPerInterval$steps[match(data$interval, stepsPerInterval$interval)], data$steps))
```


```r
imputedData[as.character(imputedData$date) == "2012-10-01", 1] <- 0
```


```r
totalStepsPerDay_i <- aggregate(steps ~ date, imputedData, sum)
hist(totalStepsPerDay_i$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps")

#Create Histogram to show difference. 
hist(totalStepsPerDay$steps, main = paste("Total Steps Each Day"), col="red", xlab="Number of Steps", add=T)
legend("topright", c("Imputed", "Non-imputed"), col=c("blue", "red"), lwd=10)
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 


```r
meanTotalStepsPerDay.i <- mean(totalStepsPerDay_i$steps)
medianTotalStepsPerDay.i <- median(totalStepsPerDay_i$steps)
```


```r
meanDiff <- meanTotalStepsPerDay.i - meanTotalStepsPerDay
medianDiff <- medianTotalStepsPerDay.i - medianTotalStepsPerDay
```


```r
totalDiff <- sum(totalStepsPerDay_i$steps) - sum(totalStepsPerDay$steps)
```


## Are there differences in activity patterns between weekdays and weekends?

```r
weekdays <- c("Monday", "Tuesday", "Wednesday", "Thursday", 
              "Friday")
imputedData$dow = as.factor(ifelse(is.element(weekdays(as.Date(imputedData$date)),weekdays), "Weekday", "Weekend"))

stepsPerInterval_i <- aggregate(steps ~ interval + dow, imputedData, mean)

library(lattice)

xyplot(stepsPerInterval_i$steps ~ stepsPerInterval_i$interval|stepsPerInterval_i$dow, main="Average Steps per Day by Interval",xlab="Interval", ylab="Steps",layout=c(1,2), type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 
