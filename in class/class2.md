Control Structures
================
Prosper Ayinebyona
2026-11-01

# Control structures

Control structures let us control the flow of an R program.

- if and else: test a condition and act on it
- for: execute a loop a fixed number of times
- while: execute a loop while a condition is true
- break: stop the execution of a loop
- next: skip an iteration of a loop

## if and else

The structure:

``` r
if(condition){
  do something
} else{
  do something else
}
```

``` r
x <- runif(1,0,10)
x
```

    ## [1] 3.849895

``` r
# hist(x)
# y <- rnorm(100)
# hist(y)
# mean(y)
# sd(y)

if(x>3){
  print("X is greater than three")
} else{
  print("x is less than three")
}
```

    ## [1] "X is greater than three"

The result of if/else can be saved in a variable:

``` r
y <- if(x>3){
  10
} else{
  0
}

y
```

    ## [1] 10

### For

``` r
x <- c("a","b","c","d")
x
```

    ## [1] "a" "b" "c" "d"

``` r
for (i in 1:4) {
  print(x[i])
}
```

    ## [1] "a"
    ## [1] "b"
    ## [1] "c"
    ## [1] "d"

The same loop using `seq_along()`, which follows the length of x:

``` r
for (i in seq_along(x)) {
  print(x[i])
}
```

    ## [1] "a"
    ## [1] "b"
    ## [1] "c"
    ## [1] "d"

Looping directly over the elements:

``` r
for (letter in x) {
  print(letter)
}
```

    ## [1] "a"
    ## [1] "b"
    ## [1] "c"
    ## [1] "d"

### While loop

The loop runs as long as the condition is true.

``` r
count <- 0
while(count<10){
  print(count)
  count<-count+1
}
```

    ## [1] 0
    ## [1] 1
    ## [1] 2
    ## [1] 3
    ## [1] 4
    ## [1] 5
    ## [1] 6
    ## [1] 7
    ## [1] 8
    ## [1] 9

The loop below continues while z is between 3 and 10. Each time a coin
is flipped: if it is 1, z goes up by 1, otherwise z goes down by 1.

``` r
z <- 5
set.seed(1)

while (z >= 3 && z <= 10) {
  coin <- rbinom(1, 1, 0.5)

  if (coin == 1) {
    z <- z + 1
  } else {
    z <- z - 1
  }
}

print(z)
```

    ## [1] 2

## next, break

`next` is used to skip an iteration of a loop. Here the first 5
iterations are skipped:

``` r
for (i in 1:10) {
  if (i <= 5) {
    next
  }
  print(i)
}
```

    ## [1] 6
    ## [1] 7
    ## [1] 8
    ## [1] 9
    ## [1] 10

`break` is used to stop a loop. Here the loop stops after i = 21:

``` r
for (i in 1:100) {
  print(1:i)

  if (i > 20) {
    break
  }
}
```

    ## [1] 1
    ## [1] 1 2
    ## [1] 1 2 3
    ## [1] 1 2 3 4
    ## [1] 1 2 3 4 5
    ## [1] 1 2 3 4 5 6
    ## [1] 1 2 3 4 5 6 7
    ## [1] 1 2 3 4 5 6 7 8
    ## [1] 1 2 3 4 5 6 7 8 9
    ##  [1]  1  2  3  4  5  6  7  8  9 10
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21

## Functions

A function takes inputs (arguments), does a calculation and returns the
result.

``` r
f2 <- function(x,y){
  result <- (x^2) + (y^2) + (2*x*y) - 3
  return(result)
}

f2(4,6)
```

    ## [1] 97

## titanic dataset

`apply()` function helps us to apply a function on dimensions of matrix.

Titanic is a table with 4 dimensions: 1 = Class, 2 = Sex, 3 = Age and 4
= Survived.

``` r
View(Titanic)
```

``` r
data("Titanic")
dimnames(Titanic)
```

    ## $Class
    ## [1] "1st"  "2nd"  "3rd"  "Crew"
    ## 
    ## $Sex
    ## [1] "Male"   "Female"
    ## 
    ## $Age
    ## [1] "Child" "Adult"
    ## 
    ## $Survived
    ## [1] "No"  "Yes"

``` r
# Class by Age
apply(Titanic, c(1,3), sum)
```

    ##       Age
    ## Class  Child Adult
    ##   1st      6   319
    ##   2nd     24   261
    ##   3rd     79   627
    ##   Crew     0   885

``` r
# Class by Survived, only rows 3 and 4 (3rd class and Crew)
apply(Titanic, c(1,4), sum) [3:4,]
```

    ##       Survived
    ## Class   No Yes
    ##   3rd  528 178
    ##   Crew 673 212

``` r
# Class, Sex and Age
apply(Titanic, c(1,2,3), sum)
```

    ## , , Age = Child
    ## 
    ##       Sex
    ## Class  Male Female
    ##   1st     5      1
    ##   2nd    11     13
    ##   3rd    48     31
    ##   Crew    0      0
    ## 
    ## , , Age = Adult
    ## 
    ##       Sex
    ## Class  Male Female
    ##   1st   175    144
    ##   2nd   168     93
    ##   3rd   462    165
    ##   Crew  862     23

### exercises

- make function to calculate summary statistics on variable
- make a function that runs t test
- applications of apply() functions

My answers are in [Assignment 3](assignment3.md).
