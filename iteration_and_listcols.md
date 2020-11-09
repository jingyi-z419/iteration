Iteration and List Columns
================

## Lists

You can put anything in a list

``` r
l = list(
  vec_numeric = 5:8,
  vec_logical = c(TRUE, TRUE, FALSE, TRUE, FALSE, FALSE),
  mat = matrix(1:8, nrow = 2, ncol = 4),
  summary = summary(rnorm(100))
)
```

``` r
l
```

    ## $vec_numeric
    ## [1] 5 6 7 8
    ## 
    ## $vec_logical
    ## [1]  TRUE  TRUE FALSE  TRUE FALSE FALSE
    ## 
    ## $mat
    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    3    5    7
    ## [2,]    2    4    6    8
    ## 
    ## $summary
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## -2.7612 -0.8292 -0.1276 -0.1173  0.5589  2.1616

``` r
l$vec_numeric
```

    ## [1] 5 6 7 8

``` r
l[[1]]
```

    ## [1] 5 6 7 8

``` r
l[["vec_numeric"]][1:3]
```

    ## [1] 5 6 7

## `for` loop

Create a new list.

``` r
list_norm =
  list(
    a = rnorm(20, mean = 3, sd = 1),
    b = rnorm(30, mean = 0, sd = 5),
    c = rnorm(40, mean = 10, sd = .2),
    d = rnorm(20, mean = -3, sd = 1)
  )
```

``` r
list_norm[[1]]
```

    ##  [1] 1.1204486 3.8569123 2.8886777 1.2544767 4.4562984 4.0580352 3.8030521
    ##  [8] 2.4802486 2.0277564 2.1728917 1.8497799 2.8247540 3.9927017 2.7729360
    ## [15] 0.9873088 2.4623481 3.0538312 4.1794198 2.7102311 3.9916343

Pause and get my old function

``` r
mean_and_sd = function(x) {
  
  if (!is.numeric(x)) {
    stop("Input must be numeric")
  }
  
  if (length(x) < 3) {
    stop("Input must have at least three numbers")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)
  
  tibble(
  mean = mean_x,
  sd = sd_x
  
  )

}
```

I can apply that function to each list element

``` r
mean_and_sd(list_norm[[1]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.85  1.07

``` r
mean_and_sd(list_norm[[2]])
```

    ## # A tibble: 1 x 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.221  5.47

``` r
mean_and_sd(list_norm[[3]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  10.0 0.187

``` r
mean_and_sd(list_norm[[4]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -3.34 0.838

Let’s use a for loop:

``` r
output = vector("list", length = 4)

for (i in 1:4) {
  
  output[[i]] = mean_and_sd(list_norm[[i]])
  
}
```

## Let’s try map\!

``` r
output = map(list_norm, mean_and_sd)
```

What if you want a different function…?

``` r
output = map(list_norm, IQR) # IQR, median, mean
```

``` r
output = map_dbl(list_norm, median, .id = "input")
```

``` r
output = map_df(list_norm, mean_and_sd, .id = "input")
```

## List columns\!

``` r
listcol_df =
  tibble(
    name = c("a", "b", "c", "d"),
    samp = list_norm
  )
```

``` r
listcol_df %>% pull(name)
```

    ## [1] "a" "b" "c" "d"

``` r
listcol_df %>% pull(samp)
```

    ## $a
    ##  [1] 1.1204486 3.8569123 2.8886777 1.2544767 4.4562984 4.0580352 3.8030521
    ##  [8] 2.4802486 2.0277564 2.1728917 1.8497799 2.8247540 3.9927017 2.7729360
    ## [15] 0.9873088 2.4623481 3.0538312 4.1794198 2.7102311 3.9916343
    ## 
    ## $b
    ##  [1]  -0.6857206   0.7458893   6.0674792   1.2023987  -1.4877408  -5.8604645
    ##  [7]  -5.0359634   2.2097802   7.7008113  -3.0900583  -5.1747766   3.6205498
    ## [13] -10.2143850   9.9273983  -4.6692247   1.6452935  -2.4056431  -4.7387163
    ## [19]  -9.6667345  -1.2127067   9.9360586   3.1193865  -4.9856804   0.1967381
    ## [25]   6.9949523   1.4055289   4.5806846   5.2516935  -6.2480293  -5.7501752
    ## 
    ## $c
    ##  [1] 10.255516 10.197411 10.256540  9.891617 10.371385 10.168639  9.951674
    ##  [8] 10.019328 10.131549  9.826774  9.761576 10.184451  9.951016 10.362187
    ## [15]  9.881450 10.274868  9.919132  9.939404  9.954900 10.033396  9.846440
    ## [22] 10.336633  9.832058  9.981751 10.202779 10.205216 10.075079 10.257818
    ## [29]  9.725239 10.252213 10.127984  9.904292 10.006059  9.678529 10.236500
    ## [36]  9.938120  9.950829  9.997955  9.828919  9.844718
    ## 
    ## $d
    ##  [1] -4.612826 -4.246171 -4.010682 -2.090418 -3.452288 -3.612483 -3.427650
    ##  [8] -3.431210 -2.846710 -1.755469 -4.999921 -3.416854 -3.026118 -2.570333
    ## [15] -3.129788 -3.641991 -2.278568 -3.741899 -3.983649 -2.474168

``` r
listcol_df %>%
  filter(name == "a")
```

    ## # A tibble: 1 x 2
    ##   name  samp        
    ##   <chr> <named list>
    ## 1 a     <dbl [20]>

Let’s try some operations.

``` r
mean_and_sd(listcol_df$samp[[1]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.85  1.07

Can I just … map?

``` r
map(listcol_df$samp, mean_and_sd)
```

    ## $a
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.85  1.07
    ## 
    ## $b
    ## # A tibble: 1 x 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.221  5.47
    ## 
    ## $c
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  10.0 0.187
    ## 
    ## $d
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -3.34 0.838

So … can i add a list column?

``` r
listcol_df =  
  listcol_df %>%
  mutate(
    summary = map(samp, mean_and_sd),
    medians = map_dbl(samp, median)
    )
```
