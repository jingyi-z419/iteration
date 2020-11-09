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
    ## 1  2.41  3.58

## Let’s simulate a lot
