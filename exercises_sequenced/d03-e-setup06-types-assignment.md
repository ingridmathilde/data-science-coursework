Types
================
Zachary del Rosario
2020-06-26

*Purpose*: Vectors can hold data of only one *type*. While this isn’t a
course on computer science, there are some type “gotchas” to look out
for when doing data science. This exercise will help us get ahead of
those issues.

*Reading*: [Types](https://rstudio.cloud/learn/primers/1.2)

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

# Objects vs Strings

<!-- -------------------------------------------------- -->

**q1** Describe what is wrong with the code below.

``` r
## TASK: Describe what went wrong here
## Set our airport
airport <- "BOS"

## Check our airport value
#airport == ATL
```

**Observations**:

The airport data type is character but is being compared against an
object. To rectify this, put ALT in quotations (so “ALT”).

# Casting

<!-- -------------------------------------------------- -->

Sometimes our data will not be in the form we want; in this case we may
need to *cast* the data to another format.

  - `as.integer(x)` converts to integer
  - `as.numeric(x)` converts to real (floating point)
  - `as.character(x)` converts to character (string)
  - `as.logical(x)` converts to logical (boolean)

**q2** Cast the following vector `v_string` to integers.

``` r
v_string <- c("00", "45", "90")
v_integer <- as.integer(v_string)
print(v_integer)
```

    ## [1]  0 45 90
