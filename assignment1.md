Assignment 1: Importing Data in R
================
Prosper Ayinebyona
2026-10-31

## Introduction

In this assignment I import data from three sources:

1.  A CSV file
2.  A statistical package (SPSS, Stata and SAS files)
3.  A database management system (MySQL)
4.  A built-in R dataset
5.  A web API

For each dataset I display the first 5 rows.

Packages used: `haven`, `DBI`, `RMySQL` and `jsonlite`.

``` r
# installing libraries
# install.packages("haven")
# install.packages("DBI")
# install.packages("RMySQL")
# install.packages("jsonlite")
```

## 1. Importing a CSV file

The file `sample.csv` is in the same folder as this R Markdown file, so
I only need the file name.

``` r
csv_data <- read.csv("sample.csv")
head(csv_data, 5)
```

    ##      X  name department city age rating  join_date Jan_sales Feb_sales
    ## 1 E100 Alice      Sales  NYC  25      A 2021-03-01      5000      5200
    ## 2 E101   Bob      Sales   LA  30      B 2020-07-15      4200        NA
    ## 3 E102 Carol      Sales  NYC  NA      A 2022-01-10        NA      4600
    ## 4 E103  Dave         IT   SF  35      B 2019-11-20      6000      6100
    ## 5 E104   Eve         IT   SF  28      B 2021-06-01      5800        NA
    ##   Mar_sales Apr_sales
    ## 1      5300      5400
    ## 2      4400      4500
    ## 3      4700      4800
    ## 4        NA      6300
    ## 5      6000      6100

## 2. Importing data from a statistical package

I used the `haven` package that reads files from SPSS, Stata and SAS. I
used the example files that come with the package.

### SPSS file (.sav)

``` r
library(haven)

spss_data <- read_sav(system.file("examples", "iris.sav", package = "haven"))
head(spss_data, 5)
```

    ## # A tibble: 5 × 5
    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species   
    ##          <dbl>       <dbl>        <dbl>       <dbl> <dbl+lbl> 
    ## 1          5.1         3.5          1.4         0.2 1 [setosa]
    ## 2          4.9         3            1.4         0.2 1 [setosa]
    ## 3          4.7         3.2          1.3         0.2 1 [setosa]
    ## 4          4.6         3.1          1.5         0.2 1 [setosa]
    ## 5          5           3.6          1.4         0.2 1 [setosa]

### Stata file (.dta)

``` r
stata_data <- read_dta(system.file("examples", "iris.dta", package = "haven"))
head(stata_data, 5)
```

    ## # A tibble: 5 × 5
    ##   sepallength sepalwidth petallength petalwidth species
    ##         <dbl>      <dbl>       <dbl>      <dbl> <chr>  
    ## 1        5.10       3.5         1.40      0.200 setosa 
    ## 2        4.90       3           1.40      0.200 setosa 
    ## 3        4.70       3.20        1.30      0.200 setosa 
    ## 4        4.60       3.10        1.5       0.200 setosa 
    ## 5        5          3.60        1.40      0.200 setosa

### SAS file (.sas7bdat)

``` r
sas_data <- read_sas(system.file("examples", "iris.sas7bdat", package = "haven"))
head(sas_data, 5)
```

    ## # A tibble: 5 × 5
    ##   Sepal_Length Sepal_Width Petal_Length Petal_Width Species
    ##          <dbl>       <dbl>        <dbl>       <dbl> <chr>  
    ## 1          5.1         3.5          1.4         0.2 setosa 
    ## 2          4.9         3            1.4         0.2 setosa 
    ## 3          4.7         3.2          1.3         0.2 setosa 
    ## 4          4.6         3.1          1.5         0.2 setosa 
    ## 5          5           3.6          1.4         0.2 setosa

## 3. Importing data from a database (MySQL)

I connect to the `NewLifeHospital` database on my computer and read the
`Patients` table.

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
head(patients, 5)
```

    ##   PatientID  FirstName   LastName Gender DateOfBirth     Telephone
    ## 1         1 Ayinebyona    Prosper   Male  2000-03-14 +250788123001
    ## 2         2       Aime Iradukunda   Male  1999-07-22 +250788123002
    ## 3         3    Charles Nizeyimana   Male  2001-11-05 +250788123003
    ## 4         4      Alain    Uwineza   Male  2000-05-30 +250788123004
    ## 5         5  Alexandre     Natete   Male  1998-09-12 +250788123005
    ##              Address BloodGroup RegistrationDate
    ## 1    Kacyiru, Kigali         O+       2024-01-10
    ## 2  Kimironko, Kigali         A+       2024-01-15
    ## 3     Remera, Kigali         B+       2024-02-02
    ## 4     Gisozi, Kigali        AB+       2024-02-18
    ## 5 Nyamirambo, Kigali         O-       2024-03-01

Close the connection when finished:

``` r
dbDisconnect(con)
```

    ## [1] TRUE

## 4. Importing data from a built-in library

R comes with the `datasets` package, which has ready-made datasets. I
load the `mtcars` dataset.

``` r
data("mtcars")
head(mtcars, 5)
```

    ##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
    ## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
    ## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
    ## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
    ## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
    ## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2

## 5. Importing data from an API

a `jsonlite` package reads JSON data from a web API. I use
JSONPlaceholder, a free practice API that needs no account or key.

``` r
library(jsonlite)
```

    ## Warning: package 'jsonlite' was built under R version 4.4.3

``` r
todos <- fromJSON("https://jsonplaceholder.typicode.com/todos")
head(todos, 5)
```

    ##   userId id                                                           title
    ## 1      1  1                                              delectus aut autem
    ## 2      1  2                              quis ut nam facilis et officia qui
    ## 3      1  3                                             fugiat veniam minus
    ## 4      1  4                                                et porro tempora
    ## 5      1  5 laboriosam mollitia et enim quasi adipisci quia provident illum
    ##   completed
    ## 1     FALSE
    ## 2     FALSE
    ## 3     FALSE
    ## 4      TRUE
    ## 5     FALSE

## Conclusion

I imported a CSV file using `read.csv()`, data from SPSS, Stata and SAS
files using the `haven` package, and data from a MySQL database using
`DBI` and `RMySQL`. The first 5 rows of each dataset were displayed with
`head()`. I also loaded the built-in `mtcars` dataset using `data()`. I
also imported data from a web API using `fromJSON()` from the `jsonlite`
package.
