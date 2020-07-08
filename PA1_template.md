---
title: "Assignment_course5"
author: "Elisabetta Crescio"
date: "8/7/2020"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Assignment on step activity

In this analysis the following data have been analyzed:
[ Activity monitoring data ](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip)
The variables included in this dataset are:

-steps: Number of steps taking in a 5-minute interval (missing values are coded as \color{red}{\verb|NA|}NA)
-date: The date on which the measurement was taken in YYYY-MM-DD format
-interval: Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

```{r ,echo=TRUE}

data<-read.csv("activity.csv",header=TRUE,na.strings="NA")

```


##What is mean total number of steps taken per day?

The total number of steps taken per day can be analyzed by producing and histogram of the variable *step*, removing NA values:

```{r ,echo=TRUE}

bad<-is.na(data$steps) 
```

## Histogram of total number of steps

You can also embed plots, for example:

```{r steps, echo=TRUE}
hist(data$steps[!bad],main="Total number of steps per day",xlab="number of steps",ylab="Frequancy",col="blue"
)
```

We can see that most of data are below 100 steps per day.

The mean number of steps taken by day and the median are respectively:
```{r , echo=TRUE}

mean(data$steps[!bad]) 
median(data$steps[!bad])
print(mean(data$steps[!bad]))
print(median(data$steps[!bad]))

```
## What is the average daily activity pattern?
We want to see what is the mean number of steps taken daily depending on the time of day, i.e. the variable *interval* in our dataframe. We can create a time series plot by summarizing the data in the following way:
```{r , echo=TRUE}
data2 <- aggregate(steps ~ interval, data = data, FUN = mean) 
```
With *aggregate* we create a new dataframe where the columns are the data intervals corresponding to one day and the total steps averaged over all days. Wo obtain the following time series plot:


## Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
```{r , echo=TRUE}
which.max(data2$steps)
data2$interval[which.max(data2$steps)]
```

```{r , echo=TRUE}
plot(data2$interval,data2$steps,type="l",main="Mean number of steps by time interval",xlab="time interval")

```


## Imputing missing values
The presence of missing days may introduce bias into some calculations or summaries of the data. We can search for NA data and replace them with the mean number of steps taken (calculated above) for the corresponding interval. The number of NA data is:

```{r , echo=TRUE}
sum(is.na(data))
```

We can replace the NA in the following way:
```{r , echo=TRUE}
meansteps_byinterval<-rep(data2$steps,61)
data$meansteps_byinterval<-meansteps_byinterval
data$steps[bad]<-data$meansteps_byinterval[bad]

```
We add at the original dataframe a new column where the averaged number of steps are repeated for all days and we replace the NA with the mean of steps taken at the corresponding interval.
We can study the effect of taking into account NA values by plotting again the histogram and calculating the mean and median:

```{r , echo=TRUE}
hist(data$steps,main="Total number of steps per day",xlab="number of steps",ylab="Frequancy",col="blue"
)
```
We can observe that the number of entries in the histogram has changed, but the main and median did not change.
```{r , echo=TRUE}
mean(data$steps) ##mean of steps after replacing NA
median(data$steps)
print(mean(data$steps))
print(median(data$steps))

```
## Are there differences in activity patterns between weekdays and weekends?

We want to compare the activity during working and non-working days. To do so, we create a new column which represents a factor variable to distinguish if it is a working day or not.
```{r , echo=TRUE}
data$weekend<-weekdays(as.Date(data$date))

```
 Then we subset the dataframes depending on this factor variable and create two new dataframes with the *aggregate* command to have the variables *interval* and the mean steps averaged over all the days ready for the time series plot:

```{r , echo=TRUE}
data$weekend[which(weekdays(as.Date(data$date))!="domingo" | weekdays(as.Date(data$date))!="sábado" ) ] <- "Weekday"
data$weekend[which(weekdays(as.Date(data$date))=="domingo")] <- "Weekend"
data$weekend[which(weekdays(as.Date(data$date))=="sábado")] <- "Weekend"

data_weekend<-data[which(data$weekend=="Weekend"),]
data_weekday<-data[which(data$weekend=="Weekday"),]
data_weekend<-aggregate(steps ~ interval, data = data_weekend, FUN = mean)
data_weekday<-aggregate(steps ~ interval, data = data_weekday, FUN = mean)


```
We compare the activity in working days and non-working days plotting two timeseries as follows:
```{r , echo=TRUE}
par(mfrow=c(2,1))
plot(data_weekday$interval,data_weekday$steps,type="l",main="Mean number of steps by time interval during the week",xlab="time interval")
plot(data_weekend$interval,data_weekend$steps,type="l",main="Mean number of steps by time interval during the weekend",xlab="time interval")

```

