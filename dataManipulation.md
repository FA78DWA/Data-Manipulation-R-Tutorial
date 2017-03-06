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
