Final Challenge
================
ingridmathilde
2020-08-07

  - [Grading Rubric](#grading-rubric)
      - [Individual](#individual)
      - [Team](#team)
      - [Due Date](#due-date)

*Purpose*: Explore Food Consumption vs CO2.

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

\#Setup & Data Wrangling

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.1     ✓ dplyr   1.0.0
    ## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ──────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(gapminder)
library(ggrepel)
```

``` r
food_consumption <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-02-18/food_consumption.csv')
```

    ## Parsed with column specification:
    ## cols(
    ##   country = col_character(),
    ##   food_category = col_character(),
    ##   consumption = col_double(),
    ##   co2_emmission = col_double()
    ## )

``` r
gapminder_pop_total <- read_csv("./data/population_total.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double(),
    ##   country = col_character()
    ## )

    ## See spec(...) for full column specifications.

``` r
gapminder_geo <- read_csv("./data/countries_gapminder.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   geo = col_character(),
    ##   name = col_character(),
    ##   four_regions = col_character(),
    ##   eight_regions = col_character(),
    ##   six_regions = col_character(),
    ##   members_oecd_g77 = col_character(),
    ##   Latitude = col_double(),
    ##   Longitude = col_double(),
    ##   `UN member since` = col_character(),
    ##   `World bank region` = col_character(),
    ##   `World bank, 4 income groups 2017` = col_character(),
    ##   `World bank, 3 income groups 2017` = col_logical()
    ## )

``` r
gapminder_pop_2014 <-
  gapminder_pop_total %>%
  select("country", "population" = "2014")

gapminder_geo_select <-
  gapminder_geo %>%
  select(
    "country" = "name", 
    "region" = "World bank region", 
    "income_grp" = "World bank, 4 income groups 2017"
  )

###All shamelessly copied from Angela's github###

## First, I'm fixing the USA
food_consumption_US_only <-
  food_consumption %>%
  filter(country == "USA") %>%
  select(-country) %>%
  mutate(country = "United States") %>%
  select(country, food_category, consumption, co2_emmission)

## Next, I'm fixing Hong Kong
food_consumption_HK_only <-
  food_consumption %>%
  filter(country == "Hong Kong SAR. China") %>%
  select(-country) %>%
  mutate(country = "Hong Kong, China") %>%
  select(country, food_category, consumption, co2_emmission)

## Next, I'm fixing Taiwan
food_consumption_TW_only <-
  food_consumption %>%
  filter(country == "Taiwan. ROC") %>%
  select(-country) %>%
  mutate(country = "Taiwan") %>%
  select(country, food_category, consumption, co2_emmission)

## Macedonia (Macedonia, FYR in gapminder)
food_consumption_Macedonia_only <-
  food_consumption %>%
  filter(country == "Macedonia") %>%
  select(-country) %>%
  mutate(country = "Macedonia, FYR") %>%
  select(country, food_category, consumption, co2_emmission)

## Congo, Rep.
food_consumption_CongoRep_only <-
  food_consumption %>%
  filter(country == "Congo") %>%
  select(-country) %>%
  mutate(country = "Congo, Rep.") %>%
  select(country, food_category, consumption, co2_emmission)


## Now, I'm binding them all together!
food_consumption_mod <-
  food_consumption %>%
  filter(
    country != "USA" &
    country != "Hong Kong SAR. China" &
    country != "Taiwan. ROC" &
    country != "Macedonia" &
    country != "Congo"
  ) %>%
  bind_rows(
    food_consumption_US_only,
    food_consumption_HK_only,
    food_consumption_TW_only,
    food_consumption_Macedonia_only,
    food_consumption_CongoRep_only
    )
```

``` r
df_food_all <-
  food_consumption_mod %>%
  left_join(gapminder_pop_2014, by = "country") %>%
  left_join(gapminder_geo_select, by = "country") %>%
  filter(food_category %in% c("Beef", "Fish", "Lamb & Goat", "Pork", "Poultry", "Eggs", "Milk - inc. cheese")) %>% 
  group_by(country) %>%
  mutate(
    co2_food_country = sum(co2_emmission), 
    food_consumption_country = sum(consumption)
  ) %>%
  ungroup() %>% 
  mutate(
    percent_diet = consumption/food_consumption_country,
    percent_co2 = co2_emmission/co2_food_country
  )

df_food_high_income <- df_food_all %>% 
  filter(income_grp == "High income")
```

``` r
group_width <- 50

high_income_groups <- df_food_high_income %>% 
  select(
    country,
    region,
    income_grp,
    population,
    food_consumption_country,
    co2_food_country
  ) %>% 
  distinct() %>% 
  mutate(
    food_consumption_group = cut_width(food_consumption_country, group_width)
  )

high_income_group_outliers <- high_income_groups %>% 
  group_by(food_consumption_group) %>%
  mutate(
    IQRange = IQR(co2_food_country, na.rm = TRUE),
    lower = quantile(co2_food_country, 0.25, na.rm = TRUE) - 1.5 * IQR(co2_food_country, na.rm = TRUE),
    upper = quantile(co2_food_country, 0.75, na.rm = TRUE) + 1.5 * IQR(co2_food_country, na.rm = TRUE)
  ) %>% 
  ungroup() %>% 
  filter(upper < co2_food_country | co2_food_country < lower)

high_income_beef <- df_food_high_income %>% 
  filter(food_category == "Beef")

high_income_groups %>% 
  ggplot(
    aes(
      x = food_consumption_country,
      y = co2_food_country,
      group = cut_width(food_consumption_country, group_width)
    )
  ) +
  geom_boxplot(
    outlier.color = "red" 
  ) +
  geom_label_repel(
    data = high_income_group_outliers,
    mapping = aes(
      label = country,
      color = income_grp
    )
  ) +
  labs(
    title = "CO2 emissions per capita as a function of Consumption per capita",
    x = "Total Food Consumption per capita\n (kg/person/year)",
    y = "Total Food-Related CO2 Emissions per capita\n (kg/per/year)",
    color = "Income Group"
  )
```

![](p01-food_emissions_files/figure-gfm/and%20some%20final%20plots-1.png)<!-- -->

``` r
high_income_groups %>% 
  ggplot(
    aes(
      x = food_consumption_country,
      y = co2_food_country,
    )
  ) +
  geom_point(
    aes(
      alpha = high_income_beef$percent_diet
    )
  ) +
  labs(
    title = "CO2 emissions per capita as a function of Consumption per capita",
    x = "Total Food Consumption per capita\n (kg/person/year)",
    y = "Total Food-Related CO2 Emissions per capita\n (kg/per/year)",
    color = "Income Group"
  )
```

![](p01-food_emissions_files/figure-gfm/and%20some%20final%20plots-2.png)<!-- -->
