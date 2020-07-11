Isolating Data
================
Zachary del Rosario
2020-05-05

\#Setup

*Purpose*: One of the keys to a successful analysis is the ability to
*focus* on particular topics. When analyzing a dataset, our ability to
focus is tied to our facility at *isolating data*. In this exercise, you
will practice isolating columns with `select()`, picking specific rows
with `filter()`, and sorting your data`with`arrange()\` to see what
rises to the top.

*Reading*: [Isolating Data with
dplyr](https://rstudio.cloud/learn/primers/2.2) (All topics)

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.1     ✓ dplyr   1.0.0
    ## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ──────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(nycflights13) # For `flights` data
```

We’ll use the `nycflights13` dataset for this exercise; upon loading the
package, the data are stored in the variable name `flights`. For
instance:

``` r
flights %>% glimpse
```

    ## Rows: 336,776
    ## Columns: 19
    ## $ year           <int> 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, …
    ## $ month          <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
    ## $ day            <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
    ## $ dep_time       <int> 517, 533, 542, 544, 554, 554, 555, 557, 557, 558, 558,…
    ## $ sched_dep_time <int> 515, 529, 540, 545, 600, 558, 600, 600, 600, 600, 600,…
    ## $ dep_delay      <dbl> 2, 4, 2, -1, -6, -4, -5, -3, -3, -2, -2, -2, -2, -2, -…
    ## $ arr_time       <int> 830, 850, 923, 1004, 812, 740, 913, 709, 838, 753, 849…
    ## $ sched_arr_time <int> 819, 830, 850, 1022, 837, 728, 854, 723, 846, 745, 851…
    ## $ arr_delay      <dbl> 11, 20, 33, -18, -25, 12, 19, -14, -8, 8, -2, -3, 7, -…
    ## $ carrier        <chr> "UA", "UA", "AA", "B6", "DL", "UA", "B6", "EV", "B6", …
    ## $ flight         <int> 1545, 1714, 1141, 725, 461, 1696, 507, 5708, 79, 301, …
    ## $ tailnum        <chr> "N14228", "N24211", "N619AA", "N804JB", "N668DN", "N39…
    ## $ origin         <chr> "EWR", "LGA", "JFK", "JFK", "LGA", "EWR", "EWR", "LGA"…
    ## $ dest           <chr> "IAH", "IAH", "MIA", "BQN", "ATL", "ORD", "FLL", "IAD"…
    ## $ air_time       <dbl> 227, 227, 160, 183, 116, 150, 158, 53, 140, 138, 149, …
    ## $ distance       <dbl> 1400, 1416, 1089, 1576, 762, 719, 1065, 229, 944, 733,…
    ## $ hour           <dbl> 5, 5, 5, 5, 6, 5, 6, 6, 6, 6, 6, 6, 6, 6, 6, 5, 6, 6, …
    ## $ minute         <dbl> 15, 29, 40, 45, 0, 58, 0, 0, 0, 0, 0, 0, 0, 0, 0, 59, …
    ## $ time_hour      <dttm> 2013-01-01 05:00:00, 2013-01-01 05:00:00, 2013-01-01 …

\#\#Question 1

**q1** Select all the variables whose name ends with `_time`.

``` r
df_q1 <- 
  flights %>% 
    select(ends_with("_time"))
```

Use the following code to check your answer.

\#\#Question 2

**q2** Re-arrange the columns to place `dest, origin, carrier` at the
front, but retain all other columns.

*Hint*: The function `everything()` will be useful\!

``` r
df_q2 <- flights %>% select(everything()) 

# TODO: Modify to move columns to the front
df_q2 <- flights %>% select(dest, origin, carrier, everything())
df_q2
```

    ## # A tibble: 336,776 x 19
    ##    dest  origin carrier  year month   day dep_time sched_dep_time dep_delay
    ##    <chr> <chr>  <chr>   <int> <int> <int>    <int>          <int>     <dbl>
    ##  1 IAH   EWR    UA       2013     1     1      517            515         2
    ##  2 IAH   LGA    UA       2013     1     1      533            529         4
    ##  3 MIA   JFK    AA       2013     1     1      542            540         2
    ##  4 BQN   JFK    B6       2013     1     1      544            545        -1
    ##  5 ATL   LGA    DL       2013     1     1      554            600        -6
    ##  6 ORD   EWR    UA       2013     1     1      554            558        -4
    ##  7 FLL   EWR    B6       2013     1     1      555            600        -5
    ##  8 IAD   LGA    EV       2013     1     1      557            600        -3
    ##  9 MCO   JFK    B6       2013     1     1      557            600        -3
    ## 10 ORD   LGA    AA       2013     1     1      558            600        -2
    ## # … with 336,766 more rows, and 10 more variables: arr_time <int>,
    ## #   sched_arr_time <int>, arr_delay <dbl>, flight <int>, tailnum <chr>,
    ## #   air_time <dbl>, distance <dbl>, hour <dbl>, minute <dbl>, time_hour <dttm>

The following is a *unit test* of your code; if you managed to solve
task **q2** correctly, the following code will execute without error.

Since R will only show the first few columns of a tibble, using
`select()` in this fashion will help us see the values of particular
columns.

\#\#Question 3

**q3** Fix the following code. What is the mistake here? What is the
code trying to accomplish?

> original code: flights %\>% filter(dest = “BOS”)

``` r
#corrected code:
flights %>% filter(dest == "BOS")
```

    ## # A tibble: 15,508 x 19
    ##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
    ##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
    ##  1  2013     1     1      559            559         0      702            706
    ##  2  2013     1     1      639            640        -1      739            749
    ##  3  2013     1     1      801            805        -4      900            919
    ##  4  2013     1     1      803            810        -7      903            925
    ##  5  2013     1     1      820            830       -10      940            954
    ##  6  2013     1     1      923            919         4     1026           1030
    ##  7  2013     1     1      957            733       144     1056            853
    ##  8  2013     1     1     1033           1017        16     1130           1136
    ##  9  2013     1     1     1154           1200        -6     1253           1306
    ## 10  2013     1     1     1237           1245        -8     1340           1350
    ## # … with 15,498 more rows, and 11 more variables: arr_delay <dbl>,
    ## #   carrier <chr>, flight <int>, tailnum <chr>, origin <chr>, dest <chr>,
    ## #   air_time <dbl>, distance <dbl>, hour <dbl>, minute <dbl>, time_hour <dttm>

**The error here was that the operation was not boolean (= vs ==).**

The next error is *far more insidious*….

\#\#Question 4

**q4** This code doesn’t quite what the user intended. What went wrong?

``` r
BOS <- "LGA"
flights %>% filter(dest == BOS)
```

    ## # A tibble: 1 x 19
    ##    year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
    ##   <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
    ## 1  2013     7    27       NA            106        NA       NA            245
    ## # … with 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
    ## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
    ## #   hour <dbl>, minute <dbl>, time_hour <dttm>

**In this code, the user has assigned “LGA” string to the object BOS.
They then filtered their data to search for instances where the
destination is equal to the object BOS (BOS) vs the string BOS (“BOS”).
And since we assigned the object BOS to the string “LGA”, it filtered it
to show the instances where the destination is “LGA”. A very confusing
set of steps to get to some weird data.**

\#\#Question 5

It will take practice to get used to when and when not to use
quotations. Don’t worry—we’ll get lots of practice\!

This dataset is called `nycflights`; in what sense is it focused on New
York city? Let’s do a quick check to get an idea:

**q5** Perform **two** filters; first

``` r
df_q5a <- flights %>% 
  filter(dest %in% c("JFK", "LGA", "EWR"))

df_q5b <- flights %>% 
  filter(dest %in% c("JFK", "LGA", "EWR"))
```

Use the following code to check your answer.

*Aside*: Data are not just numbers. Data are *numbers with context*.
Every dataset is put together for some reason. This reason will inform
what observations (rows) and variables (columns) are *in the data*, and
which are *not in the data*. Conversely, thinking carefully about what
data a person or organization bothered to collect—and what they
ignored—can tell you something about the *perspective* of those who
collected the data. Thinking about these issues is partly what separates
**data science** from programming or machine learning. (`end-rant`)

\#\#Question 6

**q6** Sort the flights in *descending* order by their `air_time`. Bring
`air_time, dest` to the front. What can you tell about the longest
flights?

``` r
df_q6 <- flights %>%
  arrange(desc(air_time)) %>% 
  select(
    air_time, 
    dest, everything()
    )

df_q6
```

    ## # A tibble: 336,776 x 19
    ##    air_time dest   year month   day dep_time sched_dep_time dep_delay arr_time
    ##       <dbl> <chr> <int> <int> <int>    <int>          <int>     <dbl>    <int>
    ##  1      695 HNL    2013     3    17     1337           1335         2     1937
    ##  2      691 HNL    2013     2     6      853            900        -7     1542
    ##  3      686 HNL    2013     3    15     1001           1000         1     1551
    ##  4      686 HNL    2013     3    17     1006           1000         6     1607
    ##  5      683 HNL    2013     3    16     1001           1000         1     1544
    ##  6      679 HNL    2013     2     5      900            900         0     1555
    ##  7      676 HNL    2013    11    12      936            930         6     1630
    ##  8      676 HNL    2013     3    14      958           1000        -2     1542
    ##  9      675 HNL    2013    11    20     1006           1000         6     1639
    ## 10      671 HNL    2013     3    15     1342           1335         7     1924
    ## # … with 336,766 more rows, and 10 more variables: sched_arr_time <int>,
    ## #   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>, origin <chr>,
    ## #   distance <dbl>, hour <dbl>, minute <dbl>, time_hour <dttm>

``` r
df_q6 %>% 
  filter(dest == "HNL") %>% 
  ggplot(aes(carrier)) +
  geom_bar()
```

![](d05-e-data01-isolate-assignment_files/figure-gfm/q6-task-1.png)<!-- -->

``` r
## NOTE: No need to change this!
assertthat::assert_that(
  assertthat::are_equal(
    df_q6 %>% head(1) %>% pull(air_time),
    flights %>% pull(air_time) %>% max(na.rm = TRUE)
  )
)
```

    ## [1] TRUE

``` r
assertthat::assert_that(
  assertthat::are_equal(
    df_q6 %>% filter(!is.na(air_time)) %>% tail(1) %>% pull(air_time),
    flights %>% pull(air_time) %>% min(na.rm = TRUE)
  )
)
```

    ## [1] TRUE

``` r
assertthat::assert_that(
  assertthat::are_equal(
    names(df_q6)[1:2],
    c("air_time", "dest")
  )
)
```

    ## [1] TRUE

``` r
print("Great job!")
```

    ## [1] "Great job!"

**The longest flights are when going to Honolulu - makes sense because
that is 4,957 miles away\! These flights are run exclusively by United
Airlines and unsurprisingly, Hawaiian Airlines.**

<!-- include-exit-ticket -->

# Exit Ticket

<!-- -------------------------------------------------- -->

Once you have completed this exercise, make sure to fill out the **exit
ticket survey**, [linked
here](https://docs.google.com/forms/d/e/1FAIpQLSeuq2LFIwWcm05e8-JU84A3irdEL7JkXhMq5Xtoalib36LFHw/viewform?usp=pp_url&entry.693978880=e-data01-isolate-assignment.Rmd).
