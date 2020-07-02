---
title: "project week 2"
author: "Mateo Córdoba Toro"
date: "2/7/2020"
output: html_document
---

# Project Assignment 1 - Week 2

## Mateo Córdoba Toro 

### Loading Data 

The data was downloaded in the folder containing the project in R. The data was loaded with the name "Data".

```{r Load Data, message=FALSE}
data <- read.csv("activity.csv",sep=",",header = TRUE)
```

### Libraries

To carry out the work, 4 libraries were used:

- ggplot2 Generate charts
- dplyr Data Management
- lubridate date handling
- chron Date management (identify weekends)

```{r Libraries, message= FALSE, results='hide', warning= FALSE}
library(ggplot2)
library(dplyr)
library(lubridate)
library(chron)
```


## What is mean total number of steps taken per day?

The number of steps per day is calculated by grouping the data by date and then adding the steps.

```{r steps per day, warning= FALSE, message=FALSE}
df <- data %>% group_by(date) %>% summarise(suma  = sum(steps, na.rm = TRUE))

# Histogram 
qplot(suma,data=df,geom = "histogram", bins=5)+
  labs(title = "Histogram of the total number of steps taken each day",
       x     = "Steps",
       y     = "Freq")+
  geom_vline(xintercept = mean(df$suma), colour="blue", linetype = "solid") +
  geom_vline(xintercept = median(df$suma), colour="red", linetype = "solid")  +
  annotate("text", label = paste("Mean = ",trunc(mean(df$suma)), sep = " "), 
           x = 19500, y = 25, size = 5, colour = "blue") +
  annotate("text", label = paste("Median = ",median(df$suma), sep = " "),
           x = 19500, y = 21, size = 5, colour = "red")

```


### Mean and median

```{r mean and median, warning= FALSE}
summary(df$suma)
```


## What is the average daily activity pattern?

The data are grouped by interval and later the average of the steps taken by interval is estimated to later graph the generated time series.

```{r average daily pattern, , warning= FALSE, message=FALSE}

df_interval <- data %>% group_by(interval) %>% summarise( mean = mean(steps, na.rm = TRUE))

plot(df_interval$interval,df_interval$mean, type = "l",
     main = "Time series plot of the average number of steps taken", 
     xlab = "Interval", 
     ylab = "Mean steps")

```

## Imputing missing values

### Calulate the  the total number of missing values

The total missing values in the dataset 

```{r total of missing values, warning= FALSE, message=FALSE}

table(is.na(data$steps))[2]

```

### Imputing Strategy

The imputation strategy is to fill in the missing values with the average of the total steps.

```{r imputtign strategy, warning= FALSE, message=FALSE}
data[is.na(data$steps),1] <- mean(data$steps, na.rm = TRUE)

```

### Histogram 

When the missing values are replaced by the mean of the total data, it is observed how the distribution of the histogram coincides towards the mean. Behavior that is not surprising since it is for this value that the missing values are being replaced.

```{r imp missing values hist}
df_imp  <- data %>% group_by(date) %>% summarise(suma  = sum(steps, na.rm = TRUE))
qplot(suma,data=df_imp,geom = "histogram", bins=5)+
  labs(title = "Imp_Histogram of the total number of steps taken each day",
       x     = "Imp_Steps",
       y     = "Freq")+
  geom_vline(xintercept = mean(df_imp$suma), colour="blue", linetype = "solid") +
  geom_vline(xintercept = median(df_imp$suma), colour="red", linetype = "solid")  +
  annotate("text", label = paste("Median = Mean = ",trunc(median(df_imp$suma)), sep = " "),
           x = 19000, y = 21, size = 5, colour = "red")

```

### Mean and median 



```{r imputing mean and median, warning= FALSE, message=FALSE}
summary(df_imp$suma)
```

## Are there differences in activity patterns between weekdays and weekends?

A new "Weekend" variable was created that classifies between days of the week and weekends.

```{r weekend clasification data, warning= FALSE, message=FALSE}
data <- data %>% mutate( Weekend = as.factor(case_when(is.weekend(data$date) == FALSE ~ "WEEKDAY",
                                               is.weekend(data$date) == TRUE  ~ "WEEKEND")))

```

We made the graph taking the x-axis as the intervals and the axis and the mean of the steps that were calculated later.

A slight rise is observed in the middle of the steps taken on weekends.

```{r steps weekend panel}
data_1 <- aggregate(steps ~ interval + Weekend, data, mean)

ggplot(data_1, aes(interval, steps))+
  geom_line()+
  facet_grid(Weekend~.)+
  labs(title = "Steps taken per 5-minute interval",
       x = "Interval",
       y = " Average Number of Steps")
```

