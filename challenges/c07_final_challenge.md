Final Challenge
================
ingridmathilde
2020-07-22

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

    ## ── Attaching packages ───────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.1     ✓ dplyr   1.0.0
    ## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ──────────────────────────────────────────────────────── tidyverse_conflicts() ──
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
df_food_pop <-
  food_consumption_mod %>%
  left_join(gapminder_pop_2014, by = "country") %>%
  left_join(gapminder_geo_select, by = "country") %>%
  group_by(country) %>%
  mutate(
    co2_food_country = sum(co2_emmission), 
    food_consumption_country = sum(consumption)
  ) %>%
  ungroup()
```

\#Population Calculations

``` r
df_food_pop_totals <- df_food_pop %>% 
  mutate(
    total_consumption_food = consumption*population,
    total_co2_food = co2_emmission*population
  )

df_food_pop_totals
```

    ## # A tibble: 1,430 x 11
    ##    country food_category consumption co2_emmission population region income_grp
    ##    <chr>   <chr>               <dbl>         <dbl>      <dbl> <chr>  <chr>     
    ##  1 Argent… Pork                10.5          37.2    42600000 Latin… Upper mid…
    ##  2 Argent… Poultry             38.7          41.5    42600000 Latin… Upper mid…
    ##  3 Argent… Beef                55.5        1712      42600000 Latin… Upper mid…
    ##  4 Argent… Lamb & Goat          1.56         54.6    42600000 Latin… Upper mid…
    ##  5 Argent… Fish                 4.36          6.96   42600000 Latin… Upper mid…
    ##  6 Argent… Eggs                11.4          10.5    42600000 Latin… Upper mid…
    ##  7 Argent… Milk - inc. …      195.          278.     42600000 Latin… Upper mid…
    ##  8 Argent… Wheat and Wh…      103.           19.7    42600000 Latin… Upper mid…
    ##  9 Argent… Rice                 8.77         11.2    42600000 Latin… Upper mid…
    ## 10 Argent… Soybeans             0             0      42600000 Latin… Upper mid…
    ## # … with 1,420 more rows, and 4 more variables: co2_food_country <dbl>,
    ## #   food_consumption_country <dbl>, total_consumption_food <dbl>,
    ## #   total_co2_food <dbl>

***Question to answer here:* Who are the outliers for milk (including
cheese) in terms of total consumption?**

``` r
total_co2_outliers <- df_food_pop_totals %>% 
  group_by(food_category) %>% 
  mutate(
    IQRange = IQR(total_co2_food, na.rm = TRUE),
    lower = quantile(total_co2_food, 0.25, na.rm = TRUE) - 1.5 * IQR(total_co2_food, na.rm = TRUE),
    upper = quantile(total_co2_food, 0.75, na.rm = TRUE) + 1.5 * IQR(total_co2_food, na.rm = TRUE)
  ) %>% 
  ungroup() %>% 
  filter(upper < total_co2_food | total_co2_food < lower)

df_food_pop_totals %>%
  filter(food_category %in% c("Milk - inc. cheese")) %>% 
  ggplot(
    aes(
      x = total_co2_food,
      y = food_category
    )
  ) +
  geom_boxplot(outlier.color = "red") +
  geom_label_repel(
    data = filter(total_co2_outliers, food_category %in% c("Milk - inc. cheese")),
    mapping = aes(label = country)
  ) +
  labs(
    title = "Total Milk-Related CO2 emissions",
    x = "CO2 emissions per country",
    y = " "
  ) +
  scale_x_log10(labels = scales::label_number_si())
```

    ## Warning: Removed 7 rows containing non-finite values (stat_boxplot).

![](c07_final_challenge_files/figure-gfm/milk%20total%20consumption/co2-1.png)<!-- -->

The outliers in total co2 emissions for milk including cheese are:

  - India: largest population in the world and huge industry informed by
    [Hinduism](https://www.bloomberg.com/news/features/2018-01-23/india-s-sacred-cow-now-threatens-an-83-billion-dairy-industry).
  - United States: despite [recent
    declines](https://www.theguardian.com/food/2020/jan/06/us-dairy-industry-suffering-americans-consume-less-milk),
    the total consumption is still high
  - China: Despite [extremely high
    rates](https://www.foodsmatter.com/allergy_intolerance/cow's_milk_allergy_general/articles/dairy_intol_china.html#:~:text=Although%20there%20are%20no%20official,people%20to%20drink%20more%20milk.)
    of adult lactose intolerance, it is marketed as a [food of
    affluence](https://www.theguardian.com/environment/2019/mar/29/can-the-world-quench-chinas-bottomless-thirst-for-milk)
    and progress and is therefore consumed enormously.
  - Pakistan: Pakistan is the sixth most populous country in the world
    and around half of households have [at least one milk-producing
    cow](http://www.fao.org/3/i0588e/I0588E07.htm)
  - Brazil: huge population and [huge cow
    industry](https://www.cheesemarketnews.com/articlearch/passport/brazil.html)
  - Russia: Big population I guess?
  - Germany: 13th in [milk consumption per
    capita](https://en.wikipedia.org/wiki/List_of_countries_by_milk_consumption_per_capita)
  - France: huge cheese producer
  - United Kingdom:
  - Italy: 24th in [milk consumption per
    capita](https://en.wikipedia.org/wiki/List_of_countries_by_milk_consumption_per_capita)
  - Turkey: 34th in [milk consumption per
    capita](https://en.wikipedia.org/wiki/List_of_countries_by_milk_consumption_per_capita)
  - Mexico: 78th in [milk consumption per
    capita](https://en.wikipedia.org/wiki/List_of_countries_by_milk_consumption_per_capita)
  - Argentina: 33th in [milk consumption per
    capita](https://en.wikipedia.org/wiki/List_of_countries_by_milk_consumption_per_capita)
  - Japan: 109th in [milk consumption per
    capita](https://en.wikipedia.org/wiki/List_of_countries_by_milk_consumption_per_capita)
    and seems to be a recent addition to the
    [diet](https://lib.dr.iastate.edu/cgi/viewcontent.cgi?referer=https://www.google.com/&httpsredir=1&article=1420&context=card_workingpapers)
  - Grenada: ???

<!-- end list -->

``` r
total_co2_outliers %>%
  filter(food_category %in% c("Milk - inc. cheese")) %>% 
  ggplot(
    aes(
      x = co2_emmission,
      y = reorder(country, co2_emmission)
    )
  ) +
  geom_col(
    aes(
      fill = region
    )
  ) +
  labs(
    title = "CO2 emissions due to milk per capita for \noutliers of total CO2 emissions by country",
    x = "CO2 emissions due to milk (including cheese) per capita",
    y = "Country"
  )
```

![](c07_final_challenge_files/figure-gfm/outliers%20examinination-1.png)<!-- -->

India and China have a really low consumption per capita. Most of the
countries with higher per capita milk consumption are in Europe and
Central Asia.

Is there a relationship between milk consumption and population?

``` r
df_food_pop_totals %>% 
  filter(food_category == "Milk - inc. cheese") %>% 
  ggplot() +
  geom_point(
    aes(
      x = population,
      y = co2_emmission
    )
  ) +   
  labs(
    title = "Milk-Related CO2 vs Population",
    x = "Population",
    y = "CO2 emissions per capita"
  ) +
  scale_x_log10(labels = scales::label_number_si())
```

    ## Warning: Removed 7 rows containing missing values (geom_point).

![](c07_final_challenge_files/figure-gfm/milk%20consumption%20vs%20population-1.png)<!-- -->

Nope\! No relationship there really.

``` r
beef_v_milk <- df_food_pop_totals %>% 
  filter(food_category %in% c("Milk - inc. cheese", "Beef")) %>%
  select(
    country,
    region,
    income_grp,
    co2_food_country,
    food_category,
    co2_emmission,
    consumption
  ) %>% 
  pivot_wider(names_from = food_category, values_from = c(co2_emmission, consumption))
  
beef_v_milk
```

    ## # A tibble: 130 x 8
    ##    country region income_grp co2_food_country co2_emmission_B… `co2_emmission_…
    ##    <chr>   <chr>  <chr>                 <dbl>            <dbl>            <dbl>
    ##  1 Argent… Latin… Upper mid…            2172.            1712              278.
    ##  2 Austra… East … High inco…            1939.            1045.             334.
    ##  3 Albania Europ… Upper mid…            1778.             694.             433.
    ##  4 Iceland Europ… High inco…            1731.             412.             322.
    ##  5 New Ze… East … High inco…            1751.             694.             196.
    ##  6 Uruguay Latin… High inco…            1635.             898.             300.
    ##  7 Luxemb… Europ… High inco…            1598.             922.             364.
    ##  8 Brazil  Latin… Upper mid…            1617.            1211.             213.
    ##  9 Kazakh… Europ… Upper mid…            1575.             721.             410.
    ## 10 Sweden  Europ… High inco…            1527.             758.             486.
    ## # … with 120 more rows, and 2 more variables: consumption_Beef <dbl>,
    ## #   `consumption_Milk - inc. cheese` <dbl>

``` r
beef_v_milk %>% 
  ggplot() +
  geom_point(
    aes(
      x = co2_emmission_Beef,
      y = `co2_emmission_Milk - inc. cheese`,
      color = region
    )
  ) +   
  labs(
    title = "Milk vs Beef: CO2 Emissions",
    x = "Beef CO2 emissions per capita",
    y = "Milk CO2 emissions per capita"
  ) +
  scale_x_log10(labels = scales::label_number_si()) +
  scale_y_log10(labels = scales::label_number_si())
```

![](c07_final_challenge_files/figure-gfm/milk%20vs%20beef-1.png)<!-- -->

``` r
beef_v_milk %>% 
  ggplot() +
  geom_point(
    aes(
      x = consumption_Beef,
      y = `consumption_Milk - inc. cheese`,
      color = region
    )
  ) +   
  labs(
    title = "Milk vs Beef: Consumption",
    x = "Beef Consumptin per capita",
    y = "Milk Consumption per capita"
  ) +
  scale_x_log10(labels = scales::label_number_si()) +
  scale_y_log10(labels = scales::label_number_si())
```

![](c07_final_challenge_files/figure-gfm/milk%20vs%20beef-2.png)<!-- -->

``` r
beef_v_milk %>% 
  ggplot() +
  geom_point(
    aes(
      x = co2_emmission_Beef,
      y = `co2_emmission_Milk - inc. cheese`,
      color = income_grp
    )
  ) +   
  labs(
    title = "Milk vs Beef: CO2 Emissions",
    x = "Beef CO2 emissions per capita",
    y = "Milk CO2 emissions per capita"
  ) +
  scale_x_log10(labels = scales::label_number_si()) +
  scale_y_log10(labels = scales::label_number_si())
```

![](c07_final_challenge_files/figure-gfm/milk%20vs%20beef-3.png)<!-- -->

On a log-log scale, there is a linear relationship between beef and milk
consumption/co2-related emissions. In general, higher income countries
just consume more of both beef and milk - again, makes sense. Makes me
wonder - between these income groups, is there a higher consumption of
certain foods over others?

``` r
df_percents <- df_food_pop_totals %>% 
  mutate(
    percent_diet = consumption/food_consumption_country,
    percent_co2 = co2_emmission/co2_food_country
  ) %>% 
  select(
    country,
    region,
    income_grp,
    food_category,
    percent_diet,
    percent_co2
  ) 

df_percents
```

    ## # A tibble: 1,430 x 6
    ##    country  region        income_grp   food_category    percent_diet percent_co2
    ##    <chr>    <chr>         <chr>        <chr>                   <dbl>       <dbl>
    ##  1 Argenti… Latin Americ… Upper middl… Pork                  0.0245      0.0171 
    ##  2 Argenti… Latin Americ… Upper middl… Poultry               0.0900      0.0191 
    ##  3 Argenti… Latin Americ… Upper middl… Beef                  0.129       0.788  
    ##  4 Argenti… Latin Americ… Upper middl… Lamb & Goat           0.00363     0.0251 
    ##  5 Argenti… Latin Americ… Upper middl… Fish                  0.0102      0.00320
    ##  6 Argenti… Latin Americ… Upper middl… Eggs                  0.0265      0.00481
    ##  7 Argenti… Latin Americ… Upper middl… Milk - inc. che…      0.454       0.128  
    ##  8 Argenti… Latin Americ… Upper middl… Wheat and Wheat…      0.240       0.00905
    ##  9 Argenti… Latin Americ… Upper middl… Rice                  0.0204      0.00516
    ## 10 Argenti… Latin Americ… Upper middl… Soybeans              0           0      
    ## # … with 1,420 more rows

``` r
df_percents %>%
  ggplot() +
  geom_boxplot(
    aes(
      x = percent_diet,
      y = food_category
    )
  ) +
  facet_wrap(.~region)
```

![](c07_final_challenge_files/figure-gfm/most%20eaten%20foods-1.png)<!-- -->

``` r
df_percents %>%
  ggplot() +
  geom_boxplot(
    aes(
      x = percent_co2,
      y = food_category
    )
  ) +
  facet_wrap(.~region)
```

![](c07_final_challenge_files/figure-gfm/most%20eaten%20foods-2.png)<!-- -->

``` r
df_percents %>%
  ggplot() +
  geom_boxplot(
    aes(
      x = percent_diet,
      y = food_category
    )
  ) +
  facet_wrap(.~income_grp)
```

![](c07_final_challenge_files/figure-gfm/most%20eaten%20foods-3.png)<!-- -->

Top foods by region:

  - East Asia & Pacific: Rice
  - Europe & Central Asia: Milk & Cheese
  - Latin America & Caribbean: Milk & Cheese
  - Middle East & North Africa: Wheat
  - North America: Milk & Cheese
  - South Asia: Rice
  - Sub-Saharan Africa: Wheat
  - NA: Milk & Cheese

Top Food Emissions by region (I predict beef…):

  - East Asia & Pacific: Beef
  - Europe & Central Asia: Beef
  - Latin America & Caribbean: Beef
  - Middle East & North Africa: Beef
  - North America: Beef
  - South Asia: Rice (that must be a lot of rice…)
  - Sub-Saharan Africa: Beef
  - NA: Beef

That was sort of boring.

Top foods by income group:

  - High income: Milk & Cheese
  - Low income: Rice
  - Lower middle income: Wheat
  - Upper middle income: Milk & Cheese
  - NA: Milk & Cheese

Beef was the top when mapping co2 emissions vs income group.

``` r
total_co2_outliers <- df_food_pop_totals %>% 
  group_by(food_category) %>% 
  mutate(
    IQRange = IQR(co2_emmission, na.rm = TRUE),
    lower = quantile(co2_emmission, 0.25, na.rm = TRUE) - 1.5 * IQR(co2_emmission, na.rm = TRUE),
    upper = quantile(co2_emmission, 0.75, na.rm = TRUE) + 1.5 * IQR(co2_emmission, na.rm = TRUE)
  ) %>% 
  ungroup() %>% 
  filter(upper < co2_emmission)

df_food_pop_totals %>%
  filter(food_category %in% c("Milk - inc. cheese")) %>% 
  ggplot(
    aes(
      x = co2_emmission,
      y = food_category
    )
  ) +
  geom_boxplot(outlier.color = "red") +
  geom_label_repel(
    data = filter(total_co2_outliers, food_category %in% c("Milk - inc. cheese")),
    mapping = aes(label = country)
  ) +
  labs(
    title = "Consumption by food category"
  )
```

![](c07_final_challenge_files/figure-gfm/per%20person%20milk%20consumption/co2-1.png)<!-- -->

Finland is the only outlier here with well above the median milk
consumption per capita. But this is sort of boring because we all know
Northern Europe drinks a lot of milk…
