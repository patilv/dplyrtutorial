---
title       : dplyr
subtitle    : 
author      : Almost everything from the tutorial from Kevin Markham
job         : And Hadley Wikham's dplyr resources without which nothing exists
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
---

## What is dplyr?

* Tools for data exploration and transformation
* Intuitive to write and easy to read
* Super-fast on data frames

---
## Installing


```r
if (packageVersion("devtools") < 1.6) {
  install.packages("devtools")
}
devtools::install_github("hadley/lazyeval")
devtools::install_github("hadley/dplyr")
install.packages(c("hflights", "Lahman"))
```

---

##  Load our packages


```r
suppressMessages(library(dplyr))
library(hflights)
```

hflights: Dataset on commercial domestic flights that departed Houston (IAH and HOU) in 2011.

---
## Our data


```r
# explore data
data(hflights)
head(hflights,3)
```

```
##      Year Month DayofMonth DayOfWeek DepTime ArrTime UniqueCarrier
## 5424 2011     1          1         6    1400    1500            AA
## 5425 2011     1          2         7    1401    1501            AA
## 5426 2011     1          3         1    1352    1502            AA
##      FlightNum TailNum ActualElapsedTime AirTime ArrDelay DepDelay Origin
## 5424       428  N576AA                60      40      -10        0    IAH
## 5425       428  N557AA                60      45       -9        1    IAH
## 5426       428  N541AA                70      48       -8       -8    IAH
##      Dest Distance TaxiIn TaxiOut Cancelled CancellationCode Diverted
## 5424  DFW      224      7      13         0                         0
## 5425  DFW      224      6       9         0                         0
## 5426  DFW      224      5      17         0                         0
```

---

## tbl_df

Wraps a data frame that prints nicely


```r
flights <- tbl_df(hflights)
head(flights,3) # Can also use print(flights,3) instead
```

```
## Source: local data frame [3 x 21]
## 
##   Year Month DayofMonth DayOfWeek DepTime ArrTime UniqueCarrier FlightNum
## 1 2011     1          1         6    1400    1500            AA       428
## 2 2011     1          2         7    1401    1501            AA       428
## 3 2011     1          3         1    1352    1502            AA       428
## Variables not shown: TailNum (chr), ActualElapsedTime (int), AirTime
##   (int), ArrDelay (int), DepDelay (int), Origin (chr), Dest (chr),
##   Distance (int), TaxiIn (int), TaxiOut (int), Cancelled (int),
##   CancellationCode (chr), Diverted (int)
```

---
## Reverting back to regular dataframe

```r
# convert to a normal data frame to see all of the columns
head(data.frame(flights),3)
```

```
##      Year Month DayofMonth DayOfWeek DepTime ArrTime UniqueCarrier
## 5424 2011     1          1         6    1400    1500            AA
## 5425 2011     1          2         7    1401    1501            AA
## 5426 2011     1          3         1    1352    1502            AA
##      FlightNum TailNum ActualElapsedTime AirTime ArrDelay DepDelay Origin
## 5424       428  N576AA                60      40      -10        0    IAH
## 5425       428  N557AA                60      45       -9        1    IAH
## 5426       428  N541AA                70      48       -8       -8    IAH
##      Dest Distance TaxiIn TaxiOut Cancelled CancellationCode Diverted
## 5424  DFW      224      7      13         0                         0
## 5425  DFW      224      6       9         0                         0
## 5426  DFW      224      5      17         0                         0
```

---
## Basic single table (df) verbs

1. `filter`: for subsetting variables
2. `select`: for subsetting rows
3. `arrange`: for re-ordering rows
4. `mutate`: for adding new columns
5. `summarise` or `summarize`: for reducing each group to a smaller number of summary statistics 

---

## filter: Keep rows matching criteria


```r
# base R: flights[flights$Month==1 & flights$DayofMonth==1, ]
filter(flights, Month==1 & DayofMonth==1)
```

```
## Source: local data frame [552 x 21]
## 
##    Year Month DayofMonth DayOfWeek DepTime ArrTime UniqueCarrier FlightNum
## 1  2011     1          1         6    1400    1500            AA       428
## 2  2011     1          1         6     728     840            AA       460
## 3  2011     1          1         6    1631    1736            AA      1121
## 4  2011     1          1         6    1756    2112            AA      1294
## 5  2011     1          1         6    1012    1347            AA      1700
## 6  2011     1          1         6    1211    1325            AA      1820
## 7  2011     1          1         6     557     906            AA      1994
## 8  2011     1          1         6    1824    2106            AS       731
## 9  2011     1          1         6     654    1124            B6       620
## 10 2011     1          1         6    1639    2110            B6       622
## ..  ...   ...        ...       ...     ...     ...           ...       ...
## Variables not shown: TailNum (chr), ActualElapsedTime (int), AirTime
##   (int), ArrDelay (int), DepDelay (int), Origin (chr), Dest (chr),
##   Distance (int), TaxiIn (int), TaxiOut (int), Cancelled (int),
##   CancellationCode (chr), Diverted (int)
```

```r
# same as filter(flights, Month==1, DayofMonth==1)
```

---
## filter again


```r
head(filter(flights, UniqueCarrier=="AA" | UniqueCarrier=="UA"),2) 
```

```
## Source: local data frame [2 x 21]
## 
##   Year Month DayofMonth DayOfWeek DepTime ArrTime UniqueCarrier FlightNum
## 1 2011     1          1         6    1400    1500            AA       428
## 2 2011     1          2         7    1401    1501            AA       428
## Variables not shown: TailNum (chr), ActualElapsedTime (int), AirTime
##   (int), ArrDelay (int), DepDelay (int), Origin (chr), Dest (chr),
##   Distance (int), TaxiIn (int), TaxiOut (int), Cancelled (int),
##   CancellationCode (chr), Diverted (int)
```

```r
tail(filter(flights, UniqueCarrier=="AA" | UniqueCarrier=="UA"),2)
```

```
## Source: local data frame [2 x 21]
## 
##   Year Month DayofMonth DayOfWeek DepTime ArrTime UniqueCarrier FlightNum
## 1 2011    12         30         5    1300    1436            UA       971
## 2 2011    12         26         1     607     728            UA       978
## Variables not shown: TailNum (chr), ActualElapsedTime (int), AirTime
##   (int), ArrDelay (int), DepDelay (int), Origin (chr), Dest (chr),
##   Distance (int), TaxiIn (int), TaxiOut (int), Cancelled (int),
##   CancellationCode (chr), Diverted (int)
```

---
## filter again


```r
filter(flights, UniqueCarrier %in% c("AA", "UA"))
```

```
## Source: local data frame [5,316 x 21]
## 
##    Year Month DayofMonth DayOfWeek DepTime ArrTime UniqueCarrier FlightNum
## 1  2011     1          1         6    1400    1500            AA       428
## 2  2011     1          2         7    1401    1501            AA       428
## 3  2011     1          3         1    1352    1502            AA       428
## 4  2011     1          4         2    1403    1513            AA       428
## 5  2011     1          5         3    1405    1507            AA       428
## 6  2011     1          6         4    1359    1503            AA       428
## 7  2011     1          7         5    1359    1509            AA       428
## 8  2011     1          8         6    1355    1454            AA       428
## 9  2011     1          9         7    1443    1554            AA       428
## 10 2011     1         10         1    1443    1553            AA       428
## ..  ...   ...        ...       ...     ...     ...           ...       ...
## Variables not shown: TailNum (chr), ActualElapsedTime (int), AirTime
##   (int), ArrDelay (int), DepDelay (int), Origin (chr), Dest (chr),
##   Distance (int), TaxiIn (int), TaxiOut (int), Cancelled (int),
##   CancellationCode (chr), Diverted (int)
```

---
## select: Pick columns by name


```r
# base R approach to select DepTime, ArrTime, and FlightNum columns
head(flights[, c("DepTime", "ArrTime", "FlightNum")])
```

```
## Source: local data frame [6 x 3]
## 
##   DepTime ArrTime FlightNum
## 1    1400    1500       428
## 2    1401    1501       428
## 3    1352    1502       428
## 4    1403    1513       428
## 5    1405    1507       428
## 6    1359    1503       428
```

---
## select again


```r
# dplyr approach
print(select(flights, DepTime, ArrTime, FlightNum),n=6)
```

```
## Source: local data frame [227,496 x 3]
## 
##    DepTime ArrTime FlightNum
## 1     1400    1500       428
## 2     1401    1501       428
## 3     1352    1502       428
## 4     1403    1513       428
## 5     1405    1507       428
## 6     1359    1503       428
## ..     ...     ...       ...
```

---
## select again


```r
# use colon to select multiple contiguous columns, and use `contains` to match columns by name
# note: `starts_with`, `ends_with`, and `matches` (for regular expressions) can also be used to match columns by name
head(select(flights, Year:DayofMonth, contains("Taxi"), contains("Delay")))
```

```
## Source: local data frame [6 x 7]
## 
##   Year Month DayofMonth TaxiIn TaxiOut ArrDelay DepDelay
## 1 2011     1          1      7      13      -10        0
## 2 2011     1          2      6       9       -9        1
## 3 2011     1          3      5      17       -8       -8
## 4 2011     1          4      9      22        3        3
## 5 2011     1          5      9       9       -3        5
## 6 2011     1          6      6      13       -7       -1
```

---
## Chaining over Nesting?


```r
# nesting method to select UniqueCarrier and DepDelay columns and filter for delays over 60 minutes
head(filter(select(flights, UniqueCarrier, DepDelay), DepDelay > 60))
```

```
## Source: local data frame [6 x 2]
## 
##   UniqueCarrier DepDelay
## 1            AA       90
## 2            AA       67
## 3            AA       74
## 4            AA      125
## 5            AA       82
## 6            AA       99
```

---
## Chaining over Nesting


```r
flights %>%
    select(UniqueCarrier, DepDelay) %>%
    filter(DepDelay > 60) %>%
    head()
```

```
## Source: local data frame [6 x 2]
## 
##   UniqueCarrier DepDelay
## 1            AA       90
## 2            AA       67
## 3            AA       74
## 4            AA      125
## 5            AA       82
## 6            AA       99
```

---
## Chaining

* Chaining increases readability significantly when there are many commands
* Operator is automatically imported from the [magrittr](https://github.com/smbache/magrittr) package
* Can be used to replace nesting in R commands outside of dplyr

---
## Chaining


```r
# create two vectors and calculate Euclidian distance between them
x1 <- 1:5; x2 <- 2:6

# Usual 
sqrt(sum((x1-x2)^2))
```

```
## [1] 2.236
```

```r
# chaining method
(x1-x2)^2 %>% sum() %>% sqrt()
```

```
## [1] 2.236
```

---
## arrange: Reorder rows


```r
# base R approach to select UniqueCarrier and DepDelay columns and sort by DepDelay
head(flights[order(flights$DepDelay), c("UniqueCarrier", "DepDelay")])
```

```
## Source: local data frame [6 x 2]
## 
##   UniqueCarrier DepDelay
## 1            OO      -33
## 2            MQ      -23
## 3            XE      -19
## 4            XE      -19
## 5            CO      -18
## 6            EV      -18
```

---
## arrange


```r
# dplyr approach
flights %>%
    select(UniqueCarrier, DepDelay) %>%
    arrange(DepDelay) %>% # arrange(desc(DepDelay)) for descending order
    head()
```

```
## Source: local data frame [6 x 2]
## 
##   UniqueCarrier DepDelay
## 1            OO      -33
## 2            MQ      -23
## 3            XE      -19
## 4            XE      -19
## 5            CO      -18
## 6            EV      -18
```

---
## mutate: create new variables that are functions of existing variables


```r
# base R approach to create a new variable Speed (in mph)
flights$Speed <- flights$Distance / flights$AirTime*60
head(flights[, c("Distance", "AirTime", "Speed")])
```

```
## Source: local data frame [6 x 3]
## 
##   Distance AirTime Speed
## 1      224      40 336.0
## 2      224      45 298.7
## 3      224      48 280.0
## 4      224      39 344.6
## 5      224      44 305.5
## 6      224      45 298.7
```

---
## mutate


```r
# dplyr approach (prints the new variable but does not store it)
flights %>%
    select(Distance, AirTime) %>%
    mutate(Speed = Distance/AirTime*60) %>%
    head()
```

```
## Source: local data frame [6 x 3]
## 
##   Distance AirTime Speed
## 1      224      40 336.0
## 2      224      45 298.7
## 3      224      48 280.0
## 4      224      39 344.6
## 5      224      44 305.5
## 6      224      45 298.7
```

```r
# store the new variable: flights <- flights %>% mutate(Speed = Distance/AirTime*60)
```

---
## summarise/summarize: Reduce multiple variables to values

* Primarily useful with data that has been grouped by one or more variables
* `group_by` creates the groups that will be operated on
* `summarise` uses the provided aggregation function to summarise each group


```r
# base R approaches to calculate the average arrival delay to each destination
head(aggregate(ArrDelay ~ Dest, flights, mean))
```

```
##   Dest ArrDelay
## 1  ABQ    7.226
## 2  AEX    5.839
## 3  AGS    4.000
## 4  AMA    6.840
## 5  ANC   26.081
## 6  ASE    6.795
```

```r
# or head(with(flights, tapply(ArrDelay, Dest, mean, na.rm=TRUE)))
```

---
## summarise


```r
# dplyr approach: create a table grouped by Dest, and then summarise each group by taking the mean of ArrDelay
flights %>%
    group_by(Dest) %>%
    summarise(avg_delay = mean(ArrDelay, na.rm=TRUE)) %>%
    head()
```

```
## Source: local data frame [6 x 2]
## 
##   Dest avg_delay
## 1  ABQ     7.226
## 2  AEX     5.839
## 3  AGS     4.000
## 4  AMA     6.840
## 5  ANC    26.081
## 6  ASE     6.795
```

---
## summarise_each/mutate_each: apply the same summary/mutate function(s) to multiple columns at once


```r
# for each carrier, calculate the percentage of flights cancelled or diverted
flights %>%
    group_by(UniqueCarrier) %>%
    summarise_each(funs(mean), Cancelled, Diverted) %>%
    head()
```

```
## Source: local data frame [6 x 3]
## 
##   UniqueCarrier Cancelled Diverted
## 1            AA  0.018496 0.001850
## 2            AS  0.000000 0.002740
## 3            B6  0.025899 0.005755
## 4            CO  0.006783 0.002627
## 5            DL  0.015903 0.003029
## 6            EV  0.034483 0.003176
```

---
## summarise_each


```r
# for each carrier, calculate the minimum and maximum arrival and departure delays
flights %>%
    group_by(UniqueCarrier) %>%
    summarise_each(funs(min(., na.rm=TRUE), max(., na.rm=TRUE)), matches("Delay")) %>%
    head()
```

```
## Source: local data frame [6 x 5]
## 
##   UniqueCarrier ArrDelay_min DepDelay_min ArrDelay_max DepDelay_max
## 1            AA          -39          -15          978          970
## 2            AS          -43          -15          183          172
## 3            B6          -44          -14          335          310
## 4            CO          -55          -18          957          981
## 5            DL          -32          -17          701          730
## 6            EV          -40          -18          469          479
```

---
## mutate_each 

```r
flights %>%
    select(matches("Delay")) %>%
    head(3)
```

```
## Source: local data frame [3 x 2]
## 
##   ArrDelay DepDelay
## 1      -10        0
## 2       -9        1
## 3       -8       -8
```

```r
flights %>%
    select(matches("Delay")) %>%
    mutate_each(funs(half=./2)) %>%
    head(3)
```

```
## Source: local data frame [3 x 2]
## 
##   ArrDelay DepDelay
## 1     -5.0      0.0
## 2     -4.5      0.5
## 3     -4.0     -4.0
```

---
## n():  counts the number of rows in a group


```r
# for each day of the year, count the total number of flights and sort in descending order
flights %>%
    group_by(Month, DayofMonth) %>%
    summarise(flight_count = n()) %>%
    arrange(desc(flight_count)) %>%
    head()
```

```
## Source: local data frame [6 x 3]
## Groups: Month
## 
##   Month DayofMonth flight_count
## 1     1          3          702
## 2     1          2          678
## 3     1         20          663
## 4     1         27          663
## 5     1         13          662
## 6     1          7          661
```


```r
# rewrite more simply with the `tally` function
flights %>%
    group_by(Month, DayofMonth) %>%
    tally(sort = TRUE)
```

---
## n_distinct(vector): counts the number of unique items in that vector


```r
# for each destination, count the total number of flights and the number of distinct planes that flew there
flights %>%
    group_by(Dest) %>%
    summarise(flight_count = n(), plane_count = n_distinct(TailNum)) %>%
    head()
```

```
## Source: local data frame [6 x 3]
## 
##   Dest flight_count plane_count
## 1  ABQ         2812         716
## 2  AEX          724         215
## 3  AGS            1           1
## 4  AMA         1297         158
## 5  ANC          125          38
## 6  ASE          125          60
```

---
## Grouping without summarising


```r
# for each destination, show the number of cancelled and not cancelled flights
flights %>%
    group_by(Dest) %>%
    select(Cancelled) %>%
    table() %>%
    head()
```

```
##      Cancelled
## Dest     0  1
##   ABQ 2787 25
##   AEX  712 12
##   AGS    1  0
##   AMA 1265 32
##   ANC  125  0
##   ASE  120  5
```

---
## Window Functions

* Aggregation function (like `mean`) takes n inputs and returns 1 value
* [Window function](http://cran.r-project.org/web/packages/dplyr/vignettes/window-functions.html) takes n inputs and returns n values
* Includes ranking and ordering functions (like `min_rank`), offset functions (`lead` and `lag`), and cumulative aggregates (like `cummean`).

---
## Try this


```r
# for each carrier, calculate which two days of the year they had their longest departure delays
# note: smallest (not largest) value is ranked as 1, so you have to use `desc` to rank by largest value
flights %>%
    group_by(UniqueCarrier) %>%
    select(Month, DayofMonth, DepDelay) %>%
    filter(min_rank(desc(DepDelay)) <= 2) %>%
    arrange(UniqueCarrier, desc(DepDelay)) %>%
    head()
```

```
## Source: local data frame [6 x 4]
## Groups: UniqueCarrier
## 
##   UniqueCarrier Month DayofMonth DepDelay
## 1            AA    12         12      970
## 2            AA    11         19      677
## 3            AS     2         28      172
## 4            AS     7          6      138
## 5            B6    10         29      310
## 6            B6     8         19      283
```

---
## Other things to play with


```r
# for each carrier, calculate which two days of the year they had their longest departure delays --- rewrite previous with the `top_n` function
flights %>%
    group_by(UniqueCarrier) %>%
    select(Month, DayofMonth, DepDelay) %>%
    top_n(2) %>%
    arrange(UniqueCarrier, desc(DepDelay))

# for each month, calculate the number of flights and the change from the previous month
flights %>%
    group_by(Month) %>%
    summarise(flight_count = n()) %>%
    mutate(change = flight_count - lag(flight_count))

# rewrite previous with the `tally` function
flights %>%
    group_by(Month) %>%
    tally() %>%
    mutate(change = n - lag(n))
```

---
## Other Useful Convenience Functions


```r
# randomly sample a fixed number of rows, without replacement
flights %>% sample_n(5)

# randomly sample a fraction of rows, with replacement
flights %>% sample_frac(0.25, replace=TRUE)

# base R approach to view the structure of an object
str(flights)

# dplyr approach: better formatting, and adapts to your screen width
glimpse(flights)
```

---
## do : for doing arbitrary operations


```r
mtcars %>% group_by(cyl) %>% do(head(.,2))
```

```
## Source: local data frame [6 x 11]
## Groups: cyl
## 
##    mpg cyl  disp  hp drat    wt  qsec vs am gear carb
## 1 22.8   4 108.0  93 3.85 2.320 18.61  1  1    4    1
## 2 24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2
## 3 21.0   6 160.0 110 3.90 2.620 16.46  0  1    4    4
## 4 21.0   6 160.0 110 3.90 2.875 17.02  0  1    4    4
## 5 18.7   8 360.0 175 3.15 3.440 17.02  0  0    3    2
## 6 14.3   8 360.0 245 3.21 3.570 15.84  0  0    3    4
```


```r
# Another example
models <- mtcars %>% group_by(cyl) %>% do(lm = lm(mpg ~ wt, data = .))
models %>% summarise(rsq = summary(lm)$r.squared)
```

---
## Binary verbs

4 joins from SQL

* `inner_join(x, y)`: matching x + y
* `left_join(x, y)` : all x + matching y
* `semi_join(x, y)` : all x with match in y
* `anti_join(x, y)` : all x without match in y

---
## Resources

* [Kevin Markham's tutorial](http://www.dataschool.io/dplyr-tutorial-for-faster-data-manipulation-in-r/)
* [Official dplyr reference manual and vignettes on CRAN](http://cran.r-project.org/web/packages/dplyr/index.html): vignettes are well-written and cover many aspects of dplyr
* [July 2014 webinar about dplyr (and ggvis) by Hadley Wickham](http://pages.rstudio.net/Webinar-Series-Recording-Essential-Tools-for-R.html) and related [slides/code](https://github.com/rstudio/webinars/tree/master/2014-01): mostly conceptual, with a bit of code
* [dplyr tutorial by Hadley Wickham](https://www.dropbox.com/sh/i8qnluwmuieicxc/AAAgt9tIKoIm7WZKIyK25lh6a) at the [useR! 2014 conference](http://user2014.stat.ucla.edu/): excellent, in-depth tutorial with lots of example code (Dropbox link includes slides, code files, and data files)
* [dplyr GitHub repo](https://github.com/hadley/dplyr) and [list of releases](https://github.com/hadley/dplyr/releases)
