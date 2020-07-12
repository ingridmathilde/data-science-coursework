Visualization: Bar Charts
================
Zach del Rosario
2020-05-13

# Setup

*Purpose*: *Bar charts* are a key tool for EDA. In this exercise, we’ll
learn how to construct a variety of different bar charts, as well as
when—and when *not*—to use various charts.

*Reading*: [Bar Charts](https://rstudio.cloud/learn/primers/3.2)
*Topics*: (All topics) *Reading Time*: \~30 minutes

``` r
library(tidyverse)
```

# Question 2

**q1** In the reading, you learned the relation between `geom_bar()` and
`geom_col()`. Use that knowledge to convert the following `geom_bar()`
plot into the same visual using `geom_col()`.

``` r
original_q1 <- mpg %>%
  ggplot(aes(trans)) +
  geom_bar()

answer_q1 <- count(mpg, trans) %>% 
  ggplot(aes(x = trans, y = n)) +
  geom_col()

original_q1
```

![](d06-e-vis01-bar-charts-assignment_files/figure-gfm/q1-task-1.png)<!-- -->

``` r
answer_q1
```

![](d06-e-vis01-bar-charts-assignment_files/figure-gfm/q1-task-2.png)<!-- -->

The reading mentioned that when using `geom_col()` our x-y data should
be `1-to-1`. This next exercise will probe what happens when our data
are not `1-to-1`, and yet we use a `geom_col()`. Note that a
[one-to-one](https://en.wikipedia.org/wiki/Injective_function) function
is one where each input leads to a single output. For the `mpg` dataset,
we can see that the pairs `cty, hwy` clearly don’t have this one-to-one
property:

``` r
## NOTE: Run this chunk for an illustration
mpg %>% filter(cty == 20)
```

    ## # A tibble: 11 x 11
    ##    manufacturer model    displ  year   cyl trans  drv     cty   hwy fl    class 
    ##    <chr>        <chr>    <dbl> <int> <int> <chr>  <chr> <int> <int> <chr> <chr> 
    ##  1 audi         a4         2    2008     4 manua… f        20    31 p     compa…
    ##  2 audi         a4 quat…   2    2008     4 manua… 4        20    28 p     compa…
    ##  3 hyundai      tiburon    2    2008     4 manua… f        20    28 r     subco…
    ##  4 hyundai      tiburon    2    2008     4 auto(… f        20    27 r     subco…
    ##  5 subaru       foreste…   2.5  2008     4 manua… 4        20    27 r     suv   
    ##  6 subaru       foreste…   2.5  2008     4 auto(… 4        20    26 r     suv   
    ##  7 subaru       impreza…   2.5  2008     4 auto(… 4        20    25 p     compa…
    ##  8 subaru       impreza…   2.5  2008     4 auto(… 4        20    27 r     compa…
    ##  9 subaru       impreza…   2.5  2008     4 manua… 4        20    27 r     compa…
    ## 10 volkswagen   new bee…   2.5  2008     5 manua… f        20    28 r     subco…
    ## 11 volkswagen   new bee…   2.5  2008     5 auto(… f        20    29 r     subco…

# Question 1

**q2** The following code attempts to visualize `cty, hwy` from `mpg`
using `geom_col()`. There’s something fishy about the `hwy` values;
what’s wrong here?

*Hint*: Try changing the `position` parameter for `geom_col()`.

``` r
original_q2 <- mpg %>%
  ggplot(aes(x = cty, y = hwy)) +
  geom_col()

answer_q2 <- mpg %>%
  ggplot(aes(x = cty, y = hwy)) +
  geom_col(position = "dodge")

original_q2
```

![](d06-e-vis01-bar-charts-assignment_files/figure-gfm/q2-task-1.png)<!-- -->

``` r
answer_q2
```

![](d06-e-vis01-bar-charts-assignment_files/figure-gfm/q2-task-2.png)<!-- -->

  - **The original plot seems to be adding together the values of hwy
    aka highway miles per gallon (hwy is at most 31) - perhaps it is
    defaulting to position = “stack”.**
  - **When I change to position = “dodge”, it looks like the bar
    displayed is the average(?) hwy per cty aka city miles per gallon.**
  - **I wouldn’t say any of this with confidence yet**

A more standard way to visualize this kind of data is a *scatterplot*,
which we’ll study later. For now, here’s an example of a more effective
way to visualize `cty` vs `hwy`:

``` r
## NOTE: Run this chunk for an illustration
mpg %>%
  ggplot(aes(cty, hwy)) +
  geom_point()
```

![](d06-e-vis01-bar-charts-assignment_files/figure-gfm/q2-compare-1.png)<!-- -->

**q3** The following are two different visualizations of the `mpg`
dataset. Document your observations between the `v1` and `v2` visuals.
Then, determine which—`v1` or `v2`—enabled you to make more
observations. What was the difference between the two visuals?

``` r
## TODO: Run this code without changing, describe your observations on the data
mpg %>%
  ggplot(aes(class, fill = class)) +
  geom_bar()
```

![](d06-e-vis01-bar-charts-assignment_files/figure-gfm/q4-v1-1.png)<!-- -->

  - **SUVs are most represented in our data set.**
  - **2seaters then minivans are least represented in our data set.**
  - **Pickup, subcompact, midsize, and compact are similarly represented
    in the data set (ordered by least to most).**
  - **The appreciate the different colors for the bars, but including
    the legend is redundant.**
  - **I would like to order these as most to least represented or vice
    versa.**

<!-- end list -->

``` r
## TODO: Run this code without changing, describe your observations on the data
mpg %>%
  ggplot(aes(class, fill = drv)) +
  geom_bar()
```

![](d06-e-vis01-bar-charts-assignment_files/figure-gfm/q4-v2-1.png)<!-- -->

  - **This graph displays the count of each class aka type of car, and
    is then further broken down by drv aka type of drive train (f =
    front-wheel drive, r = rear wheel drive, 4 = 4wd).**
  - **SUVs primarily 4wd and pickups are entirely 4wd.**
  - **Midsize, compact, and subcompact are primarily front-wheel
    drive.**
  - **2seaters are entirely rear-wheel drive.**
  - **Again, I would order the plot in most represented to least or vice
    versa.**

**Compare `v1` and `v2`**: **I would order the data by the most
represented to least represented by count for both plots.** \* **I would
select one plot or the other based on the story I am trying to tell:** +
**if I’m trying to explain which car is the most represented in the data
set, I would select v1.** + **if I’m trying to explain which car is the
most represented in the data set and how each breaks down in terms of
drive train, I would obviously select v2.** \* **If I were to use v1, I
would omit the legend - the meaning of the colors is obvious.** \* **v2
is a richer graph in information but requires more thought.**

  - Which visualization—`v1` or `v2`—enabled you to make more
    observations?
  - What was the difference between `v1` and `v2`?
  - **v2 allows me to make more interesting observations.**
  - **v1 maps color according to class, while v2 includes new
    information by mapping color according to drv.**

# Question 4

**q4** The following code has a bug; it does not do what its author
intended. Identify and fix the bug. What does the resulting graph tell
you about the relation between `manufacturer` and `class` of cars in
this dataset?

*Note*: I use a `theme()` call to rotate the x-axis labels. We’ll learn
how to do this in a future exercise.

``` r
original_q4 <- mpg %>%
  ggplot(aes(x = manufacturer, fill = class), position = "dodge") +
  geom_bar() +
  theme(axis.text.x = element_text(angle = 270, vjust = 0.5, hjust = 0))

answer_q4 <- mpg %>%
  ggplot(aes(x = manufacturer, fill = class)) +
  geom_bar(position = "dodge") +
  theme(axis.text.x = element_text(angle = 270, vjust = 0.5, hjust = 0))

original_q4
```

![](d06-e-vis01-bar-charts-assignment_files/figure-gfm/q4-task-1.png)<!-- -->

``` r
answer_q4
```

![](d06-e-vis01-bar-charts-assignment_files/figure-gfm/q4-task-2.png)<!-- -->

**Observations** \* **The user put position argument in the ggplot()
function, but it needs to be in the geom\_bar().** \* **Each
manufacturer is represented by different drive trains. Could this be due
to offerings or chance?** \* **Most of the pickups are Dodge.** \*

**q5** The following graph is hard to read. What other form of faceting
would make the visual more convenient to read? Modify the code below
with your suggested improvement.

``` r
mpg %>%
  ggplot(aes(x = cyl)) +
  geom_bar() +
  facet_grid(~ manufacturer)
```

![](d06-e-vis01-bar-charts-assignment_files/figure-gfm/q5-task-1.png)<!-- -->

<!-- include-exit-ticket -->

# Exit Ticket

<!-- -------------------------------------------------- -->

Once you have completed this exercise, make sure to fill out the **exit
ticket survey**, [linked
here](https://docs.google.com/forms/d/e/1FAIpQLSeuq2LFIwWcm05e8-JU84A3irdEL7JkXhMq5Xtoalib36LFHw/viewform?usp=pp_url&entry.693978880=e-vis01-bar-charts-assignment.Rmd).
