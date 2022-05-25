Bellabeat Case Study
================
Reggie
2/16/2022

## Bellabeat

Founded in 2013 by Urška Sršen and Sando Mur, Bellabeat is a high-tech
manufacturer of health-focused products for women. Sršen used her
background as an artist to develop beautifully designed technology that
informs and inspires women around the world. Collecting data on
activity, sleep, stress, and reproductive health has allowed Bellabeat
to empower women with knowledge about their own health and habits.

While Bellabeat is a successful small company they have the potential to
become a larger player in the global smart device market. Stakeholders
believe that analyzing smart device fitness data could help unlock new
growth opportunities for the company.

## Analysis Objectives

-   What are some trends in smart device usage?
-   How could these trends apply to Bellabeat customers?
-   How could these trends help influence Bellabeat marketing strategy?

## Prepping for analysis

First the necessary R packages must be loaded.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.6     ✓ dplyr   1.0.8
    ## ✓ tidyr   1.2.0     ✓ stringr 1.4.0
    ## ✓ readr   2.1.2     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(dplyr)
library(ggplot2)
library(tidyr)

Sys.setenv(RSTUDIO_PANDOC="/usr/lib/rstudio-server/bin/pandoc")
```

Next we load the datasets (time/date formatting has been fixed in Google
Sheets).

``` r
activity <- read.csv('/cloud/project/bbeat/clean_dActivity_merged.csv')
hcalories <- read.csv('/cloud/project/bbeat/clean_hCalories_merged.csv')
hsteps <- read.csv('/cloud/project/bbeat/clean_hSteps_merged.csv')
hintensities <- read.csv('/cloud/project/bbeat/clean_hIntensities_merged.csv')
sleep <- read.csv('/cloud/project/bbeat/clean_sleepDay_merged.csv')
```

## Data Exploration

Let’s begin by examining the data.

Our data sourced from a survey from the Fitbit Fitness Tracker submitted
to an Amazon Mechanical Turk between 03/12/2016 - 05/12/2016. We can see
that there could be a potential problem with the data being somewhat out
of date, but we will continue with the analysis anyway.

``` r
head(activity)
```

    ##           Id      Date TotalSteps TotalDistance TrackerDistance
    ## 1 1503960366 4/12/2016      13162          8.50            8.50
    ## 2 1503960366 4/13/2016      10735          6.97            6.97
    ## 3 1503960366 4/14/2016      10460          6.74            6.74
    ## 4 1503960366 4/15/2016       9762          6.28            6.28
    ## 5 1503960366 4/16/2016      12669          8.16            8.16
    ## 6 1503960366 4/17/2016       9705          6.48            6.48
    ##   LoggedActivitiesDistance VeryActiveDistance ModeratelyActiveDistance
    ## 1                        0               1.88                     0.55
    ## 2                        0               1.57                     0.69
    ## 3                        0               2.44                     0.40
    ## 4                        0               2.14                     1.26
    ## 5                        0               2.71                     0.41
    ## 6                        0               3.19                     0.78
    ##   LightActiveDistance SedentaryActiveDistance VeryActiveMinutes
    ## 1                6.06                       0                25
    ## 2                4.71                       0                21
    ## 3                3.91                       0                30
    ## 4                2.83                       0                29
    ## 5                5.04                       0                36
    ## 6                2.51                       0                38
    ##   FairlyActiveMinutes LightlyActiveMinutes SedentaryMinutes Calories
    ## 1                  13                  328              728     1985
    ## 2                  19                  217              776     1797
    ## 3                  11                  181             1218     1776
    ## 4                  34                  209              726     1745
    ## 5                  10                  221              773     1863
    ## 6                  20                  164              539     1728

``` r
head(sleep)
```

    ##           Id              SleepDay      Date TotalSleepRecords
    ## 1 1503960366 4/12/2016 12:00:00 AM 4/12/2016                 1
    ## 2 1503960366 4/13/2016 12:00:00 AM 4/13/2016                 2
    ## 3 1503960366 4/15/2016 12:00:00 AM 4/15/2016                 1
    ## 4 1503960366 4/16/2016 12:00:00 AM 4/16/2016                 2
    ## 5 1503960366 4/17/2016 12:00:00 AM 4/17/2016                 1
    ## 6 1503960366 4/19/2016 12:00:00 AM 4/19/2016                 1
    ##   TotalMinutesAsleep TotalTimeInBed
    ## 1                327            346
    ## 2                384            407
    ## 3                412            442
    ## 4                340            367
    ## 5                700            712
    ## 6                304            320

``` r
head(hintensities)
```

    ##           Id          ActivityHour      Date     Day     Time TotalIntensity
    ## 1 1503960366 4/12/2016 12:00:00 AM 4/12/2016 Tuesday 00:00:00             20
    ## 2 1503960366  4/12/2016 1:00:00 AM 4/12/2016 Tuesday 01:00:00              8
    ## 3 1503960366  4/12/2016 2:00:00 AM 4/12/2016 Tuesday 02:00:00              7
    ## 4 1503960366  4/12/2016 3:00:00 AM 4/12/2016 Tuesday 03:00:00              0
    ## 5 1503960366  4/12/2016 4:00:00 AM 4/12/2016 Tuesday 04:00:00              0
    ## 6 1503960366  4/12/2016 5:00:00 AM 4/12/2016 Tuesday 05:00:00              0
    ##   AverageIntensity
    ## 1         0.333333
    ## 2         0.133333
    ## 3         0.116667
    ## 4         0.000000
    ## 5         0.000000
    ## 6         0.000000

The data looks good. The formatting is correct (time has not been
separated in the ‘sleep’ dataset as it is unlikley it will be used).

``` r
n_distinct(activity$Id)
```

    ## [1] 33

``` r
n_distinct(hcalories$Id)
```

    ## [1] 33

``` r
n_distinct(hintensities$Id)
```

    ## [1] 33

``` r
n_distinct(hsteps$Id)
```

    ## [1] 33

``` r
n_distinct(sleep$Id)
```

    ## [1] 24

It appears there are 33 unique samples in each dataset with the
exception of the ‘sleep’ set which only has 24.

``` r
activity %>% select(Calories, TotalSteps, SedentaryMinutes) %>% summary()
```

    ##     Calories      TotalSteps    SedentaryMinutes
    ##  Min.   :   0   Min.   :    0   Min.   :   0.0  
    ##  1st Qu.:1828   1st Qu.: 3790   1st Qu.: 729.8  
    ##  Median :2134   Median : 7406   Median :1057.5  
    ##  Mean   :2304   Mean   : 7638   Mean   : 991.2  
    ##  3rd Qu.:2793   3rd Qu.:10727   3rd Qu.:1229.5  
    ##  Max.   :4900   Max.   :36019   Max.   :1440.0

``` r
hintensities %>% select(TotalIntensity, AverageIntensity) %>% summary()
```

    ##  TotalIntensity   AverageIntensity
    ##  Min.   :  0.00   Min.   :0.0000  
    ##  1st Qu.:  0.00   1st Qu.:0.0000  
    ##  Median :  3.00   Median :0.0500  
    ##  Mean   : 12.04   Mean   :0.2006  
    ##  3rd Qu.: 16.00   3rd Qu.:0.2667  
    ##  Max.   :180.00   Max.   :3.0000

``` r
sleep %>% select(TotalMinutesAsleep, TotalTimeInBed) %>% summary()
```

    ##  TotalMinutesAsleep TotalTimeInBed 
    ##  Min.   : 58.0      Min.   : 61.0  
    ##  1st Qu.:361.0      1st Qu.:403.0  
    ##  Median :433.0      Median :463.0  
    ##  Mean   :419.5      Mean   :458.6  
    ##  3rd Qu.:490.0      3rd Qu.:526.0  
    ##  Max.   :796.0      Max.   :961.0

From this brief summary we can glean a few things about the data:

-   The average amount of calories burnt per day is 2304
-   The average amount of steps taken per day is 7638
-   Average sedentary time is 991.2 minutes or about 16 hours and 31
    minutes
-   Most people spend 458.6 minutes in bed and 419.5 minutes asleep

Now lets take a more in-depth look.

## Data Exploration (cont.)

``` r
mod_int <- hintensities %>%
  group_by(Day) %>%
  summarize(mean_int = mean(TotalIntensity))

ggplot(data=mod_int) + geom_col(mapping = aes(x=Day,y=mean_int),fill="darkgreen") + labs(title="Total Intensity vs Day")
```

![](https://github.com/reggie-dang/projects/blob/reggie-dang-patch-1/figure-gfm/Intensity%20vs%20Day-1.png)<!-- -->

From this chart we can see that there is a slight preference for high
activity/intensity on Saturday.

``` r
tim_int <- hintensities %>%
  group_by(Time) %>%
  summarize(mean_int = mean(TotalIntensity))

ggplot(data=tim_int) + geom_col(mapping = aes(x=Time,y=mean_int),fill="darkgreen") + theme(axis.text.x = element_text(angle = 90)) + labs(title="Total Intensity vs Time")
```

![](https://github.com/reggie-dang/projects/blob/reggie-dang-patch-1/figure-gfm/Intensity%20vs%20Time-1.png)<!-- -->

From this second chart we can intuit that most people prefer to be most
active between the times of 5pm and 7pm.

``` r
ggplot(data=sleep) + geom_jitter(mapping = aes(x=TotalTimeInBed,y=TotalMinutesAsleep),color="darkgreen") + labs(title="Time in Bed vs Time Asleep")
```

![](https://github.com/reggie-dang/projects/blob/reggie-dang-patch-1/figure-gfm/Time%20in%20Bed%20vs%20Time%20Asleep-1.png)<!-- -->

This one is rather obvious, but this chart shows that there is a direct
correlation between time spent in bed and the time spent asleep.

``` r
act_merge <- merge(activity, sleep, by=c('Id', 'Date'))
ggplot(data=act_merge, aes(x=SedentaryMinutes,y=TotalMinutesAsleep)) + geom_point(color="darkgreen") + geom_smooth() + labs(title="Sedentary Time vs Time Asleep")
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](https://github.com/reggie-dang/projects/blob/reggie-dang-patch-1/figure-gfm/Time%20Idle%20vs%20Time%20Asleep-1.png)<!-- -->

With this we can see a downward trend where more sedentary time
correlates to less sleep.

## Summary

Our analysis of the Fitbit data has been rather insightful and with it
we can make a number of recommendations to help Bellabeat with the
development of its app.

First, since most people have a preference for being most active on
Saturdays, the app could add a reward system that positively reinforces
being active for consecutive days or on days other than Saturday.

Next, the times most people prefer to be active are between the hours of
5pm and 7pm. This makes sense as most people tend to work out or go to
the gym after work. The app should have a reminder system that alerts
people during these hours to help motivate them. While on the topic of
an alarm / reminder function of the app we should also add a sleep time
reminder to help cultivate a consistent bedtime.

We could also have the app give out health tips, like how being more
active can help increase your quality of sleep.
