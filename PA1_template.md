# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

### Load the data


```r
DF <- read.csv(unz("activity.zip", "activity.csv"))
```

### Process/transform the data

**Intervals**

The column `interval` represents 5-minute intervals.


```r
unique(DF$interval)
```

```
##   [1]    0    5   10   15   20   25   30   35   40   45   50   55  100  105
##  [15]  110  115  120  125  130  135  140  145  150  155  200  205  210  215
##  [29]  220  225  230  235  240  245  250  255  300  305  310  315  320  325
##  [43]  330  335  340  345  350  355  400  405  410  415  420  425  430  435
##  [57]  440  445  450  455  500  505  510  515  520  525  530  535  540  545
##  [71]  550  555  600  605  610  615  620  625  630  635  640  645  650  655
##  [85]  700  705  710  715  720  725  730  735  740  745  750  755  800  805
##  [99]  810  815  820  825  830  835  840  845  850  855  900  905  910  915
## [113]  920  925  930  935  940  945  950  955 1000 1005 1010 1015 1020 1025
## [127] 1030 1035 1040 1045 1050 1055 1100 1105 1110 1115 1120 1125 1130 1135
## [141] 1140 1145 1150 1155 1200 1205 1210 1215 1220 1225 1230 1235 1240 1245
## [155] 1250 1255 1300 1305 1310 1315 1320 1325 1330 1335 1340 1345 1350 1355
## [169] 1400 1405 1410 1415 1420 1425 1430 1435 1440 1445 1450 1455 1500 1505
## [183] 1510 1515 1520 1525 1530 1535 1540 1545 1550 1555 1600 1605 1610 1615
## [197] 1620 1625 1630 1635 1640 1645 1650 1655 1700 1705 1710 1715 1720 1725
## [211] 1730 1735 1740 1745 1750 1755 1800 1805 1810 1815 1820 1825 1830 1835
## [225] 1840 1845 1850 1855 1900 1905 1910 1915 1920 1925 1930 1935 1940 1945
## [239] 1950 1955 2000 2005 2010 2015 2020 2025 2030 2035 2040 2045 2050 2055
## [253] 2100 2105 2110 2115 2120 2125 2130 2135 2140 2145 2150 2155 2200 2205
## [267] 2210 2215 2220 2225 2230 2235 2240 2245 2250 2255 2300 2305 2310 2315
## [281] 2320 2325 2330 2335 2340 2345 2350 2355
```

The intervals were coded by appending the hour and minute and then casting to integers. These can be converted to strings, padded with zeros on the left.


```r
DF$interval <- formatC(DF$interval, width=4, flag=0)
```

**Dates**

The column `date` represents dates in YYYY-MM-HH format. These can be converted to `Date` objects.


```r
DF$date <- as.Date(DF$date)
```


## What is mean total number of steps taken per day?

Ignore missing values for this section.

### Total steps per day


```r
daily_totals <- aggregate(steps ~ date, DF, sum)
daily_totals
```

```
##          date steps
## 1  2012-10-02   126
## 2  2012-10-03 11352
## 3  2012-10-04 12116
## 4  2012-10-05 13294
## 5  2012-10-06 15420
## 6  2012-10-07 11015
## 7  2012-10-09 12811
## 8  2012-10-10  9900
## 9  2012-10-11 10304
## 10 2012-10-12 17382
## 11 2012-10-13 12426
## 12 2012-10-14 15098
## 13 2012-10-15 10139
## 14 2012-10-16 15084
## 15 2012-10-17 13452
## 16 2012-10-18 10056
## 17 2012-10-19 11829
## 18 2012-10-20 10395
## 19 2012-10-21  8821
## 20 2012-10-22 13460
## 21 2012-10-23  8918
## 22 2012-10-24  8355
## 23 2012-10-25  2492
## 24 2012-10-26  6778
## 25 2012-10-27 10119
## 26 2012-10-28 11458
## 27 2012-10-29  5018
## 28 2012-10-30  9819
## 29 2012-10-31 15414
## 30 2012-11-02 10600
## 31 2012-11-03 10571
## 32 2012-11-05 10439
## 33 2012-11-06  8334
## 34 2012-11-07 12883
## 35 2012-11-08  3219
## 36 2012-11-11 12608
## 37 2012-11-12 10765
## 38 2012-11-13  7336
## 39 2012-11-15    41
## 40 2012-11-16  5441
## 41 2012-11-17 14339
## 42 2012-11-18 15110
## 43 2012-11-19  8841
## 44 2012-11-20  4472
## 45 2012-11-21 12787
## 46 2012-11-22 20427
## 47 2012-11-23 21194
## 48 2012-11-24 14478
## 49 2012-11-25 11834
## 50 2012-11-26 11162
## 51 2012-11-27 13646
## 52 2012-11-28 10183
## 53 2012-11-29  7047
```

### Histogram of total steps per day


```r
hist(daily_totals$steps, xlab="Total Steps", ylab="# Days",
     main="Histogram of Total Steps per Day")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png)

### Mean and median steps per day


```r
mean(daily_totals$steps, na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(daily_totals$steps, na.rm=TRUE)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

Ignore missing values for this section too.

### Time series plot of average steps per interval


```r
interval_avgs <- aggregate(steps ~ interval, DF, mean, na.rm=TRUE)
plot(strptime(interval_avgs$interval, format="%H%M"), interval_avgs$steps, type="l",
     xlab="Time", ylab="Average Steps in 5-Minute Interval", main="Time Series Plot of Daily Pattern")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)

### Interval with maximum average steps


```r
interval_avgs$interval[which.max(interval_avgs$steps)]
```

```
## [1] "0835"
```


## Imputing missing values

### Total missing values


```r
sum(is.na(DF$steps))
```

```
## [1] 2304
```

### Imputation strategy


```r
tapply(DF$steps, DF$date, function(x) sum(is.na(x)))
```

```
## 2012-10-01 2012-10-02 2012-10-03 2012-10-04 2012-10-05 2012-10-06 
##        288          0          0          0          0          0 
## 2012-10-07 2012-10-08 2012-10-09 2012-10-10 2012-10-11 2012-10-12 
##          0        288          0          0          0          0 
## 2012-10-13 2012-10-14 2012-10-15 2012-10-16 2012-10-17 2012-10-18 
##          0          0          0          0          0          0 
## 2012-10-19 2012-10-20 2012-10-21 2012-10-22 2012-10-23 2012-10-24 
##          0          0          0          0          0          0 
## 2012-10-25 2012-10-26 2012-10-27 2012-10-28 2012-10-29 2012-10-30 
##          0          0          0          0          0          0 
## 2012-10-31 2012-11-01 2012-11-02 2012-11-03 2012-11-04 2012-11-05 
##          0        288          0          0        288          0 
## 2012-11-06 2012-11-07 2012-11-08 2012-11-09 2012-11-10 2012-11-11 
##          0          0          0        288        288          0 
## 2012-11-12 2012-11-13 2012-11-14 2012-11-15 2012-11-16 2012-11-17 
##          0          0        288          0          0          0 
## 2012-11-18 2012-11-19 2012-11-20 2012-11-21 2012-11-22 2012-11-23 
##          0          0          0          0          0          0 
## 2012-11-24 2012-11-25 2012-11-26 2012-11-27 2012-11-28 2012-11-29 
##          0          0          0          0          0          0 
## 2012-11-30 
##        288
```

There are 8 days completely missing, and the other days have no missing data.

Impute using the mean for each 5-minute interval.

### Apply imputation


```r
DF_fill <- DF
avg_steps <- rep(interval_avgs$steps, length=dim(DF)[1])
DF_fill$steps <- ifelse(is.na(DF$steps), avg_steps, DF$steps)
head(DF_fill)
```

```
##       steps       date interval
## 1 1.7169811 2012-10-01     0000
## 2 0.3396226 2012-10-01     0005
## 3 0.1320755 2012-10-01     0010
## 4 0.1509434 2012-10-01     0015
## 5 0.0754717 2012-10-01     0020
## 6 2.0943396 2012-10-01     0025
```

### Total steps per day, after imputation


```r
daily_totals_fill <- aggregate(steps ~ date, DF_fill, sum)
hist(daily_totals_fill$steps, xlab="Total Steps", ylab="# Days",
     main="Histogram of Total Steps per Day - Imputed")
```

![](PA1_template_files/figure-html/unnamed-chunk-13-1.png)

```r
mean(daily_totals_fill$steps)
```

```
## [1] 10766.19
```

```r
median(daily_totals_fill$steps)
```

```
## [1] 10766.19
```

The mean total daily steps did not change due to imputation. The median increased slightly.

The total daily number of steps only changed for the 8 days with missing data. These days were completely missing, so they were replaced with the average at each interval. Consequently, their totals are the average total.


## Are there differences in activity patterns between weekdays and weekends?

Use the imputed data for this section.

### Create a weekday/weekend factor


```r
DF_fill$weekday <- as.factor(ifelse(weekdays(DF_fill$date) %in% c("Saturday", "Sunday"), "weekend", "weekday"))
head(DF_fill)
```

```
##       steps       date interval weekday
## 1 1.7169811 2012-10-01     0000 weekday
## 2 0.3396226 2012-10-01     0005 weekday
## 3 0.1320755 2012-10-01     0010 weekday
## 4 0.1509434 2012-10-01     0015 weekday
## 5 0.0754717 2012-10-01     0020 weekday
## 6 2.0943396 2012-10-01     0025 weekday
```

### Time series plot of average steps per interval, panel by weekday/weekend


```r
interval_weekday_avgs <- aggregate(steps~interval*weekday, DF_fill, mean)
interval_weekday_avgs$interval <- strptime(interval_weekday_avgs$interval, format="%H%M", tz="UTC")

library(ggplot2)
library(scales)
ggplot(interval_weekday_avgs, aes(interval, steps)) + geom_line() + facet_grid(weekday~.) +
  scale_x_datetime(labels = date_format("%H:%M")) + theme_bw() +
  labs(title="Daily Pattern - Imputed, Weekday vs Weekend", x="Time", y="Average Steps in 5-Minute Interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-15-1.png)

The mid-morning peak is higher on weekdays. The other peaks are lower on weekdays. Activity starts a few hours earlier on weekdays and ends a few hours later on weekends.

