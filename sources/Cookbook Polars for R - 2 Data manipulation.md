---
title: Cookbook Polars for R - 2 Data manipulation
---

# Cookbook Polars for R - 2 Data manipulation

url:: https://ddotta.github.io/cookbook-rpolars/data_manipulation.html
up: [[sources]]

#source

Below is the code used to create the fake data needed to run code in this document. Click to expand it! 👇  
Code  

    #cb1-1library(polars)
    #cb1-2library(dplyr)
    #cb1-3library(tidyr)
    #cb1-4library(data.table)
    #cb1-5library(arrow)
    #cb1-6library(microbenchmark)
    #cb1-7iris_dt <- as.data.table(iris)
    #cb1-8
    #cb1-9# Creation of Series
    #cb1-10mynumbers_serie <- pl$Series(1:3)
    #cb1-11myletters_serie <- pl$Series(c("a","b","c"))
    #cb1-12
    #cb1-13# Creation of mydf DataFrame 
    #cb1-14mydf <- pl$DataFrame(
    #cb1-15  col1 = mynumbers_serie,
    #cb1-16  col2 = myletters_serie
    #cb1-17)
## Introduction to methods

The added value of Polars consists in the `methods`. Those powerful methods are accessed using the `$` operator.

* For `Series`, see [this section](#list-of-methods-to-apply-to-series) for the methods available in {polars}.
* For `DataFrames`, see [this section](#list-of-methods-to-apply-to-dataframe) for the methods available in {polars}.

Some examples with `Series`:  

    #cb2-1# To get a sum
    #cb2-2mynumbers_serie$sum()
    #cb4-1# To sort
    #cb4-2mynumbers_serie$sort()
    polars Series: shape: (3,)
    Series: '' [i32]
    [
        1
        2
        3
    ]
Some examples with `DataFrame`:  

    #cb6-1# To get a character vector of column names
    #cb6-2mydf$columns
    #cb8-1# To get dimensions of DataFrame
    #cb8-2mydf$shape
    #cb10-1# We can mix standard R functions and methods
    #cb10-2length(mydf$columns)
Polars includes a very useful chaining method in data manipulation operations. From this point of view, Polars is more like dplyr and data.table. This is how the chaining method is defined in the official documentation:
> In polars our method chaining syntax takes the form `object$m1()$m2()`, where object is our data `object`, and `m1()` and `m2()` are appropriate methods, like subsetting or aggregation expressions.

Let’s see an example with the iris dataset:  
* polars
* R base
* dplyr
* data.table

    #cb12-1pl$DataFrame(iris)$
    #cb12-2  group_by(
    #cb12-3  "Species")$
    #cb12-4  median()
    shape: (3, 5)
    ┌────────────┬──────────────┬─────────────┬──────────────┬─────────────┐
    │ Species    ┆ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width │
    │ ---        ┆ ---          ┆ ---         ┆ ---          ┆ ---         │
    │ cat        ┆ f64          ┆ f64         ┆ f64          ┆ f64         │
    ╞════════════╪══════════════╪═════════════╪══════════════╪═════════════╡
    │ virginica  ┆ 6.5          ┆ 3.0         ┆ 5.55         ┆ 2.0         │
    │ versicolor ┆ 5.9          ┆ 2.8         ┆ 4.35         ┆ 1.3         │
    │ setosa     ┆ 5.0          ┆ 3.4         ┆ 1.5          ┆ 0.2         │
    └────────────┴──────────────┴─────────────┴──────────────┴─────────────┘
    #cb14-1aggregate(. ~ Species, iris, median)

         Species Sepal.Length Sepal.Width Petal.Length Petal.Width
    1     setosa          5.0         3.4         1.50         0.2
    2 versicolor          5.9         2.8         4.35         1.3
    3  virginica          6.5         3.0         5.55         2.0
    #cb16-1iris |>
    #cb16-2  group_by(Species) |>
    #cb16-3  summarise(across(everything(),median))
    # A tibble: 3 × 5
      Species    Sepal.Length Sepal.Width Petal.Length Petal.Width
      <fct>             <dbl>       <dbl>        <dbl>       <dbl>
    1 setosa              5           3.4         1.5          0.2
    2 versicolor          5.9         2.8         4.35         1.3
    3 virginica           6.5         3           5.55         2  
    #cb18-1iris_dt[, lapply(.SD, median), by = Species]

          Species Sepal.Length Sepal.Width Petal.Length Petal.Width
           <fctr>        <num>       <num>        <num>       <num>
    1:     setosa          5.0         3.4         1.50         0.2
    2: versicolor          5.9         2.8         4.35         1.3
    3:  virginica          6.5         3.0         5.55         2.0
In the Polars code used above, you will notice that we have introduced **line breaks**. We could have written the whole code on the same line but for the sake of readability I prefer to separate the methods used by line breaks.

## Conversion between Series/DataFrames and vector/data.frames

### From vector/data.frames to Series/DataFrames

These conversions have already been seen earlier in this book.  

    #cb20-1# To convert vector to Polars Series
    #cb20-2myvector <- pl$Series(c("a","b","c"))
    Warning in pl$Series(c("a", "b", "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb22-1# To convert data.frames to DataFrames
    #cb22-2iris_polars <- pl$DataFrame(iris)
### From Series/DataFrames to vector/data.frames

Here, we can use `to_r()` and `to_data_frame()` methods.  

    #cb23-1# To convert Polars Series to vector or list
    #cb23-2myletters_serie$to_r()
    #cb25-1# To convert DataFrames to data.frames
    #cb25-2mydf$to_data_frame()
      col1 col2
    1    1    a
    2    2    b
    3    3    c
## Initial informations on a DataFrame

Here is a list of instructions that I frequently use to quickly get information about a `DataFrame`.

### Get the schema

A `DataFrame` has a `schema` attribute which is a named list of DataTypes.  

    $col1
    DataType: Int32

    $col2
    DataType: String
    #cb29-1# This works also on LazyFrame
    #cb29-2mydf$lazy()$schema
    $col1
    DataType: Int32

    $col2
    DataType: String
### Get the dimensions

A `DataFrame` has a `shape` attribute which is a two length numeric vector of c(nrows,ncols).

### Get columns types

A `DataFrame` has a `dtypes` attribute which is a list of dtypes (for data type) for every column of the `DataFrame`.

Alternatively, the `dtype_strings()` method can be used to get columns types in a character/string vector.  

    #cb33-1# With dtypes attribute (with a "s" and without parentheses)
    #cb33-2mydf$dtypes
    [[1]]
    DataType: Int32

    [[2]]
    DataType: String
    #cb35-1# This works also on LazyFrame
    #cb35-2mydf$lazy()$dtypes
    [[1]]
    DataType: Int32

    [[2]]
    DataType: String
    #cb37-1# With dtype_strings() method (wihout a "s" and with parentheses)
    #cb37-2mydf$dtype_strings()
### Get a glimpse

You can access a dense preview of a `DataFrame` by using the `glimpse()` method. f The formatting is done one line per column, so wide `DataFrame` show nicely. Each line will show the column name, the dtypes attributes and the first few values.  

    #cb39-1pl$DataFrame(iris)$glimpse()

    & Sepal.Length <f64> 5.1, 4.9, 4.7, 4.6, 5, 5.4, 4.6, 5, 4.4, 4.9
    & Sepal.Width  <f64> 3.5, 3, 3.2, 3.1, 3.6, 3.9, 3.4, 3.4, 2.9, 3.1
    & Petal.Length <f64> 1.4, 1.4, 1.3, 1.5, 1.4, 1.7, 1.4, 1.5, 1.4, 1.5
    & Petal.Width  <f64> 0.2, 0.2, 0.2, 0.2, 0.2, 0.4, 0.3, 0.2, 0.2, 0.1
    & Species      <cat> setosa, setosa, setosa, setosa, setosa, setosa, setosa, setosa, setosa, setosa
### Get summary statistics

You can access some summary statistics from a `DataFrame` by using the `describe()` method.  

    #cb41-1pl$DataFrame(iris)$describe()

    shape: (9, 6)
    ┌────────────┬──────────────────┬──────────────────┬─────────────────┬─────────────────┬───────────┐
    │ statistic  ┆ Sepal.Length     ┆ Sepal.Width      ┆ Petal.Length    ┆ Petal.Width     ┆ Species   │
    │ ---        ┆ ---              ┆ ---              ┆ ---             ┆ ---             ┆ ---       │
    │ str        ┆ str              ┆ str              ┆ str             ┆ str             ┆ str       │
    ╞════════════╪══════════════════╪══════════════════╪═════════════════╪═════════════════╪═══════════╡
    │ count      ┆ 150              ┆ 150              ┆ 150             ┆ 150             ┆ 150       │
    │ null_count ┆ 0                ┆ 0                ┆ 0               ┆ 0               ┆ 0         │
    │ mean       ┆ 5.84333333333333 ┆ 3.05733333333333 ┆ 3.7579999999999 ┆ 1.1993333333333 ┆ null      │
    │            ┆ 4                ┆ 32               ┆ 996             ┆ 336             ┆           │
    │ std        ┆ 0.82806612797786 ┆ 0.43586628493669 ┆ 1.7652982332594 ┆ 0.7622376689603 ┆ null      │
    │            ┆ 3                ┆ 82               ┆ 664             ┆ 466             ┆           │
    │ min        ┆ 4.3              ┆ 2.0              ┆ 1.0             ┆ 0.1             ┆ setosa    │
    │ 25%        ┆ 5.1              ┆ 2.8              ┆ 1.6             ┆ 0.3             ┆ null      │
    │ 50%        ┆ 5.8              ┆ 3.0              ┆ 4.4             ┆ 1.3             ┆ null      │
    │ 75%        ┆ 6.4              ┆ 3.3              ┆ 5.1             ┆ 1.8             ┆ null      │
    │ max        ┆ 7.9              ┆ 4.4              ┆ 6.9             ┆ 2.5             ┆ virginica │
    └────────────┴──────────────────┴──────────────────┴─────────────────┴─────────────────┴───────────┘
### Get the rows number

A `DataFrame` has a `width` attribute which returns the width of the `DataFrame`.  

    #cb43-1# With width attribute
    #cb43-2mydf$width
### Get the columns number

A `DataFrame` has a `height` attribute which returns the width of the `DataFrame`.  

    #cb45-1# With height attribute
    #cb45-2mydf$height
### Get the columns names

A `DataFrame` has a `columns` attribute which returns the columns names of the `DataFrame` in a character vector.  

    #cb49-1# This works also on LazyFrame
    #cb49-2mydf$lazy()$columns
### Rename columns

A character vector can be passed to the `columns` attribute to rename the columns names of the `DataFrame`.  

    #cb51-1mydf$columns <- c("colA","colB")
    #cb51-2mydf$columns
### Get the size

The `estimated_size()` method can be used to get an estimation of the total allocated size (in Bytes) of a `DataFrame`.

### Get the first n rows

The `head()` method can be used to get the first n rows of a `DataFrame`.  

    #cb55-1# To get the 2 first rows
    #cb55-2mydf$head(2)
    shape: (2, 2)
    ┌──────┬──────┐
    │ colA ┆ colB │
    │ ---  ┆ ---  │
    │ i32  ┆ str  │
    ╞══════╪══════╡
    │ 1    ┆ a    │
    │ 2    ┆ b    │
    └──────┴──────┘
### Count values in a DataFrame

The `value_counts()` method can be used to count values in a Series of a DataFrame. `value_counts()` works with a `Series`. It must therefore be supplied either with square brackets or with the `select()` method. See [here](#select-columns) to learn about it.  
* polars
* R base
* dplyr
* data.table

    #cb57-1# 1st option with square brackets
    #cb57-2pl$DataFrame(iris)[,c("Species")]$value_counts()
    shape: (3, 2)
    ┌────────────┬───────┐
    │ Species    ┆ count │
    │ ---        ┆ ---   │
    │ cat        ┆ u32   │
    ╞════════════╪═══════╡
    │ setosa     ┆ 50    │
    │ versicolor ┆ 50    │
    │ virginica  ┆ 50    │
    └────────────┴───────┘
    #cb59-1# 2nd option with select() method
    #cb59-2pl$DataFrame(iris)$select(pl$col("Species"))$to_series()$value_counts()
    shape: (3, 2)
    ┌────────────┬───────┐
    │ Species    ┆ count │
    │ ---        ┆ ---   │
    │ cat        ┆ u32   │
    ╞════════════╪═══════╡
    │ setosa     ┆ 50    │
    │ versicolor ┆ 50    │
    │ virginica  ┆ 50    │
    └────────────┴───────┘

        setosa versicolor  virginica 
            50         50         50 
         Species  n
    1     setosa 50
    2 versicolor 50
    3  virginica 50
    #cb65-1iris_dt[, .N, by = Species]

          Species     N
           <fctr> <int>
    1:     setosa    50
    2: versicolor    50
    3:  virginica    50
### Count NA over columns in a DataFrame

The `null_count()` method can be used to count NA values of a DataFrame.  
* polars
* R base
* dplyr
* data.table

    #cb67-1mydfNA <- pl$DataFrame(
    #cb67-2  colA = pl$Series(c("a",NA,"c")),
    #cb67-3  colB = pl$Series(c("d",NA,NA)))
    Warning in pl$Series(c("a", NA, "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("d", NA, NA)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    shape: (1, 2)
    ┌──────┬──────┐
    │ colA ┆ colB │
    │ ---  ┆ ---  │
    │ u32  ┆ u32  │
    ╞══════╪══════╡
    │ 1    ┆ 2    │
    └──────┴──────┘
    #cb72-1mydfNA <- data.frame(
    #cb72-2  colA = c("a",NA,"c"),
    #cb72-3  colB = c("d",NA,NA))
    #cb72-4sapply(mydfNA, function(x) sum(is.na(x)))
    #cb74-1mydfNA |>
    #cb74-2  summarise(across(everything(), ~sum(is.na(.))))
    #cb76-1mydfNA_dt <- as.data.table(mydfNA)
    #cb76-2mydfNA_dt[, lapply(.SD, function(x) sum(is.na(x))), .SDcols = names(mydfNA_dt)]
        colA  colB
       <int> <int>
    1:     1     2
## Filter rows

The first option to filter rows of a DataFrame is to use square brackets `[]` indexing (with integer row number).  

    #cb78-1data(iris)
    #cb78-2# The first four lines
    #cb78-3pl$DataFrame(iris)[1:4,]
    shape: (4, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬─────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---     │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat     │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═════════╡
    │ 5.1          ┆ 3.5         ┆ 1.4          ┆ 0.2         ┆ setosa  │
    │ 4.9          ┆ 3.0         ┆ 1.4          ┆ 0.2         ┆ setosa  │
    │ 4.7          ┆ 3.2         ┆ 1.3          ┆ 0.2         ┆ setosa  │
    │ 4.6          ┆ 3.1         ┆ 1.5          ┆ 0.2         ┆ setosa  │
    └──────────────┴─────────────┴──────────────┴─────────────┴─────────┘
    #cb80-1# The lines 1, 3 and 5
    #cb80-2pl$DataFrame(iris)[c(1,3,5),]
    shape: (3, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬─────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---     │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat     │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═════════╡
    │ 5.1          ┆ 3.5         ┆ 1.4          ┆ 0.2         ┆ setosa  │
    │ 4.7          ┆ 3.2         ┆ 1.3          ┆ 0.2         ┆ setosa  │
    │ 5.0          ┆ 3.6         ┆ 1.4          ┆ 0.2         ┆ setosa  │
    └──────────────┴─────────────┴──────────────┴─────────────┴─────────┘
It’s convenient when you have to quickly inspect your data. But you’ll quickly be limited by the square brackets, as they don’t accept conditions with the expressions. For example `pl$DataFrame(iris)[Petal.Length > 6]` doesn’t work.

The second and best option is to use the `filter()` method. It must be used with the `Polars expression`, here the `col()` method which allows to designate the columns on which the filter condition will be applied.

Let’s see in details what’s inside a `filter()` method with an example:

* `pl$col("Petal.Length")`: this expression selects the `Petal.Length` column from iris;  
* `>6`: applies a Boolean condition to this expression (for all Petals that have a length > 6).

In the example below, we will use `&` operator to apply multiple conditions in `filter()` method:  
* polars
* R base
* dplyr
* data.table

    #cb82-1pl$DataFrame(iris)$filter(
    #cb82-2  pl$col("Petal.Length") > 6 & pl$col("Petal.Width") < 2)
    shape: (2, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬───────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species   │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---       │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat       │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═══════════╡
    │ 7.3          ┆ 2.9         ┆ 6.3          ┆ 1.8         ┆ virginica │
    │ 7.4          ┆ 2.8         ┆ 6.1          ┆ 1.9         ┆ virginica │
    └──────────────┴─────────────┴──────────────┴─────────────┴───────────┘
    #cb84-1iris[iris$Petal.Length > 6 & iris$Petal.Width < 2,] # here don't forget the comma

        Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
    108          7.3         2.9          6.3         1.8 virginica
    131          7.4         2.8          6.1         1.9 virginica
    #cb86-1iris |>
    #cb86-2  filter(Petal.Length > 6 & Petal.Width < 2) 
      Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
    1          7.3         2.9          6.3         1.8 virginica
    2          7.4         2.8          6.1         1.9 virginica
    #cb88-1iris_dt[Petal.Length > 6 & Petal.Width < 2]

       Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
              <num>       <num>        <num>       <num>    <fctr>
    1:          7.3         2.9          6.3         1.8 virginica
    2:          7.4         2.8          6.1         1.9 virginica
The equivalent of `%in%` R operator is the `is_in()` method. It should be used in association with the `lit()` method.  

    #cb90-1pl$DataFrame(
    #cb90-2  colA = pl$Series(c("a","b","c"))
    #cb90-3  )$filter(
    #cb90-4  pl$col("colA")$is_in(pl$lit(c("a","b")))
    #cb90-5  )
    Warning in pl$Series(c("a", "b", "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    shape: (2, 1)
    ┌──────┐
    │ colA │
    │ ---  │
    │ str  │
    ╞══════╡
    │ a    │
    │ b    │
    └──────┘
Another reason for using the `filter()` method is that filter expressions can be optimised in [lazy mode](https://ddotta.github.io/cookbook-rpolars/lazy_execution.html#introduction-to-lazy-mode) by the query optimiser. Square brackets `[]` can only be used in **eager mode**.  
There is another way **to speed up filter processing on rows** : tell polars that the column(s) used to filter rows are already sorted! To do this, you can use the `set_sorted()` method.  
Here’s an example:  

    #cb93-1mydf <- pl$DataFrame(
    #cb93-2  col1 = pl$Series(sort(runif(10000000)))
    #cb93-3)
    Warning in pl$Series(sort(runif(1e+07))): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb95-1microbenchmark(
    #cb95-2  "Without telling col1 is sorted" = mydf$filter(pl$col("col1") < 100),
    #cb95-3  "Telling col1 is sorted" = mydf$with_columns(pl$col("col1")$set_sorted())$filter(pl$col("col1") < 100)
    #cb95-4  )
    Unit: milliseconds
                               expr      min       lq     mean   median       uq
     Without telling col1 is sorted 6.557458 6.896630 7.235332 7.032378 7.248586
             Telling col1 is sorted 3.868157 4.142338 4.465664 4.343943 4.534148
          max neval
     12.53200   100
     10.22463   100
## Select columns

### Selecting by name

The first option for selecting columns of a DataFrame is to use square brackets `[]`.  
The second option is to use the `select()` method. In this case, it must be used with the `col()` method which allows to designate the columns to be selected with a character vector.  
* polars
* R base
* dplyr
* data.table

    #cb97-1# 1st option : with square brackets syntax
    #cb97-2pl$DataFrame(iris)[1:3,c("Petal.Length","Petal.Width")] 
    shape: (3, 2)
    ┌──────────────┬─────────────┐
    │ Petal.Length ┆ Petal.Width │
    │ ---          ┆ ---         │
    │ f64          ┆ f64         │
    ╞══════════════╪═════════════╡
    │ 1.4          ┆ 0.2         │
    │ 1.4          ┆ 0.2         │
    │ 1.3          ┆ 0.2         │
    └──────────────┴─────────────┘
    #cb99-1# 2nd option : with select() method
    #cb99-2pl$DataFrame(iris)$select(
    #cb99-3  pl$col(c("Petal.Length","Petal.Width"))
    #cb99-4)$head(3) # display the first 3 lines
    shape: (3, 2)
    ┌──────────────┬─────────────┐
    │ Petal.Length ┆ Petal.Width │
    │ ---          ┆ ---         │
    │ f64          ┆ f64         │
    ╞══════════════╪═════════════╡
    │ 1.4          ┆ 0.2         │
    │ 1.4          ┆ 0.2         │
    │ 1.3          ┆ 0.2         │
    └──────────────┴─────────────┘
    #cb101-1iris[1:3,c("Petal.Length","Petal.Width")] 

      Petal.Length Petal.Width
    1          1.4         0.2
    2          1.4         0.2
    3          1.3         0.2
    #cb103-1iris |>
    #cb103-2  select(Petal.Length,Petal.Width) |>
    #cb103-3  head(3)
      Petal.Length Petal.Width
    1          1.4         0.2
    2          1.4         0.2
    3          1.3         0.2
    #cb105-1iris_dt[1:3,.(Petal.Length,Petal.Width)]

       Petal.Length Petal.Width
              <num>       <num>
    1:          1.4         0.2
    2:          1.4         0.2
    3:          1.3         0.2
**Keep in mind that when:**   
- Square brackets are used:  
- **Selecting only one column** from a `DataFrame`, the output is a `Series`;  
- Whereas **if there is more than one column selected** , the output is a `DataFrame`.  
- `select()` method is used:  
- **The output is always a `DataFrame`** rather than a `Series` even if one column is selected.  
=> If you need a `Series` you can use the `to_series()` method. See [here](#get-series-from-dataFrame).  

    #cb107-1inherits(pl$DataFrame(iris)[,"Petal.Length"],"Series")

    #cb109-1inherits(pl$DataFrame(iris)[,"Petal.Length"],"DataFrame")

    #cb111-1inherits(pl$DataFrame(iris)[,c("Petal.Length","Petal.Width")],"DataFrame")

    #cb113-1inherits(pl$DataFrame(iris)[,c("Petal.Length","Petal.Width")],"Series")

With Polars if you want to obtain a result in an R data.frame, you can simply add the method `to_data_frame()` at the end of the method chaining. See [here](#from-seriesdataframes-to-vectordata.frames) for examples.  
Beyond the minor differences discussed above, there are **two major reasons why you should use the `select()` method over the syntax with square brackets:**   
- When you select and transform multiple columns with `select()` method, Polars will run these selections **in parallel** ;  
- Expressions used in `select()` method can be optimised in [lazy mode](https://ddotta.github.io/cookbook-rpolars/lazy_execution.html#introduction-to-lazy-mode) by the query optimizer.

Finally, the `select()` method can also be used to re-order columns of a `DataFrame`.  
For example, to re-order the columns in alphabetical order:  

    #cb115-1pl$DataFrame(iris)$select(
    #cb115-2  pl$col(sort(pl$DataFrame(iris)$columns)))$
    #cb115-3  head(3)
    shape: (3, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬─────────┐
    │ Petal.Length ┆ Petal.Width ┆ Sepal.Length ┆ Sepal.Width ┆ Species │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---     │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat     │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═════════╡
    │ 1.4          ┆ 0.2         ┆ 5.1          ┆ 3.5         ┆ setosa  │
    │ 1.4          ┆ 0.2         ┆ 4.9          ┆ 3.0         ┆ setosa  │
    │ 1.3          ┆ 0.2         ┆ 4.7          ┆ 3.2         ┆ setosa  │
    └──────────────┴─────────────┴──────────────┴─────────────┴─────────┘
### Selecting by data type

To select columns by data type from a `DataFrame`, you can pass a data type to `pl$col` expression. For example:  
* polars
* R base
* dplyr
* data.table

    #cb117-1# Select only Float64 columns
    #cb117-2pl$DataFrame(iris)$select(pl$col(pl$Float64))$head(3)
    shape: (3, 4)
    ┌──────────────┬─────────────┬──────────────┬─────────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width │
    │ ---          ┆ ---         ┆ ---          ┆ ---         │
    │ f64          ┆ f64         ┆ f64          ┆ f64         │
    ╞══════════════╪═════════════╪══════════════╪═════════════╡
    │ 5.1          ┆ 3.5         ┆ 1.4          ┆ 0.2         │
    │ 4.9          ┆ 3.0         ┆ 1.4          ┆ 0.2         │
    │ 4.7          ┆ 3.2         ┆ 1.3          ┆ 0.2         │
    └──────────────┴─────────────┴──────────────┴─────────────┘
    #cb119-1# Select only numeric columns
    #cb119-2pl$DataFrame(iris)$select(pl$col(pl$numeric_dtypes))$head(3) 
    shape: (3, 4)
    ┌──────────────┬─────────────┬──────────────┬─────────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width │
    │ ---          ┆ ---         ┆ ---          ┆ ---         │
    │ f64          ┆ f64         ┆ f64          ┆ f64         │
    ╞══════════════╪═════════════╪══════════════╪═════════════╡
    │ 5.1          ┆ 3.5         ┆ 1.4          ┆ 0.2         │
    │ 4.9          ┆ 3.0         ┆ 1.4          ┆ 0.2         │
    │ 4.7          ┆ 3.2         ┆ 1.3          ┆ 0.2         │
    └──────────────┴─────────────┴──────────────┴─────────────┘
    #cb121-1# Select only factor columns
    #cb121-2as.data.frame(iris[1:3, sapply(iris, is.factor)])
      iris[1:3, sapply(iris, is.factor)]
    1                             setosa
    2                             setosa
    3                             setosa
    #cb123-1# Select only numeric columns
    #cb123-2iris[1:3, sapply(iris, is.numeric)]
      Sepal.Length Sepal.Width Petal.Length Petal.Width
    1          5.1         3.5          1.4         0.2
    2          4.9         3.0          1.4         0.2
    3          4.7         3.2          1.3         0.2
    #cb125-1# Select only factor columns
    #cb125-2iris %>% 
    #cb125-3  select_if(is.factor)
           Species
    1       setosa
    2       setosa
    3       setosa
    4       setosa
    5       setosa
    6       setosa
    7       setosa
    8       setosa
    9       setosa
    10      setosa
    11      setosa
    12      setosa
    13      setosa
    14      setosa
    15      setosa
    16      setosa
    17      setosa
    18      setosa
    19      setosa
    20      setosa
    21      setosa
    22      setosa
    23      setosa
    24      setosa
    25      setosa
    26      setosa
    27      setosa
    28      setosa
    29      setosa
    30      setosa
    31      setosa
    32      setosa
    33      setosa
    34      setosa
    35      setosa
    36      setosa
    37      setosa
    38      setosa
    39      setosa
    40      setosa
    41      setosa
    42      setosa
    43      setosa
    44      setosa
    45      setosa
    46      setosa
    47      setosa
    48      setosa
    49      setosa
    50      setosa
    51  versicolor
    52  versicolor
    53  versicolor
    54  versicolor
    55  versicolor
    56  versicolor
    57  versicolor
    58  versicolor
    59  versicolor
    60  versicolor
    61  versicolor
    62  versicolor
    63  versicolor
    64  versicolor
    65  versicolor
    66  versicolor
    67  versicolor
    68  versicolor
    69  versicolor
    70  versicolor
    71  versicolor
    72  versicolor
    73  versicolor
    74  versicolor
    75  versicolor
    76  versicolor
    77  versicolor
    78  versicolor
    79  versicolor
    80  versicolor
    81  versicolor
    82  versicolor
    83  versicolor
    84  versicolor
    85  versicolor
    86  versicolor
    87  versicolor
    88  versicolor
    89  versicolor
    90  versicolor
    91  versicolor
    92  versicolor
    93  versicolor
    94  versicolor
    95  versicolor
    96  versicolor
    97  versicolor
    98  versicolor
    99  versicolor
    100 versicolor
    101  virginica
    102  virginica
    103  virginica
    104  virginica
    105  virginica
    106  virginica
    107  virginica
    108  virginica
    109  virginica
    110  virginica
    111  virginica
    112  virginica
    113  virginica
    114  virginica
    115  virginica
    116  virginica
    117  virginica
    118  virginica
    119  virginica
    120  virginica
    121  virginica
    122  virginica
    123  virginica
    124  virginica
    125  virginica
    126  virginica
    127  virginica
    128  virginica
    129  virginica
    130  virginica
    131  virginica
    132  virginica
    133  virginica
    134  virginica
    135  virginica
    136  virginica
    137  virginica
    138  virginica
    139  virginica
    140  virginica
    141  virginica
    142  virginica
    143  virginica
    144  virginica
    145  virginica
    146  virginica
    147  virginica
    148  virginica
    149  virginica
    150  virginica
    #cb127-1# Select only numeric columns
    #cb127-2iris %>% 
    #cb127-3  select_if(is.numeric)
        Sepal.Length Sepal.Width Petal.Length Petal.Width
    1            5.1         3.5          1.4         0.2
    2            4.9         3.0          1.4         0.2
    3            4.7         3.2          1.3         0.2
    4            4.6         3.1          1.5         0.2
    5            5.0         3.6          1.4         0.2
    6            5.4         3.9          1.7         0.4
    7            4.6         3.4          1.4         0.3
    8            5.0         3.4          1.5         0.2
    9            4.4         2.9          1.4         0.2
    10           4.9         3.1          1.5         0.1
    11           5.4         3.7          1.5         0.2
    12           4.8         3.4          1.6         0.2
    13           4.8         3.0          1.4         0.1
    14           4.3         3.0          1.1         0.1
    15           5.8         4.0          1.2         0.2
    16           5.7         4.4          1.5         0.4
    17           5.4         3.9          1.3         0.4
    18           5.1         3.5          1.4         0.3
    19           5.7         3.8          1.7         0.3
    20           5.1         3.8          1.5         0.3
    21           5.4         3.4          1.7         0.2
    22           5.1         3.7          1.5         0.4
    23           4.6         3.6          1.0         0.2
    24           5.1         3.3          1.7         0.5
    25           4.8         3.4          1.9         0.2
    26           5.0         3.0          1.6         0.2
    27           5.0         3.4          1.6         0.4
    28           5.2         3.5          1.5         0.2
    29           5.2         3.4          1.4         0.2
    30           4.7         3.2          1.6         0.2
    31           4.8         3.1          1.6         0.2
    32           5.4         3.4          1.5         0.4
    33           5.2         4.1          1.5         0.1
    34           5.5         4.2          1.4         0.2
    35           4.9         3.1          1.5         0.2
    36           5.0         3.2          1.2         0.2
    37           5.5         3.5          1.3         0.2
    38           4.9         3.6          1.4         0.1
    39           4.4         3.0          1.3         0.2
    40           5.1         3.4          1.5         0.2
    41           5.0         3.5          1.3         0.3
    42           4.5         2.3          1.3         0.3
    43           4.4         3.2          1.3         0.2
    44           5.0         3.5          1.6         0.6
    45           5.1         3.8          1.9         0.4
    46           4.8         3.0          1.4         0.3
    47           5.1         3.8          1.6         0.2
    48           4.6         3.2          1.4         0.2
    49           5.3         3.7          1.5         0.2
    50           5.0         3.3          1.4         0.2
    51           7.0         3.2          4.7         1.4
    52           6.4         3.2          4.5         1.5
    53           6.9         3.1          4.9         1.5
    54           5.5         2.3          4.0         1.3
    55           6.5         2.8          4.6         1.5
    56           5.7         2.8          4.5         1.3
    57           6.3         3.3          4.7         1.6
    58           4.9         2.4          3.3         1.0
    59           6.6         2.9          4.6         1.3
    60           5.2         2.7          3.9         1.4
    61           5.0         2.0          3.5         1.0
    62           5.9         3.0          4.2         1.5
    63           6.0         2.2          4.0         1.0
    64           6.1         2.9          4.7         1.4
    65           5.6         2.9          3.6         1.3
    66           6.7         3.1          4.4         1.4
    67           5.6         3.0          4.5         1.5
    68           5.8         2.7          4.1         1.0
    69           6.2         2.2          4.5         1.5
    70           5.6         2.5          3.9         1.1
    71           5.9         3.2          4.8         1.8
    72           6.1         2.8          4.0         1.3
    73           6.3         2.5          4.9         1.5
    74           6.1         2.8          4.7         1.2
    75           6.4         2.9          4.3         1.3
    76           6.6         3.0          4.4         1.4
    77           6.8         2.8          4.8         1.4
    78           6.7         3.0          5.0         1.7
    79           6.0         2.9          4.5         1.5
    80           5.7         2.6          3.5         1.0
    81           5.5         2.4          3.8         1.1
    82           5.5         2.4          3.7         1.0
    83           5.8         2.7          3.9         1.2
    84           6.0         2.7          5.1         1.6
    85           5.4         3.0          4.5         1.5
    86           6.0         3.4          4.5         1.6
    87           6.7         3.1          4.7         1.5
    88           6.3         2.3          4.4         1.3
    89           5.6         3.0          4.1         1.3
    90           5.5         2.5          4.0         1.3
    91           5.5         2.6          4.4         1.2
    92           6.1         3.0          4.6         1.4
    93           5.8         2.6          4.0         1.2
    94           5.0         2.3          3.3         1.0
    95           5.6         2.7          4.2         1.3
    96           5.7         3.0          4.2         1.2
    97           5.7         2.9          4.2         1.3
    98           6.2         2.9          4.3         1.3
    99           5.1         2.5          3.0         1.1
    100          5.7         2.8          4.1         1.3
    101          6.3         3.3          6.0         2.5
    102          5.8         2.7          5.1         1.9
    103          7.1         3.0          5.9         2.1
    104          6.3         2.9          5.6         1.8
    105          6.5         3.0          5.8         2.2
    106          7.6         3.0          6.6         2.1
    107          4.9         2.5          4.5         1.7
    108          7.3         2.9          6.3         1.8
    109          6.7         2.5          5.8         1.8
    110          7.2         3.6          6.1         2.5
    111          6.5         3.2          5.1         2.0
    112          6.4         2.7          5.3         1.9
    113          6.8         3.0          5.5         2.1
    114          5.7         2.5          5.0         2.0
    115          5.8         2.8          5.1         2.4
    116          6.4         3.2          5.3         2.3
    117          6.5         3.0          5.5         1.8
    118          7.7         3.8          6.7         2.2
    119          7.7         2.6          6.9         2.3
    120          6.0         2.2          5.0         1.5
    121          6.9         3.2          5.7         2.3
    122          5.6         2.8          4.9         2.0
    123          7.7         2.8          6.7         2.0
    124          6.3         2.7          4.9         1.8
    125          6.7         3.3          5.7         2.1
    126          7.2         3.2          6.0         1.8
    127          6.2         2.8          4.8         1.8
    128          6.1         3.0          4.9         1.8
    129          6.4         2.8          5.6         2.1
    130          7.2         3.0          5.8         1.6
    131          7.4         2.8          6.1         1.9
    132          7.9         3.8          6.4         2.0
    133          6.4         2.8          5.6         2.2
    134          6.3         2.8          5.1         1.5
    135          6.1         2.6          5.6         1.4
    136          7.7         3.0          6.1         2.3
    137          6.3         3.4          5.6         2.4
    138          6.4         3.1          5.5         1.8
    139          6.0         3.0          4.8         1.8
    140          6.9         3.1          5.4         2.1
    141          6.7         3.1          5.6         2.4
    142          6.9         3.1          5.1         2.3
    143          5.8         2.7          5.1         1.9
    144          6.8         3.2          5.9         2.3
    145          6.7         3.3          5.7         2.5
    146          6.7         3.0          5.2         2.3
    147          6.3         2.5          5.0         1.9
    148          6.5         3.0          5.2         2.0
    149          6.2         3.4          5.4         2.3
    150          5.9         3.0          5.1         1.8
    #cb129-1# Select only factor columns
    #cb129-2iris_dt[, .SD, .SDcols = is.factor]
           Species
            <fctr>
      1:    setosa
      2:    setosa
      3:    setosa
      4:    setosa
      5:    setosa
     ---          
    146: virginica
    147: virginica
    148: virginica
    149: virginica
    150: virginica
    #cb131-1# Select only numeric columns
    #cb131-2iris_dt[, .SD, .SDcols = is.numeric]
         Sepal.Length Sepal.Width Petal.Length Petal.Width
                <num>       <num>        <num>       <num>
      1:          5.1         3.5          1.4         0.2
      2:          4.9         3.0          1.4         0.2
      3:          4.7         3.2          1.3         0.2
      4:          4.6         3.1          1.5         0.2
      5:          5.0         3.6          1.4         0.2
     ---                                                  
    146:          6.7         3.0          5.2         2.3
    147:          6.3         2.5          5.0         1.9
    148:          6.5         3.0          5.2         2.0
    149:          6.2         3.4          5.4         2.3
    150:          5.9         3.0          5.1         1.8
### Selecting with a regex

To select columns with a regex from a `DataFrame`, you can pass it in `pl$col` expression. For example, to select all columns that starts with “Sepal” in iris dataset:  
* polars
* R base
* dplyr
* data.table

    #cb133-1pl$DataFrame(iris)$select(
    #cb133-2  pl$col("^Sepal.*$")
    #cb133-3)$head(3) # display the first 3 lines
    shape: (3, 2)
    ┌──────────────┬─────────────┐
    │ Sepal.Length ┆ Sepal.Width │
    │ ---          ┆ ---         │
    │ f64          ┆ f64         │
    ╞══════════════╪═════════════╡
    │ 5.1          ┆ 3.5         │
    │ 4.9          ┆ 3.0         │
    │ 4.7          ┆ 3.2         │
    └──────────────┴─────────────┘
    #cb135-1iris[, grep("^Sepal.*$", colnames(iris))]

        Sepal.Length Sepal.Width
    1            5.1         3.5
    2            4.9         3.0
    3            4.7         3.2
    4            4.6         3.1
    5            5.0         3.6
    6            5.4         3.9
    7            4.6         3.4
    8            5.0         3.4
    9            4.4         2.9
    10           4.9         3.1
    11           5.4         3.7
    12           4.8         3.4
    13           4.8         3.0
    14           4.3         3.0
    15           5.8         4.0
    16           5.7         4.4
    17           5.4         3.9
    18           5.1         3.5
    19           5.7         3.8
    20           5.1         3.8
    21           5.4         3.4
    22           5.1         3.7
    23           4.6         3.6
    24           5.1         3.3
    25           4.8         3.4
    26           5.0         3.0
    27           5.0         3.4
    28           5.2         3.5
    29           5.2         3.4
    30           4.7         3.2
    31           4.8         3.1
    32           5.4         3.4
    33           5.2         4.1
    34           5.5         4.2
    35           4.9         3.1
    36           5.0         3.2
    37           5.5         3.5
    38           4.9         3.6
    39           4.4         3.0
    40           5.1         3.4
    41           5.0         3.5
    42           4.5         2.3
    43           4.4         3.2
    44           5.0         3.5
    45           5.1         3.8
    46           4.8         3.0
    47           5.1         3.8
    48           4.6         3.2
    49           5.3         3.7
    50           5.0         3.3
    51           7.0         3.2
    52           6.4         3.2
    53           6.9         3.1
    54           5.5         2.3
    55           6.5         2.8
    56           5.7         2.8
    57           6.3         3.3
    58           4.9         2.4
    59           6.6         2.9
    60           5.2         2.7
    61           5.0         2.0
    62           5.9         3.0
    63           6.0         2.2
    64           6.1         2.9
    65           5.6         2.9
    66           6.7         3.1
    67           5.6         3.0
    68           5.8         2.7
    69           6.2         2.2
    70           5.6         2.5
    71           5.9         3.2
    72           6.1         2.8
    73           6.3         2.5
    74           6.1         2.8
    75           6.4         2.9
    76           6.6         3.0
    77           6.8         2.8
    78           6.7         3.0
    79           6.0         2.9
    80           5.7         2.6
    81           5.5         2.4
    82           5.5         2.4
    83           5.8         2.7
    84           6.0         2.7
    85           5.4         3.0
    86           6.0         3.4
    87           6.7         3.1
    88           6.3         2.3
    89           5.6         3.0
    90           5.5         2.5
    91           5.5         2.6
    92           6.1         3.0
    93           5.8         2.6
    94           5.0         2.3
    95           5.6         2.7
    96           5.7         3.0
    97           5.7         2.9
    98           6.2         2.9
    99           5.1         2.5
    100          5.7         2.8
    101          6.3         3.3
    102          5.8         2.7
    103          7.1         3.0
    104          6.3         2.9
    105          6.5         3.0
    106          7.6         3.0
    107          4.9         2.5
    108          7.3         2.9
    109          6.7         2.5
    110          7.2         3.6
    111          6.5         3.2
    112          6.4         2.7
    113          6.8         3.0
    114          5.7         2.5
    115          5.8         2.8
    116          6.4         3.2
    117          6.5         3.0
    118          7.7         3.8
    119          7.7         2.6
    120          6.0         2.2
    121          6.9         3.2
    122          5.6         2.8
    123          7.7         2.8
    124          6.3         2.7
    125          6.7         3.3
    126          7.2         3.2
    127          6.2         2.8
    128          6.1         3.0
    129          6.4         2.8
    130          7.2         3.0
    131          7.4         2.8
    132          7.9         3.8
    133          6.4         2.8
    134          6.3         2.8
    135          6.1         2.6
    136          7.7         3.0
    137          6.3         3.4
    138          6.4         3.1
    139          6.0         3.0
    140          6.9         3.1
    141          6.7         3.1
    142          6.9         3.1
    143          5.8         2.7
    144          6.8         3.2
    145          6.7         3.3
    146          6.7         3.0
    147          6.3         2.5
    148          6.5         3.0
    149          6.2         3.4
    150          5.9         3.0
    #cb137-1iris %>%
    #cb137-2  select(starts_with("Sepal"))
        Sepal.Length Sepal.Width
    1            5.1         3.5
    2            4.9         3.0
    3            4.7         3.2
    4            4.6         3.1
    5            5.0         3.6
    6            5.4         3.9
    7            4.6         3.4
    8            5.0         3.4
    9            4.4         2.9
    10           4.9         3.1
    11           5.4         3.7
    12           4.8         3.4
    13           4.8         3.0
    14           4.3         3.0
    15           5.8         4.0
    16           5.7         4.4
    17           5.4         3.9
    18           5.1         3.5
    19           5.7         3.8
    20           5.1         3.8
    21           5.4         3.4
    22           5.1         3.7
    23           4.6         3.6
    24           5.1         3.3
    25           4.8         3.4
    26           5.0         3.0
    27           5.0         3.4
    28           5.2         3.5
    29           5.2         3.4
    30           4.7         3.2
    31           4.8         3.1
    32           5.4         3.4
    33           5.2         4.1
    34           5.5         4.2
    35           4.9         3.1
    36           5.0         3.2
    37           5.5         3.5
    38           4.9         3.6
    39           4.4         3.0
    40           5.1         3.4
    41           5.0         3.5
    42           4.5         2.3
    43           4.4         3.2
    44           5.0         3.5
    45           5.1         3.8
    46           4.8         3.0
    47           5.1         3.8
    48           4.6         3.2
    49           5.3         3.7
    50           5.0         3.3
    51           7.0         3.2
    52           6.4         3.2
    53           6.9         3.1
    54           5.5         2.3
    55           6.5         2.8
    56           5.7         2.8
    57           6.3         3.3
    58           4.9         2.4
    59           6.6         2.9
    60           5.2         2.7
    61           5.0         2.0
    62           5.9         3.0
    63           6.0         2.2
    64           6.1         2.9
    65           5.6         2.9
    66           6.7         3.1
    67           5.6         3.0
    68           5.8         2.7
    69           6.2         2.2
    70           5.6         2.5
    71           5.9         3.2
    72           6.1         2.8
    73           6.3         2.5
    74           6.1         2.8
    75           6.4         2.9
    76           6.6         3.0
    77           6.8         2.8
    78           6.7         3.0
    79           6.0         2.9
    80           5.7         2.6
    81           5.5         2.4
    82           5.5         2.4
    83           5.8         2.7
    84           6.0         2.7
    85           5.4         3.0
    86           6.0         3.4
    87           6.7         3.1
    88           6.3         2.3
    89           5.6         3.0
    90           5.5         2.5
    91           5.5         2.6
    92           6.1         3.0
    93           5.8         2.6
    94           5.0         2.3
    95           5.6         2.7
    96           5.7         3.0
    97           5.7         2.9
    98           6.2         2.9
    99           5.1         2.5
    100          5.7         2.8
    101          6.3         3.3
    102          5.8         2.7
    103          7.1         3.0
    104          6.3         2.9
    105          6.5         3.0
    106          7.6         3.0
    107          4.9         2.5
    108          7.3         2.9
    109          6.7         2.5
    110          7.2         3.6
    111          6.5         3.2
    112          6.4         2.7
    113          6.8         3.0
    114          5.7         2.5
    115          5.8         2.8
    116          6.4         3.2
    117          6.5         3.0
    118          7.7         3.8
    119          7.7         2.6
    120          6.0         2.2
    121          6.9         3.2
    122          5.6         2.8
    123          7.7         2.8
    124          6.3         2.7
    125          6.7         3.3
    126          7.2         3.2
    127          6.2         2.8
    128          6.1         3.0
    129          6.4         2.8
    130          7.2         3.0
    131          7.4         2.8
    132          7.9         3.8
    133          6.4         2.8
    134          6.3         2.8
    135          6.1         2.6
    136          7.7         3.0
    137          6.3         3.4
    138          6.4         3.1
    139          6.0         3.0
    140          6.9         3.1
    141          6.7         3.1
    142          6.9         3.1
    143          5.8         2.7
    144          6.8         3.2
    145          6.7         3.3
    146          6.7         3.0
    147          6.3         2.5
    148          6.5         3.0
    149          6.2         3.4
    150          5.9         3.0
    #cb139-1iris_dt[, .SD, .SDcols = grep("^Sepal", colnames(iris_dt))]

         Sepal.Length Sepal.Width
                <num>       <num>
      1:          5.1         3.5
      2:          4.9         3.0
      3:          4.7         3.2
      4:          4.6         3.1
      5:          5.0         3.6
     ---                         
    146:          6.7         3.0
    147:          6.3         2.5
    148:          6.5         3.0
    149:          6.2         3.4
    150:          5.9         3.0
### Selecting using lists

It is also possible to select columns from a `DataFrame` using `R lists` which can be very practical depending on the case. Here’s an example:  

    #cb141-1data(iris)
    #cb141-2
    #cb141-3l_expr = list(
    #cb141-4  pl$col("Petal.Length"),
    #cb141-5  pl$col("Species")
    #cb141-6)
    #cb141-7
    #cb141-8# Select only categorical columns
    #cb141-9pl$DataFrame(iris)$select(l_expr)$head(3) 
    shape: (3, 2)
    ┌──────────────┬─────────┐
    │ Petal.Length ┆ Species │
    │ ---          ┆ ---     │
    │ f64          ┆ cat     │
    ╞══════════════╪═════════╡
    │ 1.4          ┆ setosa  │
    │ 1.4          ┆ setosa  │
    │ 1.3          ┆ setosa  │
    └──────────────┴─────────┘
This way of selecting columns also works with simple R lists:  

    #cb143-1data(iris)
    #cb143-2
    #cb143-3l_expr = list(
    #cb143-4  "Petal.Length",
    #cb143-5  "Species",
    #cb143-6  "Petal.Width"
    #cb143-7)
    #cb143-8
    #cb143-9# Select only categorical columns
    #cb143-10pl$DataFrame(iris)$select(l_expr)$head(3) 
    shape: (3, 3)
    ┌──────────────┬─────────┬─────────────┐
    │ Petal.Length ┆ Species ┆ Petal.Width │
    │ ---          ┆ ---     ┆ ---         │
    │ f64          ┆ cat     ┆ f64         │
    ╞══════════════╪═════════╪═════════════╡
    │ 1.4          ┆ setosa  ┆ 0.2         │
    │ 1.4          ┆ setosa  ┆ 0.2         │
    │ 1.3          ┆ setosa  ┆ 0.2         │
    └──────────────┴─────────┴─────────────┘
### Selecting - other ways

The selectors API of polars enables to use other methods to select columns.  
Here’s a few examples selecting the first and last column of a `DataFrame` respectively with `first()` and `last()` methods:  

    #cb145-1# Select the first column
    #cb145-2pl$DataFrame(iris)$select(
    #cb145-3  pl$first()
    #cb145-4)$head(3)
    shape: (3, 1)
    ┌──────────────┐
    │ Sepal.Length │
    │ ---          │
    │ f64          │
    ╞══════════════╡
    │ 5.1          │
    │ 4.9          │
    │ 4.7          │
    └──────────────┘
    #cb147-1# Select the last column
    #cb147-2pl$DataFrame(iris)$select(
    #cb147-3  pl$last()
    #cb147-4)$head(3)
    shape: (3, 1)
    ┌─────────┐
    │ Species │
    │ ---     │
    │ cat     │
    ╞═════════╡
    │ setosa  │
    │ setosa  │
    │ setosa  │
    └─────────┘
## Modify/Add columns

### Modify existing column

Similar to the dplyr package, the `select()` method can also be used to **modify existing data**. However, the result will exclude any columns that were not specified in the expression.

For example, if we want to get in the data.frame iris the `Petal.Length` column rounded without decimals.  

    #cb149-1pl$DataFrame(iris)$select(
    #cb149-2  pl$col("Petal.Length")$round(decimals = 0)
    #cb149-3)$head(3) # display the first 3 lines
    shape: (3, 1)
    ┌──────────────┐
    │ Petal.Length │
    │ ---          │
    │ f64          │
    ╞══════════════╡
    │ 1.0          │
    │ 1.0          │
    │ 1.0          │
    └──────────────┘
The problem here is that we would like to keep all the iris columns and not just `Petal.Length`.

Again, let’s look at the official documentation:
> To modify or add some columns—whilst preserving all others in the dataset—it is therefore better to use the `with_columns()` method.

Note that the `with_columns()` method only accepts expressions. Let’s use it in an example:  
* polars
* R base
* dplyr
* data.table

    #cb151-1pl$DataFrame(iris)$with_columns(
    #cb151-2  pl$col("Petal.Length")$round(decimals = 0)
    #cb151-3)$head(3) # display the first 3 lines
    shape: (3, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬─────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---     │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat     │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═════════╡
    │ 5.1          ┆ 3.5         ┆ 1.0          ┆ 0.2         ┆ setosa  │
    │ 4.9          ┆ 3.0         ┆ 1.0          ┆ 0.2         ┆ setosa  │
    │ 4.7          ┆ 3.2         ┆ 1.0          ┆ 0.2         ┆ setosa  │
    └──────────────┴─────────────┴──────────────┴─────────────┴─────────┘
    #cb153-1iris$Petal.Length <- round(iris$Petal.Length, digits = 0)
    #cb153-2iris[1:3,]
      Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    1          5.1         3.5            1         0.2  setosa
    2          4.9         3.0            1         0.2  setosa
    3          4.7         3.2            1         0.2  setosa
    #cb155-1data(iris)
    #cb155-2iris |>
    #cb155-3  mutate(Petal.Length = round(Petal.Length,0)) |>
    #cb155-4  head(3)
      Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    1          5.1         3.5            1         0.2  setosa
    2          4.9         3.0            1         0.2  setosa
    3          4.7         3.2            1         0.2  setosa
    #cb157-1iris_dt[,Petal.Length := round(Petal.Length, digits = 0)]
    #cb157-2iris_dt[1:3,]
       Sepal.Length Sepal.Width Petal.Length Petal.Width Species
              <num>       <num>        <num>       <num>  <fctr>
    1:          5.1         3.5            1         0.2  setosa
    2:          4.9         3.0            1         0.2  setosa
    3:          4.7         3.2            1         0.2  setosa
### Add a new column

If you want to add a column to a data.frame, you use the same syntax as above with `with_columns()`. Simply use the `alias()` method to specify the name of the newly created column.  

    #cb159-1pl$DataFrame(iris)$with_columns(
    #cb159-2  pl$col("Petal.Length")$round(decimals = 0)$alias("Petal.Length.rounded")
    #cb159-3)$head(3) # display the first 3 lines
    shape: (3, 6)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬─────────┬──────────────────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species ┆ Petal.Length.rounded │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---     ┆ ---                  │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat     ┆ f64                  │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═════════╪══════════════════════╡
    │ 5.1          ┆ 3.5         ┆ 1.4          ┆ 0.2         ┆ setosa  ┆ 1.0                  │
    │ 4.9          ┆ 3.0         ┆ 1.4          ┆ 0.2         ┆ setosa  ┆ 1.0                  │
    │ 4.7          ┆ 3.2         ┆ 1.3          ┆ 0.2         ┆ setosa  ┆ 1.0                  │
    └──────────────┴─────────────┴──────────────┴─────────────┴─────────┴──────────────────────┘
### Add a new column with a constant value

If you need to **create a new column with a constant value** (i.e. the same value for all the rows in your DataFrame), you can use the literal `lit()` method. It works with the main types of Polars.  

    #cb161-1pl$DataFrame(iris)$with_columns(
    #cb161-2  pl$lit("toto")$alias("mynewcolumn")
    #cb161-3)$head(3) # display the first 3 lines
    shape: (3, 6)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬─────────┬─────────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species ┆ mynewcolumn │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---     ┆ ---         │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat     ┆ str         │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═════════╪═════════════╡
    │ 5.1          ┆ 3.5         ┆ 1.4          ┆ 0.2         ┆ setosa  ┆ toto        │
    │ 4.9          ┆ 3.0         ┆ 1.4          ┆ 0.2         ┆ setosa  ┆ toto        │
    │ 4.7          ┆ 3.2         ┆ 1.3          ┆ 0.2         ┆ setosa  ┆ toto        │
    └──────────────┴─────────────┴──────────────┴─────────────┴─────────┴─────────────┘
### Add a new column based on conditions

To add new columns based on conditions, the `when-then-otherwise` expression must be used.

Here’s the syntax:

    pl$when(**Boolean Expression**)$
      then(**Value if True**)$
      otherwise(**Value if False)$
      alias(**New Column Name**)
Here’s an example with equivalent syntax to help you understand:  
* polars
* R base
* dplyr
* data.table

    #cb164-1pl$DataFrame(iris)$with_columns(
    #cb164-2  pl$when(pl$col("Petal.Length") <= 2)$then(pl$lit("<=2"))$
    #cb164-3    when(pl$col("Petal.Length") <= 5)$then(pl$lit("<=5"))$
    #cb164-4    otherwise(pl$lit(">5"))$
    #cb164-5    alias("mygroups")
    #cb164-6# we only need to display 2 variables to check that it's OK
    #cb164-7)$select(
    #cb164-8  pl$col(c("Petal.Length","mygroups"))
    #cb164-9)[c(1,2,59,150),]
    shape: (4, 2)
    ┌──────────────┬──────────┐
    │ Petal.Length ┆ mygroups │
    │ ---          ┆ ---      │
    │ f64          ┆ str      │
    ╞══════════════╪══════════╡
    │ 1.4          ┆ <=2      │
    │ 1.4          ┆ <=2      │
    │ 4.6          ┆ <=5      │
    │ 5.1          ┆ >5       │
    └──────────────┴──────────┘
    #cb166-1iris$mygroups <- ifelse(iris$Petal.Length <= 2, "<=2",
    #cb166-2                        ifelse(iris$Petal.Length <= 5, "<=5", ">5"))
    #cb166-3# we only need to display 2 variables to check that it's OK
    #cb166-4iris[c(1,2,59,150), c("Petal.Length", "mygroups")]
        Petal.Length mygroups
    1            1.4      <=2
    2            1.4      <=2
    59           4.6      <=5
    150          5.1       >5
    #cb168-1iris |>
    #cb168-2  mutate(
    #cb168-3    mygroups = case_when(
    #cb168-4      Petal.Length <=2 ~ "<=2",
    #cb168-5      Petal.Length <=5 ~ "<=5",
    #cb168-6      .default = ">5")
    #cb168-7  ) |>
    #cb168-8  # we only need to display 2 variables  to check that it's OK
    #cb168-9  select(Petal.Length,mygroups) |>
    #cb168-10  slice(1,2,59,150)
      Petal.Length mygroups
    1          1.4      <=2
    2          1.4      <=2
    3          4.6      <=5
    4          5.1       >5
    #cb170-1iris_dt[, mygroups := case_when(
    #cb170-2  Petal.Length <= 2 ~ "<=2",
    #cb170-3  Petal.Length <= 5 ~ "<=5",
    #cb170-4  TRUE ~ ">5"
    #cb170-5)]
    #cb170-6# we only need to display 2 variables to check that it's OK
    #cb170-7iris_dt[c(1,2,59,150), .(Petal.Length, mygroups)]
       Petal.Length mygroups
              <num>   <char>
    1:            1      <=2
    2:            1      <=2
    3:            5      <=5
    4:            5      <=5
### Add a new column by group

To add new columns by group, the `over` expression must be used. This expression is similar to performing a groupby aggregation and joining the result back into the original dataframe. Here’s an example with equivalent syntax to help you understand:  

    #cb172-1df <- data.frame(
    #cb172-2  name = c("X","X","Y","Y","Z","Z"),
    #cb172-3  adress = c("A","B","C","D","E","F"),
    #cb172-4  col2 = c(2L,4L,1L,3L,4L,2L),
    #cb172-5  col3 = c(5L,19L,17L,12L,11L,15L)
    #cb172-6)
    #cb172-7df
      name adress col2 col3
    1    X      A    2    5
    2    X      B    4   19
    3    Y      C    1   17
    4    Y      D    3   12
    5    Z      E    4   11
    6    Z      F    2   15
* polars
* R base
* dplyr
* data.table

    #cb174-1pl$DataFrame(df)$with_columns(
    #cb174-2  pl$col("col3")$max()$over("name")$name$suffix("_max")
    #cb174-3)
    shape: (6, 5)
    ┌──────┬────────┬──────┬──────┬──────────┐
    │ name ┆ adress ┆ col2 ┆ col3 ┆ col3_max │
    │ ---  ┆ ---    ┆ ---  ┆ ---  ┆ ---      │
    │ str  ┆ str    ┆ i32  ┆ i32  ┆ i32      │
    ╞══════╪════════╪══════╪══════╪══════════╡
    │ X    ┆ A      ┆ 2    ┆ 5    ┆ 19       │
    │ X    ┆ B      ┆ 4    ┆ 19   ┆ 19       │
    │ Y    ┆ C      ┆ 1    ┆ 17   ┆ 17       │
    │ Y    ┆ D      ┆ 3    ┆ 12   ┆ 17       │
    │ Z    ┆ E      ┆ 4    ┆ 11   ┆ 15       │
    │ Z    ┆ F      ┆ 2    ┆ 15   ┆ 15       │
    └──────┴────────┴──────┴──────┴──────────┘
    #cb176-1result <- aggregate(col3 ~ name, data = df, FUN = max)
    #cb176-2colnames(result) <- c("name", "col3_max")
    #cb176-3merge(df, result, by = "name", all.x = TRUE)
      name adress col2 col3 col3_max
    1    X      A    2    5       19
    2    X      B    4   19       19
    3    Y      C    1   17       17
    4    Y      D    3   12       17
    5    Z      E    4   11       15
    6    Z      F    2   15       15
    #cb178-1df |>
    #cb178-2  group_by(name) |>
    #cb178-3  mutate(col3_max = max(col3))
    # A tibble: 6 × 5
    # Groups:   name [3]
      name  adress  col2  col3 col3_max
      <chr> <chr>  <int> <int>    <int>
    1 X     A          2     5       19
    2 X     B          4    19       19
    3 Y     C          1    17       17
    4 Y     D          3    12       17
    5 Z     E          4    11       15
    6 Z     F          2    15       15
    #cb180-1dt <- as.data.table(df)
    #cb180-2dt[, col3_max := max(col3), by = name]
    #cb180-3dt
         name adress  col2  col3 col3_max
       <char> <char> <int> <int>    <int>
    1:      X      A     2     5       19
    2:      X      B     4    19       19
    3:      Y      C     1    17       17
    4:      Y      D     3    12       17
    5:      Z      E     4    11       15
    6:      Z      F     2    15       15
If you need to pass multiple column names in the `over` expression, you can either list them like this `over("name","adress")` or - more conveniently - use a character vector `over(c("name","adress"))`.

## Rename columns

Similar to the dplyr package, the `rename()` method can also be used to rename existing column.  
The renaming logic is identical to that of `dplyr`, and is performed as follows: `new_name="old_name"`.  
Note the double quotes `""` surrounding the name of the old variable to be renamed which does not exist with `dplyr` (see examples below).  
* polars
* R base
* dplyr
* data.table

    #cb182-1data(iris)
    #cb182-2pl$DataFrame(iris)$
    #cb182-3  rename(
    #cb182-4    sepal_length = "Sepal.Length", 
    #cb182-5    sepal_width = "Sepal.Width",
    #cb182-6    `length of petal` = "Petal.Length",
    #cb182-7    `width of petal` = "Petal.Width",
    #cb182-8    species = "Species"
    #cb182-9  )$columns
    [1] "sepal_length"    "sepal_width"     "length of petal" "width of petal" 
    [5] "species"        
    #cb184-1data(iris)
    #cb184-2names(iris) <- c("sepal_length","sepal_width","length of petal","width of petal","species")
    #cb184-3names(iris)
    [1] "sepal_length"    "sepal_width"     "length of petal" "width of petal" 
    [5] "species"        
    #cb186-1data(iris)
    #cb186-2iris |>
    #cb186-3  rename(
    #cb186-4    sepal_length = Sepal.Length, 
    #cb186-5    sepal_width = Sepal.Width,
    #cb186-6    `length of petal` = Petal.Length,
    #cb186-7    `width of petal` = Petal.Width,
    #cb186-8    species = Species
    #cb186-9  ) |>
    #cb186-10  names()
    [1] "sepal_length"    "sepal_width"     "length of petal" "width of petal" 
    [5] "species"        
    #cb188-1setnames(iris_dt, 
    #cb188-2         old = c("Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width", "Species"),
    #cb188-3         new = c("sepal_length", "sepal_width", "length_of_petal", "width_of_petal", "species"))
    #cb188-4names(iris_dt)
    [1] "sepal_length"    "sepal_width"     "length_of_petal" "width_of_petal" 
    [5] "species"         "mygroups"       
## Remove columns

### Removing by name

To remove columns from a DataFrame, the `drop` method must be used.  
Here’s an example with equivalent syntax:  
* polars
* R base
* dplyr
* data.table

    #cb190-1pl$DataFrame(iris)$
    #cb190-2  drop(c("Petal.Width","Sepal.Length","Sepal.Width"))$
    #cb190-3  head(3)
    shape: (3, 2)
    ┌──────────────┬─────────┐
    │ Petal.Length ┆ Species │
    │ ---          ┆ ---     │
    │ f64          ┆ cat     │
    ╞══════════════╪═════════╡
    │ 1.4          ┆ setosa  │
    │ 1.4          ┆ setosa  │
    │ 1.3          ┆ setosa  │
    └──────────────┴─────────┘
    #cb192-1iris[1:3,!(names(iris) %in% c("Petal.Width","Sepal.Length","Sepal.Width"))]

      Petal.Length Species
    1          1.4  setosa
    2          1.4  setosa
    3          1.3  setosa
    #cb194-1iris |>
    #cb194-2  select(-c(Petal.Width,Sepal.Length,Sepal.Width)) |>
    #cb194-3  head(3)
      Petal.Length Species
    1          1.4  setosa
    2          1.4  setosa
    3          1.3  setosa
    #cb196-1iris_dt[, c("Petal.Width","Sepal.Length","Sepal.Width") := NULL][1:3,]

       Petal.Length Species
              <num>  <fctr>
    1:          1.4  setosa
    2:          1.4  setosa
    3:          1.3  setosa
### Removing with a regex

To remove columns with a regex from a DataFrame, the `drop` expression must be used.  
Let’s see an example where you want to drop columns whose names starts with “Petal” in `iris`.  
* polars
* R base
* dplyr
* data.table

    #cb198-1num_to_drop <- !grepl("^Petal",pl$DataFrame(iris)$columns)
    #cb198-2col_to_drop <- pl$DataFrame(iris)$columns[num_to_drop]
    #cb198-3
    #cb198-4pl$DataFrame(iris)$
    #cb198-5  drop(col_to_drop)$
    #cb198-6  head(3)
    shape: (3, 2)
    ┌──────────────┬─────────────┐
    │ Petal.Length ┆ Petal.Width │
    │ ---          ┆ ---         │
    │ f64          ┆ f64         │
    ╞══════════════╪═════════════╡
    │ 1.4          ┆ 0.2         │
    │ 1.4          ┆ 0.2         │
    │ 1.3          ┆ 0.2         │
    └──────────────┴─────────────┘
    #cb200-1iris[1:3,!grepl("^Petal",names(iris))]

      Sepal.Length Sepal.Width Species
    1          5.1         3.5  setosa
    2          4.9         3.0  setosa
    3          4.7         3.2  setosa
    #cb202-1iris |>
    #cb202-2  select(-starts_with("Petal")) |>
    #cb202-3  head(3)
      Sepal.Length Sepal.Width Species
    1          5.1         3.5  setosa
    2          4.9         3.0  setosa
    3          4.7         3.2  setosa
    #cb204-1iris_dt[, which(grepl("^Petal$", names(iris_dt))):=NULL][1:3,]

       Petal.Length Species
              <num>  <fctr>
    1:          1.4  setosa
    2:          1.4  setosa
    3:          1.3  setosa
All the ways we’ve seen in the section about selecting columns from a `DataFrame` (by name, data type and with a list) also work with `drop()` method!

## Aggregation by group

Another frequently used data manipulation is the aggregation of data by group. To do this, we indicate in the `group_by()` method which column will be used to group the data.frame. And the `agg()` method which specifies the expression to aggregate.

The methods available for the `agg()` method are (in each group):

* `first()` get the first element
* `last()` get the last element
* `n_unique()` get the number of unique elements
* `count()` get the number of elements
* `sum()` sum the elements
* `min()` get the smallest element
* `max()` get the largest element
* `mean()` get the average of elements
* `median()` get the median
* `quantile()` calculate quantiles

Here’s a minimal example with `sum` applied to 2 different columns:  
* polars
* R base
* dplyr
* data.table

    #cb206-1pl$DataFrame(iris)$
    #cb206-2  group_by("Species")$
    #cb206-3  agg(pl$col(c("Petal.Length","Petal.Width"))$sum())
    shape: (3, 3)
    ┌────────────┬──────────────┬─────────────┐
    │ Species    ┆ Petal.Length ┆ Petal.Width │
    │ ---        ┆ ---          ┆ ---         │
    │ cat        ┆ f64          ┆ f64         │
    ╞════════════╪══════════════╪═════════════╡
    │ virginica  ┆ 277.6        ┆ 101.3       │
    │ versicolor ┆ 213.0        ┆ 66.3        │
    │ setosa     ┆ 73.1         ┆ 12.3        │
    └────────────┴──────────────┴─────────────┘
    #cb208-1aggregate(cbind(Petal.Length, Petal.Width) ~ Species, data = iris, FUN = sum)

         Species Petal.Length Petal.Width
    1     setosa         73.1        12.3
    2 versicolor        213.0        66.3
    3  virginica        277.6       101.3
    #cb210-1data(iris)
    #cb210-2iris |>
    #cb210-3  group_by(Species) |>
    #cb210-4  summarise(across(c(Petal.Length, Petal.Width), sum))
    # A tibble: 3 × 3
      Species    Petal.Length Petal.Width
      <fct>             <dbl>       <dbl>
    1 setosa             73.1        12.3
    2 versicolor        213          66.3
    3 virginica         278.        101. 
    #cb212-1iris_dt <- as.data.table(iris)
    #cb212-2iris_dt[, .(Petal.Length = sum(Petal.Length), Petal.Width = sum(Petal.Width)), by = Species]
          Species Petal.Length Petal.Width
           <fctr>        <num>       <num>
    1:     setosa         73.1        12.3
    2: versicolor        213.0        66.3
    3:  virginica        277.6       101.3
Be careful! **Calling multiple aggregations on the same column produces columns of the same name** which generates an error with R.

You can use the `alias()` or `suffix()` method to ensure column names are unique. For example:  

    #cb214-1pl$DataFrame(iris)$
    #cb214-2  group_by("Species")$
    #cb214-3  agg(
    #cb214-4    # With alias()
    #cb214-5    pl$col(c("Petal.Length"))$sum()$alias("Petal.Length_Sum"),
    #cb214-6    pl$col(c("Petal.Length"))$mean()$alias("Petal.Length_Mean"),
    #cb214-7    # With suffix()
    #cb214-8    pl$col(c("Petal.Width"))$sum()$name$suffix("_Sum"),
    #cb214-9    pl$col(c("Petal.Width"))$mean()$name$suffix("_Mean")
    #cb214-10  )
    shape: (3, 5)
    ┌────────────┬──────────────────┬───────────────────┬─────────────────┬──────────────────┐
    │ Species    ┆ Petal.Length_Sum ┆ Petal.Length_Mean ┆ Petal.Width_Sum ┆ Petal.Width_Mean │
    │ ---        ┆ ---              ┆ ---               ┆ ---             ┆ ---              │
    │ cat        ┆ f64              ┆ f64               ┆ f64             ┆ f64              │
    ╞════════════╪══════════════════╪═══════════════════╪═════════════════╪══════════════════╡
    │ versicolor ┆ 213.0            ┆ 4.26              ┆ 66.3            ┆ 1.326            │
    │ setosa     ┆ 73.1             ┆ 1.462             ┆ 12.3            ┆ 0.246            │
    │ virginica  ┆ 277.6            ┆ 5.552             ┆ 101.3           ┆ 2.026            │
    └────────────┴──────────────────┴───────────────────┴─────────────────┴──────────────────┘
You can perform more advanced aggregations, for example **calculate the sum of all the floating point columns** in iris:  
* polars
* R base
* dplyr
* data.table

    #cb216-1pl$DataFrame(iris)$
    #cb216-2  group_by("Species")$
    #cb216-3  agg(pl$col(pl$Float64)$sum())
    shape: (3, 5)
    ┌────────────┬──────────────┬─────────────┬──────────────┬─────────────┐
    │ Species    ┆ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width │
    │ ---        ┆ ---          ┆ ---         ┆ ---          ┆ ---         │
    │ cat        ┆ f64          ┆ f64         ┆ f64          ┆ f64         │
    ╞════════════╪══════════════╪═════════════╪══════════════╪═════════════╡
    │ versicolor ┆ 296.8        ┆ 138.5       ┆ 213.0        ┆ 66.3        │
    │ virginica  ┆ 329.4        ┆ 148.7       ┆ 277.6        ┆ 101.3       │
    │ setosa     ┆ 250.3        ┆ 171.4       ┆ 73.1         ┆ 12.3        │
    └────────────┴──────────────┴─────────────┴──────────────┴─────────────┘
    #cb218-1iris_sum_by_species_base <- aggregate(iris[, sapply(iris, is.numeric)], by = list(Species = iris$Species), FUN = sum)
    #cb218-2iris_sum_by_species_base
         Species Sepal.Length Sepal.Width Petal.Length Petal.Width
    1     setosa        250.3       171.4         73.1        12.3
    2 versicolor        296.8       138.5        213.0        66.3
    3  virginica        329.4       148.7        277.6       101.3
    #cb220-1iris |>
    #cb220-2  group_by(Species) %>%
    #cb220-3  summarise(across(where(is.numeric), sum))
    # A tibble: 3 × 5
      Species    Sepal.Length Sepal.Width Petal.Length Petal.Width
      <fct>             <dbl>       <dbl>        <dbl>       <dbl>
    1 setosa             250.        171.         73.1        12.3
    2 versicolor         297.        138.        213          66.3
    3 virginica          329.        149.        278.        101. 
    #cb222-1iris_dt <- as.data.table(iris)
    #cb222-2iris_sum_by_species_dt <- iris_dt[, lapply(.SD, sum), by = Species, .SDcols = sapply(iris_dt, is.numeric)]
    #cb222-3iris_sum_by_species_dt
          Species Sepal.Length Sepal.Width Petal.Length Petal.Width
           <fctr>        <num>       <num>        <num>       <num>
    1:     setosa        250.3       171.4         73.1        12.3
    2: versicolor        296.8       138.5        213.0        66.3
    3:  virginica        329.4       148.7        277.6       101.3
## Sort a DataFrame

### Simply sort a DataFrame

The `sort()` method with a character vector can be used to sort a `DataFrame`.  
* polars
* R base
* dplyr
* data.table

    #cb224-1# Sort by one column
    #cb224-2pl$DataFrame(iris)$
    #cb224-3  sort("Species")$
    #cb224-4  head(3)
    shape: (3, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬─────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---     │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat     │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═════════╡
    │ 5.1          ┆ 3.5         ┆ 1.4          ┆ 0.2         ┆ setosa  │
    │ 4.9          ┆ 3.0         ┆ 1.4          ┆ 0.2         ┆ setosa  │
    │ 4.7          ┆ 3.2         ┆ 1.3          ┆ 0.2         ┆ setosa  │
    └──────────────┴─────────────┴──────────────┴─────────────┴─────────┘
    #cb226-1# Sort by two columns
    #cb226-2pl$DataFrame(iris)$
    #cb226-3  sort(c("Species","Petal.Length"))$
    #cb226-4  head(3)
    shape: (3, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬─────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---     │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat     │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═════════╡
    │ 4.6          ┆ 3.6         ┆ 1.0          ┆ 0.2         ┆ setosa  │
    │ 4.3          ┆ 3.0         ┆ 1.1          ┆ 0.1         ┆ setosa  │
    │ 5.0          ┆ 3.2         ┆ 1.2          ┆ 0.2         ┆ setosa  │
    └──────────────┴─────────────┴──────────────┴─────────────┴─────────┘
    #cb228-1# Sort by two columns one in a decreasing manner and the other in an increasing manner
    #cb228-2pl$DataFrame(iris)$
    #cb228-3  sort(c("Species","Petal.Length"), descending = c(TRUE,FALSE))$
    #cb228-4  head(3)
    shape: (3, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬───────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species   │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---       │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat       │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═══════════╡
    │ 4.9          ┆ 2.5         ┆ 4.5          ┆ 1.7         ┆ virginica │
    │ 6.0          ┆ 3.0         ┆ 4.8          ┆ 1.8         ┆ virginica │
    │ 6.2          ┆ 2.8         ┆ 4.8          ┆ 1.8         ┆ virginica │
    └──────────────┴─────────────┴──────────────┴─────────────┴───────────┘
    #cb230-1# Sort by one column
    #cb230-2iris[order(iris$Species),][1:3,]
      Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    1          5.1         3.5          1.4         0.2  setosa
    2          4.9         3.0          1.4         0.2  setosa
    3          4.7         3.2          1.3         0.2  setosa
    #cb232-1# Sort by two columns
    #cb232-2iris[order(iris$Species,iris$Petal.Length),][1:3,]
       Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    23          4.6         3.6          1.0         0.2  setosa
    14          4.3         3.0          1.1         0.1  setosa
    15          5.8         4.0          1.2         0.2  setosa
    #cb234-1# Sort by two columns one in a decreasing manner and the other in an increasing manner
    #cb234-2iris[order(rev(iris$Species),iris$Petal.Length),][1:3,]
        Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
    107          4.9         2.5          4.5         1.7 virginica
    127          6.2         2.8          4.8         1.8 virginica
    139          6.0         3.0          4.8         1.8 virginica
    #cb236-1# Sort by one column
    #cb236-2iris |>
    #cb236-3  arrange(Species) |>
    #cb236-4  head(3)
      Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    1          5.1         3.5          1.4         0.2  setosa
    2          4.9         3.0          1.4         0.2  setosa
    3          4.7         3.2          1.3         0.2  setosa
    #cb238-1# Sort by two columns
    #cb238-2iris |>
    #cb238-3  arrange(Species, Petal.Length) |>
    #cb238-4  head(3)
      Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    1          4.6         3.6          1.0         0.2  setosa
    2          4.3         3.0          1.1         0.1  setosa
    3          5.8         4.0          1.2         0.2  setosa
    #cb240-1# Sort by two columns one in a decreasing manner and the other in an increasing manner
    #cb240-2iris |>
    #cb240-3  arrange(desc(Species), Petal.Length) |>
    #cb240-4  head(3)
      Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
    1          4.9         2.5          4.5         1.7 virginica
    2          6.2         2.8          4.8         1.8 virginica
    3          6.0         3.0          4.8         1.8 virginica
    #cb242-1# Sort by one column
    #cb242-2iris_dt[order(Species)][1:3,]
       Sepal.Length Sepal.Width Petal.Length Petal.Width Species
              <num>       <num>        <num>       <num>  <fctr>
    1:          5.1         3.5          1.4         0.2  setosa
    2:          4.9         3.0          1.4         0.2  setosa
    3:          4.7         3.2          1.3         0.2  setosa
    #cb244-1# Sort by two columns
    #cb244-2iris_dt[order(Species,Petal.Length)][1:3,]
       Sepal.Length Sepal.Width Petal.Length Petal.Width Species
              <num>       <num>        <num>       <num>  <fctr>
    1:          4.6         3.6          1.0         0.2  setosa
    2:          4.3         3.0          1.1         0.1  setosa
    3:          5.8         4.0          1.2         0.2  setosa
    #cb246-1# Sort by two columns one in a decreasing manner and the other in an increasing manner
    #cb246-2iris_dt[order(-Species,Petal.Length)][1:3,]
       Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
              <num>       <num>        <num>       <num>    <fctr>
    1:          4.9         2.5          4.5         1.7 virginica
    2:          6.2         2.8          4.8         1.8 virginica
    3:          6.0         3.0          4.8         1.8 virginica
### Keep unique rows

If you want to keep unique/distinct rows from a `DataFrame`, you can use the `unique()` method:  
* polars
* R base
* dplyr
* data.table

    #cb248-1# With one column
    #cb248-2pl$DataFrame(iris)$unique(
    #cb248-3  subset = "Species"
    #cb248-4)
    shape: (3, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬────────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species    │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---        │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat        │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪════════════╡
    │ 6.3          ┆ 3.3         ┆ 6.0          ┆ 2.5         ┆ virginica  │
    │ 7.0          ┆ 3.2         ┆ 4.7          ┆ 1.4         ┆ versicolor │
    │ 5.1          ┆ 3.5         ┆ 1.4          ┆ 0.2         ┆ setosa     │
    └──────────────┴─────────────┴──────────────┴─────────────┴────────────┘
    #cb250-1# With one column keeping last entry
    #cb250-2pl$DataFrame(iris)$unique(
    #cb250-3  subset = "Species",
    #cb250-4  keep = "last"
    #cb250-5)
    shape: (3, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬────────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species    │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---        │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat        │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪════════════╡
    │ 5.0          ┆ 3.3         ┆ 1.4          ┆ 0.2         ┆ setosa     │
    │ 5.9          ┆ 3.0         ┆ 5.1          ┆ 1.8         ┆ virginica  │
    │ 5.7          ┆ 2.8         ┆ 4.1          ┆ 1.3         ┆ versicolor │
    └──────────────┴─────────────┴──────────────┴─────────────┴────────────┘
    #cb252-1# With two colums, keeping last entry and maintaining the same order
    #cb252-2pl$DataFrame(
    #cb252-3  x = c(1L, 1:3, 3L),
    #cb252-4  y = c(1L, 1:3, 3L),
    #cb252-5  z = c(1L, 1:3, 4L)
    #cb252-6)$unique(
    #cb252-7  subset = c("x","y"),
    #cb252-8  keep = "last",
    #cb252-9  maintain_order = TRUE
    #cb252-10)
    shape: (3, 3)
    ┌─────┬─────┬─────┐
    │ x   ┆ y   ┆ z   │
    │ --- ┆ --- ┆ --- │
    │ i32 ┆ i32 ┆ i32 │
    ╞═════╪═════╪═════╡
    │ 1   ┆ 1   ┆ 1   │
    │ 2   ┆ 2   ┆ 2   │
    │ 3   ┆ 3   ┆ 4   │
    └─────┴─────┴─────┘
    #cb254-1# With one column
    #cb254-2aggregate(. ~ Species, data = iris, FUN = head, N = 1)
         Species Sepal.Length.1 Sepal.Length.2 Sepal.Length.3 Sepal.Length.4
    1     setosa            5.1            4.9            4.7            4.6
    2 versicolor            7.0            6.4            6.9            5.5
    3  virginica            6.3            5.8            7.1            6.3
      Sepal.Length.5 Sepal.Length.6 Sepal.Width.1 Sepal.Width.2 Sepal.Width.3
    1            5.0            5.4           3.5           3.0           3.2
    2            6.5            5.7           3.2           3.2           3.1
    3            6.5            7.6           3.3           2.7           3.0
      Sepal.Width.4 Sepal.Width.5 Sepal.Width.6 Petal.Length.1 Petal.Length.2
    1           3.1           3.6           3.9            1.4            1.4
    2           2.3           2.8           2.8            4.7            4.5
    3           2.9           3.0           3.0            6.0            5.1
      Petal.Length.3 Petal.Length.4 Petal.Length.5 Petal.Length.6 Petal.Width.1
    1            1.3            1.5            1.4            1.7           0.2
    2            4.9            4.0            4.6            4.5           1.4
    3            5.9            5.6            5.8            6.6           2.5
      Petal.Width.2 Petal.Width.3 Petal.Width.4 Petal.Width.5 Petal.Width.6
    1           0.2           0.2           0.2           0.2           0.4
    2           1.5           1.5           1.3           1.5           1.3
    3           1.9           2.1           1.8           2.2           2.1
    #cb256-1# With one column keeping last entry
    #cb256-2aggregate(. ~ Species, data = iris, FUN = tail, n = 1)
         Species Sepal.Length Sepal.Width Petal.Length Petal.Width
    1     setosa          5.0         3.3          1.4         0.2
    2 versicolor          5.7         2.8          4.1         1.3
    3  virginica          5.9         3.0          5.1         1.8
    #cb258-1# With two colums, keeping last entry and maintaining the same order
    #cb258-2mytest <- data.frame(
    #cb258-3  x = c(1L, 1:3, 3L),
    #cb258-4  y = c(1L, 1:3, 3L),
    #cb258-5  z = c(1L, 1:3, 4L)
    #cb258-6) 
    #cb258-7aggregate(. ~ x + y, data = mytest, FUN = tail, n = 1)
      x y z
    1 1 1 1
    2 2 2 2
    3 3 3 4
    #cb260-1# With one column
    #cb260-2iris |>
    #cb260-3  distinct(Species, .keep_all = TRUE)
      Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
    1          5.1         3.5          1.4         0.2     setosa
    2          7.0         3.2          4.7         1.4 versicolor
    3          6.3         3.3          6.0         2.5  virginica
    #cb262-1# With one column keeping last entry
    #cb262-2iris |>
    #cb262-3  group_by(Species) |>
    #cb262-4  slice_tail() |>
    #cb262-5  ungroup()
    # A tibble: 3 × 5
      Sepal.Length Sepal.Width Petal.Length Petal.Width Species   
             <dbl>       <dbl>        <dbl>       <dbl> <fct>     
    1          5           3.3          1.4         0.2 setosa    
    2          5.7         2.8          4.1         1.3 versicolor
    3          5.9         3            5.1         1.8 virginica 
    #cb264-1# With two colums, keeping last entry and maintaining the same order
    #cb264-2data.frame(
    #cb264-3  x = c(1L, 1:3, 3L),
    #cb264-4  y = c(1L, 1:3, 3L),
    #cb264-5  z = c(1L, 1:3, 4L)
    #cb264-6) |>
    #cb264-7  group_by(x,y) |>
    #cb264-8  slice_tail() |>
    #cb264-9  ungroup()
    # A tibble: 3 × 3
          x     y     z
      <int> <int> <int>
    1     1     1     1
    2     2     2     2
    3     3     3     4
    #cb266-1# With one column
    #cb266-2unique(iris_dt, by = "Species")
       Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
              <num>       <num>        <num>       <num>     <fctr>
    1:          5.1         3.5          1.4         0.2     setosa
    2:          7.0         3.2          4.7         1.4 versicolor
    3:          6.3         3.3          6.0         2.5  virginica
    #cb268-1# With one column keeping last entry
    #cb268-2unique(iris_dt, by = "Species", fromLast = TRUE)
       Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
              <num>       <num>        <num>       <num>     <fctr>
    1:          5.0         3.3          1.4         0.2     setosa
    2:          5.7         2.8          4.1         1.3 versicolor
    3:          5.9         3.0          5.1         1.8  virginica
    #cb270-1# With two colums, keeping last entry and maintaining the same order
    #cb270-2mytest_dt <- data.table(
    #cb270-3  x = c(1L, 1:3, 3L),
    #cb270-4  y = c(1L, 1:3, 3L),
    #cb270-5  z = c(1L, 1:3, 4L)
    #cb270-6) 
    #cb270-7unique(mytest_dt, by = c("x","y"), fromLast = TRUE)
           x     y     z
       <int> <int> <int>
    1:     1     1     1
    2:     2     2     2
    3:     3     3     4
### Keep some columns from sorted DataFrame

If you want to keep only some columns from a sorted `DataFrame`, you can use the `sort_by()` method with the `select()` method.  
In details, `sort_by()` can sort a column by the ordering of another column, or multiple other columns.  
It’s the equivalent of `order()` method of R base.  
* polars
* R base
* dplyr
* data.table

    #cb272-1pl$DataFrame(iris)$
    #cb272-2  select(pl$col("Petal.Length")$
    #cb272-3  sort_by("Petal.Width"))
    shape: (150, 1)
    ┌──────────────┐
    │ Petal.Length │
    │ ---          │
    │ f64          │
    ╞══════════════╡
    │ 1.5          │
    │ 1.4          │
    │ 1.1          │
    │ 1.5          │
    │ 1.4          │
    │ …            │
    │ 5.6          │
    │ 5.6          │
    │ 6.0          │
    │ 6.1          │
    │ 5.7          │
    └──────────────┘
    #cb274-1data(iris)
    #cb274-2iris[order(iris$Petal.Width), "Petal.Length", drop = FALSE]
        Petal.Length
    10           1.5
    13           1.4
    14           1.1
    33           1.5
    38           1.4
    1            1.4
    2            1.4
    3            1.3
    4            1.5
    5            1.4
    8            1.5
    9            1.4
    11           1.5
    12           1.6
    15           1.2
    21           1.7
    23           1.0
    25           1.9
    26           1.6
    28           1.5
    29           1.4
    30           1.6
    31           1.6
    34           1.4
    35           1.5
    36           1.2
    37           1.3
    39           1.3
    40           1.5
    43           1.3
    47           1.6
    48           1.4
    49           1.5
    50           1.4
    7            1.4
    18           1.4
    19           1.7
    20           1.5
    41           1.3
    42           1.3
    46           1.4
    6            1.7
    16           1.5
    17           1.3
    22           1.5
    27           1.6
    32           1.5
    45           1.9
    24           1.7
    44           1.6
    58           3.3
    61           3.5
    63           4.0
    68           4.1
    80           3.5
    82           3.7
    94           3.3
    70           3.9
    81           3.8
    99           3.0
    74           4.7
    83           3.9
    91           4.4
    93           4.0
    96           4.2
    54           4.0
    56           4.5
    59           4.6
    65           3.6
    72           4.0
    75           4.3
    88           4.4
    89           4.1
    90           4.0
    95           4.2
    97           4.2
    98           4.3
    100          4.1
    51           4.7
    60           3.9
    64           4.7
    66           4.4
    76           4.4
    77           4.8
    92           4.6
    135          5.6
    52           4.5
    53           4.9
    55           4.6
    62           4.2
    67           4.5
    69           4.5
    73           4.9
    79           4.5
    85           4.5
    87           4.7
    120          5.0
    134          5.1
    57           4.7
    84           5.1
    86           4.5
    130          5.8
    78           5.0
    107          4.5
    71           4.8
    104          5.6
    108          6.3
    109          5.8
    117          5.5
    124          4.9
    126          6.0
    127          4.8
    128          4.9
    138          5.5
    139          4.8
    150          5.1
    102          5.1
    112          5.3
    131          6.1
    143          5.1
    147          5.0
    111          5.1
    114          5.0
    122          4.9
    123          6.7
    132          6.4
    148          5.2
    103          5.9
    106          6.6
    113          5.5
    125          5.7
    129          5.6
    140          5.4
    105          5.8
    118          6.7
    133          5.6
    116          5.3
    119          6.9
    121          5.7
    136          6.1
    142          5.1
    144          5.9
    146          5.2
    149          5.4
    115          5.1
    137          5.6
    141          5.6
    101          6.0
    110          6.1
    145          5.7
    #cb276-1data(iris)
    #cb276-2iris |>
    #cb276-3  arrange(Petal.Width) |>
    #cb276-4  select(Petal.Length)
        Petal.Length
    1            1.5
    2            1.4
    3            1.1
    4            1.5
    5            1.4
    6            1.4
    7            1.4
    8            1.3
    9            1.5
    10           1.4
    11           1.5
    12           1.4
    13           1.5
    14           1.6
    15           1.2
    16           1.7
    17           1.0
    18           1.9
    19           1.6
    20           1.5
    21           1.4
    22           1.6
    23           1.6
    24           1.4
    25           1.5
    26           1.2
    27           1.3
    28           1.3
    29           1.5
    30           1.3
    31           1.6
    32           1.4
    33           1.5
    34           1.4
    35           1.4
    36           1.4
    37           1.7
    38           1.5
    39           1.3
    40           1.3
    41           1.4
    42           1.7
    43           1.5
    44           1.3
    45           1.5
    46           1.6
    47           1.5
    48           1.9
    49           1.7
    50           1.6
    51           3.3
    52           3.5
    53           4.0
    54           4.1
    55           3.5
    56           3.7
    57           3.3
    58           3.9
    59           3.8
    60           3.0
    61           4.7
    62           3.9
    63           4.4
    64           4.0
    65           4.2
    66           4.0
    67           4.5
    68           4.6
    69           3.6
    70           4.0
    71           4.3
    72           4.4
    73           4.1
    74           4.0
    75           4.2
    76           4.2
    77           4.3
    78           4.1
    79           4.7
    80           3.9
    81           4.7
    82           4.4
    83           4.4
    84           4.8
    85           4.6
    86           5.6
    87           4.5
    88           4.9
    89           4.6
    90           4.2
    91           4.5
    92           4.5
    93           4.9
    94           4.5
    95           4.5
    96           4.7
    97           5.0
    98           5.1
    99           4.7
    100          5.1
    101          4.5
    102          5.8
    103          5.0
    104          4.5
    105          4.8
    106          5.6
    107          6.3
    108          5.8
    109          5.5
    110          4.9
    111          6.0
    112          4.8
    113          4.9
    114          5.5
    115          4.8
    116          5.1
    117          5.1
    118          5.3
    119          6.1
    120          5.1
    121          5.0
    122          5.1
    123          5.0
    124          4.9
    125          6.7
    126          6.4
    127          5.2
    128          5.9
    129          6.6
    130          5.5
    131          5.7
    132          5.6
    133          5.4
    134          5.8
    135          6.7
    136          5.6
    137          5.3
    138          6.9
    139          5.7
    140          6.1
    141          5.1
    142          5.9
    143          5.2
    144          5.4
    145          5.1
    146          5.6
    147          5.6
    148          6.0
    149          6.1
    150          5.7
    #cb278-1data(iris)
    #cb278-2iris_dt <- as.data.table(iris)
    #cb278-3iris_dt[order(Petal.Width)][,.(Petal.Length)]
         Petal.Length
                <num>
      1:          1.5
      2:          1.4
      3:          1.1
      4:          1.5
      5:          1.4
     ---             
    146:          5.6
    147:          5.6
    148:          6.0
    149:          6.1
    150:          5.7
If you want to use multiple columns/expressions, you can pass it in a `list` like this for example `sort_by(list("Petal.Width","Sepal.Width"))` or `sort_by(list("Petal.Width", pl$col("Sepal.Width")))`

## Join DataFrames

To perform joins, the `join()` method must be used.

Multiple strategies are available:

* `"inner"`: returns row with matching keys in both frames. Non-matching rows in either the left or right frame are discarded.  
* `"left"`: returns all rows in the left dataframe, whether or not a match in the right-frame is found. Non-matching rows have their right columns null-filled.  
* `"outer"`: returns all rows from both the left and right dataframe. If no match is found in one frame, columns from the other frame are null-filled.  
* `"semi"`: returns all rows from the left frame in which the join key is also present in the right frame.  
* `"anti"`: returns all rows from the left frame in which the join key is not present in the right frame.  
* `"cross"`: returns the cartesian product of all rows from the left frame with all rows from the right frame. Duplicates rows are retained. The table length of A cross-joined with B is always len(A) × len(B).

The main arguments are: - `on`: name(s) of the join columns in both `DataFrames`.  
- `how`: join strategy.  
- `suffix`: suffix to append to columns with a duplicate name.

Let’s see a simple example:  
* polars
* R base
* dplyr
* data.table

    #cb280-1# First, creation of colors_species Polars DataFrame
    #cb280-2colors <- pl$DataFrame(
    #cb280-3  Surname = pl$Series(c("toto","titi","tata")),
    #cb280-4  Color = pl$Series(c("blue","red","yellow"))
    #cb280-5)
    Warning in pl$Series(c("toto", "titi", "tata")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("blue", "red", "yellow")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb283-1values <- pl$DataFrame(
    #cb283-2  Surname = pl$Series(c("toto","titi","tata")),
    #cb283-3  value = pl$Series(c(10,20,30))
    #cb283-4)
    Warning in pl$Series(c("toto", "titi", "tata")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c(10, 20, 30)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb286-1# Let's join !
    #cb286-2colors$join(
    #cb286-3  other = values,
    #cb286-4  on = "Surname",
    #cb286-5  how = "left"
    #cb286-6)
    shape: (3, 3)
    ┌─────────┬────────┬───────┐
    │ Surname ┆ Color  ┆ value │
    │ ---     ┆ ---    ┆ ---   │
    │ str     ┆ str    ┆ f64   │
    ╞═════════╪════════╪═══════╡
    │ toto    ┆ blue   ┆ 10.0  │
    │ titi    ┆ red    ┆ 20.0  │
    │ tata    ┆ yellow ┆ 30.0  │
    └─────────┴────────┴───────┘
    #cb288-1colors_df <- data.frame(
    #cb288-2  Surname = c("toto","titi","tata"),
    #cb288-3  Color = c("blue","red","yellow")
    #cb288-4)
    #cb288-5values_df <- data.frame(
    #cb288-6  Surname = c("toto","titi","tata"),
    #cb288-7  value = c(10,20,30)
    #cb288-8)
    #cb288-9merge(colors_df, values_df, by = "Surname", all.x = TRUE)
      Surname  Color value
    1    tata yellow    30
    2    titi    red    20
    3    toto   blue    10
    #cb290-1colors_df |>
    #cb290-2  left_join(values_df,
    #cb290-3            by = "Surname") 
      Surname  Color value
    1    toto   blue    10
    2    titi    red    20
    3    tata yellow    30
    #cb292-1merge(as.data.table(colors_df), 
    #cb292-2      as.data.table(values_df), 
    #cb292-3      by = "Surname", all.x = TRUE)
    Key: <Surname>
       Surname  Color value
        <char> <char> <num>
    1:    tata yellow    30
    2:    titi    red    20
    3:    toto   blue    10
If join columns have different names in both `DataFrames`, you can use arguments `"left_on"` and `"right_on"`.  
Here’s an example:  

    #cb294-1values2 <- values$rename(Surname2 = "Surname")
    #cb294-2
    #cb294-3colors$join(
    #cb294-4  other = values2,
    #cb294-5  left_on = "Surname",
    #cb294-6  right_on = "Surname2",
    #cb294-7  how = "left"
    #cb294-8)
    shape: (3, 3)
    ┌─────────┬────────┬───────┐
    │ Surname ┆ Color  ┆ value │
    │ ---     ┆ ---    ┆ ---   │
    │ str     ┆ str    ┆ f64   │
    ╞═════════╪════════╪═══════╡
    │ toto    ┆ blue   ┆ 10.0  │
    │ titi    ┆ red    ┆ 20.0  │
    │ tata    ┆ yellow ┆ 30.0  │
    └─────────┴────────┴───────┘
When we join on **integer** columns that are **sorted** polars uses **a fast-track algorithm**.

To use the fast-track algorithm polars needs to know the join columns are sorted. See the tip [in this section](#filter-rows) for an example with `filter()` method.

**Polars can’t use the fast-track algorithm for joining string columns** as the algorithm works on integers.

=> **To use the fast-track algorithm** the string column must be cast to **categorical dtype** . See an example [here](#cast).

## Concatenate DataFrames

To perform concatenations, the `concat()` method must be used.  
In this section, we will see **vertical** , **horizontal** and **diagonal** concatenation.

Before we need to create some datasets to run our examples. Click to expand it! 👇  
Code  

    #cb296-1dfup <- pl$DataFrame(
    #cb296-2  col1 = pl$Series(c("a")),
    #cb296-3  col2 = pl$Series(c("b")),
    #cb296-4  col3 = pl$Series(c("c"))
    #cb296-5)
    Warning in pl$Series(c("a")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("b")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
Code  

    #cb300-1dfdown <- pl$DataFrame(
    #cb300-2  col1 = pl$Series(c("x")),
    #cb300-3  col2 = pl$Series(c("y")),
    #cb300-4  col3 = pl$Series(c("z"))
    #cb300-5)
    Warning in pl$Series(c("x")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("y")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("z")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
Code  

    #cb304-1dfleft <- pl$DataFrame(col1 = pl$Series(c("a","b","c")))

    Warning in pl$Series(c("a", "b", "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
Code  

    #cb306-1dfright <- pl$DataFrame(col2 = pl$Series(c("x","y","z")))

    Warning in pl$Series(c("x", "y", "z")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
Code  

    #cb308-1dfup_df <- dfup$to_data_frame()
    #cb308-2dfdown_df <- dfdown$to_data_frame()
    #cb308-3dfleft_df <- dfleft$to_data_frame()
    #cb308-4dfright_df <- dfright$to_data_frame()
    #cb308-5
    #cb308-6dfup_dt <- as.data.table(dfup)
    #cb308-7dfdown_dt <- as.data.table(dfdown)
    #cb308-8dfleft_dt <- as.data.table(dfleft)
    #cb308-9dfright_dt <- as.data.table(dfright)
### Vertical concatenation

To concatenate multiple `DataFrames` vertically (=by row), you can use the `concat()` method and the argument `how = "vertical"`.  
* polars
* R base
* dplyr
* data.table

    #cb309-1pl$concat(list(dfup,dfdown),how = "vertical")

    shape: (2, 3)
    ┌──────┬──────┬──────┐
    │ col1 ┆ col2 ┆ col3 │
    │ ---  ┆ ---  ┆ ---  │
    │ str  ┆ str  ┆ str  │
    ╞══════╪══════╪══════╡
    │ a    ┆ b    ┆ c    │
    │ x    ┆ y    ┆ z    │
    └──────┴──────┴──────┘
      col1 col2 col3
    1    a    b    c
    2    x    y    z
    #cb313-1bind_rows(list(dfup_df,dfdown_df))

      col1 col2 col3
    1    a    b    c
    2    x    y    z
    #cb315-1rbindlist(list(dfup_dt, dfdown_dt))

         col1   col2   col3
       <char> <char> <char>
    1:      a      b      c
    2:      x      y      z
### Horizontal concatenation

To concatenate multiple `DataFrames` horizontally (=by col), you can use the `concat()` method and the argument `how = "horizontal"`.  
* polars
* R base
* dplyr
* data.table

    #cb317-1pl$concat(list(dfleft,dfright),how = "horizontal")

    shape: (3, 2)
    ┌──────┬──────┐
    │ col1 ┆ col2 │
    │ ---  ┆ ---  │
    │ str  ┆ str  │
    ╞══════╪══════╡
    │ a    ┆ x    │
    │ b    ┆ y    │
    │ c    ┆ z    │
    └──────┴──────┘
    #cb319-1cbind(dfleft_df,dfright_df)

      col1 col2
    1    a    x
    2    b    y
    3    c    z
    #cb321-1cbind(list(dfleft_df,dfright_df))

         [,1]        
    [1,] data.frame,1
    [2,] data.frame,1
    #cb323-1data.table(dfleft_dt, dfright_dt)

         col1   col2
       <char> <char>
    1:      a      x
    2:      b      y
    3:      c      z
### Diagonal concatenation

To concatenate multiple `DataFrames` diagonally, you can use the `concat()` method and the argument `how = "diagonal"`.  
Diagonal concatenation is useful when the column names are not identical in initial `DataFrames`.  
* polars
* R base
* dplyr
* data.table

    #cb325-1dfup <- dfup$rename(col4="col3")
    #cb325-2pl$concat(list(dfup,dfdown),how = "diagonal")
    shape: (2, 4)
    ┌──────┬──────┬──────┬──────┐
    │ col1 ┆ col2 ┆ col4 ┆ col3 │
    │ ---  ┆ ---  ┆ ---  ┆ ---  │
    │ str  ┆ str  ┆ str  ┆ str  │
    ╞══════╪══════╪══════╪══════╡
    │ a    ┆ b    ┆ c    ┆ null │
    │ x    ┆ y    ┆ null ┆ z    │
    └──────┴──────┴──────┴──────┘
    #cb327-1dfup_df <- dfup$to_data_frame()
    #cb327-2dfup_df[setdiff(names(dfdown_df), names(dfup_df))] <- NA
    #cb327-3dfdown_df[setdiff(names(dfup_df), names(dfdown_df))] <- NA
    #cb327-4rbind(dfup_df,dfdown_df)
      col1 col2 col4 col3
    1    a    b    c <NA>
    2    x    y <NA>    z
    #cb329-1dfup_df <- dfup$to_data_frame()
    #cb329-2bind_rows(list(dfup_df,dfdown_df))
      col1 col2 col4 col3
    1    a    b    c <NA>
    2    x    y <NA>    z
    #cb331-1dfup_dt <- as.data.table(dfup)
    #cb331-2rbindlist(list(dfup_dt, dfdown_dt),fill = TRUE)
         col1   col2   col4   col3
       <char> <char> <char> <char>
    1:      a      b      c   <NA>
    2:      x      y   <NA>      z
## Pivot a DataFrame

### From long to wide

The `pivot()` method can be used to pivot a `DataFrame` from long to wide.

Let’s go for a first example :  

    #cb333-1df <- data.frame(
    #cb333-2  country = c(rep("France",3),rep("Italy","3")),
    #cb333-3  city = c("Paris", "Lille", "Nice", "Roma", "Milan", "Napoli"),
    #cb333-4  location = c("North","North","South","South","North","South"),
    #cb333-5  population = c(2.1, 0.2, 0.4, 2.8, 1.4, 3.0)
    #cb333-6)
    #cb333-7df
      country   city location population
    1  France  Paris    North        2.1
    2  France  Lille    North        0.2
    3  France   Nice    South        0.4
    4   Italy   Roma    South        2.8
    5   Italy  Milan    North        1.4
    6   Italy Napoli    South        3.0
* polars
* R base
* tidyr
* data.table

    #cb335-1pl$DataFrame(df)$pivot(
    #cb335-2  index = "country", 
    #cb335-3  columns = "city",
    #cb335-4  values = "population", 
    #cb335-5)
    shape: (2, 7)
    ┌─────────┬───────┬───────┬──────┬──────┬───────┬────────┐
    │ country ┆ Paris ┆ Lille ┆ Nice ┆ Roma ┆ Milan ┆ Napoli │
    │ ---     ┆ ---   ┆ ---   ┆ ---  ┆ ---  ┆ ---   ┆ ---    │
    │ str     ┆ f64   ┆ f64   ┆ f64  ┆ f64  ┆ f64   ┆ f64    │
    ╞═════════╪═══════╪═══════╪══════╪══════╪═══════╪════════╡
    │ France  ┆ 2.1   ┆ 0.2   ┆ 0.4  ┆ null ┆ null  ┆ null   │
    │ Italy   ┆ null  ┆ null  ┆ null ┆ 2.8  ┆ 1.4   ┆ 3.0    │
    └─────────┴───────┴───────┴──────┴──────┴───────┴────────┘
    #cb337-1reshape(df[,-which(names(df) %in% c("location"))], 
    #cb337-2        idvar = "country", 
    #cb337-3        timevar = "city", 
    #cb337-4        direction = "wide")
      country population.Paris population.Lille population.Nice population.Roma
    1  France              2.1              0.2             0.4              NA
    4   Italy               NA               NA              NA             2.8
      population.Milan population.Napoli
    1               NA                NA
    4              1.4                 3
    #cb339-1df |>
    #cb339-2  pivot_wider(
    #cb339-3    id_cols = country,
    #cb339-4    names_from = city,
    #cb339-5    values_from = population
    #cb339-6  )
    # A tibble: 2 × 7
      country Paris Lille  Nice  Roma Milan Napoli
      <chr>   <dbl> <dbl> <dbl> <dbl> <dbl>  <dbl>
    1 France    2.1   0.2   0.4  NA    NA       NA
    2 Italy    NA    NA    NA     2.8   1.4      3
    #cb341-1df_dt <- as.data.table(df)
    #cb341-2dcast(df_dt, country ~ city, value.var = "population")
    Key: <country>
       country Lille Milan Napoli  Nice Paris  Roma
        <char> <num> <num>  <num> <num> <num> <num>
    1:  France   0.2    NA     NA   0.4   2.1    NA
    2:   Italy    NA   1.4      3    NA    NA   2.8
You can also aggregate the results using a function that you enter in the argument `aggregate_function` in `pivot()` method.

In this case, the `aggregate_function` argument of `pivot()` is the equivalent of `values_fn` of `pivot_wider()` from `{tidyr}` and `fun.aggregate` of `dcast()` from `{data.table}`.  
* polars
* R base
* tidyr
* data.table

    #cb343-1pl$DataFrame(df)$pivot(
    #cb343-2  index = "country", 
    #cb343-3  columns = "location",
    #cb343-4  values = "population",
    #cb343-5  aggregate_function = "mean"
    #cb343-6)
    shape: (2, 3)
    ┌─────────┬───────┬───────┐
    │ country ┆ North ┆ South │
    │ ---     ┆ ---   ┆ ---   │
    │ str     ┆ f64   ┆ f64   │
    ╞═════════╪═══════╪═══════╡
    │ France  ┆ 1.15  ┆ 0.4   │
    │ Italy   ┆ 1.4   ┆ 2.9   │
    └─────────┴───────┴───────┘
    #cb345-1df_summary <- aggregate(population ~ country + location, data = df, FUN = mean)
    #cb345-2df_final <- reshape(df_summary, idvar = "country", timevar = "location", direction = "wide")
    #cb345-3colnames(df_final) <- c("country", "North", "South")
    #cb345-4df_final
      country North South
    1  France  1.15   0.4
    2   Italy  1.40   2.9
    #cb347-1df |>
    #cb347-2  pivot_wider(id_cols = country,
    #cb347-3              names_from = location, 
    #cb347-4              values_from = population, 
    #cb347-5              values_fn = mean)
    # A tibble: 2 × 3
      country North South
      <chr>   <dbl> <dbl>
    1 France   1.15   0.4
    2 Italy    1.4    2.9
    #cb349-1df_dt <- as.data.table(df)
    #cb349-2dcast(df_dt, country ~ location, value.var = "population", fun.aggregate = mean)
    Key: <country>
       country North South
        <char> <num> <num>
    1:  France  1.15   0.4
    2:   Italy  1.40   2.9
However with `{polars}`, we can also run an expression as an aggregation function.  
With `{tidyr}` and `{data.table}`, you need to calculate this in advance.  
For example:  
* polars
* R base
* tidyr
* data.table

    #cb351-1pl$DataFrame(df)$pivot(
    #cb351-2  index = "country", 
    #cb351-3  columns = "location",
    #cb351-4  values = "population",
    #cb351-5  aggregate_function = pl$element()$sum()$sqrt()
    #cb351-6)
    shape: (2, 3)
    ┌─────────┬──────────┬──────────┐
    │ country ┆ North    ┆ South    │
    │ ---     ┆ ---      ┆ ---      │
    │ str     ┆ f64      ┆ f64      │
    ╞═════════╪══════════╪══════════╡
    │ France  ┆ 1.516575 ┆ 0.632456 │
    │ Italy   ┆ 1.183216 ┆ 2.408319 │
    └─────────┴──────────┴──────────┘
    #cb353-1df_summarized <- aggregate(population ~ country + location, df, FUN = function(x) sqrt(sum(x)))
    #cb353-2df_final <- reshape(df_summarized, idvar = "country", timevar = "location", direction = "wide")
    #cb353-3colnames(df_final) <- c("country", "North", "South")
    #cb353-4df_final
      country    North     South
    1  France 1.516575 0.6324555
    2   Italy 1.183216 2.4083189
    #cb355-1df |>
    #cb355-2  group_by(country, location) |>
    #cb355-3  summarise(population_sum = sqrt(sum(population))) |>
    #cb355-4  pivot_wider(names_from = location, values_from = population_sum)
    # A tibble: 2 × 3
    # Groups:   country [2]
      country North South
      <chr>   <dbl> <dbl>
    1 France   1.52 0.632
    2 Italy    1.18 2.41 
    #cb357-1dt_final <- df_dt[, .(population_sum = sqrt(sum(population))), by = .(country, location)]
    #cb357-2dcast(dt_final, country ~ location, value.var = "population_sum")
    Key: <country>
       country    North     South
        <char>    <num>     <num>
    1:  France 1.516575 0.6324555
    2:   Italy 1.183216 2.4083189
### From wide to long

The `melt()` method can be used to pivot a `DataFrame` from wide to long.

Let’s see with an example :  

    #cb359-1df <- data.frame(
    #cb359-2  country = c("France","Italy"),
    #cb359-3  North = c(1.1,1.4),
    #cb359-4  South = c(0.4,2.9)
    #cb359-5)
* polars
* R base
* tidyr
* data.table

    #cb360-1pl$DataFrame(df)$melt(
    #cb360-2  id_vars = "country",
    #cb360-3  value_vars = c("North","South")
    #cb360-4)
    shape: (4, 3)
    ┌─────────┬──────────┬───────┐
    │ country ┆ variable ┆ value │
    │ ---     ┆ ---      ┆ ---   │
    │ str     ┆ str      ┆ f64   │
    ╞═════════╪══════════╪═══════╡
    │ France  ┆ North    ┆ 1.1   │
    │ Italy   ┆ North    ┆ 1.4   │
    │ France  ┆ South    ┆ 0.4   │
    │ Italy   ┆ South    ┆ 2.9   │
    └─────────┴──────────┴───────┘
    #cb362-1melted_df  <- reshape(df, 
    #cb362-2        varying = c("North", "South"), 
    #cb362-3        v.names = "value", 
    #cb362-4        idvar = "country", 
    #cb362-5        times = c("North", "South"), 
    #cb362-6        timevar = "variable", 
    #cb362-7        direction = "long")
    #cb362-8rownames(melted_df) <- NULL
    #cb362-9melted_df
      country variable value
    1  France    North   1.1
    2   Italy    North   1.4
    3  France    South   0.4
    4   Italy    South   2.9
    #cb364-1df |>
    #cb364-2  pivot_longer(
    #cb364-3    !country,
    #cb364-4    names_to = "variable",
    #cb364-5    values_to = "value"
    #cb364-6  )
    # A tibble: 4 × 3
      country variable value
      <chr>   <chr>    <dbl>
    1 France  North      1.1
    2 France  South      0.4
    3 Italy   North      1.4
    4 Italy   South      2.9
    #cb366-1df_dt <- as.data.table(df)
    #cb366-2melt(df_dt, id.vars = "country", variable.name = "variable", value.name = "value")
       country variable value
        <char>   <fctr> <num>
    1:  France    North   1.1
    2:   Italy    North   1.4
    3:  France    South   0.4
    4:   Italy    South   2.9
## Dealing with missing values

We have already introduced missing values in [here](https://ddotta.github.io/cookbook-rpolars/first_steps.html#missing-values). In this section, we will go further and understand how to deal with missing values with polars and R.

### Check if Series has missing values

The `is_null()` and `is_not_null()` methods can be used to check if `Series` has missing values.  
Theses methods are the equivalent of `is.na()` and `!is.na()` of R base.

Let’s see two examples combining thes methods with `select()` and `filter()` methods:  
* polars
* R base

    #cb368-1mydfNA <- pl$DataFrame(
    #cb368-2  colA = pl$Series(c("a",NA,"c")),
    #cb368-3  colB = pl$Series(c("d",NA,NA))
    #cb368-4)
    Warning in pl$Series(c("a", NA, "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("d", NA, NA)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb371-1# Find null values
    #cb371-2mydfNA$
    #cb371-3  select(
    #cb371-4    pl$col("colA"),
    #cb371-5    pl$col("colA")$is_null()$alias("is_null"),
    #cb371-6    pl$col("colA")$is_not_null()$alias("is_not_null")
    #cb371-7  )
    shape: (3, 3)
    ┌──────┬─────────┬─────────────┐
    │ colA ┆ is_null ┆ is_not_null │
    │ ---  ┆ ---     ┆ ---         │
    │ str  ┆ bool    ┆ bool        │
    ╞══════╪═════════╪═════════════╡
    │ a    ┆ false   ┆ true        │
    │ null ┆ true    ┆ false       │
    │ c    ┆ false   ┆ true        │
    └──────┴─────────┴─────────────┘
    #cb373-1# Filter by null values
    #cb373-2mydfNA$
    #cb373-3  filter(
    #cb373-4    pl$col("colA")$is_not_null() & pl$col("colB")$is_not_null()
    #cb373-5  )
    shape: (1, 2)
    ┌──────┬──────┐
    │ colA ┆ colB │
    │ ---  ┆ ---  │
    │ str  ┆ str  │
    ╞══════╪══════╡
    │ a    ┆ d    │
    └──────┴──────┘
    #cb375-1mydfNA2 <- mydfNA$to_data_frame()
    #cb375-2
    #cb375-3# Find null values
    #cb375-4data.frame(
    #cb375-5  colA = mydfNA2$colA,
    #cb375-6  is_null = is.na(mydfNA2$colA),
    #cb375-7  is_not_null = !is.na(mydfNA2$colA)
    #cb375-8)
      colA is_null is_not_null
    1    a   FALSE        TRUE
    2 <NA>    TRUE       FALSE
    3    c   FALSE        TRUE
    #cb377-1# Filter by null values
    #cb377-2mydfNA2[!is.na(mydfNA2$colA) & !is.na(mydfNA2$colB), ]
### Replace missing values with a constant

The `fill_null()` method can be used to replace missing values in a `Series` with a constant.

Here’s some examples where I replace missing values from all columns of a `DataFrame`:  
* polars
* R base
* dplyr
* data.table

    #cb379-1mydfNA <- pl$DataFrame(
    #cb379-2  colA = pl$Series(c("a",NA,"c")),
    #cb379-3  colB = pl$Series(c("d",NA,NA))
    #cb379-4)
    Warning in pl$Series(c("a", NA, "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("d", NA, NA)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb382-1# In the same columns
    #cb382-2mydfNA$
    #cb382-3  with_columns(
    #cb382-4  pl$all()$fill_null("missing")
    #cb382-5)
    shape: (3, 2)
    ┌─────────┬─────────┐
    │ colA    ┆ colB    │
    │ ---     ┆ ---     │
    │ str     ┆ str     │
    ╞═════════╪═════════╡
    │ a       ┆ d       │
    │ missing ┆ missing │
    │ c       ┆ missing │
    └─────────┴─────────┘
    #cb384-1# In new columns suffixed by "_corrected"
    #cb384-2mydfNA$
    #cb384-3  with_columns(
    #cb384-4  pl$all()$fill_null("missing")$name$suffix("_corrected")
    #cb384-5)
    shape: (3, 4)
    ┌──────┬──────┬────────────────┬────────────────┐
    │ colA ┆ colB ┆ colA_corrected ┆ colB_corrected │
    │ ---  ┆ ---  ┆ ---            ┆ ---            │
    │ str  ┆ str  ┆ str            ┆ str            │
    ╞══════╪══════╪════════════════╪════════════════╡
    │ a    ┆ d    ┆ a              ┆ d              │
    │ null ┆ null ┆ missing        ┆ missing        │
    │ c    ┆ null ┆ c              ┆ missing        │
    └──────┴──────┴────────────────┴────────────────┘
    #cb386-1mydfNA2 <- mydfNA$to_data_frame()
    #cb386-2
    #cb386-3# In the same columns
    #cb386-4mydfNA2[is.na(mydfNA2)] <- "missing"
    #cb386-5mydfNA2
         colA    colB
    1       a       d
    2 missing missing
    3       c missing
    #cb388-1# In new columns suffixed by "_corrected"
    #cb388-2mydfNA2 <- mydfNA$to_data_frame()
    #cb388-3
    #cb388-4transform(mydfNA2,
    #cb388-5          colA_corrected = ifelse(is.na(colA), "missing", colA),
    #cb388-6          colB_corrected = ifelse(is.na(colB), "missing", colB))
      colA colB colA_corrected colB_corrected
    1    a    d              a              d
    2 <NA> <NA>        missing        missing
    3    c <NA>              c        missing
    #cb390-1mydfNA2 <- mydfNA$to_data_frame()
    #cb390-2
    #cb390-3# In the same columns
    #cb390-4mydfNA2 %>%
    #cb390-5  mutate(across(everything(), ~ifelse(is.na(.), "missing", .)))
         colA    colB
    1       a       d
    2 missing missing
    3       c missing
    #cb392-1# In new columns suffixed by "_corrected"
    #cb392-2mydfNA2 %>%
    #cb392-3  mutate(across(c(colA, colB), ~ifelse(is.na(.), "missing", .), .names = "{col}_corrected"))
      colA colB colA_corrected colB_corrected
    1    a    d              a              d
    2 <NA> <NA>        missing        missing
    3    c <NA>              c        missing
    #cb394-1mydfNA2 <- mydfNA$to_data_frame()
    #cb394-2mydfNA2_dt <- as.data.table(mydfNA2)
    #cb394-3
    #cb394-4# In the same columns
    #cb394-5mydfNA2_dt[is.na(mydfNA2_dt)] <- "missing"
    #cb394-6mydfNA2_dt
          colA    colB
        <char>  <char>
    1:       a       d
    2: missing missing
    3:       c missing
    #cb396-1# In new columns suffixed by "_corrected"
    #cb396-2mydfNA2_dt[,
    #cb396-3           c("colA_corrected", "colB_corrected") := lapply(.SD, function(x) ifelse(is.na(x), "missing", x)),
    #cb396-4           .SDcols = c("colA", "colB")]
    #cb396-5mydfNA2_dt
          colA    colB colA_corrected colB_corrected
        <char>  <char>         <char>         <char>
    1:       a       d              a              d
    2: missing missing        missing        missing
    3:       c missing              c        missing
Be careful, **the `fill_null()` method can in some cases modify data types like `cast()`.** This can happen, for example, when you’re working on an integer column and you want to replace the missing values with a string => the column will then has a string dtype!

### Replace missing values with a strategy

The `fill_null()` method of polars has a `strategy` argument for replacing missing values:

* `forward`: replace with the previous non-null value in the `Series`
* `backward`: replace with the next non-null value in the `Series`
* `min`: replace with the smallest value in the `Series`
* `max`: replace with the largest value in the `Series`
* `mean`: replace with the median value in the `Series`
* `zero`: replace with `0`
* `one`: replace with `1`

We can set a limit on how many rows to fill-forward or backward with `limit`

Here’s some examples :  

    #cb398-1# In the same columns
    #cb398-2mydfNA <- pl$DataFrame(
    #cb398-3  colA = pl$Series(c("a",NA,"c")),
    #cb398-4  colB = pl$Series(c("d",NA,NA)),
    #cb398-5  colC = pl$Series(c(1,NA,3))
    #cb398-6)
    Warning in pl$Series(c("a", NA, "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("d", NA, NA)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c(1, NA, 3)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb402-1# With forward strategy
    #cb402-2mydfNA$
    #cb402-3  with_columns(
    #cb402-4    pl$all()$fill_null(strategy = "forward")$name$suffix("_corrected")
    #cb402-5  )
    shape: (3, 6)
    ┌──────┬──────┬──────┬────────────────┬────────────────┬────────────────┐
    │ colA ┆ colB ┆ colC ┆ colA_corrected ┆ colB_corrected ┆ colC_corrected │
    │ ---  ┆ ---  ┆ ---  ┆ ---            ┆ ---            ┆ ---            │
    │ str  ┆ str  ┆ f64  ┆ str            ┆ str            ┆ f64            │
    ╞══════╪══════╪══════╪════════════════╪════════════════╪════════════════╡
    │ a    ┆ d    ┆ 1.0  ┆ a              ┆ d              ┆ 1.0            │
    │ null ┆ null ┆ null ┆ a              ┆ d              ┆ 1.0            │
    │ c    ┆ null ┆ 3.0  ┆ c              ┆ d              ┆ 3.0            │
    └──────┴──────┴──────┴────────────────┴────────────────┴────────────────┘
    #cb404-1# With forward strategy and a limit
    #cb404-2mydfNA$
    #cb404-3  with_columns(
    #cb404-4    pl$all()$fill_null(strategy = "forward", limit = 1)$name$suffix("_corrected")
    #cb404-5  )
    shape: (3, 6)
    ┌──────┬──────┬──────┬────────────────┬────────────────┬────────────────┐
    │ colA ┆ colB ┆ colC ┆ colA_corrected ┆ colB_corrected ┆ colC_corrected │
    │ ---  ┆ ---  ┆ ---  ┆ ---            ┆ ---            ┆ ---            │
    │ str  ┆ str  ┆ f64  ┆ str            ┆ str            ┆ f64            │
    ╞══════╪══════╪══════╪════════════════╪════════════════╪════════════════╡
    │ a    ┆ d    ┆ 1.0  ┆ a              ┆ d              ┆ 1.0            │
    │ null ┆ null ┆ null ┆ a              ┆ d              ┆ 1.0            │
    │ c    ┆ null ┆ 3.0  ┆ c              ┆ null           ┆ 3.0            │
    └──────┴──────┴──────┴────────────────┴────────────────┴────────────────┘
    #cb406-1# With backward strategy
    #cb406-2mydfNA$
    #cb406-3  with_columns(
    #cb406-4    pl$all()$fill_null(strategy = "backward")$name$suffix("_corrected")
    #cb406-5  )
    shape: (3, 6)
    ┌──────┬──────┬──────┬────────────────┬────────────────┬────────────────┐
    │ colA ┆ colB ┆ colC ┆ colA_corrected ┆ colB_corrected ┆ colC_corrected │
    │ ---  ┆ ---  ┆ ---  ┆ ---            ┆ ---            ┆ ---            │
    │ str  ┆ str  ┆ f64  ┆ str            ┆ str            ┆ f64            │
    ╞══════╪══════╪══════╪════════════════╪════════════════╪════════════════╡
    │ a    ┆ d    ┆ 1.0  ┆ a              ┆ d              ┆ 1.0            │
    │ null ┆ null ┆ null ┆ c              ┆ null           ┆ 3.0            │
    │ c    ┆ null ┆ 3.0  ┆ c              ┆ null           ┆ 3.0            │
    └──────┴──────┴──────┴────────────────┴────────────────┴────────────────┘
    #cb408-1# With mean strategy only on "colC" column
    #cb408-2mydfNA$
    #cb408-3  with_columns(
    #cb408-4    pl$col("colC")$fill_null(strategy = "mean")$name$suffix("_corrected")
    #cb408-5  )
    shape: (3, 4)
    ┌──────┬──────┬──────┬────────────────┐
    │ colA ┆ colB ┆ colC ┆ colC_corrected │
    │ ---  ┆ ---  ┆ ---  ┆ ---            │
    │ str  ┆ str  ┆ f64  ┆ f64            │
    ╞══════╪══════╪══════╪════════════════╡
    │ a    ┆ d    ┆ 1.0  ┆ 1.0            │
    │ null ┆ null ┆ null ┆ 2.0            │
    │ c    ┆ null ┆ 3.0  ┆ 3.0            │
    └──────┴──────┴──────┴────────────────┘
### Replace missing values with an expression

Of course, you are not limited to the built-in strategies of polars => with `fill_null()` you can also use expressions to replace missing values. It works with expression from the same column and from another column.

Here’s some examples:  
* polars
* R base
* dplyr
* data.table

    #cb410-1# Replace missing values with the mean of the non-null values for that column
    #cb410-2mydfNA$
    #cb410-3  with_columns(
    #cb410-4    pl$col("colC")$fill_null(pl$mean("colC"))$name$suffix("_corrected")
    #cb410-5  )
    shape: (3, 4)
    ┌──────┬──────┬──────┬────────────────┐
    │ colA ┆ colB ┆ colC ┆ colC_corrected │
    │ ---  ┆ ---  ┆ ---  ┆ ---            │
    │ str  ┆ str  ┆ f64  ┆ f64            │
    ╞══════╪══════╪══════╪════════════════╡
    │ a    ┆ d    ┆ 1.0  ┆ 1.0            │
    │ null ┆ null ┆ null ┆ 2.0            │
    │ c    ┆ null ┆ 3.0  ┆ 3.0            │
    └──────┴──────┴──────┴────────────────┘
    #cb412-1# Replace missing values with the values from another column
    #cb412-2mydfNA$
    #cb412-3  with_columns(
    #cb412-4    pl$col("colB")$fill_null(pl$col("colA"))$name$suffix("_corrected")
    #cb412-5  )
    shape: (3, 4)
    ┌──────┬──────┬──────┬────────────────┐
    │ colA ┆ colB ┆ colC ┆ colB_corrected │
    │ ---  ┆ ---  ┆ ---  ┆ ---            │
    │ str  ┆ str  ┆ f64  ┆ str            │
    ╞══════╪══════╪══════╪════════════════╡
    │ a    ┆ d    ┆ 1.0  ┆ d              │
    │ null ┆ null ┆ null ┆ null           │
    │ c    ┆ null ┆ 3.0  ┆ c              │
    └──────┴──────┴──────┴────────────────┘
    #cb414-1mydfNA2 <- mydfNA$to_data_frame()
    #cb414-2
    #cb414-3# Replace missing values with the mean of the non-null values for that column
    #cb414-4mydfNA2$colC_corrected <- ifelse(is.na(mydfNA2$colC), mean(mydfNA2$colC, na.rm = TRUE), mydfNA2$colC)
    #cb414-5
    #cb414-6mydfNA2 <- mydfNA$to_data_frame()
    #cb414-7# Replace missing values with the values from another column
    #cb414-8mydfNA2$colB_corrected <- ifelse(is.na(mydfNA2$colB), mydfNA2$colA, mydfNA2$colB)
    #cb414-9mydfNA2
      colA colB colC colB_corrected
    1    a    d    1              d
    2 <NA> <NA>   NA           <NA>
    3    c <NA>    3              c
    #cb416-1mydfNA2 <- mydfNA$to_data_frame()
    #cb416-2
    #cb416-3# Replace missing values with the mean of the non-null values for that column
    #cb416-4mydfNA2 %>%
    #cb416-5  mutate(colC_corrected = ifelse(is.na(colC), mean(mydfNA2$colC, na.rm = TRUE), colC))
      colA colB colC colC_corrected
    1    a    d    1              1
    2 <NA> <NA>   NA              2
    3    c <NA>    3              3
    #cb418-1# Replace missing values with the values from another column
    #cb418-2mydfNA2 %>%
    #cb418-3  mutate(colB_corrected = ifelse(is.na(colB), colA, colB))
      colA colB colC colB_corrected
    1    a    d    1              d
    2 <NA> <NA>   NA           <NA>
    3    c <NA>    3              c
    #cb420-1mydfNA2 <- mydfNA$to_data_frame()
    #cb420-2mydfNA2_dt <- as.data.table(mydfNA2)
    #cb420-3
    #cb420-4# Replace missing values with the mean of the non-null values for that column
    #cb420-5mydfNA2_dt[, colC_corrected := ifelse(is.na(colC), mean(mydfNA2_dt$colC, na.rm = TRUE), colC)]
    #cb420-6mydfNA2_dt
         colA   colB  colC colC_corrected
       <char> <char> <num>          <num>
    1:      a      d     1              1
    2:   <NA>   <NA>    NA              2
    3:      c   <NA>     3              3
    #cb422-1# Replace missing values with the values from another column
    #cb422-2mydfNA2_dt[, colB_corrected := ifelse(is.na(colB), colA, colB)]
    #cb422-3mydfNA2_dt
         colA   colB  colC colC_corrected colB_corrected
       <char> <char> <num>          <num>         <char>
    1:      a      d     1              1              d
    2:   <NA>   <NA>    NA              2           <NA>
    3:      c   <NA>     3              3              c
### Replace missing values with a sequence of columns

The `coalesce()` method can be used to replace missing values based on a sequence of columns.

Here’s an example creating a new column “col4” that has the first non-null value as we go through some columns (in order!):  
* polars
* R base
* dplyr
* data.table

    #cb424-1mynewdfNA <- pl$DataFrame(
    #cb424-2  col1 = pl$Series(c(NA,"y",NA)),
    #cb424-3  col2 = pl$Series(c(NA,"v","w")),
    #cb424-4  col3 = pl$Series(c("r","s",NA))
    #cb424-5)
    Warning in pl$Series(c(NA, "y", NA)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c(NA, "v", "w")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("r", "s", NA)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb428-1mynewdfNA$
    #cb428-2  with_columns(
    #cb428-3    pl$coalesce("col1","col2","col3")$alias("col4")
    #cb428-4  )
    shape: (3, 4)
    ┌──────┬──────┬──────┬──────┐
    │ col1 ┆ col2 ┆ col3 ┆ col4 │
    │ ---  ┆ ---  ┆ ---  ┆ ---  │
    │ str  ┆ str  ┆ str  ┆ str  │
    ╞══════╪══════╪══════╪══════╡
    │ null ┆ null ┆ r    ┆ r    │
    │ y    ┆ v    ┆ s    ┆ y    │
    │ null ┆ w    ┆ null ┆ w    │
    └──────┴──────┴──────┴──────┘
    #cb430-1mynewdfNA2 <-  mynewdfNA$to_data_frame()
    #cb430-2
    #cb430-3mynewdfNA2$col4 <- coalesce(mynewdfNA2$col1, mynewdfNA2$col2, mynewdfNA2$col3)
    #cb430-4mynewdfNA2
      col1 col2 col3 col4
    1 <NA> <NA>    r    r
    2    y    v    s    y
    3 <NA>    w <NA>    w
    #cb432-1mynewdfNA2 <-  mynewdfNA$to_data_frame()
    #cb432-2
    #cb432-3mynewdfNA2 %>%
    #cb432-4  mutate(col4 = coalesce(col1, col2, col3))
      col1 col2 col3 col4
    1 <NA> <NA>    r    r
    2    y    v    s    y
    3 <NA>    w <NA>    w
    #cb434-1mynewdfNA2 <-  mynewdfNA$to_data_frame()
    #cb434-2mynewdfNA2_dt <- as.data.table(mynewdfNA2)
    #cb434-3
    #cb434-4mynewdfNA2_dt[, col4 := fcoalesce(col1, col2, col3)]
    #cb434-5mynewdfNA2_dt
         col1   col2   col3   col4
       <char> <char> <char> <char>
    1:   <NA>   <NA>      r      r
    2:      y      v      s      y
    3:   <NA>      w   <NA>      w
## Others useful methods

### On Series

#### Change name of Series

The `alias()` method is very useful especially in method chaining operation.  
With R base, the syntax is longer.  
* polars
* R base

    #cb436-1pl$Series(1:3, name = "toto")$alias("titi")

    Warning in pl$Series(1:3, name = "toto"): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    polars Series: shape: (3,)
    Series: 'titi' [i32]
    [
        1
        2
        3
    ]
    #cb439-1toto <- 1:3
    #cb439-2titi <- toto
    #cb439-3rm(toto)
#### Reduce Boolean Series

The `all()` and `any()` methods can be used to check if all or any values in a vector evaluate to TRUE for some expression.  
* polars
* R base

    #cb440-1# all(pl$Series(c(TRUE,TRUE))) doesn't work
    #cb440-2pl$Series(c(TRUE, TRUE, NA))$all()
    Warning in pl$Series(c(TRUE, TRUE, NA)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb443-1pl$Series(c(TRUE, TRUE, FALSE))$all()

    Warning in pl$Series(c(TRUE, TRUE, FALSE)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb446-1pl$Series(c(TRUE, TRUE, TRUE))$all()

    Warning in pl$Series(c(TRUE, TRUE, TRUE)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
#### Get data type of Series

The `dtype()` method can be used to get data type of `Series`.  
* polars
* R base

    Warning in pl$Series(letters): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c(1, 2)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
Polars is strongly typed. `print(ls(pl$dtypes))` returns the full list of valid Polars types. Caution, some type names differ from what they are called in R base. See below!  
* polars
* R base

    #cb465-1pl$Series(c("x","y","z"))$dtype

    Warning in pl$Series(c("x", "y", "z")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb468-1pl$Series(c(1, 2, 3))$dtype

    Warning in pl$Series(c(1, 2, 3)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c(1:3)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb474-1pl$Series(c(TRUE,FALSE))$dtype

    Warning in pl$Series(c(TRUE, FALSE)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb477-1pl$Series(factor(c("a","b","c")))$dtype

    Warning in pl$Series(factor(c("a", "b", "c"))): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    DataType: Categorical(
        Some(
            local,
        ),
        Physical,
    )
    #cb480-1pl$Series(Sys.Date())$dtype

    Warning in pl$Series(Sys.Date()): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c(0, 1)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb494-1typeof(factor(c("a","b","c")))

To summarise the main types between Polars and R:

|-------------|-----------|
| Utf8        | character |
| Float64     | double    |
| Int32       | integer   |
| Boolean     | logical   |
| Categorical | Factor    |
| Date        | Date      |

To learn more about Data types in Polars, see [here](https://pola-rs.github.io/polars-book/user-guide/concepts/data-types/).

#### Cast

The `cast()` method can be used to convert the data types of a column to a new one.  
* polars
* R base
* dplyr
* data.table

    #cb498-1pl$DataFrame(iris)$with_columns(
    #cb498-2  pl$col("Petal.Length")$cast(pl$Int8), # The "Petal.Length" column is converted into integers
    #cb498-3  pl$col("Species")$cast(pl$Utf8) # The "Species" column is converted into strings
    #cb498-4  )$schema
    $Sepal.Length
    DataType: Float64

    $Sepal.Width
    DataType: Float64

    $Petal.Length
    DataType: Int8

    $Petal.Width
    DataType: Float64

    $Species
    DataType: String
    #cb500-1data(iris)
    #cb500-2iris$Petal.Length <- as.integer(iris$Petal.Length)
    #cb500-3iris$Species <- as.integer(iris$Species)
    #cb500-4str(iris)
    'data.frame':   150 obs. of  5 variables:
     $ Sepal.Length: num  5.1 4.9 4.7 4.6 5 5.4 4.6 5 4.4 4.9 ...
     $ Sepal.Width : num  3.5 3 3.2 3.1 3.6 3.9 3.4 3.4 2.9 3.1 ...
     $ Petal.Length: int  1 1 1 1 1 1 1 1 1 1 ...
     $ Petal.Width : num  0.2 0.2 0.2 0.2 0.2 0.4 0.3 0.2 0.2 0.1 ...
     $ Species     : int  1 1 1 1 1 1 1 1 1 1 ...
    #cb502-1data(iris)
    #cb502-2iris |>
    #cb502-3  mutate(
    #cb502-4    Petal.Length = as.integer(Petal.Length),
    #cb502-5    Species = as.character(Species)) |>
    #cb502-6  str()
    'data.frame':   150 obs. of  5 variables:
     $ Sepal.Length: num  5.1 4.9 4.7 4.6 5 5.4 4.6 5 4.4 4.9 ...
     $ Sepal.Width : num  3.5 3 3.2 3.1 3.6 3.9 3.4 3.4 2.9 3.1 ...
     $ Petal.Length: int  1 1 1 1 1 1 1 1 1 1 ...
     $ Petal.Width : num  0.2 0.2 0.2 0.2 0.2 0.4 0.3 0.2 0.2 0.1 ...
     $ Species     : chr  "setosa" "setosa" "setosa" "setosa" ...
    #cb504-1iris_dt[, `:=`(Petal.Length = as.integer(Petal.Length),
    #cb504-2               Species = as.character(Species))]
    #cb504-3str(iris_dt)
    Classes 'data.table' and 'data.frame':  150 obs. of  5 variables:
     $ Sepal.Length: num  5.1 4.9 4.7 4.6 5 5.4 4.6 5 4.4 4.9 ...
     $ Sepal.Width : num  3.5 3 3.2 3.1 3.6 3.9 3.4 3.4 2.9 3.1 ...
     $ Petal.Length: int  1 1 1 1 1 1 1 1 1 1 ...
     $ Petal.Width : num  0.2 0.2 0.2 0.2 0.2 0.4 0.3 0.2 0.2 0.1 ...
     $ Species     : chr  "setosa" "setosa" "setosa" "setosa" ...
     - attr(*, ".internal.selfref")=<externalptr> 
When working with very large tables we can **Reduce the memory footprint** by modifying the number of bits allocated to an element. ⚠️

For example, the example below illustrates how converting Float64 to Float8 reduces memory usage:  

    #cb506-1pl$DataFrame(iris)$estimated_size()

    #cb508-1#| label: reduce-footprint-polars
    #cb508-2pl$DataFrame(iris)$with_columns(
    #cb508-3  pl$col("Petal.Length")$cast(pl$Float32),
    #cb508-4  pl$col("Petal.Width")$cast(pl$Float32),
    #cb508-5  pl$col("Sepal.Length")$cast(pl$Float32),
    #cb508-6  pl$col("Sepal.Width")$cast(pl$Float32)
    #cb508-7  )$estimated_size()
When performing downcasting, it is crucial to ensure that the chosen number of bits is sufficient to accommodate the largest and smallest numbers in the column.

A quick reminder:

|---------|----------------------------|-------------------------|
| Int8    | -128 to +127               |                         |
| Int16   | -32768 to +32767           |                         |
| Int32   | -2147483648 to +2147483647 |                         |
| Int64   | –2E63 to –2E63-1           |                         |
| Float32 | -3.4E+38 to +3.4E+38       | about 7 decimal digits  |
| Float64 | -1.7E+308 to +1.7E+308     | about 16 decimal digits |

#### Check if Series is numeric

The `is_numeric()` method can be used to check if `Series` is numeric.  
Note that unlike R base, there is no method to check if a Series is character (in this case, its type is anyway Utf8).  
* polars
* R base

    #cb510-1pl$Series(1:4)$is_numeric()

    Warning in pl$Series(1:4): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb513-1pl$Series(c("a", "b", "c"))$is_numeric()

    Warning in pl$Series(c("a", "b", "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb518-1is.numeric(c("a","b","c"))

#### Check if Series is sorted

The `is_sorted()` method can be used to check if `Series` is sorted.  
Note that R base provides `is.unsorted()` which returns the opposite boolean to `is_sorted()` of Polars.  
* polars
* R base

    #cb520-1pl$Series(1:4)$is_sorted()

    Warning in pl$Series(1:4): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb523-1pl$Series(c(1,3,2))$is_sorted()

    Warning in pl$Series(c(1, 3, 2)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
#### Get length of a Series

The `len()` method can be used to get the length of a `Series`.  
* polars
* R base

    Warning in pl$Series(1:4): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
#### Check if Series are equal

The `series_equal()` method can be used to check if a `Series` is equal with another `Series`.  
Caution, if two series are identical but one is named and the other is not then `series_equal()` returns FALSE.  
* polars
* R base

    #cb535-1pl$Series(1:4)$equals(pl$Series(1:4))

    Warning in pl$Series(1:4): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(1:4): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    #cb538-1pl$Series(1:4,name = "toto")$equals(pl$Series(1:4))

    Warning in pl$Series(1:4, name = "toto"): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(1:4, name = "toto"): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
#### Convert Series to Polars DataFrame

The `to_frame()` method can be used to convert a `Series` to a `DataFrame`.  
In this case, a `DataFrame` with only one column will be created. If the `Series` is initially named then the column of the `DataFrame` will be named as such.  

    #cb543-1pl$Series(1:3, "toto")$to_frame()

    Warning in pl$Series(1:3, "toto"): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    shape: (3, 1)
    ┌──────┐
    │ toto │
    │ ---  │
    │ i32  │
    ╞══════╡
    │ 1    │
    │ 2    │
    │ 3    │
    └──────┘
#### Get value Counts of a Series

The `value_counts()` method can be used to get a value counts of a `Series`.  
* polars
* R base
* dplyr
* data.table

    #cb546-1pl$Series(iris$Species)$value_counts()

    Warning in pl$Series(iris$Species): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    shape: (3, 2)
    ┌────────────┬───────┐
    │            ┆ count │
    │ ---        ┆ ---   │
    │ cat        ┆ u32   │
    ╞════════════╪═══════╡
    │ setosa     ┆ 50    │
    │ versicolor ┆ 50    │
    │ virginica  ┆ 50    │
    └────────────┴───────┘

        setosa versicolor  virginica 
            50         50         50 
         Species  n
    1     setosa 50
    2 versicolor 50
    3  virginica 50
    #cb553-1iris_dt[, .N, by = Species]

          Species     N
           <char> <int>
    1:     setosa    50
    2: versicolor    50
    3:  virginica    50
#### Sum across `Series`

The `sum()` method can be used to get a sum of a `Series`.

* From a single `Series`:

* polars
* R base

    Warning in pl$Series(1:3): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
* From a `DataFrame` and a column as **a string**:

* polars
* R base
* dplyr
* data.table

    #cb560-1pl$DataFrame(iris)$select(pl$sum("Petal.Length"))

    shape: (1, 1)
    ┌──────────────┐
    │ Petal.Length │
    │ ---          │
    │ f64          │
    ╞══════════════╡
    │ 563.7        │
    └──────────────┘
    #cb564-1iris |> summarise(sum(Petal.Length))

      sum(Petal.Length)
    1             563.7
    #cb566-1sum(iris_dt[, Petal.Length])

* From a `DataFrame` and a column as **an expression**:

    #cb568-1pl$DataFrame(iris)$select(pl$sum("Petal.Width"))

    shape: (1, 1)
    ┌─────────────┐
    │ Petal.Width │
    │ ---         │
    │ f64         │
    ╞═════════════╡
    │ 179.9       │
    └─────────────┘
* From a `DataFrame` and a column as a list and **sum horizontally**:

In this case, use `with_columns()` method.  
* polars
* R base
* dplyr
* data.table

    #cb570-1df_pl <- pl$DataFrame(col1 = c(10L,20L), col2= c(30L,40L), col3 = c(40L,50L))
    #cb570-2df_pl$with_columns(pl$sum("col1", "col3"))
    shape: (2, 3)
    ┌──────┬──────┬──────┐
    │ col1 ┆ col2 ┆ col3 │
    │ ---  ┆ ---  ┆ ---  │
    │ i32  ┆ i32  ┆ i32  │
    ╞══════╪══════╪══════╡
    │ 30   ┆ 30   ┆ 90   │
    │ 30   ┆ 40   ┆ 90   │
    └──────┴──────┴──────┘
    #cb572-1df <- data.frame(col1 = c(10L,20L), col2= c(30L,40L), col3 = c(40L,50L))
    #cb572-2mysum <- rowSums(df[, c("col1", "col3")])
    #cb572-3cbind(df,mysum)
      col1 col2 col3 mysum
    1   10   30   40    50
    2   20   40   50    70
    #cb574-1df |>
    #cb574-2  rowwise() |>
    #cb574-3  mutate(mysum = sum(col1,col3))
    # A tibble: 2 × 4
    # Rowwise: 
       col1  col2  col3 mysum
      <int> <int> <int> <int>
    1    10    30    40    50
    2    20    40    50    70
    #cb576-1df_dt <- as.data.table(df)
    #cb576-2df_dt[, somme := rowSums(.SD), .SDcols = c("col1", "col3")]
    #cb576-3print(df_dt)
        col1  col2  col3 somme
       <int> <int> <int> <num>
    1:    10    30    40    50
    2:    20    40    50    70
* From a `DataFrame` and **sum horizontally** all columns:

In this case, use `list(*)`.  

    #cb578-1df_pl$with_columns(pl$sum_horizontal(list("*")))

    shape: (2, 4)
    ┌──────┬──────┬──────┬───────────┐
    │ col1 ┆ col2 ┆ col3 ┆           │
    │ ---  ┆ ---  ┆ ---  ┆ ---       │
    │ i32  ┆ i32  ┆ i32  ┆ list[str] │
    ╞══════╪══════╪══════╪═══════════╡
    │ 10   ┆ 30   ┆ 40   ┆ ["*"]     │
    │ 20   ┆ 40   ┆ 50   ┆ ["*"]     │
    └──────┴──────┴──────┴───────────┘
With iris `Dataframe`, you must first select numerical variables:  

    #cb580-1pl$DataFrame(iris)$
    #cb580-2  select(
    #cb580-3  pl$col(c("Petal.Length","Petal.Width")))$
    #cb580-4  with_columns(pl$sum_horizontal(list("*")))
    shape: (150, 3)
    ┌──────────────┬─────────────┬───────────┐
    │ Petal.Length ┆ Petal.Width ┆           │
    │ ---          ┆ ---         ┆ ---       │
    │ f64          ┆ f64         ┆ list[str] │
    ╞══════════════╪═════════════╪═══════════╡
    │ 1.4          ┆ 0.2         ┆ ["*"]     │
    │ 1.4          ┆ 0.2         ┆ ["*"]     │
    │ 1.3          ┆ 0.2         ┆ ["*"]     │
    │ 1.5          ┆ 0.2         ┆ ["*"]     │
    │ 1.4          ┆ 0.2         ┆ ["*"]     │
    │ …            ┆ …           ┆ …         │
    │ 5.2          ┆ 2.3         ┆ ["*"]     │
    │ 5.0          ┆ 1.9         ┆ ["*"]     │
    │ 5.2          ┆ 2.0         ┆ ["*"]     │
    │ 5.4          ┆ 2.3         ┆ ["*"]     │
    │ 5.1          ┆ 1.8         ┆ ["*"]     │
    └──────────────┴─────────────┴───────────┘
### On DataFrames

#### Get Series from DataFrame

The `to_series()` method can be used to get one column from `DataFrame` as `Series`.  
* polars
* R base
* dplyr
* data.table

    #cb582-1pl$DataFrame(iris)$select(pl$col("Petal.Length"))$to_series()

    polars Series: shape: (150,)
    Series: 'Petal.Length' [f64]
    [
        1.4
        1.4
        1.3
        1.5
        1.4
        …
        5.2
        5.0
        5.2
        5.4
        5.1
    ]
      [1] 1.4 1.4 1.3 1.5 1.4 1.7 1.4 1.5 1.4 1.5 1.5 1.6 1.4 1.1 1.2 1.5 1.3 1.4
     [19] 1.7 1.5 1.7 1.5 1.0 1.7 1.9 1.6 1.6 1.5 1.4 1.6 1.6 1.5 1.5 1.4 1.5 1.2
     [37] 1.3 1.4 1.3 1.5 1.3 1.3 1.3 1.6 1.9 1.4 1.6 1.4 1.5 1.4 4.7 4.5 4.9 4.0
     [55] 4.6 4.5 4.7 3.3 4.6 3.9 3.5 4.2 4.0 4.7 3.6 4.4 4.5 4.1 4.5 3.9 4.8 4.0
     [73] 4.9 4.7 4.3 4.4 4.8 5.0 4.5 3.5 3.8 3.7 3.9 5.1 4.5 4.5 4.7 4.4 4.1 4.0
     [91] 4.4 4.6 4.0 3.3 4.2 4.2 4.2 4.3 3.0 4.1 6.0 5.1 5.9 5.6 5.8 6.6 4.5 6.3
    [109] 5.8 6.1 5.1 5.3 5.5 5.0 5.1 5.3 5.5 6.7 6.9 5.0 5.7 4.9 6.7 4.9 5.7 6.0
    [127] 4.8 4.9 5.6 5.8 6.1 6.4 5.6 5.1 5.6 6.1 5.6 5.5 4.8 5.4 5.6 5.1 5.1 5.9
    [145] 5.7 5.2 5.0 5.2 5.4 5.1
    #cb586-1iris |>
    #cb586-2  pull(Petal.Length)
      [1] 1.4 1.4 1.3 1.5 1.4 1.7 1.4 1.5 1.4 1.5 1.5 1.6 1.4 1.1 1.2 1.5 1.3 1.4
     [19] 1.7 1.5 1.7 1.5 1.0 1.7 1.9 1.6 1.6 1.5 1.4 1.6 1.6 1.5 1.5 1.4 1.5 1.2
     [37] 1.3 1.4 1.3 1.5 1.3 1.3 1.3 1.6 1.9 1.4 1.6 1.4 1.5 1.4 4.7 4.5 4.9 4.0
     [55] 4.6 4.5 4.7 3.3 4.6 3.9 3.5 4.2 4.0 4.7 3.6 4.4 4.5 4.1 4.5 3.9 4.8 4.0
     [73] 4.9 4.7 4.3 4.4 4.8 5.0 4.5 3.5 3.8 3.7 3.9 5.1 4.5 4.5 4.7 4.4 4.1 4.0
     [91] 4.4 4.6 4.0 3.3 4.2 4.2 4.2 4.3 3.0 4.1 6.0 5.1 5.9 5.6 5.8 6.6 4.5 6.3
    [109] 5.8 6.1 5.1 5.3 5.5 5.0 5.1 5.3 5.5 6.7 6.9 5.0 5.7 4.9 6.7 4.9 5.7 6.0
    [127] 4.8 4.9 5.6 5.8 6.1 6.4 5.6 5.1 5.6 6.1 5.6 5.5 4.8 5.4 5.6 5.1 5.1 5.9
    [145] 5.7 5.2 5.0 5.2 5.4 5.1
    #cb588-1iris_dt[, c(Petal.Length)]

      [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
     [38] 1 1 1 1 1 1 1 1 1 1 1 1 1 4 4 4 4 4 4 4 3 4 3 3 4 4 4 3 4 4 4 4 3 4 4 4 4
     [75] 4 4 4 5 4 3 3 3 3 5 4 4 4 4 4 4 4 4 4 3 4 4 4 4 3 4 6 5 5 5 5 6 4 6 5 6 5
    [112] 5 5 5 5 5 5 6 6 5 5 4 6 4 5 6 4 4 5 5 6 6 5 5 5 6 5 5 4 5 5 5 5 5 5 5 5 5
    [149] 5 5
#### Get a R List from DataFrame

The `to_list()` method can be used to get a R List from `DataFrame`.  
* polars
* R base

    #cb590-1mylist <- pl$DataFrame(iris)$to_list()
    #cb590-2names(mylist)
    [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"     

    #cb592-1mylist <- as.list(iris)
    #cb592-2names(mylist)
    [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"     

#### Get a slice of a DataFrame

The `slice()` method can be used to get a slice of a `DataFrame`.  
With Polars, numeric default is 0! Thus the equivalent to `slice(1,3)` with Polars will be `1:4` in R Base and data.table and `slice(1,4)` with dplyr.  
* polars
* R base
* dplyr
* data.table

    #cb594-1pl$DataFrame(iris)$slice(1,3)

    shape: (3, 5)
    ┌──────────────┬─────────────┬──────────────┬─────────────┬─────────┐
    │ Sepal.Length ┆ Sepal.Width ┆ Petal.Length ┆ Petal.Width ┆ Species │
    │ ---          ┆ ---         ┆ ---          ┆ ---         ┆ ---     │
    │ f64          ┆ f64         ┆ f64          ┆ f64         ┆ cat     │
    ╞══════════════╪═════════════╪══════════════╪═════════════╪═════════╡
    │ 4.9          ┆ 3.0         ┆ 1.4          ┆ 0.2         ┆ setosa  │
    │ 4.7          ┆ 3.2         ┆ 1.3          ┆ 0.2         ┆ setosa  │
    │ 4.6          ┆ 3.1         ┆ 1.5          ┆ 0.2         ┆ setosa  │
    └──────────────┴─────────────┴──────────────┴─────────────┴─────────┘
      Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    2          4.9         3.0          1.4         0.2  setosa
    3          4.7         3.2          1.3         0.2  setosa
    4          4.6         3.1          1.5         0.2  setosa
      Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    1          4.9         3.0          1.4         0.2  setosa
    2          4.7         3.2          1.3         0.2  setosa
    3          4.6         3.1          1.5         0.2  setosa
       Sepal.Length Sepal.Width Petal.Length Petal.Width Species
              <num>       <num>        <int>       <num>  <char>
    1:          4.9         3.0            1         0.2  setosa
    2:          4.7         3.2            1         0.2  setosa
    3:          4.6         3.1            1         0.2  setosa
#### Get a structure from a DataFrame

The `to_struct()` method can be used to get a structure from a `DataFrame`.  

    #cb602-1mystruc <- pl$DataFrame(iris)$to_struct()
    #cb602-2mystruc
    polars Series: shape: (150,)
    Series: '' [struct[5]]
    [
        {5.1,3.5,1.4,0.2,"setosa"}
        {4.9,3.0,1.4,0.2,"setosa"}
        {4.7,3.2,1.3,0.2,"setosa"}
        {4.6,3.1,1.5,0.2,"setosa"}
        {5.0,3.6,1.4,0.2,"setosa"}
        …
        {6.7,3.0,5.2,2.3,"virginica"}
        {6.3,2.5,5.0,1.9,"virginica"}
        {6.5,3.0,5.2,2.0,"virginica"}
        {6.2,3.4,5.4,2.3,"virginica"}
        {5.9,3.0,5.1,1.8,"virginica"}
    ]
`to_struct()` returns a `Series` which can be converted to a R list with `to_r` method.  

    #cb604-1mylist <- mystruc$to_r()
    #cb604-2str(mylist)
    List of 5
     $ Sepal.Length: num [1:150] 5.1 4.9 4.7 4.6 5 5.4 4.6 5 4.4 4.9 ...
     $ Sepal.Width : num [1:150] 3.5 3 3.2 3.1 3.6 3.9 3.4 3.4 2.9 3.1 ...
     $ Petal.Length: num [1:150] 1.4 1.4 1.3 1.5 1.4 1.7 1.4 1.5 1.4 1.5 ...
     $ Petal.Width : num [1:150] 0.2 0.2 0.2 0.2 0.2 0.4 0.3 0.2 0.2 0.1 ...
     $ Species     : Factor w/ 3 levels "setosa","versicolor",..: 1 1 1 1 1 1 1 1 1 1 ...
     - attr(*, "is_struct")= logi TRUE
mylist is now a list where each element is a column of the initial DataFrame.

With `to_frame()` and `unnest()` methods, we can reconstruct the original DataFrame:  

    #cb606-1back_df <- mystruc$to_frame()$unnest()
    #cb606-2back_df
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
#### Drop all rows that contain null values

The `drop_nulls()` method can be used to drop all rows that contain null values in a `DataFrame`.

By default, `drop_nulls()` use all columns to drop rows:  
* polars
* R base
* dplyr
* data.table

    #cb608-1data_pl <- pl$DataFrame(
    #cb608-2  col1 = pl$Series(c(NA,"b","c")),
    #cb608-3  col2 = pl$Series(c(1,2,NA))
    #cb608-4)
    Warning in pl$Series(c(NA, "b", "c")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c(1, 2, NA)): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    shape: (1, 2)
    ┌──────┬──────┐
    │ col1 ┆ col2 │
    │ ---  ┆ ---  │
    │ str  ┆ f64  │
    ╞══════╪══════╡
    │ b    ┆ 2.0  │
    └──────┴──────┘
    #cb613-1data_df <- data.frame(
    #cb613-2  col1 = c(NA, "b", "c"),
    #cb613-3  col2 = c(1, 2, NA)
    #cb613-4)
    #cb613-5data_df[complete.cases(data_df), ]
    #cb615-1data_df <- data.frame(
    #cb615-2  col1 = c(NA, "b", "c"),
    #cb615-3  col2 = c(1, 2, NA)
    #cb615-4)
    #cb615-5data_df %>%
    #cb615-6  filter(complete.cases(.))
    #cb617-1data_dt <- data.table(
    #cb617-2  col1 = c(NA, "b", "c"),
    #cb617-3  col2 = c(1, 2, NA)
    #cb617-4)
    #cb617-5na.omit(data_dt)
         col1  col2
       <char> <num>
    1:      b     2
If you want, you can specify a column (or multiple columns):  
* polars
* R base
* dplyr
* data.table

    #cb619-1data_pl$drop_nulls("col1")

    shape: (2, 2)
    ┌──────┬──────┐
    │ col1 ┆ col2 │
    │ ---  ┆ ---  │
    │ str  ┆ f64  │
    ╞══════╪══════╡
    │ b    ┆ 2.0  │
    │ c    ┆ null │
    └──────┴──────┘
    #cb621-1data_df[!is.na(data_df$col1), ]

    #cb623-1data_df |>
    #cb623-2  filter(!is.na(col1))
    #cb625-1data_dt[complete.cases(data_dt[, .SD, .SDcols = "col1"]), ]

         col1  col2
       <char> <num>
    1:      b     2
    2:      c    NA
## Strings methods

In polars, a lot of **strings methods** are useful. [Here](https://pola-rs.github.io/polars/py-polars/html/reference/series/string.html) is the list.  
To use them, simply prefix them with `str`.

### Get substrings

The `str$slice()` method can be used to create substrings of the string values of a **Utf8 Series**.

`str$slice()` does not work like R base’s `substr()` function for finding the substring of interest: - `substr()` takes two arguments: the first and last elements; - `str$slice()` takes two arguments: the first element and the extraction length.  
With Polars, numeric default is 0! Thus the equivalent to `str$slice(0,3)` with Polars will be `substr(1,3)`.

Two further comments:

* If the second argument `length` is not specified, the sub-character string of interest will default to the end of the character string. For example in a `DataFrame` if `mycol` is a string column of length 4, `pl.col("mycol").str.slice(1)` is equivalent to `substr(mycol,2,4)` in dplyr.

* The first argument accepts negative values, which means that sub-strings can be considered starting from the end. For example in a `DataFrame` if `mycol` is a string column of length 4, `pl.col("mycol").str.slice(-2)` is equivalent to `substr(mycol,3,4)` in dplyr.

Let’s see an example:  

    #cb627-1mydf <- data.frame(
    #cb627-2  col1 = 1:4,
    #cb627-3  col2 = c("One_X","One_Y","Two_X","Two_Y")
    #cb627-4)
* polars
* R base
* dplyr
* data.table

    #cb628-1pl$DataFrame(mydf)$with_columns(
    #cb628-2  pl$col("col2")$str$slice(0,length=3)$alias("level"),
    #cb628-3  pl$col("col2")$str$slice(-1)$alias("x_y")
    #cb628-4)
    shape: (4, 4)
    ┌──────┬───────┬───────┬─────┐
    │ col1 ┆ col2  ┆ level ┆ x_y │
    │ ---  ┆ ---   ┆ ---   ┆ --- │
    │ i32  ┆ str   ┆ str   ┆ str │
    ╞══════╪═══════╪═══════╪═════╡
    │ 1    ┆ One_X ┆ One   ┆ X   │
    │ 2    ┆ One_Y ┆ One   ┆ Y   │
    │ 3    ┆ Two_X ┆ Two   ┆ X   │
    │ 4    ┆ Two_Y ┆ Two   ┆ Y   │
    └──────┴───────┴───────┴─────┘
    #cb630-1mydf$level <- substr(mydf$col2, 1, 3)
    #cb630-2mydf$x_y <- substr(mydf$col2, nchar(mydf$col2), nchar(mydf$col2))
    #cb630-3mydf
      col1  col2 level x_y
    1    1 One_X   One   X
    2    2 One_Y   One   Y
    3    3 Two_X   Two   X
    4    4 Two_Y   Two   Y
    #cb632-1mydf |>
    #cb632-2  mutate(level = substr(col2, 1, 3),
    #cb632-3         x_y = substr(col2, nchar(col2), nchar(col2)))
      col1  col2 level x_y
    1    1 One_X   One   X
    2    2 One_Y   One   Y
    3    3 Two_X   Two   X
    4    4 Two_Y   Two   Y
    #cb634-1mydt <- as.data.table(mydf) 
    #cb634-2mydt[, c("level", "x_y") := .(substr(col2, 1, 3), substr(col2, nchar(col2), nchar(col2)))]
    #cb634-3mydt
        col1   col2  level    x_y
       <int> <char> <char> <char>
    1:     1  One_X    One      X
    2:     2  One_Y    One      Y
    3:     3  Two_X    Two      X
    4:     4  Two_Y    Two      Y
### Check if string values start with a substring

The `str$starts_with()` method can be used to check if string values start with a substring. It returns a **Boolean**.

Let’s see an example where we create new Boolean columns based on the start of a character string:  
* polars
* R base
* dplyr
* data.table

    #cb636-1pl$DataFrame(mydf)$with_columns(
    #cb636-2  pl$col("col2")$str$starts_with("One")$alias("is_one"),
    #cb636-3  pl$col("col2")$str$starts_with("Two")$alias("is_two")
    #cb636-4)
    shape: (4, 6)
    ┌──────┬───────┬───────┬─────┬────────┬────────┐
    │ col1 ┆ col2  ┆ level ┆ x_y ┆ is_one ┆ is_two │
    │ ---  ┆ ---   ┆ ---   ┆ --- ┆ ---    ┆ ---    │
    │ i32  ┆ str   ┆ str   ┆ str ┆ bool   ┆ bool   │
    ╞══════╪═══════╪═══════╪═════╪════════╪════════╡
    │ 1    ┆ One_X ┆ One   ┆ X   ┆ true   ┆ false  │
    │ 2    ┆ One_Y ┆ One   ┆ Y   ┆ true   ┆ false  │
    │ 3    ┆ Two_X ┆ Two   ┆ X   ┆ false  ┆ true   │
    │ 4    ┆ Two_Y ┆ Two   ┆ Y   ┆ false  ┆ true   │
    └──────┴───────┴───────┴─────┴────────┴────────┘
    #cb638-1mydf$is_one <- grepl("^One", mydf$col2)
    #cb638-2mydf$is_two <- grepl("^Two", mydf$col2)
    #cb638-3mydf
      col1  col2 level x_y is_one is_two
    1    1 One_X   One   X   TRUE  FALSE
    2    2 One_Y   One   Y   TRUE  FALSE
    3    3 Two_X   Two   X  FALSE   TRUE
    4    4 Two_Y   Two   Y  FALSE   TRUE
    #cb640-1mydf |>
    #cb640-2  mutate(level = substr(col2, 1, 3),
    #cb640-3         x_y = substr(col2, nchar(col2), nchar(col2)))
      col1  col2 level x_y is_one is_two
    1    1 One_X   One   X   TRUE  FALSE
    2    2 One_Y   One   Y   TRUE  FALSE
    3    3 Two_X   Two   X  FALSE   TRUE
    4    4 Two_Y   Two   Y  FALSE   TRUE
    #cb642-1mydt <- as.data.table(mydf) 
    #cb642-2mydt[, c("is_one", "is_two") := .(grepl("^One", col2), grepl("^Two", col2))]
    #cb642-3mydt
        col1   col2  level    x_y is_one is_two
       <int> <char> <char> <char> <lgcl> <lgcl>
    1:     1  One_X    One      X   TRUE  FALSE
    2:     2  One_Y    One      Y   TRUE  FALSE
    3:     3  Two_X    Two      X  FALSE   TRUE
    4:     4  Two_Y    Two      Y  FALSE   TRUE
### Check if string values end with a substring

The `str$ends_with()` method can be used to check if string values start with a substring. It returns a **Boolean**.

Let’s see an example where we filter the lines of a `DataFrame` based on the start of a character string:  
* polars
* R base
* dplyr
* data.table

    #cb644-1pl$DataFrame(mydf)$filter(
    #cb644-2  pl$col("col2")$str$ends_with("X")
    #cb644-3)
    shape: (2, 6)
    ┌──────┬───────┬───────┬─────┬────────┬────────┐
    │ col1 ┆ col2  ┆ level ┆ x_y ┆ is_one ┆ is_two │
    │ ---  ┆ ---   ┆ ---   ┆ --- ┆ ---    ┆ ---    │
    │ i32  ┆ str   ┆ str   ┆ str ┆ bool   ┆ bool   │
    ╞══════╪═══════╪═══════╪═════╪════════╪════════╡
    │ 1    ┆ One_X ┆ One   ┆ X   ┆ true   ┆ false  │
    │ 3    ┆ Two_X ┆ Two   ┆ X   ┆ false  ┆ true   │
    └──────┴───────┴───────┴─────┴────────┴────────┘
    #cb646-1mydf[substr(mydf$col2, nchar(mydf$col2), nchar(mydf$col2)) == "X", ]

      col1  col2 level x_y is_one is_two
    1    1 One_X   One   X   TRUE  FALSE
    3    3 Two_X   Two   X  FALSE   TRUE
    #cb648-1mydf |>
    #cb648-2  filter(endsWith(col2, "X"))
      col1  col2 level x_y is_one is_two
    1    1 One_X   One   X   TRUE  FALSE
    2    3 Two_X   Two   X  FALSE   TRUE
    #cb650-1mydt <- as.data.table(mydf) 
    #cb650-2mydt[substr(col2, nchar(col2), nchar(col2)) == "X"]
        col1   col2  level    x_y is_one is_two
       <int> <char> <char> <char> <lgcl> <lgcl>
    1:     1  One_X    One      X   TRUE  FALSE
    2:     3  Two_X    Two      X  FALSE   TRUE
## Create your methods

With `R` you can create your own method/function with `function()`.  
Let’s try to create a R function to captue some `DataFrame` transformations.

Our simple function:  
- Takes a `DataFrame` as an input (argument `data`)  
- Convert Categorical columns into Strings  
- Make all Strings columns uppercase  
- And filter only the third first rows  

    #cb652-1fn_transformation <- function(data) {
    #cb652-2  
    #cb652-3  data$
    #cb652-4    # Convert Integer columns into Float 
    #cb652-5    with_columns(
    #cb652-6      pl$col(pl$Int32)$cast(pl$Float64))$
    #cb652-7    # Make all Strings columns uppercase
    #cb652-8    with_columns(
    #cb652-9      pl$col(pl$Utf8)$str$to_uppercase())$
    #cb652-10    # Filter only the third first rows
    #cb652-11    head(3)
    #cb652-12  
    #cb652-13}
Let’s apply our method to `pl$DataFrame(iris)`:  

    #cb653-1fn_transformation(pl$DataFrame(mydf))

    shape: (3, 6)
    ┌──────┬───────┬───────┬─────┬────────┬────────┐
    │ col1 ┆ col2  ┆ level ┆ x_y ┆ is_one ┆ is_two │
    │ ---  ┆ ---   ┆ ---   ┆ --- ┆ ---    ┆ ---    │
    │ f64  ┆ str   ┆ str   ┆ str ┆ bool   ┆ bool   │
    ╞══════╪═══════╪═══════╪═════╪════════╪════════╡
    │ 1.0  ┆ ONE_X ┆ ONE   ┆ X   ┆ true   ┆ false  │
    │ 2.0  ┆ ONE_Y ┆ ONE   ┆ Y   ┆ true   ┆ false  │
    │ 3.0  ┆ TWO_X ┆ TWO   ┆ X   ┆ false  ┆ true   │
    └──────┴───────┴───────┴─────┴────────┴────────┘
Our method `fn_transformation()` can now be re-used multiple times for example on another datasets:  

    #cb655-1Newdf <- pl$DataFrame(
    #cb655-2  col_categ = pl$Series(factor(c("a","b","c"))),
    #cb655-3  col_str = pl$Series(c("x","y","z")),
    #cb655-4  col_num = pl$Series(1:3)
    #cb655-5)
    Warning in pl$Series(factor(c("a", "b", "c"))): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(c("x", "y", "z")): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    Warning in pl$Series(1:3): `pl$Series()` will handle unnamed arguments differently as of 0.17.0:
    - until 0.17.0, the first argument corresponds to the values and the second argument to the name of the Series.
    - as of 0.17.0, the first argument will correspond to the name and the second argument to the values.
    Use named arguments in `pl$Series()` or replace `pl$Series(<values>, <name>)` by `as_polars_series(<values>, <name>)` to silence this warning.
    shape: (3, 3)
    ┌───────────┬─────────┬─────────┐
    │ col_categ ┆ col_str ┆ col_num │
    │ ---       ┆ ---     ┆ ---     │
    │ cat       ┆ str     ┆ f64     │
    ╞═══════════╪═════════╪═════════╡
    │ a         ┆ X       ┆ 1.0     │
    │ b         ┆ Y       ┆ 2.0     │
    │ c         ┆ Z       ┆ 3.0     │
    └───────────┴─────────┴─────────┘
Of course, in real life, we will create functions that are more complicated than our example.
