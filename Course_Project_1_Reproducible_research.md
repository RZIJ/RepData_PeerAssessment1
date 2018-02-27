Loading and preprocessing the data
----------------------------------

    setwd("C:/")
    data <- read.table("activity.csv", header = TRUE, sep = ',')
    summary(data)

    ##      steps                date          interval     
    ##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
    ##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
    ##  Median :  0.00   2012-10-03:  288   Median :1177.5  
    ##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
    ##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
    ##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
    ##  NA's   :2304     (Other)   :15840

What is mean, median and histogram of steps taken per day?
----------------------------------------------------------

    StepsTotal <- aggregate(steps ~ date, data = data, sum, na.rm = TRUE)
    mean(StepsTotal$steps, na.rm = TRUE)

    ## [1] 10766.19

    median(StepsTotal$steps, na.rm = TRUE)

    ## [1] 10765

    hist(StepsTotal$steps)

![](Course_Project_1_Reproducible_research_files/figure-markdown_strict/unnamed-chunk-2-1.png)

Timeseries Plot of the average number of steps
----------------------------------------------

    library(dplyr)

    ## Warning: package 'dplyr' was built under R version 3.4.3

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    library(ggplot2)

    ## Warning: package 'ggplot2' was built under R version 3.4.3

    TimeSeries <- data %>% group_by(interval) %>% summarise(Average = mean(steps, na.rm = TRUE))
    T <- ggplot(TimeSeries, aes(y = Average, x = interval)) + geom_line()
    print(T)

![](Course_Project_1_Reproducible_research_files/figure-markdown_strict/unnamed-chunk-3-1.png)

The 5-minute interval that, on average, contains the maximum number of steps
----------------------------------------------------------------------------

    TimeSeries[TimeSeries$Average == max(TimeSeries$Average),]

    ## # A tibble: 1 x 2
    ##   interval  Average
    ##      <int>    <dbl>
    ## 1      835 206.1698

Code to describe and show a strategy for imputing missing data
--------------------------------------------------------------

Histogram of the total number of steps taken each day after missing values are imputed
--------------------------------------------------------------------------------------

    data$steps[is.na(data$steps)] <- mean(TimeSeries$Average)
    StepsTotal <- aggregate(steps ~ date, data = data, sum, na.rm = TRUE)
    hist(StepsTotal$steps)

![](Course_Project_1_Reproducible_research_files/figure-markdown_strict/unnamed-chunk-5-1.png)

Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends
---------------------------------------------------------------------------------------------------------

    data$day <- weekdays(as.Date(data$date))
    data$day <- ifelse(data$day == "zaterdag", "Weekend", ifelse(data$day == "zondag", "Weekend", "Workingday"))
    TimeSeries <- data %>% group_by(interval, day) %>% summarise(Average = mean(steps, na.rm = TRUE))
    ggplot(TimeSeries, aes(y = Average, x = interval)) + geom_line() + facet_grid(. ~ day)

![](Course_Project_1_Reproducible_research_files/figure-markdown_strict/unnamed-chunk-6-1.png)
