---
title: Cookbook Polars for R - 1 First steps with Polars
---

# Cookbook Polars for R - 1 First steps with Polars

url:: https://ddotta.github.io/cookbook-rpolars/first_steps.html
up: [[sources]]

#source

First of all we need to install all the packages and create an big random dataset needed for this book to work, so don’t bother with the following code:  
Code  

    #cb1-1# Installation of packages for cookbook-rpolars
    #cb1-2packages <- c('dplyr','data.table','tidyr','arrow','DBI','fakir','tictoc','duckdb','microbenchmark','readr','fs','ggplot2','pryr','dbplyr','forcats','collapse')
    #cb1-3installed_packages <- packages %in% rownames(installed.packages())
    #cb1-4if (any(installed_packages == FALSE)) {
    #cb1-5  install.packages(packages[!installed_packages], dependencies = TRUE)
    #cb1-6}
    #cb1-7
    #cb1-8# Loading packages
    #cb1-9invisible(lapply(packages, library, character.only = TRUE))
    #cb1-10
    #cb1-11# Creation of iris_dt
    #cb1-12iris_dt <- as.data.table(iris)
## Installation

Until the R polars package is uploaded to CRAN, [the polars package development team](https://github.com/pola-rs/r-polars/graphs/contributors) offers [several solutions](https://rpolars.github.io/#install) for installation.

The most practical one in my opinion at the moment is to use [R-universe](https://r-universe.dev/search/) and install like this:  

    #cb2-1install.packages("polars", repos = "https://rpolars.r-universe.dev")

To know the version of the `polars` package you have just installed and to have information on which features are enabled, you can use the `polars_info()` function.  

    #cb3-1library(polars)
    #cb3-2
    #cb3-3polars_info()
    Polars R package version : 0.16.2
    Rust Polars crate version: 0.39.2

    Thread pool size: 4 

    Features:                               
    default                    TRUE
    full_features              TRUE
    disable_limit_max_threads  TRUE
    nightly                    TRUE
    sql                        TRUE
    rpolars_debug_print       FALSE

    Code completion: deactivated 
## First glimpse

Polars’ main functions are stored in the “pl” namespace and can be accessed using the “pl$” prefix to prevent conflicts with other packages and base R function names. For more, see [here](https://rpolars.github.io/reference/pl_pl/#examples).

### Convert a R data.frame to a polars DataFrame

First example to convert the most famous R data frame ([iris](https://archive.ics.uci.edu/ml/datasets/iris)) to a Polars DataFrame:  

    #cb5-1iris_polars <- pl$DataFrame(iris)
    #cb5-2iris_polars
    shape: (150, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬───────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species   │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---       │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat       │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═══════════╡
    │ 5.1          ┆ 3.5         ┆ 1.4          ┆ 0.2         ┆ setosa    │
    │ 4.9          ┆ 3.0         ┆ 1.4          ┆ 0.2         ┆ setosa    │
    │ 4.7          ┆ 3.2         ┆ 1.3          ┆ 0.2         ┆ setosa    │
    │ 4.6          ┆ 3.1         ┆ 1.5          ┆ 0.2         ┆ setosa    │
    │ 5.0          ┆ 3.6         ┆ 1.4          ┆ 0.2         ┆ setosa    │
    │ …            ┆ …           ┆ …            ┆ …           ┆ …         │
    │ 6.7          ┆ 3.0         ┆ 5.2          ┆ 2.3         ┆ virginica │
    │ 6.3          ┆ 2.5         ┆ 5.0          ┆ 1.9         ┆ virginica │
    │ 6.5          ┆ 3.0         ┆ 5.2          ┆ 2.0         ┆ virginica │
    │ 6.2          ┆ 3.4         ┆ 5.4          ┆ 2.3         ┆ virginica │
    │ 5.9          ┆ 3.0         ┆ 5.1          ┆ 1.8         ┆ virginica │
    └──────────────┴─────────────┴──────────────┴─────────────┴───────────┘
### Count the number of lines

For example, **to count the number of lines** of the iris data frame :  
* polars
* R base

    #cb7-1# With pl$ prefix
    #cb7-2pl$DataFrame(iris)$height
    #cb9-1# Using iris_polars
    #cb9-2iris_polars$height
## Data Structures

> The core base data structures provided by Polars are Series and DataFrames.

### Series and vectors

`Series` are a **1-dimensional** data structure. Within a series all elements have the same Data Type.

In Polars objects, `Series` object are like R `vectors`.  
To create a Polars Series from scratch:  
* polars
* R base

    #cb21-1mynumbers_serie <- pl$Series(1:3)

    Warning in pl$Series(1:3): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    polars Series: shape: (3,)
    Series: '' [i32]
    [
        1
        2
        3
    ]
    #cb25-1myletters_serie <- pl$Series(c("a","b","c"))

    Warning in pl$Series(c("a", "b", "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    polars Series: shape: (3,)
    Series: '' [str]
    [
        "a"
        "b"
        "c"
    ]
    #cb29-1# To name a Series
    #cb29-2pl$Series(name = "myletters", c("a","b","c"))
    Warning in pl$Series(name = "myletters", c("a", "b", "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    polars Series: shape: (3,)
    Series: 'myletters' [str]
    [
        "a"
        "b"
        "c"
    ]
    #cb32-1mynumbers_vector <- 1:3
    #cb32-2mynumbers_vector
    #cb34-1myletters_vector <- c("a","b","c")
    #cb34-2myletters_vector
### DataFrame and data.frame

A `DataFrame` is a **2-dimensional** data structure that is backed by a `Series`, and it can be seen as an abstraction of a collection (e.g. list) of Series.

In polars objects, `DataFrame` object are like R `data.frame` and close to a `tibble` and a `data.table` object. `DataFrame` has some attributes and you can see [here](https://ddotta.github.io/cookbook-rpolars/data_manipulation.html#initial-informations-on-a-dataframe) to know how you can use it.

**To create a Polars DataFrame from scratch:**  
* polars
* R base
* dplyr
* data.table

    #cb36-1# Creation of a DataFrame object with Series
    #cb36-2mydf <- pl$DataFrame(
    #cb36-3  col1 = mynumbers_serie,
    #cb36-4  col2 = myletters_serie
    #cb36-5)
    #cb36-6# Creation of a DataFrame object with Series and vectors
    #cb36-7pl$DataFrame(
    #cb36-8  col1 = mynumbers_serie,
    #cb36-9  col2 = myletters_vector
    #cb36-10)
    shape: (3, 2)
    ┌──────┬──────┐
    │ col1 ┆ col2 │
    │ ---  ┆ ---  │
    │ i32  ┆ str  │
    ╞══════╪══════╡
    │ 1    ┆ a    │
    │ 2    ┆ b    │
    │ 3    ┆ c    │
    └──────┴──────┘
    #cb38-1data.frame(
    #cb38-2  col1 = mynumbers_vector,
    #cb38-3  col2 = myletters_vector
    #cb38-4)
      col1 col2
    1    1    a
    2    2    b
    3    3    c
    #cb40-1tibble(
    #cb40-2  col1 = mynumbers_vector,
    #cb40-3  col2 = myletters_vector
    #cb40-4)
    # A tibble: 3 × 2
       col1 col2 
      <int> <chr>
    1     1 a    
    2     2 b    
    3     3 c    
    #cb42-1data.table(
    #cb42-2  col1 = mynumbers_vector,
    #cb42-3  col2 = myletters_vector
    #cb42-4)
        col1   col2
       <int> <char>
    1:     1      a
    2:     2      b
    3:     3      c
#### Missing values

As in arrow, **missing data** is represented in Polars with a `null` value. This `null` missing value applies for all data types including numerical values.

You can manually define a missing value using `NA` value in R:  

    #cb44-1pl$DataFrame(
    #cb44-2  col1 = pl$Series(c(NA,"b","c"))
    #cb44-3)
    Warning in pl$Series(c(NA, "b", "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    shape: (3, 1)
    ┌──────┐
    │ col1 │
    │ ---  │
    │ str  │
    ╞══════╡
    │ null │
    │ b    │
    │ c    │
    └──────┘
To learn more about dealing with missing values in polars, see [here](https://ddotta.github.io/cookbook-rpolars/data_manipulation.html#dealing-with-missing-values).

## Manipulation of Series and DataFrames with R standard functions

`Series` and `DataFrames` can be manipulated **with a lot of standard R functions** .  
Some examples with `Series`:  

    #cb49-1paste(myletters_serie,collapse = "")

Some examples with `DataFrames`:

## Expressions

Here I’m quoting what Damian Skrzypiec said [in his blog](https://dskrzypiec.dev/polars/) about Polars expressions:
> One of fundamental building blocks in Polars are Polars expressions. In general Polars expression is any function that transforms Polars series into another Polars series. There are few advantageous aspects of Polars expressions. Firstly expressions are optimized. Particularly if expression need to be executed on multiple columns, then it will be parallelized. It’s one of reasons behind Polars high performance. Another aspect is the fact the Polars implements an extensive set of builtin expressions that user can compose (chain) into more complex expressions.

This is what an Polars expression looks like:  

    #cb55-1pl$col("Petal.Length")$round(decimals = 0)$alias("Petal.Length.rounded")

Which means that: - Select column “Petal.Length” - Then round the column with 0 decimals - Then rename the column “Petal.Length.rounded”  
Every expression produces a new expression, and that they can be piped together.

For example:  

    #cb56-1pl$col("bar")$filter(pl$col("foo") == 1)$sum()

To learn more about Polars expressions, see [the official documentation](https://pola-rs.github.io/polars-book/user-guide/concepts/expressions/).

---

**If you have read this far and managed to reproduce the examples, congratulations! You are ready to dive into the deep end of Polars with R in the next parts of this cookbook!** 🚀

## DataFrames display on Windows

This section is for Windows and RStudio users only!

As a Windows and [RStudio](https://posit.co/download/rstudio-desktop/) user, you may encounter a problem with the display of `Polars` DataFrames.

Here’s what can happen with the default font in RStudio **Lucida Console**:  
![Displaying the mtcars DataFrame with Lucida Console font.](https://ddotta.github.io/cookbook-rpolars/content/images/polars_lucida_console.png)

To resolve this display problem, I recommend using the **Cascadia** font:  
![Displaying the mtcars DataFrame with Cascadia font.](https://ddotta.github.io/cookbook-rpolars/content/images/polars_cascadia.png)
