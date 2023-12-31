Visualization II
================

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.2     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.2     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.1     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(patchwork)
```

## Load the weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
     name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## using cached file: /Users/Jakey/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-07-09 16:21:13.688386 (8.499)

    ## file min/max dates: 1869-01-01 / 2023-07-31

    ## using cached file: /Users/Jakey/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2023-07-09 16:21:17.489598 (1.699)

    ## file min/max dates: 1965-01-01 / 2023-04-30

    ## using cached file: /Users/Jakey/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-07-09 16:21:18.908709 (0.987)

    ## file min/max dates: 1999-09-01 / 2023-06-30

``` r
weather_df
```

    ## # A tibble: 1,095 × 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # ℹ 1,085 more rows

## Remember this plot?

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5)
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

## Labels

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) + 
  labs(
  title = "Temperature plot",
  x = "Minimum daily temperature (C)",
  y = "Maximum daily temperature (C)",
  caption = "Data from the rnoaa package; temperatures in 2017"
)
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

## Scales

Start with the same plot; x and y scales

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) + 
  labs(
  title = "Temperature plot",
  x = "Minimum daily temperature (C)",
  y = "Maximum daily temperature (C)",
  caption = "Data from the rnoaa package; temperatures in 2017"
) +
  scale_x_continuous(
    breaks = c(-15, 0 ,15),
    labels = c("-15 C", "0", "15")
  ) +
  scale_y_continuous(
    position = "right"
  )
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Look at color scales.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) + 
  labs(
  title = "Temperature plot",
  x = "Minimum daily temperature (C)",
  y = "Maximum daily temperature (C)",
  caption = "Data from the rnoaa package; temperatures in 2017"
) +
  viridis::scale_color_viridis(
    name = "Location",
    discrete = TRUE
  ) 
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## Themes

Shift the legend.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) + 
  labs(
  title = "Temperature plot",
  x = "Minimum daily temperature (C)",
  y = "Maximum daily temperature (C)",
  caption = "Data from the rnoaa package; temperatures in 2017"
) +
  viridis::scale_color_viridis(
    name = "Location",
    discrete = TRUE
  )  +
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Change the overall theme.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) + 
  labs(
  title = "Temperature plot",
  x = "Minimum daily temperature (C)",
  y = "Maximum daily temperature (C)",
  caption = "Data from the rnoaa package; temperatures in 2017"
) +
  viridis::scale_color_viridis(
    name = "Location",
    discrete = TRUE
  )  +
  theme_minimal() + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

## Setting options

``` r
library(tidyverse)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = 0.6,
  out.width = "90%"
)

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_colour_discrete = scale_color_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

## Data args in `geom`

``` r
central_park = 
  weather_df %>% 
  filter(name == "CentralPark_NY")

waikiki = 
  weather_df %>% 
  filter(name == "Waikiki_HA")

ggplot(data = waikiki, aes(x = date, y = tmax, color = name)) + 
  geom_point() +
  geom_line(data = central_park)
```

    ## Warning: Removed 3 rows containing missing values (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## `patchwork`

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_density(alpha = 0.5) + 
  facet_grid(. ~ name)
```

    ## Warning: Removed 15 rows containing non-finite values (`stat_density()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

What if you want multipanel plots but can’t facet?

``` r
tmax_tmin_plot = 
  weather_df %>% 
    ggplot(aes(x = tmin, y = tmax, color = name)) +
    geom_point(alpha = 0.5) + 
    theme(legend.position = "none")

prcp_dens_plot = 
  weather_df %>% 
    filter(prcp > 0) %>% 
    ggplot(aes(x = prcp, color = name)) +
    geom_density(alpha = 0.5) +   
    theme(legend.position = "none")


tmax_date_plot = 
  weather_df %>% 
    ggplot(aes(x = date, y = tmax, color = name)) +
    geom_point(alpha = 0.5) + 
    geom_smooth(se = FALSE) +
    theme(legend.position = "none")

(tmax_tmin_plot + prcp_dens_plot) / tmax_date_plot
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 3 rows containing missing values (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

## Data manipulation

Control factors

``` r
weather_df %>% 
  mutate(
    name = factor(name),
    name = forcats::fct_relevel(name, c("Waikiki_HA"))
  ) %>% 
  ggplot(aes(x = name, y = tmax, fill = name)) +
  geom_violin(alpha = 0.5) + 
  theme(legend.position = "right") + 
  scale_fill_hue(h = c(0, 360))
```

    ## Warning: Removed 3 rows containing non-finite values (`stat_ydensity()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

Densities for tmin and tmax simultaneously

``` r
weather_df %>% 
  pivot_longer(
    tmax:tmin,
    names_to = "observation",
    values_to = "temperatures"
  ) %>%  
    ggplot(aes(x = temperatures, fill = observation)) +
  geom_density(alpha = 0.5) + 
  theme(legend.position = "right") + 
  scale_fill_hue(h = c(0, 360)) +
  facet_grid(. ~ name)
```

    ## Warning: Removed 18 rows containing non-finite values (`stat_density()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->
