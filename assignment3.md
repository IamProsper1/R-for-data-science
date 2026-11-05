Assignment 3: functions and the apply family
================
Ayinebyona Prosper
2026-11-05

## Introduction

In this assignment I:

1.  Write a function that calculates summary statistics on a variable
2.  Write a function that runs a t-test
3.  Use the apply family of functions: `apply()`, `lapply()`,
    `sapply()`, `tapply()` and `mapply()`

I use the `Patients` table from my `NewLifeHospital` MySQL database,
together with some built-in R datasets.

## Data

I read the `Patients` table from MySQL. The table has no numeric
variable to analyse, so I calculate each patient’s age in years from
`DateOfBirth` using `difftime()`.

``` r
library(DBI)
library(RMySQL)
```

    ## Warning: package 'RMySQL' was built under R version 4.4.3

``` r
# The password is saved in my .Renviron file, not in this file
con <- dbConnect(MySQL(), dbname = "NewLifeHospital", host = "localhost",
                 user = "root", password = Sys.getenv("MYSQL_PASSWORD"))
patients <- dbReadTable(con, "Patients")
dbDisconnect(con)
```

    ## [1] TRUE

``` r
# age in years from date of birth
patients$Age <- as.numeric(difftime(Sys.Date(), as.Date(patients$DateOfBirth), units = "days")) / 365.25
patients$Age <- round(patients$Age, 1)

# number of patients by gender
table(patients$Gender)
```

    ## 
    ## Female   Male 
    ##      9     11

## 1. Function for summary statistics

The function gives the number of observations, the number of missing
values, the mean, standard deviation, minimum, median and maximum of a
variable. `na.rm = TRUE` makes it work on variables with missing values.

``` r
summary_stats <- function(x) {
  result <- c(n = length(x),
              missing = sum(is.na(x)),
              mean = mean(x, na.rm = TRUE),
              sd = sd(x, na.rm = TRUE),
              min = min(x, na.rm = TRUE),
              median = median(x, na.rm = TRUE),
              max = max(x, na.rm = TRUE))
  return(round(result, 2))
}
```

Summary statistics of the patients’ age:

``` r
summary_stats(patients$Age)
```

    ##       n missing    mean      sd     min  median     max 
    ##   20.00    0.00   33.81    8.27   23.70   32.70   51.50

The mean age of the patients is 33.81, and the youngest one is 23 while
the oldest is 51

## 2. Function that runs a t-test

The function runs a t-test on two groups. Then it uses `if` and `else`
to say whether the difference between the two means is significant at
the 5% level.

``` r
run_ttest <- function(x, y) {
  test <- t.test(x, y)
  print(test)
  if (test$p.value < 0.05) {
    print("The difference between the two means is significant (p < 0.05)")
  } else {
    print("The difference between the two means is not significant (p >= 0.05)")
  }
}
```

Comparing the age of male and female patients:

``` r
male_age <- patients$Age[patients$Gender == "Male"]
female_age <- patients$Age[patients$Gender == "Female"]

run_ttest(male_age, female_age)
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  x and y
    ## t = 0.18878, df = 18, p-value = 0.8524
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -7.141405  8.551506
    ## sample estimates:
    ## mean of x mean of y 
    ##  34.12727  33.42222 
    ## 
    ## [1] "The difference between the two means is not significant (p >= 0.05)"

## 3. Applications of the apply family

### apply()

`apply()` applies a function to the rows (1) or columns (2) of a matrix
or table.

``` r
m <- matrix(1:12, nrow = 3)
m
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    4    7   10
    ## [2,]    2    5    8   11
    ## [3,]    3    6    9   12

``` r
apply(m, 1, sum)   # sum of each row
```

    ## [1] 22 26 30

``` r
apply(m, 2, sum)   # sum of each column
```

    ## [1]  6 15 24 33

With my function from part 1 on the numeric columns of `iris`:

``` r
apply(iris[, 1:4], 2, summary_stats)
```

    ##         Sepal.Length Sepal.Width Petal.Length Petal.Width
    ## n             150.00      150.00       150.00      150.00
    ## missing         0.00        0.00         0.00        0.00
    ## mean            5.84        3.06         3.76        1.20
    ## sd              0.83        0.44         1.77        0.76
    ## min             4.30        2.00         1.00        0.10
    ## median          5.80        3.00         4.35        1.30
    ## max             7.90        4.40         6.90        2.50

On the Titanic table, survival by class:

``` r
apply(Titanic, c(1, 4), sum)
```

    ##       Survived
    ## Class   No Yes
    ##   1st  122 203
    ##   2nd  167 118
    ##   3rd  528 178
    ##   Crew 673 212

### lapply()

`lapply()` applies a function to each element of a list and always
returns a list.

``` r
ages <- list(male = male_age, female = female_age)

lapply(ages, mean)
```

    ## $male
    ## [1] 34.12727
    ## 
    ## $female
    ## [1] 33.42222

``` r
lapply(ages, range)
```

    ## $male
    ## [1] 25.0 51.5
    ## 
    ## $female
    ## [1] 23.7 46.1

### sapply()

`sapply()` works like `lapply()` but simplifies the result to a vector
when it can.

``` r
sapply(ages, mean)
```

    ##     male   female 
    ## 34.12727 33.42222

``` r
sapply(patients, class)   # type of each column
```

    ##        PatientID        FirstName         LastName           Gender 
    ##        "integer"      "character"      "character"      "character" 
    ##      DateOfBirth        Telephone          Address       BloodGroup 
    ##      "character"      "character"      "character"      "character" 
    ## RegistrationDate              Age 
    ##      "character"        "numeric"

With a function written inside, the square of each number:

``` r
sapply(1:5, function(x) x^2)
```

    ## [1]  1  4  9 16 25

### tapply()

`tapply()` applies a function to a variable separately for each group.
It gives the same result as `by()` from assignment 2, but in a shorter
form.

``` r
tapply(patients$Age, patients$Gender, mean)       # mean age by gender
```

    ##   Female     Male 
    ## 33.42222 34.12727

``` r
tapply(patients$Age, patients$BloodGroup, mean)   # mean age by blood group
```

    ##       A-       A+      AB-      AB+       B-       B+       O-       O+ 
    ## 32.90000 33.00000 33.60000 27.15000 32.25000 37.56667 37.10000 34.62500

``` r
tapply(patients$Age, patients$Gender, summary_stats)
```

    ## $Female
    ##       n missing    mean      sd     min  median     max 
    ##    9.00    0.00   33.42    7.49   23.70   33.90   46.10 
    ## 
    ## $Male
    ##       n missing    mean      sd     min  median     max 
    ##   11.00    0.00   34.13    9.21   25.00   30.60   51.50

### mapply()

`mapply()` applies a function to two or more vectors at the same time,
element by element.

``` r
mapply(function(x, y) x + y, 1:5, 6:10)   # 1+6, 2+7, 3+8, ...
```

    ## [1]  7  9 11 13 15

``` r
mapply(rep, 1:3, 3:1)                     # repeat 1 three times, 2 twice, 3 once
```

    ## [[1]]
    ## [1] 1 1 1
    ## 
    ## [[2]]
    ## [1] 2 2
    ## 
    ## [[3]]
    ## [1] 3

## Conclusion

I wrote a function that calculates summary statistics on a variable and
a function that runs a t-test and says whether the result is
significant. I used them on the age of patients from my hospital
database. I also used the apply family: `apply()` on rows and columns,
`lapply()` and `sapply()` on lists, `tapply()` on groups and `mapply()`
on several vectors at once.
