Reading the dataset
-------------------

You need to get your wk and read the data through the read.csv function.
Clean the data of the NA and show the first lines.

    setwd("C:/Users/utente/Desktop/Learning/R Programming/Assign two months of steps")
    data_steps <- read.csv("activity.csv")
    data <- data_steps[ with (data_steps, { !(is.na(steps)) } ), ]
    head(data)

    ##     steps       date interval
    ## 289     0 2012-10-02        0
    ## 290     0 2012-10-02        5
    ## 291     0 2012-10-02       10
    ## 292     0 2012-10-02       15
    ## 293     0 2012-10-02       20
    ## 294     0 2012-10-02       25

Creating an histogram with the total nb of steps per day and calculating mean and median
----------------------------------------------------------------------------------------

Now you can find the total of the steps per day. You need to install the
dplyr package.

    by_day <- group_by(data, date)
    steps_by_day <- summarise(by_day, total = sum(steps))
    steps_by_day

    ## # A tibble: 53 × 2
    ##          date total
    ##        <fctr> <int>
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
    ## # ... with 43 more rows

    hist(steps_by_day$total, main="Histogram with number of steps per day", 
    xlab="Number of steps by day")

![](PA1_template_files/figure-markdown_strict/creating%20the%20histogram-1.png)

    summary(steps_by_day)

    ##          date        total      
    ##  2012-10-02: 1   Min.   :   41  
    ##  2012-10-03: 1   1st Qu.: 8841  
    ##  2012-10-04: 1   Median :10765  
    ##  2012-10-05: 1   Mean   :10766  
    ##  2012-10-06: 1   3rd Qu.:13294  
    ##  2012-10-07: 1   Max.   :21194  
    ##  (Other)   :47

The summary function on steps\_by\_day shows that the mean is 10766 and
the median is 10765.

Time series plot of the average number of steps taken
-----------------------------------------------------

Make a time series plot (i.e. type = "l") of the 5-minute interval
(x-axis) and the average number of steps taken, averaged across all days
(y-axis)

    steps_by_interval <- aggregate(steps ~ interval, data, mean)
    plot(steps_by_interval$interval, steps_by_interval$steps, type='l', 
          main="Average number of steps over all days", xlab="Interval", 
          ylab="Average number of steps")

![](PA1_template_files/figure-markdown_strict/creating%20the%20plot-1.png)

Five minutes interval with max steps
------------------------------------

Which 5-minute interval, on average across all the days in the dataset,
contains the maximum number of steps?

    max_steps_row <- which.max(steps_by_interval$steps)
    steps_by_interval[max_steps_row, ]

    ##     interval    steps
    ## 104      835 206.1698

The 5 minutes interval with the maximum number of steps is the 835 and
has 206.1698 steps.

Code to describe and show a strategy for inputting missing data
---------------------------------------------------------------

    sum(is.na(data_steps))

    ## [1] 2304

The number of rows with NA si 2304

    data_imputed <- data_steps
    for (i in 1:nrow(data_imputed)) {
      if (is.na(data_imputed$steps[i])) {
        interval_value <- data_imputed$interval[i]
        steps_value <- steps_by_interval[
          steps_by_interval$interval == interval_value,]
        data_imputed$steps[i] <- steps_value$steps
      }
    }

I've created new data set data\_no\_na which equals to data\_row but
without NA's. All NA's are replaced with mean of 5-minute interval

    df_imputed_steps_by_day <- aggregate(steps ~ date, data_imputed, sum)
    head(df_imputed_steps_by_day)

    ##         date    steps
    ## 1 2012-10-01 10766.19
    ## 2 2012-10-02   126.00
    ## 3 2012-10-03 11352.00
    ## 4 2012-10-04 12116.00
    ## 5 2012-10-05 13294.00
    ## 6 2012-10-06 15420.00

Histogram of total number of steps taken each day after NA is imputed
---------------------------------------------------------------------

    hist(df_imputed_steps_by_day$steps, main="Histogram of total number of steps per day (imputed)", 
         xlab="Total number of steps in a day")

![](PA1_template_files/figure-markdown_strict/hist-1.png)

    mean(df_imputed_steps_by_day$steps)

    ## [1] 10766.19

    median(df_imputed_steps_by_day$steps)

    ## [1] 10766.19

    mean(steps_by_day$total)

    ## [1] 10766.19

    median(steps_by_day$total)

    ## [1] 10765

Mean values stays the same but therer is slight difference in meadian
value.
