Merge datasets
================
Nidhi Patel
11/27/2020

All the years are characters becasue the child\_nets dataset has ranges
of years. [Population protected by ITN or
IRS](https://apps.who.int/gho/data/node.main.MALITNIRS?lang=en) and
[Pregnant women attending ANC at least once and receiving IPTp3
Data](https://apps.who.int/gho/data/node.main.MALIPTP3?lang=en) do not
seem to have any data.

``` r
library(tidyverse)
<<<<<<< HEAD
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(httr)
library(rvest)
```

    ## Loading required package: xml2

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     pluck

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
=======
library(httr)
library(rvest)

>>>>>>> b18cb8a51a3ca23092b77fd050566638d2f1ce57
knitr::opts_chunk$set(
  fig.width = 6,
  fig.height = 6,
  out.width = "90%")

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_color_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

## [Mortality Rates Reported](https://apps.who.int/gho/athena/data/GHO/MALARIA001.html?profile=ztable&filter=COUNTRY:*)

``` r
url = "https://apps.who.int/gho/athena/data/GHO/MALARIA001.html?profile=ztable&filter=COUNTRY:*"

reported = read_html(url)
```

extract the table(s)

``` r
reported = reported %>% 
  html_nodes(css = "table") %>% 
  first() %>% 
  html_table() %>%
  as_tibble() %>% 
  janitor::clean_names() %>% 
  select(country, year, region, numeric_value) %>% 
  rename(reported_deaths = numeric_value) %>% 
  mutate(year = as.character(year))
```

## [Mortality Rates Estimated](https://apps.who.int/gho/athena/data/GHO/MALARIA003.html?profile=ztable&filter=COUNTRY:*)

``` r
url = "https://apps.who.int/gho/athena/data/GHO/MALARIA003.html?profile=ztable&filter=COUNTRY:*"

estimated = read_html(url)
```

extract the table

``` r
estimated = estimated %>% 
  html_nodes(css = "table") %>% 
  first() %>% 
  html_table() %>%
  as_tibble() %>% 
  janitor::clean_names() %>% 
  select(country, year, region, numeric_value, low_range, high_range) %>% 
  rename(estimated_deaths = numeric_value, 
         low_est_death = low_range,
         high_est_death = high_range) %>% 
  mutate(year = as.character(year))
```

## [Reported indigneous confirmed cases](https://apps.who.int/gho/athena/data/GHO/WHS3_48.html?profile=ztable&filter=COUNTRY:*)

``` r
url = "https://apps.who.int/gho/athena/data/GHO/WHS3_48.html?profile=ztable&filter=COUNTRY:*"

reported_cases = read_html(url)
```

``` r
reported_cases = reported_cases %>% 
  html_nodes(css = "table") %>% 
  first() %>% 
  html_table() %>%
  as_tibble() %>% 
  janitor::clean_names() %>% 
  select(country, year, region, numeric_value) %>% 
  rename(reported_cases = numeric_value) %>% 
  mutate(year = as.character(year))
```

## [Malaria estimated cases](https://apps.who.int/gho/athena/data/GHO/MALARIA002.html?profile=ztable&filter=COUNTRY:*)

``` r
url = "https://apps.who.int/gho/athena/data/GHO/MALARIA002.html?profile=ztable&filter=COUNTRY:*"

estimated_cases = read_html(url)
```

``` r
estimated_cases = estimated_cases %>% 
  html_nodes(css = "table") %>% 
  first() %>% 
  html_table() %>%
  as_tibble() %>% 
  janitor::clean_names() %>% 
  select(country, year, region, numeric_value, low_range, high_range) %>% 
  rename(estimated_cases = numeric_value, 
         low_est_cases = low_range,
         high_est_cases = high_range) %>% 
  mutate(year = as.character(year))
```

## [Malaria incidence per 1000 pop at risk](https://apps.who.int/gho/athena/data/GHO/SDGMALARIA.html?profile=ztable&filter=COUNTRY:*;REGION:*)

``` r
url = "https://apps.who.int/gho/athena/data/GHO/SDGMALARIA.html?profile=ztable&filter=COUNTRY:*;REGION:*"

incidence = read_html(url)
```

``` r
incidence = incidence %>% 
  html_nodes(css = "table") %>% 
  first() %>% 
  html_table() %>%
  as_tibble() %>% 
  janitor::clean_names() %>% 
  select(country, year, region, numeric_value) %>% 
  rename(incidence_per1000 = numeric_value) %>% 
  mutate(year = as.character(year))
```

## [Children aged \<5 years sleeping under insecticide treated nets](https://apps.who.int/gho/athena/data/GHO/MDG_0000000013.html?profile=ztable&filter=COUNTRY:*;REGION:*)

``` r
url = "https://apps.who.int/gho/athena/data/GHO/MDG_0000000013.html?profile=ztable&filter=COUNTRY:*;REGION:*"

child_nets = read_html(url)
```

``` r
child_nets = child_nets %>% 
  html_nodes(css = "table") %>% 
  first() %>% 
  html_table() %>%
  as_tibble() %>% 
  janitor::clean_names() %>% 
  select(-comment, -gho, -publishstate, -display_value) %>% 
  rename(estimated_nets = numeric_value, 
         low_est_nets = low_range,
         high_est_nets = high_range,
         residence_area = residenceareatype) 
```

## Merge tables

``` r
burden = 
  left_join(reported, child_nets, by = c("country" = "country", "year" = "year", "region" = "region"))

burden = 
  left_join(burden, estimated, by = c("country" = "country", "year" = "year", "region" = "region"))

burden = 
  left_join(burden, reported_cases, by = c("country" = "country", "year" = "year", "region" = "region"))

burden = 
  left_join(burden, estimated_cases, by = c("country" = "country", "year" = "year", "region" = "region"))

burden = 
  left_join(burden, incidence, by = c("country" = "country", "year" = "year", "region" = "region")) %>%
  relocate(country:reported_deaths, estimated_deaths:incidence_per1000, estimated_nets:high_est_nets) %>%
  write_csv("./data/malariawho.csv")

view(burden)
```
