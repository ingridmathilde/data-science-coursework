Michelson Speed-of-light Measurements
================
ingridmathilde
2020-07-16

  - [Grading Rubric](#grading-rubric)
      - [Individual](#individual)
      - [Team](#team)
      - [Due Date](#due-date)
  - [Question 1](#question-1)
  - [Question 2](#question-2)
  - [Question 3](#question-3)
  - [Question 4](#question-4)
      - [Bibliography](#bibliography)

*Purpose*: When studying physical problems, there is an important
distinction between *error* and *uncertainty*. The primary purpose of
this challenge is to dip our toes into these factors by analyzing a real
dataset.

*Reading*: [Experimental Determination of the Velocity of
Light](https://play.google.com/books/reader?id=343nAAAAMAAJ&hl=en&pg=GBS.PA115)
(Optional)

<!-- include-rubric -->

# Grading Rubric

<!-- -------------------------------------------------- -->

Unlike exercises, **challenges will be graded**. The following rubrics
define how you will be graded, both on an individual and team basis.

## Individual

<!-- ------------------------- -->

| Category    | Unsatisfactory                                                                   | Satisfactory                                                               |
| ----------- | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| Effort      | Some task **q**’s left unattempted                                               | All task **q**’s attempted                                                 |
| Observed    | Did not document observations                                                    | Documented observations based on analysis                                  |
| Supported   | Some observations not supported by analysis                                      | All observations supported by analysis (table, graph, etc.)                |
| Code Styled | Violations of the [style guide](https://style.tidyverse.org/) hinder readability | Code sufficiently close to the [style guide](https://style.tidyverse.org/) |

## Team

<!-- ------------------------- -->

| Category   | Unsatisfactory                                                                                   | Satisfactory                                       |
| ---------- | ------------------------------------------------------------------------------------------------ | -------------------------------------------------- |
| Documented | No team contributions to Wiki                                                                    | Team contributed to Wiki                           |
| Referenced | No team references in Wiki                                                                       | At least one reference in Wiki to member report(s) |
| Relevant   | References unrelated to assertion, or difficult to find related analysis based on reference text | Reference text clearly points to relevant analysis |

## Due Date

<!-- ------------------------- -->

All the deliverables stated in the rubrics above are due on the day of
the class discussion of that exercise. See the
[Syllabus](https://docs.google.com/document/d/1jJTh2DH8nVJd2eyMMoyNGroReo0BKcJrz1eONi3rPSc/edit?usp=sharing)
for more information.

``` r
# Libraries
library(tidyverse)
library(googlesheets4)
library(cowplot)

url <- "https://docs.google.com/spreadsheets/d/1av_SXn4j0-4Rk0mQFik3LLr-uf0YdA06i3ugE6n-Zdo/edit?usp=sharing"

# Parameters
LIGHTSPEED_VACUUM    <- 299792.458 # Exact speed of light in a vacuum (km / s)
LIGHTSPEED_MICHELSON <- 299944.00  # Michelson's speed estimate (km / s)
LIGHTSPEED_PM        <- 51         # Michelson error estimate (km / s)
```

*Background*: In 1879 Albert Michelson led an experimental campaign to
measure the speed of light. His approach was a development upon the
method of Foucault, and resulted in a new estimate of
\(v_0 = 299944 \pm 51\) kilometers per second (in a vacuum). This is
very close to the modern *exact* value of `r LIGHTSPEED_VACUUM`. In this
challenge, you will analyze Michelson’s original data, and explore some
of the factors associated with his experiment.

I’ve already copied Michelson’s data from his 1880 publication; the code
chunk below will load these data from a public googlesheet.

*Aside*: The speed of light is *exact* (there is **zero error** in the
value `LIGHTSPEED_VACUUM`) because the meter is actually
[*defined*](https://en.wikipedia.org/wiki/Metre#Speed_of_light_definition)
in terms of the speed of light\!

``` r
## Note: No need to edit this chunk!
gs4_deauth()
ss <- gs4_get(url)
df_michelson <-
  read_sheet(ss) %>%
  select(Date, Distinctness, Temp, Velocity) %>%
  mutate(Distinctness = as_factor(Distinctness))
```

    ## Reading from "michelson1879"

    ## Range "Sheet1"

``` r
df_michelson %>% glimpse
```

    ## Rows: 100
    ## Columns: 4
    ## $ Date         <dttm> 1879-06-05, 1879-06-07, 1879-06-07, 1879-06-07, 1879-06…
    ## $ Distinctness <fct> 3, 2, 2, 2, 2, 2, 3, 3, 3, 3, 2, 2, 2, 2, 2, 1, 3, 3, 2,…
    ## $ Temp         <dbl> 76, 72, 72, 72, 72, 72, 83, 83, 83, 83, 83, 90, 90, 71, …
    ## $ Velocity     <dbl> 299850, 299740, 299900, 300070, 299930, 299850, 299950, …

*Data dictionary*:

  - `Date`: Date of measurement
  - `Distinctness`: Distinctness of measured images: 3 = good, 2 = fair,
    1 = poor
  - `Temp`: Ambient temperature (Fahrenheit)
  - `Velocity`: Measured speed of light (km / s)

# Question 1

**q1** Re-create the following table (from Michelson (1880), pg. 139)
using `df_michelson` and `dplyr`. Note that your values *will not* match
those of Michelson *exactly*; why might this be?

| Distinctness | n  | MeanVelocity |
| ------------ | -- | ------------ |
| 3            | 46 | 299860       |
| 2            | 39 | 299860       |
| 1            | 15 | 299810       |

``` r
## TODO: Compute summaries
df_q1 <- df_michelson
df_q1 %>%
  arrange(desc(Distinctness)) %>%
  group_by(Distinctness) %>% 
  summarise(n = n(), MeanVelocity = mean(Velocity)) %>%
  knitr::kable()
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

| Distinctness |  n | MeanVelocity |
| :----------- | -: | -----------: |
| 1            | 15 |     299808.0 |
| 2            | 39 |     299858.5 |
| 3            | 46 |     299861.7 |

**Observations**:

  - **If the velocity is higher, I would expect the images to be more
    distinct.**
  - **He records more instances of level 2 or 3 distinctness than 1 -
    could this be an issue of threshold? A rating system is also dicey -
    could he have tested with multiple people to get a better sense of
    the accuracy of his rating system?**
  - **The velocities are different because we are using type double for
    velocity. I imagine he did not have that degree of precise
    calculation. So I’m wondering - should we?**

The `Velocity` values in the dataset are the speed of light *in air*;
Michelson introduced a couple of adjustments to estimate the speed of
light in a vacuum. In total, he added \(+92\) km/s to his mean estimate
for `VelocityVacuum` (from Michelson (1880), pg. 141). While this isn’t
fully rigorous (\(+92\) km/s is based on the mean temperature), we’ll
simply apply this correction to all the observations in the dataset.

# Question 2

**q2** Create a new variable `VelocityVacuum` with the \(+92\) km/s
adjustment to `Velocity`. Assign this new dataframe to `df_q2`.

``` r
## TODO: Adjust the data, assign to df_q2
df_q2 <- df_michelson %>% 
  mutate(VelocityVacuum = Velocity + 92)

glimpse(df_q2)
```

    ## Rows: 100
    ## Columns: 5
    ## $ Date           <dttm> 1879-06-05, 1879-06-07, 1879-06-07, 1879-06-07, 1879-…
    ## $ Distinctness   <fct> 3, 2, 2, 2, 2, 2, 3, 3, 3, 3, 2, 2, 2, 2, 2, 1, 3, 3, …
    ## $ Temp           <dbl> 76, 72, 72, 72, 72, 72, 83, 83, 83, 83, 83, 90, 90, 71…
    ## $ Velocity       <dbl> 299850, 299740, 299900, 300070, 299930, 299850, 299950…
    ## $ VelocityVacuum <dbl> 299942, 299832, 299992, 300162, 300022, 299942, 300042…

As part of his study, Michelson assessed the various potential sources
of error, and provided his best-guess for the error in his
speed-of-light estimate. These values are provided in
`LIGHTSPEED_MICHELSON`—his nominal estimate—and
`LIGHTSPEED_PM`—plus/minus bounds on his estimate. Put differently,
Michelson believed the true value of the speed-of-light probably lay
between `LIGHTSPEED_MICHELSON - LIGHTSPEED_PM` and `LIGHTSPEED_MICHELSON
+ LIGHTSPEED_PM`.

Let’s introduce some terminology:\[2\]

  - **Error** is the difference between a true value and an estimate of
    that value; for instance `LIGHTSPEED_VACUUM - LIGHTSPEED_MICHELSON`.
  - **Uncertainty** is an analyst’s *assessment* of the error.

Since a “true” value is often not known in practice, one generally does
not know the error. The best they can do is quantify their degree of
uncertainty. We will learn some means of quantifying uncertainty in this
class, but for many real problems uncertainty includes some amount of
human judgment.\[2\]

# Question 3

**q3** Compare Michelson’s speed of light estimate against the modern
speed of light value. Is Michelson’s estimate of the error (his
uncertainty) greater or less than the true error?

``` r
## TODO: Compare Michelson's estimate and error against the true value
## Your code here!

#Use this as reference for small data sets: https://www.physics.upenn.edu/sites/default/files/Managing%20Errors%20and%20Uncertainty.pdf

error <- LIGHTSPEED_VACUUM - LIGHTSPEED_MICHELSON
relative_error <- (LIGHTSPEED_MICHELSON - LIGHTSPEED_VACUUM)/LIGHTSPEED_VACUUM*100
factor <- abs(error)/LIGHTSPEED_PM

df_error <- df_q2 %>% 
  arrange(desc(Distinctness)) %>% 
  group_by(Distinctness) %>% 
  summarise(
    count = n(),
    avg = mean(VelocityVacuum), 
    max = max(VelocityVacuum), 
    min = min(VelocityVacuum),
    mean_uncertainty = (max-min)/(2*sqrt(count)),
    rel_uncertainty = mean_uncertainty/avg,
    rel_err = abs((avg - LIGHTSPEED_VACUUM)/LIGHTSPEED_VACUUM)*100
    )
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
df_error %>%
  knitr::kable()
```

| Distinctness | count |      avg |    max |    min | mean\_uncertainty | rel\_uncertainty |  rel\_err |
| :----------- | ----: | -------: | -----: | -----: | ----------------: | ---------------: | --------: |
| 1            |    15 | 299900.0 | 299992 | 299712 |          36.14784 |        0.0001205 | 0.0358721 |
| 2            |    39 | 299950.5 | 300162 | 299742 |          33.62691 |        0.0001121 | 0.0527043 |
| 3            |    46 | 299953.7 | 300092 | 299812 |          20.64187 |        0.0000688 | 0.0537976 |

***Observations***:

  - **Michelson’s error is 2.9714118 times his uncertainty. His nominal
    estimate (2.9994410^{5} km/s) and error (+/- 51 km/s) did not
    capture the true speed of light in a vacuum (2.997924610^{5} kms/s).
    That being said, his relative error is so small - 0.050549 %\!**
  - **Even when broken down by Distinctness groups, the realtive error
    is still teeny-tiny\!**

# Question 4

**q4** You have access to a few other variables. Construct a few
visualizations of `VelocityVacuum` against these other factors. Are
there other patterns in the data that might help explain the difference
between Michelson’s estimate and `LIGHTSPEED_VACUUM`?

``` r
## TODO: Compare Michelson's estimate and error against the true value
## Your code here!

dat_text <- data.frame(
  label = paste("N = ",as.character(pull(df_error,2))),
  Distinctness   = pull(df_error,1),
  x     = c(299950, 299960, 299970),
  y     = c(0.001, 0.0012, 0.0014)
)


plot1 <- df_q2 %>% 
  ggplot() +
  geom_density(
    aes(VelocityVacuum, 
        color = Distinctness, 
        group = Distinctness)
    ) +
  facet_grid(Distinctness~.) +
  annotate("text", 
           x = 299750, 
           y = 0.004, 
           label = c("Vacuum \nSpeed of Light"), 
           size = 2.5) +
  geom_vline(xintercept = LIGHTSPEED_VACUUM) +
  geom_text(
    data= dat_text,
    mapping = aes(x = x, y = y, label = label) 
  )

plot3 <- df_q2 %>% 
  ggplot() +
  geom_density2d_filled(aes(x = Temp,y = Velocity), alpha = 0.5) +
  geom_density2d(aes(x = Temp,y = Velocity),color = "black") +
  facet_grid(Distinctness~.) +
  geom_hline(yintercept = LIGHTSPEED_VACUUM, color = "black", size = 2)

plot5 <- df_error %>% 
  ggplot() +
  geom_hline(yintercept = LIGHTSPEED_MICHELSON, size = 2) +
  geom_hline(yintercept = LIGHTSPEED_VACUUM) +
  geom_pointrange(
    aes(x = Distinctness, 
        y = avg, 
        ymin = min, 
        ymax = max, 
        color = Distinctness
        ), 
    width = 0.2) +
   geom_errorbar(
    aes(x = Distinctness, 
        y = avg, 
        ymin = avg-mean_uncertainty, 
        ymax = avg+mean_uncertainty, 
        color = Distinctness
        ), 
    width = 0.2) +
  geom_label(
    aes(x = Distinctness, 
        y = 299680, 
        label = paste("N = ", as.character(count))
        )
    ) +
  theme(
    legend.position = "NONE"
    ) +
   annotate("text", 
           x = 1.5, 
           y = 299830, 
           label = c("True Vacuum \nSpeed of Light"), 
           size = 3,
           color = "black") +
    annotate("text", 
           x = 1.5, 
           y = 299980, 
           label = c("Michelson's Nominal \nSpeed of Light"), 
           size = 3,
           color = "black") +
  annotate("rect", 
           xmin = 0.5, 
           xmax = 3.5, 
           ymin = LIGHTSPEED_MICHELSON-LIGHTSPEED_PM, 
           ymax = LIGHTSPEED_MICHELSON+LIGHTSPEED_PM,
           alpha = 0.2
           ) +
  coord_cartesian(xlim = c(1, 3.))
```

    ## Warning: Ignoring unknown parameters: width

``` r
plot1 + labs(title = "The different groups organized by distinctness follow a \nroughly normal distribution", y = "Vacuum Velocity of Light (km/s)")
```

![](c02-michelson-assignment_files/figure-gfm/first%20plot-1.png)<!-- -->

***Observations for first plot***

``` r
plot3 + labs(title = "Density of Velocity of Light (measured but not corrected) vs Temperature", subtitle = "True Vacuum Velocity of Light denoted with a white horizontal line", x = "Velocity (km/s)", y = "Temperature (degrees C)")
```

![](c02-michelson-assignment_files/figure-gfm/second%20plot-1.png)<!-- -->

``` r
plot5 + labs(title = "Vacuum Velocity Uncertainty by Distinctness", y = "Average Vacuum Velocity of Light (km/s)", subtitle = "Whisker represent calculated uncertainty in the mean per Distinctness")
```

![](c02-michelson-assignment_files/figure-gfm/third%20plot-1.png)<!-- -->

## Bibliography

  - \[1\] Michelson, [Experimental Determination of the Velocity of
    Light](https://play.google.com/books/reader?id=343nAAAAMAAJ&hl=en&pg=GBS.PA115)
    (1880)
  - \[2\] Henrion and Fischhoff, [Assessing Uncertainty in Physical
    Constants](https://www.cmu.edu/epp/people/faculty/research/Fischoff-Henrion-Assessing%20uncertainty%20in%20physical%20constants.pdf)
    (1986)
