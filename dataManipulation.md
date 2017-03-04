-   [Creating and subsetting dataframes](#creating-and-subsetting-dataframes)
    -   [Sort & Order](#sort-order)
    -   [Add rows and columns](#add-rows-and-columns)

Creating and subsetting dataframes
==================================

Sort & Order
------------

``` r
set.seed(0)

## create a dataframe with 5 rows, and 3 columns named var1,2,3.
df <- data.frame("var1" = sample(1:5), "var2" = sample(6:10), "var3" = sample(11:15))
df
```

    ##   var1 var2 var3
    ## 1    5    7   11
    ## 2    2    9   15
    ## 3    4    8   14
    ## 4    3   10   12
    ## 5    1    6   13

``` r
## randomly re-arrange the rows and set the 1st and the 3rd rows in the var2 column to NA
df <- df[sample(1:5),]; df$var2[c(1,3)] = NA
df
```

    ##   var1 var2 var3
    ## 4    3   NA   12
    ## 2    2    9   15
    ## 3    4   NA   14
    ## 5    1    6   13
    ## 1    5    7   11

``` r
## subsetting the 1st column
df[,1] 
```

    ## [1] 3 2 4 1 5

``` r
## you can subset the column with ts name
df[,"var1"]
```

    ## [1] 3 2 4 1 5

``` r
## subsetting the first 2 rows and the 2nd column
df[1:2,"var2"]
```

    ## [1] NA  9

``` r
## Subset using logical statements. get the columns with rows satisfy var1 <= 3 and var3 > 11
df[(df$var1 <= 3 & df$var3>11),]
```

    ##   var1 var2 var3
    ## 4    3   NA   12
    ## 2    2    9   15
    ## 5    1    6   13

``` r
## use which to return the indices that satisfy a certain condition
df[which(df$var2 > 8),]
```

    ##   var1 var2 var3
    ## 2    2    9   15

``` r
## sort variables in ascending order by default 
sort(df$var1)
```

    ## [1] 1 2 3 4 5

``` r
## for descending order
sort(df$var1, decreasing = TRUE)
```

    ## [1] 5 4 3 2 1

``` r
## incase of sorting data with missing variables, we can add them at the end
sort(df$var2, na.last = TRUE)
```

    ## [1]  6  7  9 NA NA

``` r
## re-ordering the rows of the dataframe wrt some variable.
df[order(df$var1),]
```

    ##   var1 var2 var3
    ## 5    1    6   13
    ## 2    2    9   15
    ## 4    3   NA   12
    ## 3    4   NA   14
    ## 1    5    7   11

``` r
## we can sort using 2 variables, so that if there is a tie using the 1st variable, order with the second one
df[order(df$var1,df$var3),]
```

    ##   var1 var2 var3
    ## 5    1    6   13
    ## 2    2    9   15
    ## 4    3   NA   12
    ## 3    4   NA   14
    ## 1    5    7   11

``` r
## ordering with plyr package
library(plyr)
arrange(df, var1) #increasing order
```

    ##   var1 var2 var3
    ## 1    1    6   13
    ## 2    2    9   15
    ## 3    3   NA   12
    ## 4    4   NA   14
    ## 5    5    7   11

``` r
arrange(df, desc(var1)) #decreasing order
```

    ##   var1 var2 var3
    ## 1    5    7   11
    ## 2    4   NA   14
    ## 3    3   NA   12
    ## 4    2    9   15
    ## 5    1    6   13

Add rows and columns
--------------------

``` r
## create the dataframe
df <- data.frame("var1" = sample(1:5), "var2" = sample(6:10), "var3" = sample(11:15))
df
```

    ##   var1 var2 var3
    ## 1    4    7   12
    ## 2    5   10   15
    ## 3    1    6   14
    ## 4    2    8   11
    ## 5    3    9   13

``` r
## add var4 contains some random values
df$var4 <- rnorm(5)
df
```

    ##   var1 var2 var3      var4
    ## 1    4    7   12 0.9438362
    ## 2    5   10   15 0.8212212
    ## 3    1    6   14 0.5939013
    ## 4    2    8   11 0.9189774
    ## 5    3    9   13 0.7821363

``` r
## another way to do this is using column bind function cbind. It pastes the new column into the right side of the df. If you switch orders the new column will be added into the left side.
df2 <- cbind(df,rnorm(5))
df2
```

    ##   var1 var2 var3      var4    rnorm(5)
    ## 1    4    7   12 0.9438362  0.07456498
    ## 2    5   10   15 0.8212212 -1.98935170
    ## 3    1    6   14 0.5939013  0.61982575
    ## 4    2    8   11 0.9189774 -0.05612874
    ## 5    3    9   13 0.7821363 -0.15579551

``` r
## add rows using row bind function rbind. Order matters just like cbind.
df3 <- rbind(df,rep(100,4))
df3
```

    ##   var1 var2 var3        var4
    ## 1    4    7   12   0.9438362
    ## 2    5   10   15   0.8212212
    ## 3    1    6   14   0.5939013
    ## 4    2    8   11   0.9189774
    ## 5    3    9   13   0.7821363
    ## 6  100  100  100 100.0000000
