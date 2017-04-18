1.  Loading and preprocessing the data

It will read the csv file "activity" stored in the working directory
then convert the dates factor into a date format.

    path<-getwd()
    data_activity<-read.csv(file.path(path,"activity.csv"))
    data_activity$date<-as.Date(data_activity$date, format = "%Y-%m-%d")

1.  Histogram of the total number of steps taken each day

First we calculate the total number of steps taken each day then we make
a histogram of the frequency.

    total_steps<-aggregate(steps~date,data=data_activity,sum)

Here is the histogram of total step per day:

    hist(total_steps$steps,xlab="Total Nbr of steps taken each day",main="Freq. of total nbr of steps taken each day")

![](PA1_template_files/figure-markdown_strict/histogram-1.png)

1.  Mean and median number of steps taken each day

<!-- -->

    total_steps_mean<-mean(total_steps$steps)
    total_steps_med<-median(total_steps$steps)

The mean is 1.076618910^{4} and the median is 10765

1.  Time series plot of the average number of steps taken

First, build the data frame averaging the number of steps per 5 min
interval

    avg_steps_int<-aggregate(steps~interval, data_activity,mean)

Below, you will find the time series plot of the average number of steps
taken each day:

    plot(avg_steps_int$interval,avg_steps_int$steps, type = "l", xlab = "5 minutes intervals", ylab="Avg nbr of steps")

![](PA1_template_files/figure-markdown_strict/time%20series-1.png)

1.  The 5-minute interval that, on average, contains the maximum number
    of steps

<!-- -->

    max<-subset(avg_steps_int, steps == max(steps))
    max$interval

    ## [1] 835

The 5 minutes interval that contains the maximum number of steps is 835

1.  Code to describe and show a strategy for imputing missing data

First we calculate the total number of missing values:

    nb_na<-sum(is.na(data_activity$steps))

Then we replace all the missing values with the mean for that specific 5
minutes interval in a new data set called: new\_data\_activity

    new_data_activity<-data_activity
    for(i in 1:nrow(new_data_activity)){
      if (is.na(new_data_activity$steps[i])){
      r <- avg_steps_int$steps[avg_steps_int$interval==new_data_activity$interval[i]]
      new_data_activity$steps[i]<-r
      }
    }

1.  Histogram of the total number of steps taken each day after missing
    values are imputed

First, we calculate total number of steps per day

    new_total_steps<-aggregate(steps~date,data=new_data_activity,sum)

Then, make the histogram of total step per day

    hist(new_total_steps$steps,xlab="Total Nbr of steps taken each day",main="Freq. of total nbr of steps taken each day")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-9-1.png)

We also calculate the mean and the median of this new data set

    new_mean<-mean(new_total_steps$steps)
    new_med<-median(new_total_steps$steps)

The mean is 1.076618910^{4}and the median is 1.076618910^{4}

We can see that in this case the mean and the median are equal.

1.  Panel plot comparing the average number of steps taken per 5-minute
    interval across weekdays and weekends

<!-- -->

    #create a new variable indicating whether the day is a weekday or a weekend
    for(i in 1:nrow(new_data_activity)){
      if (weekdays(new_data_activity$date[i]) == "samedi"|weekdays(new_data_activity$date[i]) == "dimanche"){
      new_data_activity$day[i]<-"weekend"
      }
      else {
        new_data_activity$day[i]<-"weekday"  
      }
    }
    #Create a subset of only the "weekday" data then average the total nbr of steps per 5 min intervals
    weekday<-subset(new_data_activity,day=="weekday")
    avg_wd<-aggregate(steps~interval,data=weekday,mean)
    avg_wd$day<-"weekday"
    #Create a subset of only the "weekend" data then average the total nbr of steps per 5 min intervals
    weekend<-subset(new_data_activity,day=="weekend")
    avg_we<-aggregate(steps~interval,data=weekend,mean)
    avg_we$day<-"weekend"
    #Merge the two data frames
    avg_tot<-rbind(avg_wd,avg_we)
    #Make the time series in two different panels
    library(lattice)
    xyplot(steps ~ interval|day, avg_tot, t='l')

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-11-1.png)
