
# Apply functions

There are a number of `apply` commands in the R family. Each has different way of handling data

Additional information can be found in threads on [stackoverflow](http://stackoverflow.com/questions/3505701/r-grouping-functions-sapply-vs-lapply-vs-apply-vs-tapply-vs-by-vs-aggrega)

Note - the apply-style commands are *not* particularly faster than a *well-written* loop. The benefit is their one-line nature. [Read some more here](http://stackoverflow.com/questions/2275896/is-rs-apply-family-more-than-syntactic-sugar?rq=1)

## lapply

Works to apply a function to each element in the list and *returns a list back*

`lapply` takes the following arguments:
* `X` - input
* `FUN` - function to be applied
* `...` is for other arguments to be passed into fun


```r
lapply(Theoph, range)
#> $Subject
#> [1] 6 5
#> Levels: 6 < 7 < 8 < 11 < 3 < 2 < 4 < 9 < 12 < 10 < 1 < 5
#> 
#> $Wt
#> [1] 54.6 86.4
#> 
#> $Dose
#> [1] 3.10 5.86
#> 
#> $Time
#> [1]  0.0 24.6
#> 
#> $conc
#> [1]  0.0 11.4
```

Given that a dataframe is a list with dimensions - lapply can easily step through the columns and apply your function and returns a list.

## Additional apply-style functions

The below apply functions are rarely used, as packages such as `dplyr`, `tidyr`, and especially `purrr` have provided expanded functionality and simplified api's to achieve the same outcomes. As such, the below should be used as a reference to understand what those functions do _if you need them_, for example if you are supporting legacy code or need to run code in a highly controlled environment that does not have the aforementioned packages installed.

## apply

Used to evaluate a function over the margins of a matrix/array. It can apply a function by dimension (row/col).  

`apply` takes the following arguments: 
* `X` - input
* `MARGIN` - to distinguish by row or column. `1` - row, `2` - column
* `FUN` - function to be applied
* `...` is for other arguments to be passed into fun

For example to apply the function `range` across all columns in `Theoph`:


```r
apply(Theoph, 2, range)
#>      Subject Wt     Dose   Time    conc   
#> [1,] "1"     "54.6" "3.10" " 0.00" " 0.00"
#> [2,] "9"     "86.4" "5.86" "24.65" "11.40"
str(apply(Theoph, 2, range))
#>  chr [1:2, 1:5] "1" "9" "54.6" "86.4" "3.10" "5.86" ...
#>  - attr(*, "dimnames")=List of 2
#>   ..$ : NULL
#>   ..$ : chr [1:5] "Subject" "Wt" "Dose" "Time" ...
```

Notice:
- the 'range' function does not have '()' around it.
- The results are all characters - since `apply` goes over by dimension and needs a matrix/array, the whole data structure is coerced to the highest level. Since Subject is an ordered factor for `Theoph` the whole data-frame is coerced to a matrix of type `char` before the function is applied!

*tidbit* - apply is a good opportunity to use colMeans/colSums and rowMeans/rowSums - they are significantly faster, especially for large data structures.


## sapply

sapply is similar to lapply, the biggest difference is it attempts to simplify the result.


```r
lapply(Theoph, range)
#> $Subject
#> [1] 6 5
#> Levels: 6 < 7 < 8 < 11 < 3 < 2 < 4 < 9 < 12 < 10 < 1 < 5
#> 
#> $Wt
#> [1] 54.6 86.4
#> 
#> $Dose
#> [1] 3.10 5.86
#> 
#> $Time
#> [1]  0.0 24.6
#> 
#> $conc
#> [1]  0.0 11.4
sapply(Theoph, range)
#>      Subject   Wt Dose Time conc
#> [1,]       1 54.6 3.10  0.0  0.0
#> [2,]      12 86.4 5.86 24.6 11.4
str(lapply(Theoph, range))
#> List of 5
#>  $ Subject: Ord.factor w/ 12 levels "6"<"7"<"8"<"11"<..: 1 12
#>  $ Wt     : num [1:2] 54.6 86.4
#>  $ Dose   : num [1:2] 3.1 5.86
#>  $ Time   : num [1:2] 0 24.6
#>  $ conc   : num [1:2] 0 11.4
str(sapply(Theoph, range))
#>  num [1:2, 1:5] 1 12 54.6 86.4 3.1 ...
#>  - attr(*, "dimnames")=List of 2
#>   ..$ : NULL
#>   ..$ : chr [1:5] "Subject" "Wt" "Dose" "Time" ...
class(lapply(Theoph, range))
#> [1] "list"
class(sapply(Theoph, range))
#> [1] "matrix"
typeof(lapply(Theoph, range))
#> [1] "list"
typeof(sapply(Theoph, range))
#> [1] "double"
```

Generally, the "rules" for `sapply` can be thought-of as:
* Result is a list where every element is length 1 - returns a vector
* Result list where every elemtn is same length - returns a matrix
* Neither of above - returns list

## vapply

 preferred over sapply due to minor speed improvement and better consistency with return types. Can read more about it on [stack overflow here](http://stackoverflow.com/questions/12339650/why-is-vapply-safer-than-sapply)
 
 The way `vapply` works is you also specific implicitly the type of value you expect it to return.
 
`vapply(<values>, <function>, <returntype>)`


```r
sapply(Theoph[c("Wt", "conc")], summary)
#>           Wt  conc
#> Min.    54.6  0.00
#> 1st Qu. 63.6  2.88
#> Median  70.5  5.28
#> Mean    69.6  4.96
#> 3rd Qu. 74.4  7.14
#> Max.    86.4 11.40

vapply(Theoph[c("Wt", "conc")], summary, c(Min. = 0, "1st Qu." = 0, Median = 0, mean = 0,  "3rd Qu." = 0, Max. = 0))
#>           Wt  conc
#> Min.    54.6  0.00
#> 1st Qu. 63.6  2.88
#> Median  70.5  5.28
#> mean    69.6  4.96
#> 3rd Qu. 74.4  7.14
#> Max.    86.4 11.40
```

But unlike sapply, which will give back whatever, vapply will only work if explicity get back
what you ask for.

```r
sapply(Theoph, summary)
vapply(Theoph, summary, c(Min. = 0, "1st Qu." = 0, Median = 0, mean = 0,  "3rd Qu." = 0, Max. = 0))
```

A common pattern if you just expect a character value, or logical value etc. is to use `character(1)`


```r
sapply(Theoph[, c("Wt", "conc", "Time")], class)
#>        Wt      conc      Time 
#> "numeric" "numeric" "numeric"
vapply(Theoph[, c("Wt", "conc", "Time")], class, character(1))
#>        Wt      conc      Time 
#> "numeric" "numeric" "numeric"
```

Again, vapply will catch so wouldn't run into unexpected output. This can be key when you are testing conditions (eg if class == "<certain_class>") where the test conditions are very specific, so things like passing a 2 element vector for the class would cause the whole function to error. It is better to catch it early than for things to blow up unexpectedly.


```r
sapply(Theoph, class)
vapply(Theoph, class, character(1))
```

## tapply

Used to apply functions to subsets of a vector. I will briefly give some detail, though my person preference is to use `dplyr` commands with `dplyr::group_by` when I'm dealing with subsets. It is good to know, however, in case you are unable to install dplyr for whatever reason.

`tapply` takes the following arguments: 
* `X` - input
* `INDEX` is a factor or list of factors
* `FUN` - function to be applied
* `...` is for other arguments to be passed into fun
* `simplify` - logical for whether to simplify result or not

**note**: INDEX must be a factor or list of factors - if not any value passed in will be coerced to factor.


```r
with(Theoph, tapply(conc, Wt, mean))
#> 54.6 58.2 60.5 64.6   65 70.5 72.4 72.7 79.6   80 86.4 
#> 5.78 5.93 5.41 3.91 4.51 4.68 4.82 4.94 6.44 3.53 4.89
```

Without using `with`:


```r
tapply(Theoph$conc, Theoph$Wt, mean)
#> 54.6 58.2 60.5 64.6   65 70.5 72.4 72.7 79.6   80 86.4 
#> 5.78 5.93 5.41 3.91 4.51 4.68 4.82 4.94 6.44 3.53 4.89
```
