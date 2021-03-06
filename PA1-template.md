---
title: 'Reproducible Research: Course Project -1'
author: "Priyanka Ingole"
date: "June 11, 2017"
output: html_document
---

### Loading and Preprocessing the data:

- Unzip and load the data into data frame activity
```{r}
dir.create('./Activity Data')
URL<- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(URL, destfile="Activity Data/activity.zip")
unzip(zipfile="./Activity Data/activity.zip",exdir="./Activity Data")
activity<-read.csv("activity.csv", header=TRUE, sep=",")
```

### What is mean total number of steps taken per day?
For this we need to calculate- 

* Total number of steps per day.

* Make a histogram of the total number of steps per day.

* The mean of the total number of steps taken per day.

* Median of the total number of steps taken per day.


```{r,echo=TRUE}
stepsaday <- aggregate(steps ~ date, activity, sum)
hist(stepsaday$steps, main ="Total Steps per Day", col="orange", xlab="Number of Steps")
mean(stepsaday$steps)
median(stepsaday$steps)
```
![](https://github.com/prishak/RepData_PeerAssessment1/blob/master/instructions_fig/fig1-Total%20steps%20per%20Day.png) 




### What is the average daily activity pattern?

FOr this we need to know-

* Average steps per interval for all days.

* Plot the Average Number Steps per Day by Interval

* Interval,contains the maximum number of steps


```{r, echo=TRUE}
stepsainterval <- aggregate(steps~interval,activity,mean)
plot(stepsainterval$interval,stepsainterval$steps,type="l",col="dark red",xlab="INTERVAL",ylab="Ave. Number of Steps",main="Average Number of Steps per Interval")
stepsainterval[which.max(stepsainterval$steps),1]
```
![](https://github.com/prishak/RepData_PeerAssessment1/blob/master/instructions_fig/fig2-Average%20number%20of%20steps%20per%20interval.png)

### Imputing missing values
* Calculate the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```{r, echo=TRUE}
sum(!complete.cases(activity))
```

* Devise a strategy for filling in all of the missing values in the dataset

* Create a new dataset that is equal to the original dataset but with the missing data filled in.

```{r, echo=TRUE}
new_data <- transform(activity, steps = ifelse(is.na(activity$steps), stepsainterval$steps[match(activity$interval, stepsainterval$interval)],activity$steps))
new_data[as.character(new_data$date) == "2012-10-01", 1] <- 0
steps_by_day_i <- aggregate(steps ~ date, new_data, sum)
```

* Make a histogram of the total number of steps per day


```{r}
hist(steps_by_day_i$steps, main = paste("Total Steps per Day"), col="brown", xlab="Number of Steps")
```
![](https://github.com/prishak/RepData_PeerAssessment1/blob/master/instructions_fig/fig-3.png)

* Histogram to show difference
```{r, echo=TRUE}
hist(steps_by_day_i$steps, main = paste("Total Steps per Day"), col="brown", xlab="Number of Steps")

hist(stepsaday$steps, main ="Total Steps per Day", col="orange", xlab="Number of Steps", add=T)
legend("topright", c("Imputed", "Non-imputed"), col=c("brown", "orange"), lwd=5,bty= "o")

```
![](https://github.com/prishak/RepData_PeerAssessment1/blob/master/instructions_fig/fig3.total%20steps%20per%20day.png)

* Mean and Median of non imputed data:

```{r}
mean1<-mean(stepsaday$steps)
mean1
median1<-median(stepsaday$steps)
median1
```
* Mean & Median of imputed data

```{r}
mean2<-mean(steps_by_day_i$steps)
mean2
median2<-median(steps_by_day_i$steps)
median2
```
* Total difference between imputed and non-imputed data.

```{r}
mean_diff <- mean2-mean1
mean_diff
median_diff<-median2-median1
median_diff
```
* Calculate total difference
```{r}
total_diff <- sum(steps_by_day_i$steps) - sum(stepsaday$steps)
total_diff
```
### Are there differences in activity patterns between weekdays and weekends?

```{r}
weekdays <- c("Monday", "Tuesday", "Wednesday", "Thursday","Friday")
new_data$dow = as.factor(ifelse(is.element(weekdays(as.Date(new_data$date)),weekdays), "Weekday", "Weekend"))
stepsinterval1 <- aggregate(steps ~ interval + dow,new_data, mean)
library(lattice)
xyplot(stepsinterval1$steps ~ stepsinterval1$interval|stepsinterval1$dow, main="AVERAGE STEPS PER DAY BY INTERVAL",xlab="INTERVAL", ylab="STEPS",layout=c(1,2), type="l")
```

![](https://github.com/prishak/RepData_PeerAssessment1/blob/master/instructions_fig/fig4-agv%20steps%20per%20day%20by%20interval.png)
