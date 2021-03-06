Separate and Unite Columns
================
2020-06-05

*Purpose*: Data is easiest to use when it is *tidy*. In fact, the
tidyverse (including ggplot, dplyr, etc.) is specifically designed to
use tidy data. Last time we learned how to pivot data, but data can be
untidy in other ways. Pivoting helped us when data were locked up in the
*column headers*: This time, we’ll learn how to use *separate* and
*unite* to deal with *cell values* that are untidy.

*Reading*: [Separate and Unite
Columns](https://rstudio.cloud/learn/primers/4.2) *Topics*: Welcome,
separate(), unite(), Case study *Reading Time*: \~30 minutes

*Notes*: - I had trouble running the Case study in my browser. Note that
the `who` dataset is loaded by the `tidyverse`. You can run the Case
study locally if you need to\! - The case study uses `gather` instead of
`pivot_longer`; feel free to use `pivot_longer` in place.

``` r
library(tidyverse)
```

    ## ── Attaching packages ────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.1     ✓ dplyr   1.0.0
    ## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ───────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

The Case study was already a fair bit of work\! Let’s do some simple
review with `separate` and `unite`.

## Punnett Square

<!-- ------------------------- -->

Let’s make a [Punnett
square](https://en.wikipedia.org/wiki/Punnett_square) with `unite` and
some pivoting. You don’t need to remember any biology for this example:
Your task is to take `genes` and turn the data into `punnett`.

``` r
punnett <-
  tribble(
    ~parent1,   ~a,   ~A,
         "a", "aa", "aA",
         "A", "Aa", "AA"
  )
punnett
```

    ## # A tibble: 2 x 3
    ##   parent1 a     A    
    ##   <chr>   <chr> <chr>
    ## 1 a       aa    aA   
    ## 2 A       Aa    AA

``` r
genes <-
  expand_grid(
    parent1 = c("a", "A"),
    parent2 = c("a", "A")
  )
genes
```

    ## # A tibble: 4 x 2
    ##   parent1 parent2
    ##   <chr>   <chr>  
    ## 1 a       a      
    ## 2 a       A      
    ## 3 A       a      
    ## 4 A       A

**q1** Use a combination of `unite` and pivoting to turn `genes` into
the same dataframe as `punnett`.

``` r
df_q1 <-
  genes %>%
## TODO: pivot and unite here!
  pivot_wider(names_from = parent2, values_from = parent2) %>% 
  unite("a", c(parent1, a), sep = "", remove = FALSE) %>% 
  unite("A", c(parent1, A), sep = "", remove = FALSE) %>% 
  select(parent1, a, A)

df_q1
```

    ## # A tibble: 2 x 3
    ##   parent1 a     A    
    ##   <chr>   <chr> <chr>
    ## 1 a       aa    aA   
    ## 2 A       Aa    AA

Use the following test to check your answer:

``` r
## NOTE: No need to change this
assertthat::assert_that(
              all_equal(df_q1, punnett)
)
```

    ## [1] TRUE

``` r
print("Well done!")
```

    ## [1] "Well done!"

## Alloys, Revisited

<!-- ------------------------- -->

In the previous data exercise, we studied an alloys dataset:

``` r
## NOTE: No need to edit; execute
alloys_mod <- tribble(
  ~thick,  ~E00,  ~mu00,  ~E45,  ~mu45, ~rep,
   0.022, 10600,  0.321, 10700,  0.329,    1,
   0.022, 10600,  0.323, 10500,  0.331,    2,
   0.032, 10400,  0.329, 10400,  0.318,    1,
   0.032, 10300,  0.319, 10500,  0.326,    2
)
alloys_mod
```

    ## # A tibble: 4 x 6
    ##   thick   E00  mu00   E45  mu45   rep
    ##   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
    ## 1 0.022 10600 0.321 10700 0.329     1
    ## 2 0.022 10600 0.323 10500 0.331     2
    ## 3 0.032 10400 0.329 10400 0.318     1
    ## 4 0.032 10300 0.319 10500 0.326     2

This *slightly modified* version of the data no longer has a convenient
separator to help with pivoting. We’ll use a combination of pivoting and
separate to tidy these data.

**q2** Use a combination of `separate` and pivoting to tidy
`alloys_mod`.

``` r
df_q2 <-
  alloys_mod %>%
## TODO: pivot and separate here!
  pivot_longer(c(-thick, -rep), names_to = "propangle", values_to = "value") %>% 
  separate(col = propangle, into = c("prop", "angle"), sep = -2, convert = TRUE) %>% 
  pivot_wider(names_from = "prop", values_from = value)

df_q2
```

    ## # A tibble: 8 x 5
    ##   thick   rep angle     E    mu
    ##   <dbl> <dbl> <int> <dbl> <dbl>
    ## 1 0.022     1     0 10600 0.321
    ## 2 0.022     1    45 10700 0.329
    ## 3 0.022     2     0 10600 0.323
    ## 4 0.022     2    45 10500 0.331
    ## 5 0.032     1     0 10400 0.329
    ## 6 0.032     1    45 10400 0.318
    ## 7 0.032     2     0 10300 0.319
    ## 8 0.032     2    45 10500 0.326

Use the following tests to check your work:

``` r
## NOTE: No need to change this
assertthat::assert_that(
              (dim(df_q2)[1] == 8) & (dim(df_q2)[2] == 5)
)
```

    ## [1] TRUE

``` r
print("Nice!")
```

    ## [1] "Nice!"

<!-- include-exit-ticket -->

# Exit Ticket

<!-- -------------------------------------------------- -->

Once you have completed this exercise, make sure to fill out the **exit
ticket survey**, [linked
here](https://docs.google.com/forms/d/e/1FAIpQLSeuq2LFIwWcm05e8-JU84A3irdEL7JkXhMq5Xtoalib36LFHw/viewform?usp=pp_url&entry.693978880=e-data04-separate-unite-assignment.Rmd).
