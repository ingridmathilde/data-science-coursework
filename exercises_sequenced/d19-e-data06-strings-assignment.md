Data: Working with Strings
================
Zachary del Rosario
2020-06-24

*Purpose*: Strings show up in data science all the time. Even when all
our variables are numeric, our *column names* are generally strings. To
strengthen our ability to work with strings, we’ll learn how to use
*regular expressions* and apply them to wrangling and tidying data.

*Reading*: [RegexOne](https://regexone.com/); All lessons in the
Interactive Tutorial, Additional Practice Problems are optional

*Topics*: Regular expressions, `stringr` package functions, pivoting

*Note*: The [stringr
cheatsheet](https://github.com/rstudio/cheatsheets/raw/master/strings.pdf)
is a helpful reference for this exercise\!

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.1     ✓ dplyr   1.0.0
    ## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ──────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

# Intro to Stringr

<!-- -------------------------------------------------- -->

Within the Tidyverse, the package `stringr` contains a large number of
functions for helping us with strings.

### Match and Detect

<!-- ------------------------- -->

Two important ones are `str_match()` and `str_detect()`. The function
`str_match()` returns a match (a string):

``` r
## NOTE: No need to change this!
str_match(
  string = c("abc", "xyz", "123"),
  pattern = "\\D{3}"
)
```

    ##      [,1] 
    ## [1,] "abc"
    ## [2,] "xyz"
    ## [3,] NA

Whereas `str_detect()` returns a boolean

``` r
## NOTE: No need to change this!
str_detect(
  string = c("abc", "xyz", "123"),
  pattern = "\\D{3}"
)
```

    ## [1]  TRUE  TRUE FALSE

*Note*: In `R`, we can’t type a single `\` to write special characters.
Instead, we have to double-up the `\\`. For instance, if we wanted to
use the digit character, we would type `\\d`.

**q1** Filter the `mpg` dataset to include only those observations with
a `trans` of the form “l3, l4, l5, l6”.

``` r
## TASK: Mutate `trans` to extract
df_q1 <-
  mpg %>% 
  filter(str_detect(trans,"l\\d"))
df_q1
```

    ## # A tibble: 130 x 11
    ##    manufacturer model     displ  year   cyl trans  drv     cty   hwy fl    class
    ##    <chr>        <chr>     <dbl> <int> <int> <chr>  <chr> <int> <int> <chr> <chr>
    ##  1 audi         a4          1.8  1999     4 auto(… f        18    29 p     comp…
    ##  2 audi         a4          2.8  1999     6 auto(… f        16    26 p     comp…
    ##  3 audi         a4 quatt…   1.8  1999     4 auto(… 4        16    25 p     comp…
    ##  4 audi         a4 quatt…   2.8  1999     6 auto(… 4        15    25 p     comp…
    ##  5 audi         a6 quatt…   2.8  1999     6 auto(… 4        15    24 p     mids…
    ##  6 chevrolet    c1500 su…   5.3  2008     8 auto(… r        14    20 r     suv  
    ##  7 chevrolet    c1500 su…   5.3  2008     8 auto(… r        11    15 e     suv  
    ##  8 chevrolet    c1500 su…   5.3  2008     8 auto(… r        14    20 r     suv  
    ##  9 chevrolet    c1500 su…   5.7  1999     8 auto(… r        13    17 r     suv  
    ## 10 chevrolet    c1500 su…   6    2008     8 auto(… r        12    17 r     suv  
    ## # … with 120 more rows

Use the following test to check your work.

``` r
## NOTE: No need to change this!
assertthat::assert_that(
              all(
                df_q1 %>%
                pull(trans) %>%
                str_detect(., "l\\d")
              )
)
```

    ## [1] TRUE

``` r
print("Great job!")
```

    ## [1] "Great job!"

### Extract and Remove

<!-- ------------------------- -->

Two additional stringr functions are `str_extract()` and `str_remove()`.
Unlike `str_match()` which returns the entire string, the function
`str_extract()` returns *only* the first occurrence of the matched
pattern:

``` r
## NOTE: No need to change this!
str_extract(
  string = c("650-123-4567", "(814) 123 4567"),
  pattern = "\\d{3}"
)
```

    ## [1] "650" "814"

The function `str_remove()` *removes* the first occurence of the matched
pattern

``` r
## NOTE: No need to change this!
str_remove(
  string = c("650-123-4567", "(814) 123 4567"),
  pattern = "\\D"
)
```

    ## [1] "650123-4567"   "814) 123 4567"

while `str_remove_all()` removes *all* occurences of the matched pattern

``` r
## NOTE: No need to change this!
str_remove_all(
  string = c("650-123-4567", "(814) 123 4567"),
  pattern = "\\D"
)
```

    ## [1] "6501234567" "8141234567"

All of these functions are useful for processing string data.

**q2** Note that observations on the `trans` variable in the `mpg`
dataset are of the form `type(code)`. Use regular expressions to extract
the `type` and `code` into their own variables.

``` r
## TASK: Extract the "type" and "code" of trans
df_q2 <-
  mpg %>%
  mutate(
    trans_type = str_remove(trans, "\\W.*\\W"), 
    trans_code = str_extract(trans, "l\\d")
  )
df_q2 %>% glimpse
```

    ## Rows: 234
    ## Columns: 13
    ## $ manufacturer <chr> "audi", "audi", "audi", "audi", "audi", "audi", "audi", …
    ## $ model        <chr> "a4", "a4", "a4", "a4", "a4", "a4", "a4", "a4 quattro", …
    ## $ displ        <dbl> 1.8, 1.8, 2.0, 2.0, 2.8, 2.8, 3.1, 1.8, 1.8, 2.0, 2.0, 2…
    ## $ year         <int> 1999, 1999, 2008, 2008, 1999, 1999, 2008, 1999, 1999, 20…
    ## $ cyl          <int> 4, 4, 4, 4, 6, 6, 6, 4, 4, 4, 4, 6, 6, 6, 6, 6, 6, 8, 8,…
    ## $ trans        <chr> "auto(l5)", "manual(m5)", "manual(m6)", "auto(av)", "aut…
    ## $ drv          <chr> "f", "f", "f", "f", "f", "f", "f", "4", "4", "4", "4", "…
    ## $ cty          <int> 18, 21, 20, 21, 16, 18, 18, 18, 16, 20, 19, 15, 17, 17, …
    ## $ hwy          <int> 29, 29, 31, 30, 26, 26, 27, 26, 25, 28, 27, 25, 25, 25, …
    ## $ fl           <chr> "p", "p", "p", "p", "p", "p", "p", "p", "p", "p", "p", "…
    ## $ class        <chr> "compact", "compact", "compact", "compact", "compact", "…
    ## $ trans_type   <chr> "auto", "manual", "manual", "auto", "auto", "manual", "a…
    ## $ trans_code   <chr> "l5", NA, NA, NA, "l5", NA, NA, NA, "l5", NA, NA, "l5", …

Use the following test to check your work.

``` r
## NOTE: No need to change this!
# assertthat::assert_that(
#               all(
#                 df_q2 %>%
#                 mutate(
#                   flag = !str_detect(trans_type, "\\(|\\)")
#                        & !str_detect(trans_code, "\\(|\\)")
#                 ) %>%
#                 pull(flag)
#               )
# )
# print("Nice!")
```

# Regex in Other Functions

<!-- -------------------------------------------------- -->

Other functions like `pivot_longer` and `pivot_wider` also take regex
patterns. We can use these to help solve data tidying problems.

Let’s return to the alloy data from `e-data03-pivot-basics`; the version
of the data below do not have the convenient `_` separators in the
column names.

``` r
## NOTE: No need to edit; execute
alloys <- tribble(
  ~thick,  ~E00,  ~mu00,  ~E45,  ~mu45, ~rep,
   0.022, 10600,  0.321, 10700,  0.329,    1,
   0.022, 10600,  0.323, 10500,  0.331,    2,
   0.032, 10400,  0.329, 10400,  0.318,    1,
   0.032, 10300,  0.319, 10500,  0.326,    2
)
alloys
```

    ## # A tibble: 4 x 6
    ##   thick   E00  mu00   E45  mu45   rep
    ##   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
    ## 1 0.022 10600 0.321 10700 0.329     1
    ## 2 0.022 10600 0.323 10500 0.331     2
    ## 3 0.032 10400 0.329 10400 0.318     1
    ## 4 0.032 10300 0.319 10500 0.326     2

As described in the RegexOne tutorial, you can use capture groups in
parentheses `(...)` to define different groups in your regex pattern.
These can be used along with the `pivot_` functions, for instance when
you want to break apart column names into multiple groups.

**q3** Use your knowledge of regular expressions along with the
`names_pattern` argument to successfully tidy the `alloys` data.

``` r
## TASK: Tidy `alloys`
df_q3 <- alloys %>% 
  pivot_longer(
    cols = c(-thick, -rep), 
    names_to = c("property","angle"), 
    names_pattern = "(\\w)(\\d+)", 
    values_to = "value")
df_q3
```

    ## # A tibble: 16 x 5
    ##    thick   rep property angle     value
    ##    <dbl> <dbl> <chr>    <chr>     <dbl>
    ##  1 0.022     1 E        00    10600    
    ##  2 0.022     1 u        00        0.321
    ##  3 0.022     1 E        45    10700    
    ##  4 0.022     1 u        45        0.329
    ##  5 0.022     2 E        00    10600    
    ##  6 0.022     2 u        00        0.323
    ##  7 0.022     2 E        45    10500    
    ##  8 0.022     2 u        45        0.331
    ##  9 0.032     1 E        00    10400    
    ## 10 0.032     1 u        00        0.329
    ## 11 0.032     1 E        45    10400    
    ## 12 0.032     1 u        45        0.318
    ## 13 0.032     2 E        00    10300    
    ## 14 0.032     2 u        00        0.319
    ## 15 0.032     2 E        45    10500    
    ## 16 0.032     2 u        45        0.326

Use the following test to check your work.

``` r
## NOTE: No need to change this!
assertthat::assert_that(dim(df_q3)[1] == 16)
```

    ## [1] TRUE

``` r
assertthat::assert_that(dim(df_q3)[2] == 5)
```

    ## [1] TRUE

``` r
print("Well done!")
```

    ## [1] "Well done!"

<!-- include-exit-ticket -->

# Exit Ticket

<!-- -------------------------------------------------- -->

Once you have completed this exercise, make sure to fill out the **exit
ticket survey**, [linked
here](https://docs.google.com/forms/d/e/1FAIpQLSeuq2LFIwWcm05e8-JU84A3irdEL7JkXhMq5Xtoalib36LFHw/viewform?usp=pp_url&entry.693978880=e-data06-strings-assignment.Rmd).

# Notes

<!-- -------------------------------------------------- -->

\[1\] Here’s a helpful mnemonic for remembering the meaning of the `^`
and `$` characters: “When you start with power `^`, you end with money
`$`.”
