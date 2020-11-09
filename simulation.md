Simulation
================

## Let’s simulate something

``` r
sim_mean_sd = function(samp_size, mu = 3, sigma = 4) { #default true mean and sd
  
  sim_data =
    tibble(
     x = rnorm(n = samp_size, mean = mu, sd = sigma)
    )
  
  sim_data %>%
    summarize(
      mean = mean(x),
      sd = sd(x)
  )
  
}
```

I can “simulate” by running this line.

``` r
sim_mean_sd(30)
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.58  4.51

## Let’s simulate a lot

Let’s start with a for loop.

``` r
output = vector("list", length = 100)

for (i in 1:100) {
  
  output[[i]] = sim_mean_sd(samp_size = 30)
  
}

bind_rows(output)
```

    ## # A tibble: 100 x 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ##  1  3.15  3.19
    ##  2  2.97  3.68
    ##  3  3.76  3.56
    ##  4  3.62  3.52
    ##  5  3.28  3.83
    ##  6  3.63  4.49
    ##  7  2.15  3.43
    ##  8  2.62  4.94
    ##  9  3.35  4.43
    ## 10  2.90  4.41
    ## # … with 90 more rows

Let’s use a loop function

``` r
sim_results =
  rerun(100, sim_mean_sd(samp_size = 30)) %>%
  bind_rows()
```

let’s look at results …

``` r
sim_results %>%
  ggplot(aes(x = mean)) + geom_density()
```

<img src="simulation_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

``` r
sim_results %>%
  summarize(
    avg_samp_mean = mean(mean),
    sd_samp_mean = sd(mean)
  )
```

    ## # A tibble: 1 x 2
    ##   avg_samp_mean sd_samp_mean
    ##           <dbl>        <dbl>
    ## 1          3.01        0.764

``` r
sim_results %>%
  ggplot(aes(x = sd)) + geom_density()
```

<img src="simulation_files/figure-gfm/unnamed-chunk-6-2.png" width="90%" />

## Let’s try other sample sizes.

``` r
n_list =
  list(
    "n = 30" = 30,
    "n = 60" = 60,
    "n = 120" = 120,
    "n = 240" = 240
  )

output = vector("list", length = 4)

output[[1]] = rerun(100, sim_mean_sd(samp_size = n_list[[1]])) %>% bind_rows()

for (i in 1:4) {
  
  output[[i]] = 
    rerun(100, sim_mean_sd(samp_size = n_list[[i]])) %>%
    bind_rows()
    
}
```

``` r
sim_results =
  tibble(
  sample_size = c(30, 60, 120, 240)
  ) %>% 
  mutate(
    output_lists = map(.x = sample_size, ~ rerun(1000, sim_mean_sd(.x))),
    estimate_df = map(output_lists, bind_rows)
  ) %>%
  select(-output_lists) %>%
  unnest(estimate_df) # expand each df
```

Do some data frame things.

``` r
sim_results %>%
  mutate(
    sample_size = str_c("n = ", sample_size),
    sample_size = fct_inorder(sample_size)
  ) %>% 
  ggplot(aes(x = sample_size, y = mean)) +
  geom_violin() # symmetric distribution
```

<img src="simulation_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

``` r
sim_results %>%
  group_by(sample_size) %>% 
  summarize(
    avg_samp_mean = mean(mean),
    sd_samp_mean = sd(mean)
  )
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## # A tibble: 4 x 3
    ##   sample_size avg_samp_mean sd_samp_mean
    ##         <dbl>         <dbl>        <dbl>
    ## 1          30          3.01        0.740
    ## 2          60          2.98        0.495
    ## 3         120          2.99        0.371
    ## 4         240          3.00        0.256
