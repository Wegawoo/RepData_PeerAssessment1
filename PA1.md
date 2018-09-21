---
title: "Programming Assessment 1 - Reproducible Research"
output: 
  html_document: 
    keep_md: yes
---



## Loading and preprocessing the data



```r
act <- read.csv("C:\\Users\\Mark\\Downloads\\repdata_data_activity\\activity.csv")

library(dplyr)
```

```
## Warning: package 'dplyr' was built under R version 3.4.4
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
a <- filter(act,!is.na(steps))
```

```
## Warning: package 'bindrcpp' was built under R version 3.4.4
```

```r
b <- group_by(a,date)
c <- summarise(b,totalsteps = sum(steps))
c <- as.data.frame(c)
c$date <- as.Date(c$date)
```

## Histogram of the total number of steps taken each day



```r
library(ggplot2)

ggplot(c,aes(x=totalsteps)) + geom_histogram(col="black",fill="blue",alpha=0.2) + labs(title="Histogram for total days for step ranges", x="Steps", y="Days")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

## Mean and median number of steps taken each day



```r
mean_median <- summarise(c,mean = mean(totalsteps),median = median(totalsteps))
print(mean_median)
```

```
##       mean median
## 1 10766.19  10765
```

## Time series plot of the average number of steps taken



```r
d <- group_by(a,interval)
e <- summarise(d,averagesteps = mean(steps))
with(plot(interval,averagesteps,type="l",main="Avg Steps per interval",ylab="Average steps"),data=e)
```

![](PA1_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

## The 5-minute interval that, on average, contains the maximum number of steps


```r
print(as.integer(e[which.max(e$averagesteps),1]))
```

```
## [1] 835
```

## Code to describe and show a strategy for imputing missing data


```r
act_2 <- inner_join(act,e,by="interval")
act_2$steps[is.na(act_2$steps)] <- act_2$averagesteps
```

```
## Warning in act_2$steps[is.na(act_2$steps)] <- act_2$averagesteps: number of
## items to replace is not a multiple of replacement length
```

## Histogram of the total number of steps taken each day after missing values are imputed


```r
f <- group_by(act_2,date)
g <- summarise(f,totalsteps = sum(steps))
g <- as.data.frame(g)
g$date <- as.Date(g$date)

ggplot(g,aes(x=totalsteps)) + geom_histogram(col="black",fill="blue",alpha=0.2) + labs(title="Histogram for total days for step ranges accounting for NAs", x="Steps", y="Days")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

## Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends


```r
act_2$date <- as.Date(act_2$date)
act_2 <- mutate(act_2,weekday = ifelse(weekdays(act_2$date) == "Sunday" | weekdays(act_2$date) == "Saturday","weekend","weekday"))

h <- group_by(act_2,interval,weekday)
i <- summarise(h,newaveragesteps = mean(steps))

library(lattice)
xyplot(newaveragesteps ~ interval | weekday,i,type='l',main="Average Steps per Interval",ylab="Average steps")
```

![](PA1_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
