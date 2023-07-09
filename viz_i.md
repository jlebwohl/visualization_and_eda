Visualization I
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
library(ggridges)
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

## Scatterplots

Create a scatterplot.

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_i_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

New approach, same plot.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_i_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Save and edit a plot object.

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax))

weather_plot + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_i_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## Advanced scatterplot

Start with the same one and make it fancy!

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_i_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

What about the `aes` placement?

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name)) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_i_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Let’s facet some things

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.2) +
  geom_smooth(se = FALSE, size = 2) + 
  facet_grid(. ~ name)
```

    ## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
    ## ℹ Please use `linewidth` instead.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_i_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Let’s combine some elements and try a new plot.

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp), alpha = 0.5) + 
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 3 rows containing missing values (`geom_point()`).

![](viz_i_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## Some small notes

How many geoms have to exist?

You can have whatever geoms you want.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (`stat_smooth()`).

![](viz_i_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

You can use a neat geom!

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_density2d() + 
  geom_point(alpha = 0.3)
```

    ## Warning: Removed 15 rows containing non-finite values (`stat_density2d()`).

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_i_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## Univariate plots

Histograms

``` r
weather_df %>% 
  ggplot(aes(x = tmin)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (`stat_bin()`).

![](viz_i_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Adding color is weird

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) + 
  geom_histogram() +
  facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (`stat_bin()`).

![](viz_i_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

Let’s try a new geometry

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_density(alpha = 0.4)
```

    ## Warning: Removed 15 rows containing non-finite values (`stat_density()`).

![](viz_i_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

What about box plots?

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin)) + 
  geom_boxplot() 
```

    ## Warning: Removed 15 rows containing non-finite values (`stat_boxplot()`).

![](viz_i_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

Trendy plots

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin, fill = name)) + 
  geom_violin(alpha = 0.5) + 
  stat_summary(fun = "mean")
```

    ## Warning: Removed 15 rows containing non-finite values (`stat_ydensity()`).

    ## Warning: Removed 15 rows containing non-finite values (`stat_summary()`).

    ## Warning: Removed 3 rows containing missing values (`geom_segment()`).

![](viz_i_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

Ridge plots

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.67

    ## Warning: Removed 15 rows containing non-finite values
    ## (`stat_density_ridges()`).

![](viz_i_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

## Save and Embed

Let’s save a scatterplot.

``` r
weather_plot = 
  weather_df %>% 
   ggplot(aes(x = tmin, y = tmax, color = name)) +
   geom_point(alpha = 0.5)

ggsave("./results/weather_plot.pdf", weather_plot, width = 8, height = 5)
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

What about embedding?

``` r
weather_plot
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_i_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

``` r
weather_plot
```

    ## Warning: Removed 15 rows containing missing values (`geom_point()`).

![](viz_i_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->
