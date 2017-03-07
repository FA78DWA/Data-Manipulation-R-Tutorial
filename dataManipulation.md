-   [Creating and subsetting dataframes](#creating-and-subsetting-dataframes)
    -   [Sort & Order](#sort-order)
    -   [Add rows and columns](#add-rows-and-columns)
-   [Summarizing Data](#summarizing-data)
    -   [tables](#tables)
    -   [Check for missing values](#check-for-missing-values)
    -   [Sum across rows and columns](#sum-across-rows-and-columns)
    -   [Find specific values in your data](#find-specific-values-in-your-data)
    -   [Cross tabs and Flat tables](#cross-tabs-and-flat-tables)
-   [Size of the dataset](#size-of-the-dataset)
-   [Create new variables](#create-new-variables)
    -   [`ifelse`](#ifelse)
    -   [Categorical Variables](#categorical-variables)
    -   [Factor Variables](#factor-variables)
    -   [Using `mutate` from `plyr` package](#using-mutate-from-plyr-package)
-   [Resahping Data](#resahping-data)
    -   [`melt` the dataset](#melt-the-dataset)
    -   [casting dataframes](#casting-dataframes)
-   [`dplyr` Package](#dplyr-package)

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

Another way to do this is using column bind function cbind. It pastes the new column into the right side of the df. If you switch orders the new column will be added into the left side.

``` r
df2 <- cbind(df,rnorm(5))
df2
```

    ##   var1 var2 var3      var4   rnorm(5)
    ## 1    4    7   12 0.9438362 -1.4707524
    ## 2    5   10   15 0.8212212 -0.4781501
    ## 3    1    6   14 0.5939013  0.4179416
    ## 4    2    8   11 0.9189774  1.3586796
    ## 5    3    9   13 0.7821363 -0.1027877

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

Summarizing Data
================

``` r
restaurants <- read.csv("Restaurants.csv")

## Show the first 3 rows. Without specifying n, it will give the top 6 rows
head(restaurants, n=3)
```

    ##    name zipCode neighborhood councilDistrict policeDistrict
    ## 1   410   21206    Frankford               2   NORTHEASTERN
    ## 2  1919   21231  Fells Point               1   SOUTHEASTERN
    ## 3 SAUTE   21224       Canton               1   SOUTHEASTERN
    ##                          Location.1
    ## 1 4509 BELAIR ROAD\nBaltimore, MD\n
    ## 2    1919 FLEET ST\nBaltimore, MD\n
    ## 3   2844 HUDSON ST\nBaltimore, MD\n

``` r
## show the last 3 rows. Without specifying n, it will give the last 6 rows
tail(restaurants, n=3)
```

    ##              name zipCode  neighborhood councilDistrict policeDistrict
    ## 1325 ZINK'S CAFÂ   21213 Belair-Edison              13   NORTHEASTERN
    ## 1326 ZISSIMOS BAR   21211       Hampden               7       NORTHERN
    ## 1327       ZORBAS   21224     Greektown               2   SOUTHEASTERN
    ##                              Location.1
    ## 1325 3300 LAWNVIEW AVE\nBaltimore, MD\n
    ## 1326      1023 36TH ST\nBaltimore, MD\n
    ## 1327  4710 EASTERN Ave\nBaltimore, MD\n

``` r
## make summary. It gives histogram for string columns and quantiles for numeric columns
summary(restaurants)
```

    ##                            name         zipCode             neighborhood
    ##  MCDONALD'S                  :   8   Min.   :-21226   Downtown    :128  
    ##  POPEYES FAMOUS FRIED CHICKEN:   7   1st Qu.: 21202   Fells Point : 91  
    ##  SUBWAY                      :   6   Median : 21218   Inner Harbor: 89  
    ##  KENTUCKY FRIED CHICKEN      :   5   Mean   : 21185   Canton      : 81  
    ##  BURGER KING                 :   4   3rd Qu.: 21226   Federal Hill: 42  
    ##  DUNKIN DONUTS               :   4   Max.   : 21287   Mount Vernon: 33  
    ##  (Other)                     :1293                    (Other)     :863  
    ##  councilDistrict       policeDistrict
    ##  Min.   : 1.000   SOUTHEASTERN:385   
    ##  1st Qu.: 2.000   CENTRAL     :288   
    ##  Median : 9.000   SOUTHERN    :213   
    ##  Mean   : 7.191   NORTHERN    :157   
    ##  3rd Qu.:11.000   NORTHEASTERN: 72   
    ##  Max.   :14.000   EASTERN     : 67   
    ##                   (Other)     :145   
    ##                           Location.1    
    ##  1101 RUSSELL ST\nBaltimore, MD\n:   9  
    ##  201 PRATT ST\nBaltimore, MD\n   :   8  
    ##  2400 BOSTON ST\nBaltimore, MD\n :   8  
    ##  300 LIGHT ST\nBaltimore, MD\n   :   5  
    ##  300 CHARLES ST\nBaltimore, MD\n :   4  
    ##  301 LIGHT ST\nBaltimore, MD\n   :   4  
    ##  (Other)                         :1289

``` r
## more in depth details
str(restaurants)
```

    ## 'data.frame':    1327 obs. of  6 variables:
    ##  $ name           : Factor w/ 1277 levels "#1 CHINESE KITCHEN",..: 9 3 992 1 2 4 5 6 7 8 ...
    ##  $ zipCode        : int  21206 21231 21224 21211 21223 21218 21205 21211 21205 21231 ...
    ##  $ neighborhood   : Factor w/ 173 levels "Abell","Arlington",..: 53 52 18 66 104 33 98 133 98 157 ...
    ##  $ councilDistrict: int  2 1 1 14 9 14 13 7 13 1 ...
    ##  $ policeDistrict : Factor w/ 9 levels "CENTRAL","EASTERN",..: 3 6 6 4 8 3 6 4 6 6 ...
    ##  $ Location.1     : Factor w/ 1210 levels "1 BIDDLE ST\nBaltimore, MD\n",..: 835 334 554 755 492 537 505 530 507 569 ...

``` r
## Quantiles
quantile(restaurants$councilDistrict, na.rm = T)
```

    ##   0%  25%  50%  75% 100% 
    ##    1    2    9   11   14

``` r
## passing probabilities to quantile
quantile(restaurants$councilDistrict, na.rm = T, probs = c(0.5,0.7,0.9))
```

    ## 50% 70% 90% 
    ##   9  11  12

tables
------

`table()` makes a histogram for the input observations. `useNA = "ifany"` will add NA column in the output contains the number of missing values. `table()` can also take 2 variables to construct a 2D histogram.

``` r
## counting the number of occurence for eah zipcode
table(restaurants$zipCode, useNA = "ifany")
```

    ## 
    ## -21226  21201  21202  21205  21206  21207  21208  21209  21210  21211 
    ##      1    136    201     27     30      4      1      8     23     41 
    ##  21212  21213  21214  21215  21216  21217  21218  21220  21222  21223 
    ##     28     31     17     54     10     32     69      1      7     56 
    ##  21224  21225  21226  21227  21229  21230  21231  21234  21237  21239 
    ##    199     19     18      4     13    156    127      7      1      3 
    ##  21251  21287 
    ##      2      1

``` r
## 2D histogram
table(restaurants$councilDistrict, restaurants$zipCode)
```

    ##     
    ##      -21226 21201 21202 21205 21206 21207 21208 21209 21210 21211 21212
    ##   1       0     0    37     0     0     0     0     0     0     0     0
    ##   2       0     0     0     3    27     0     0     0     0     0     0
    ##   3       0     0     0     0     0     0     0     0     0     0     0
    ##   4       0     0     0     0     0     0     0     0     0     0    27
    ##   5       0     0     0     0     0     3     0     6     0     0     0
    ##   6       0     0     0     0     0     0     0     1    19     0     0
    ##   7       0     0     0     0     0     0     0     1     0    27     0
    ##   8       0     0     0     0     0     1     0     0     0     0     0
    ##   9       0     1     0     0     0     0     0     0     0     0     0
    ##   10      1     0     1     0     0     0     0     0     0     0     0
    ##   11      0   115   139     0     0     0     1     0     0     0     1
    ##   12      0    20    24     4     0     0     0     0     0     0     0
    ##   13      0     0     0    20     3     0     0     0     0     0     0
    ##   14      0     0     0     0     0     0     0     0     4    14     0
    ##     
    ##      21213 21214 21215 21216 21217 21218 21220 21222 21223 21224 21225
    ##   1      2     0     0     0     0     0     0     7     0   140     1
    ##   2      0     0     0     0     0     0     0     0     0    54     0
    ##   3      2    17     0     0     0     3     0     0     0     0     0
    ##   4      0     0     0     0     0     0     0     0     0     0     0
    ##   5      0     0    31     0     0     0     0     0     0     0     0
    ##   6      0     0    15     1     0     0     0     0     0     0     0
    ##   7      0     0     6     7    15     6     0     0     0     0     0
    ##   8      0     0     0     0     0     0     0     0     2     0     0
    ##   9      0     0     0     2     8     0     0     0    53     0     0
    ##   10     0     0     0     0     0     0     1     0     0     0    18
    ##   11     0     0     0     0     9     0     0     0     1     0     0
    ##   12    13     0     0     0     0    26     0     0     0     0     0
    ##   13    13     0     1     0     0     0     0     0     0     5     0
    ##   14     1     0     1     0     0    34     0     0     0     0     0
    ##     
    ##      21226 21227 21229 21230 21231 21234 21237 21239 21251 21287
    ##   1      0     0     0     1   124     0     0     0     0     0
    ##   2      0     0     0     0     0     0     1     0     0     0
    ##   3      0     1     0     0     0     7     0     0     2     0
    ##   4      0     0     0     0     0     0     0     3     0     0
    ##   5      0     0     0     0     0     0     0     0     0     0
    ##   6      0     0     0     0     0     0     0     0     0     0
    ##   7      0     0     0     0     0     0     0     0     0     0
    ##   8      0     2    13     0     0     0     0     0     0     0
    ##   9      0     0     0    11     0     0     0     0     0     0
    ##   10    18     0     0   133     0     0     0     0     0     0
    ##   11     0     0     0    11     0     0     0     0     0     0
    ##   12     0     0     0     0     2     0     0     0     0     0
    ##   13     0     1     0     0     1     0     0     0     0     1
    ##   14     0     0     0     0     0     0     0     0     0     0

Check for missing values
------------------------

Use `is.na` to check if the value in na or not. it gives `True` or `False` output with the same size as the input.

Note that in `R`, `True = 1`, and `false = 0`, so we can sum the output of `is.na()` to get the number of `NA` values.

``` r
## Example
x <- c(1,5,NA, 10, NA)
is.na(x) # we expect the output to be F,F,T,F,T
```

    ## [1] FALSE FALSE  TRUE FALSE  TRUE

``` r
## number of NAs
sum(is.na(x))
```

    ## [1] 2

If we want to check if there is any `NA` in the input data. we can use `any()` that gives `True` if any of its inputs is `True` and `False` otherwise.

``` r
x <- c(1,5,9, 10, NA)
any(is.na(x))
```

    ## [1] TRUE

To check if every single value satisfies a certain condition, use `all()`

``` r
y <- c(5,6,7,8,9,10)
## Check if all values in y is greater than 0
all(y>0)
```

    ## [1] TRUE

Sum across rows and columns
---------------------------

``` r
## get the number of NAs in each column
colSums(is.na(restaurants))
```

    ##            name         zipCode    neighborhood councilDistrict 
    ##               0               0               0               0 
    ##  policeDistrict      Location.1 
    ##               0               0

``` r
## make sure that there is no missing data
all(colSums(is.na(restaurants)) == 0)
```

    ## [1] TRUE

Find specific values in your data
---------------------------------

Suppose we want to find how many times the zipcode `21212` occurs in our data. Two ways...

``` r
## method # 1
sum(restaurants$zipCode == "21212")
```

    ## [1] 28

``` r
## method # 2
table(restaurants$zipCode %in% c("21212"))
```

    ## 
    ## FALSE  TRUE 
    ##  1299    28

If we are searching for the occurrence of two zipcodes `21212` and `21213`, apply the same methods. The second method is more compact.

``` r
## method # 1
sum(restaurants$zipCode == "21212" | restaurants$zipCode == "21213")
```

    ## [1] 59

``` r
## method # 2
table(restaurants$zipCode %in% c("21212", "21213"))
```

    ## 
    ## FALSE  TRUE 
    ##  1268    59

We can use the output of the previous example to subset our data, by putting the expression in the row subsetting part in `restaurants[rows,columns]`

``` r
## get the data of all restaurants with zipcode "21212" or "21213". I hide the result because it is big.
restaurants[restaurants$zipCode %in% c("21212", "21213"),]
```

Cross tabs and Flat tables
--------------------------

``` r
## load UCB admissions R dataset 
data("UCBAdmissions")

## Read it as a dataframe
df = as.data.frame(UCBAdmissions)

## Summarize data
summary(df)
```

    ##       Admit       Gender   Dept       Freq      
    ##  Admitted:12   Male  :12   A:4   Min.   :  8.0  
    ##  Rejected:12   Female:12   B:4   1st Qu.: 80.0  
    ##                            C:4   Median :170.0  
    ##                            D:4   Mean   :188.6  
    ##                            E:4   3rd Qu.:302.5  
    ##                            F:4   Max.   :512.0

Creating a cross tab that shows the `Freq` relationship between `Gender` and `Admit`. Let's take a look at the syntax below. `Freq ~` means, break `Freq` variable by. So, `Freq ~ Gender+Admit` means break `Freq` by `Gender` and `Admit`. Finally, `data=df` means take these data information from `df` that we already created.

``` r
xt <- xtabs(Freq ~ Gender+Admit, data = df)
xt
```

    ##         Admit
    ## Gender   Admitted Rejected
    ##   Male       1198     1493
    ##   Female      557     1278

``` r
## flat tables gives more compact output
ftable(xt)
```

    ##        Admit Admitted Rejected
    ## Gender                        
    ## Male             1198     1493
    ## Female            557     1278

Size of the dataset
===================

``` r
fake = rnorm(1e5)

## output in bytes
object.size(fake)
```

    ## 800024 bytes

``` r
## change the units of the output to Mb
print(object.size(fake), units="Mb")
```

    ## 0.8 Mb

Create new variables
====================

`ifelse`
--------

Given the `restaurants` dataset, we want to add column `falseZip` indicates the wronge zipcodes in this dataset.

``` r
## if the zipcode < 0 the output is true (falsezip=true)
restaurants$falsezip = ifelse(restaurants$zipCode < 0, TRUE, FALSE)

## summarize the output using table
table(restaurants$falsezip, restaurants$zipCode < 0)
```

    ##        
    ##         FALSE TRUE
    ##   FALSE  1326    0
    ##   TRUE      0    1

``` r
## we have one false zipcode (-21226)
table(restaurants$falsezip, restaurants$zipCode)
```

    ##        
    ##         -21226 21201 21202 21205 21206 21207 21208 21209 21210 21211 21212
    ##   FALSE      0   136   201    27    30     4     1     8    23    41    28
    ##   TRUE       1     0     0     0     0     0     0     0     0     0     0
    ##        
    ##         21213 21214 21215 21216 21217 21218 21220 21222 21223 21224 21225
    ##   FALSE    31    17    54    10    32    69     1     7    56   199    19
    ##   TRUE      0     0     0     0     0     0     0     0     0     0     0
    ##        
    ##         21226 21227 21229 21230 21231 21234 21237 21239 21251 21287
    ##   FALSE    18     4    13   156   127     7     1     3     2     1
    ##   TRUE      0     0     0     0     0     0     0     0     0     0

Categorical Variables
---------------------

We can put our data into groups using `cut()`. The output of `cut` is a **factor variable**.

``` r
## Break zipcode data into groups wrt quantile output. Each zipcode will have it's group in the zipGroups column
restaurants$zipGroups = cut(restaurants$zipCode, breaks = quantile(restaurants$zipCode))

## summarizing the zipGroups (histogram like table)
table(restaurants$zipGroups)
```

    ## 
    ## (-2.123e+04,2.12e+04]  (2.12e+04,2.122e+04] (2.122e+04,2.123e+04] 
    ##                   337                   375                   282 
    ## (2.123e+04,2.129e+04] 
    ##                   332

``` r
## summarizing the zipcodes wrt zipGroups
table(restaurants$zipGroups, restaurants$zipCode)
```

    ##                        
    ##                         -21226 21201 21202 21205 21206 21207 21208 21209
    ##   (-2.123e+04,2.12e+04]      0   136   201     0     0     0     0     0
    ##   (2.12e+04,2.122e+04]       0     0     0    27    30     4     1     8
    ##   (2.122e+04,2.123e+04]      0     0     0     0     0     0     0     0
    ##   (2.123e+04,2.129e+04]      0     0     0     0     0     0     0     0
    ##                        
    ##                         21210 21211 21212 21213 21214 21215 21216 21217
    ##   (-2.123e+04,2.12e+04]     0     0     0     0     0     0     0     0
    ##   (2.12e+04,2.122e+04]     23    41    28    31    17    54    10    32
    ##   (2.122e+04,2.123e+04]     0     0     0     0     0     0     0     0
    ##   (2.123e+04,2.129e+04]     0     0     0     0     0     0     0     0
    ##                        
    ##                         21218 21220 21222 21223 21224 21225 21226 21227
    ##   (-2.123e+04,2.12e+04]     0     0     0     0     0     0     0     0
    ##   (2.12e+04,2.122e+04]     69     0     0     0     0     0     0     0
    ##   (2.122e+04,2.123e+04]     0     1     7    56   199    19     0     0
    ##   (2.123e+04,2.129e+04]     0     0     0     0     0     0    18     4
    ##                        
    ##                         21229 21230 21231 21234 21237 21239 21251 21287
    ##   (-2.123e+04,2.12e+04]     0     0     0     0     0     0     0     0
    ##   (2.12e+04,2.122e+04]      0     0     0     0     0     0     0     0
    ##   (2.122e+04,2.123e+04]     0     0     0     0     0     0     0     0
    ##   (2.123e+04,2.129e+04]    13   156   127     7     1     3     2     1

**An easier way to do the `cut` task, is to use `Hmisc` package.** *It will need some other packages `survival`, `ggplot2`, see the error and download what is needed*

``` r
## Load the library
library(Hmisc)
```

    ## Loading required package: lattice

    ## Loading required package: survival

    ## Loading required package: Formula

    ## Loading required package: ggplot2

    ## 
    ## Attaching package: 'Hmisc'

    ## The following objects are masked from 'package:plyr':
    ## 
    ##     is.discrete, summarize

    ## The following objects are masked from 'package:base':
    ## 
    ##     format.pval, round.POSIXt, trunc.POSIXt, units

``` r
## cut the zipcodes into 4 groups(g=4) according to quantiles
restaurants$zipGroups = cut2(restaurants$zipCode, g=4)

## summarizing the zipGroups (histogram like table)
table(restaurants$zipGroups)
```

    ## 
    ## [-21226,21205) [ 21205,21220) [ 21220,21227) [ 21227,21287] 
    ##            338            375            300            314

Factor Variables
----------------

It might be intuative not to leave zipcodes as integers, because it will take more space and we will neve add or subtract them. That's why we make zipcode variable a `factor`.

``` r
restaurants$zipcodeF = factor(restaurants$zipCode)

## looking at the first 10 values. There are 32 different values (levels) of zipcode
restaurants$zipcodeF[1:10]
```

    ##  [1] 21206 21231 21224 21211 21223 21218 21205 21211 21205 21231
    ## 32 Levels: -21226 21201 21202 21205 21206 21207 21208 21209 ... 21287

``` r
class(restaurants$zipcodeF)
```

    ## [1] "factor"

**Another example on factor variables**

In case of character variables, `factor` arranges the levels according to the alphabetical order of the variables. Also, factor variables can be transfered into numerical variables using `as.umeric()`.

``` r
## create a sample vector of size 10 with yes or no values.
yn <- sample(c("yes","no"), size = 10, replace = T)

## Create a factor variable for the yn variable
## see here factor() will make the first value "alphabetically" as its first level.  
fct <- factor(yn)
fct
```

    ##  [1] no  yes yes no  no  no  yes no  yes no 
    ## Levels: no yes

``` r
## no = 1 because it is the first level, and yes = 2
as.numeric(fct)
```

    ##  [1] 1 2 2 1 1 1 2 1 2 1

``` r
## To make yes the first level give factor() the ordered list of levels.
fct <- factor(yn, levels = c("yes", "no"))
fct
```

    ##  [1] no  yes yes no  no  no  yes no  yes no 
    ## Levels: yes no

``` r
## yes = 1 because it is the first level, and no = 2
as.numeric(fct)
```

    ##  [1] 2 1 1 2 2 2 1 2 1 2

Using `mutate` from `plyr` package
----------------------------------

We can create new variable and add it to the dataframe one line of code using `mutate`.

``` r
## load the library
library(plyr)

## creating zipGroups from cutting zipcodes into 4 groups, and add it to the dataframe
restaurants <- mutate(restaurants, zipGroups=cut2(zipCode, g=4))
table(restaurants$zipGroups)
```

    ## 
    ## [-21226,21205) [ 21205,21220) [ 21220,21227) [ 21227,21287] 
    ##            338            375            300            314

Resahping Data
==============

`melt` the dataset
------------------

Highlighting only certian variables and transform the dataframe into a skinny tall one. The output is `id`, `variable`, and `value` dataframe. *Note that the original dataframe has 32 observations, and the melted one contains 64, that is because for each entry we want to see two variables "mpg" and "hp"*.

``` r
## load library
library(reshape2)

## load mtcars dataset that contains 11 variables(columns), 32 observations(rows)
data("mtcars")
head(mtcars)
```

    ##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
    ## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
    ## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
    ## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
    ## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
    ## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
    ## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1

``` r
## variables names
names(mtcars)
```

    ##  [1] "mpg"  "cyl"  "disp" "hp"   "drat" "wt"   "qsec" "vs"   "am"   "gear"
    ## [11] "carb"

``` r
## Melt the dataset. The id is the 3 variables we specified. each entry will have two rows each descriping a variable ("mpg" or "hp")
mtcars$carname <- rownames(mtcars)
carMelt <- melt(mtcars, id = c("carname","gear", "cyl"), measure.vars = c("mpg", "hp"))
head(carMelt)
```

    ##             carname gear cyl variable value
    ## 1         Mazda RX4    4   6      mpg  21.0
    ## 2     Mazda RX4 Wag    4   6      mpg  21.0
    ## 3        Datsun 710    4   4      mpg  22.8
    ## 4    Hornet 4 Drive    3   6      mpg  21.4
    ## 5 Hornet Sportabout    3   8      mpg  18.7
    ## 6           Valiant    3   6      mpg  18.1

``` r
## re-order the output by name to see that each entry will has two rows for the two variables ("mpg" or "hp")
carMelt[order(carMelt$carname),]
```

    ##                carname gear cyl variable value
    ## 23         AMC Javelin    3   8      mpg  15.2
    ## 55         AMC Javelin    3   8       hp 150.0
    ## 15  Cadillac Fleetwood    3   8      mpg  10.4
    ## 47  Cadillac Fleetwood    3   8       hp 205.0
    ## 24          Camaro Z28    3   8      mpg  13.3
    ## 56          Camaro Z28    3   8       hp 245.0
    ## 17   Chrysler Imperial    3   8      mpg  14.7
    ## 49   Chrysler Imperial    3   8       hp 230.0
    ## 3           Datsun 710    4   4      mpg  22.8
    ## 35          Datsun 710    4   4       hp  93.0
    ## 22    Dodge Challenger    3   8      mpg  15.5
    ## 54    Dodge Challenger    3   8       hp 150.0
    ## 7           Duster 360    3   8      mpg  14.3
    ## 39          Duster 360    3   8       hp 245.0
    ## 30        Ferrari Dino    5   6      mpg  19.7
    ## 62        Ferrari Dino    5   6       hp 175.0
    ## 18            Fiat 128    4   4      mpg  32.4
    ## 50            Fiat 128    4   4       hp  66.0
    ## 26           Fiat X1-9    4   4      mpg  27.3
    ## 58           Fiat X1-9    4   4       hp  66.0
    ## 29      Ford Pantera L    5   8      mpg  15.8
    ## 61      Ford Pantera L    5   8       hp 264.0
    ## 19         Honda Civic    4   4      mpg  30.4
    ## 51         Honda Civic    4   4       hp  52.0
    ## 4       Hornet 4 Drive    3   6      mpg  21.4
    ## 36      Hornet 4 Drive    3   6       hp 110.0
    ## 5    Hornet Sportabout    3   8      mpg  18.7
    ## 37   Hornet Sportabout    3   8       hp 175.0
    ## 16 Lincoln Continental    3   8      mpg  10.4
    ## 48 Lincoln Continental    3   8       hp 215.0
    ## 28        Lotus Europa    5   4      mpg  30.4
    ## 60        Lotus Europa    5   4       hp 113.0
    ## 31       Maserati Bora    5   8      mpg  15.0
    ## 63       Maserati Bora    5   8       hp 335.0
    ## 1            Mazda RX4    4   6      mpg  21.0
    ## 33           Mazda RX4    4   6       hp 110.0
    ## 2        Mazda RX4 Wag    4   6      mpg  21.0
    ## 34       Mazda RX4 Wag    4   6       hp 110.0
    ## 9             Merc 230    4   4      mpg  22.8
    ## 41            Merc 230    4   4       hp  95.0
    ## 8            Merc 240D    4   4      mpg  24.4
    ## 40           Merc 240D    4   4       hp  62.0
    ## 10            Merc 280    4   6      mpg  19.2
    ## 42            Merc 280    4   6       hp 123.0
    ## 11           Merc 280C    4   6      mpg  17.8
    ## 43           Merc 280C    4   6       hp 123.0
    ## 12          Merc 450SE    3   8      mpg  16.4
    ## 44          Merc 450SE    3   8       hp 180.0
    ## 13          Merc 450SL    3   8      mpg  17.3
    ## 45          Merc 450SL    3   8       hp 180.0
    ## 14         Merc 450SLC    3   8      mpg  15.2
    ## 46         Merc 450SLC    3   8       hp 180.0
    ## 25    Pontiac Firebird    3   8      mpg  19.2
    ## 57    Pontiac Firebird    3   8       hp 175.0
    ## 27       Porsche 914-2    5   4      mpg  26.0
    ## 59       Porsche 914-2    5   4       hp  91.0
    ## 20      Toyota Corolla    4   4      mpg  33.9
    ## 52      Toyota Corolla    4   4       hp  65.0
    ## 21       Toyota Corona    3   4      mpg  21.5
    ## 53       Toyota Corona    3   4       hp  97.0
    ## 6              Valiant    3   6      mpg  18.1
    ## 38             Valiant    3   6       hp 105.0
    ## 32          Volvo 142E    4   4      mpg  21.4
    ## 64          Volvo 142E    4   4       hp 109.0

``` r
## Another way to re-order is to use arrange(df, by)
arrange(carMelt, carname)
```

    ##                carname gear cyl variable value
    ## 1          AMC Javelin    3   8      mpg  15.2
    ## 2          AMC Javelin    3   8       hp 150.0
    ## 3   Cadillac Fleetwood    3   8      mpg  10.4
    ## 4   Cadillac Fleetwood    3   8       hp 205.0
    ## 5           Camaro Z28    3   8      mpg  13.3
    ## 6           Camaro Z28    3   8       hp 245.0
    ## 7    Chrysler Imperial    3   8      mpg  14.7
    ## 8    Chrysler Imperial    3   8       hp 230.0
    ## 9           Datsun 710    4   4      mpg  22.8
    ## 10          Datsun 710    4   4       hp  93.0
    ## 11    Dodge Challenger    3   8      mpg  15.5
    ## 12    Dodge Challenger    3   8       hp 150.0
    ## 13          Duster 360    3   8      mpg  14.3
    ## 14          Duster 360    3   8       hp 245.0
    ## 15        Ferrari Dino    5   6      mpg  19.7
    ## 16        Ferrari Dino    5   6       hp 175.0
    ## 17            Fiat 128    4   4      mpg  32.4
    ## 18            Fiat 128    4   4       hp  66.0
    ## 19           Fiat X1-9    4   4      mpg  27.3
    ## 20           Fiat X1-9    4   4       hp  66.0
    ## 21      Ford Pantera L    5   8      mpg  15.8
    ## 22      Ford Pantera L    5   8       hp 264.0
    ## 23         Honda Civic    4   4      mpg  30.4
    ## 24         Honda Civic    4   4       hp  52.0
    ## 25      Hornet 4 Drive    3   6      mpg  21.4
    ## 26      Hornet 4 Drive    3   6       hp 110.0
    ## 27   Hornet Sportabout    3   8      mpg  18.7
    ## 28   Hornet Sportabout    3   8       hp 175.0
    ## 29 Lincoln Continental    3   8      mpg  10.4
    ## 30 Lincoln Continental    3   8       hp 215.0
    ## 31        Lotus Europa    5   4      mpg  30.4
    ## 32        Lotus Europa    5   4       hp 113.0
    ## 33       Maserati Bora    5   8      mpg  15.0
    ## 34       Maserati Bora    5   8       hp 335.0
    ## 35           Mazda RX4    4   6      mpg  21.0
    ## 36           Mazda RX4    4   6       hp 110.0
    ## 37       Mazda RX4 Wag    4   6      mpg  21.0
    ## 38       Mazda RX4 Wag    4   6       hp 110.0
    ## 39            Merc 230    4   4      mpg  22.8
    ## 40            Merc 230    4   4       hp  95.0
    ## 41           Merc 240D    4   4      mpg  24.4
    ## 42           Merc 240D    4   4       hp  62.0
    ## 43            Merc 280    4   6      mpg  19.2
    ## 44            Merc 280    4   6       hp 123.0
    ## 45           Merc 280C    4   6      mpg  17.8
    ## 46           Merc 280C    4   6       hp 123.0
    ## 47          Merc 450SE    3   8      mpg  16.4
    ## 48          Merc 450SE    3   8       hp 180.0
    ## 49          Merc 450SL    3   8      mpg  17.3
    ## 50          Merc 450SL    3   8       hp 180.0
    ## 51         Merc 450SLC    3   8      mpg  15.2
    ## 52         Merc 450SLC    3   8       hp 180.0
    ## 53    Pontiac Firebird    3   8      mpg  19.2
    ## 54    Pontiac Firebird    3   8       hp 175.0
    ## 55       Porsche 914-2    5   4      mpg  26.0
    ## 56       Porsche 914-2    5   4       hp  91.0
    ## 57      Toyota Corolla    4   4      mpg  33.9
    ## 58      Toyota Corolla    4   4       hp  65.0
    ## 59       Toyota Corona    3   4      mpg  21.5
    ## 60       Toyota Corona    3   4       hp  97.0
    ## 61             Valiant    3   6      mpg  18.1
    ## 62             Valiant    3   6       hp 105.0
    ## 63          Volvo 142E    4   4      mpg  21.4
    ## 64          Volvo 142E    4   4       hp 109.0

casting dataframes
------------------

After melting the dataframe, we can reformat the it into different shapes `dcast`. For example, we want to see how `cyl` variable is related to other variables in the melted dataframe. *remember that we have 2 variables ("mpg" or "hp")*

``` r
## The output is the summary of the # of "mpg" and "hp" observations for each "cyl" value 
cylData <- dcast(carMelt, cyl ~ variable)
cylData
```

    ##   cyl mpg hp
    ## 1   4  11 11
    ## 2   6   7  7
    ## 3   8  14 14

``` r
## For the mean value of "mpg" and "hp" for each "cyl" value 
cylData <- dcast(carMelt, cyl ~ variable, mean)
cylData
```

    ##   cyl      mpg        hp
    ## 1   4 26.66364  82.63636
    ## 2   6 19.74286 122.28571
    ## 3   8 15.10000 209.21429

`dplyr` Package
===============

`dplyr` package is designed to manipulate dataframes. It provides a grammer (verbs) to manipulate data, so it is easy to use. An example of `dplyr` verbs:

-   `select`: return a subset of the columns

-   `filter`: extract a subset of rows based on a logical condition

-   `arrange`: reorder rows

-   `rename`: rename variables (columns)

-   `mutate`: add new variables or transform existing ones

\*`summarize`: generate summary statistics for different variables
