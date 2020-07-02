---
title: "proejfkd"
author: "Mateo Córdoba Toro"
date: "2/7/2020"
output: html_document
---

Project Assignment 1 - Week 2
================================

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
       y     = "Freq")
```