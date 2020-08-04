Statistics: Moment Arithmetic
================
Zach del Rosario
2020-06-23

*Purpose*: In a future exercise, we will need to be able to do some
basic arithmetic with *moments* of a distribution. To prepare for this
later exercise, we’ll do some practice now.

*Reading*: (None, this is the reading)

*Topics*: Moments, moment arithmetic, standardization

# Moments

<!-- -------------------------------------------------- -->

Moments are a particular kind of statistic. There is a general,
mathematical definition of a
[moment](https://en.wikipedia.org/wiki/Moment_\(mathematics\)), but we
will only need to talk about two in this class.

We’ve already seen the *mean*; this is also called the expectation. For
a random variable \(X\), the expectation is defined in terms of its pdf
\(\rho(x)\) via

\[\mathbb{E}[X] = \int x \rho(x) dx.\]

We’ve also seen the standard deviation \(\sigma\). This is related to
the variance \(\sigma^2\), which is defined for a random variable \(X\)
in terms of the expectation

\[\mathbb{V}[X] = \mathbb{E}[(X - \mathbb{E}[X])^2].\]

For future exercises, we’ll need to learn how to do basic arithmetic
with these two moments.

# Moment Arithmetic

<!-- -------------------------------------------------- -->

We will need to be able to do some basic arithmetic with the mean and
variance. The following exercises will help you remember this basic
arithmetic.

### Expectation

<!-- ------------------------- -->

The expectation is *linear*, that is
\(\mathbb{E}[aX + c] = a \mathbb{E}[X] + c\). We can use this fact to
compute the mean of simply transformed random variables.

**q1** Compute the mean of \(2 Z + 3\), where \(Z\) is a standard
normal.

``` r
## TASK: Compute the mean of 2 Z + 3
E_q1 <- 3
```

Use the following test to check your answer.

``` r
## NOTE: No need to change this!
assertthat::assert_that(assertthat::are_equal(E_q1, 3))
```

    ## [1] TRUE

``` r
print("Nice!")
```

    ## [1] "Nice!"

### Variance

<!-- ------------------------- -->

Remember that variance is the square of standard deviation.

Variance satisfies the property
\[\mathbb{V}[aX + c] = a^2 \mathbb{V}[X].\]

**q2** Compute the variance of \(2 Z + 3\), where \(Z\) is a standard
normal.

``` r
## TASK: Compute the mean of 2 Z + 3
V_q2 <- 2^2
```

Use the following test to check your answer.

``` r
## NOTE: No need to change this!
assertthat::assert_that(assertthat::are_equal(V_q2, 4))
```

    ## [1] TRUE

``` r
print("Well done!")
```

    ## [1] "Well done!"

### Standardization

<!-- ------------------------- -->

The following two exercises illustrate two important transformations.

**q3** Compute the mean and variance of \((X - 1) / 2\), where
\(\mathbb{E}[X] = 1\) and \(\mathbb{V}[X] = 4\).

``` r
## TASK: Compute the mean and variance
E_q3 <- 0
V_q3 <- 1
```

Use the following test to check your answer.

``` r
## NOTE: No need to change this!
assertthat::assert_that(assertthat::are_equal(E_q3, 0))
```

    ## [1] TRUE

``` r
assertthat::assert_that(assertthat::are_equal(V_q3, 1))
```

    ## [1] TRUE

``` r
print("Well done!")
```

    ## [1] "Well done!"

This process of centering (setting the mean to zero) and scaling a
random variable is called *standardization*. For instance, if \(X\) is a
normal random variable, then \((X - \mu) / \sigma = Z\) is a standard
normal.

**q4** Compute the mean and variance of \(1 + 2 Z\), where \(Z\) is a
standard normal.

``` r
## TASK: Compute the mean and variance
E_q4 <- 1
V_q4 <- 2^2
```

Use the following test to check your answer.

``` r
## NOTE: No need to change this!
assertthat::assert_that(assertthat::are_equal(E_q4, 1))
```

    ## [1] TRUE

``` r
assertthat::assert_that(assertthat::are_equal(V_q4, 4))
```

    ## [1] TRUE

``` r
print("Excellent!")
```

    ## [1] "Excellent!"

This example illustrates that we can create a normal with desired mean
and standard deviation by transforming a standard normal
\(\mu + \sigma Z = X\).

### Standard Error

<!-- ------------------------- -->

\[\mathbb{V}[aX + bY] = a^2 \mathbb{V}[X] + b^2 \mathbb{V}[Y] + 2 \text{Cov}[X, Y],\]

where
\(\text{Cov}[X, Y] = \mathbb{E}[(X - \mathbb{E}[X])(Y - \mathbb{E}[Y])]\)
is the *covariance* between \(X\) and \(Y\). If \(X, Y\) are
independent, then the covariance between them is zero.

Using this expression, we can prove that the standard error of the
sample mean \(\overline{X}\) is \(\sigma / \sqrt{n}\).

**q5** (Bonus) Use the identity above to prove that
\(\mathbb{V}[\overline{X}] = \sigma^2 / n\), where
\(\overline{X} = \frac{1}{n}\sum_{i=1}^n X_i\),
\(\sigma^2 = \mathbb{V}[X]\), and the \(X_i\) are mutually independent.

<!-- include-exit-ticket -->

# Exit Ticket

<!-- -------------------------------------------------- -->

Once you have completed this exercise, make sure to fill out the **exit
ticket survey**, [linked
here](https://docs.google.com/forms/d/e/1FAIpQLSeuq2LFIwWcm05e8-JU84A3irdEL7JkXhMq5Xtoalib36LFHw/viewform?usp=pp_url&entry.693978880=e-stat05-moment-assignment.Rmd).
