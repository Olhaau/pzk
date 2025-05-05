---
title: Get started - Polars R Package
---

# Get started - Polars R Package

url:: https://pola-rs.github.io/r-polars/vignettes/polars.html
up: [[sources]], [[polars]]

#source

# An Introduction to Polars from R

## What is Polars?

==Polars is [a lightning fast](https://duckdblabs.github.io/db-benchmark/) Data Frame library. Its embarrassingly parallel execution, cache efficient algorithms and expressive API makes it perfect for efficient data wrangling, data pipelines, snappy APIs, and much more besides. Polars also supports “streaming mode” for out-of-memory operations. This allows users to analyze datasets many times larger than RAM.==

The underlying computation engine is written in Rust and is built on the Apache Arrow columnar memory format. It can be used in Rust or via Python bindings. The **polars** R-package provides equivalent bindings from R. To help distinguish the different language implementations, we typically use a convention of referring to them with prefixes: rust-polars, py-polars, r-polars, nodejs-polars, etc. But within each language, the relevant library is always just called *polars*.

**polars** users can expect orders of magnitude(s) improvement compared to **dplyr** for simple transformations on datasets >500Mb. The automatic Polars optimization framework means that that this speed boost can be even greater for complex queries that chain together many operations. Performance is similar to that of **data.table** , although **polars** supports additional functionality via its relationship to the Apache Arrow memory model. For example, it can scan multiple Parquet files and datasets and selectively import random subsets without having to read all of the data.

Polars syntax is similar to that of Spark, but the workflow is column-oriented rather than row-oriented. Since R is itself a column-oriented language, this should immediately feel familiar to most R users. Like Spark and modern SQL variants, Polars optimizes queries for memory consumption and speed, so you don’t have to. However, unlike Spark, Polars is natively multithreaded instead of multinoded. This makes (r)polars much simpler to install and can be used as one would any other R package.

This R port relies on the excellent [**extendr**](https://github.com/extendr) package, which is the R equivalent to pyo3+maturin. **extendr** is very convenient for calling Rust from R, and vice versa, and is what we use to build the **polars** package. Once built, however, **polars** has no other dependencies other than R itself. This makes it very fast and lightweight to install, and so **polars** can immediately be used to tackle your big (or small!) data wrangling tasks.

## Documentation and tutorials

Users can find detailed documentation for all objects, functions, and methods on the Reference page of [this website](https://pola-rs.github.io/r-polars/). This documentation can also be accessed from the R console using the typical `?` syntax. For example, we will later use the `DataFrame()` constructor function and apply the `group_by()` method to a `DataFrame` object. The documentation for these can be accessed by typing these commands:  

    #__codelineno-0-1?DataFrame_class
    #__codelineno-0-2?DataFrame_group_by
The [Polars book](https://pola-rs.github.io/polars-book/user-guide/) offers a great introduction to the Polars data frame library, with a very large number of examples in Python and Rust. The syntax and expressions in the `polars` package for R are (deliberately) as close to the Python implementation as possible, so you can always refer to the [polars book](https://pola-rs.github.io/polars-book/user-guide/) for more ideas. Just remember to switch out any “.” (Python) for a “$” (R) when chaining methods. For example, here are two equivalent lines of code for some hypothetical dataset.  

    #__codelineno-1-1# Python
    #__codelineno-1-2df.group_by("id").mean()
    #__codelineno-2-1# R
    #__codelineno-2-2df$group_by("id")$mean()
## `Series` and `DataFrames`

In `polars`, objects of class `Series` are analogous to R vectors. Objects of class `DataFrame` are analogous to R data frames. Notice that to avoid collision with classes provided by other packages, the class name of all objects created by `polars` starts with “RPolars”. For example, a `polars` `DataFrame` has the class “RPolarsDataFrame”.

To create Polars `Series` and `DataFrames` objects, we load the library and use constructor functions with the `pl$` prefix. This prefix is very important, as most of the `polars` functions are made available via `pl$`:  

    #__codelineno-3-1library(polars)
    #__codelineno-3-2
    #__codelineno-3-3pl$Series(name = "a", values = (1:5) * 5)
    #__codelineno-3-4#> polars Series: shape: (5,)
    #__codelineno-3-5#> Series: 'a' [f64]
    #__codelineno-3-6#> [
    #__codelineno-3-7#>  5.0
    #__codelineno-3-8#>  10.0
    #__codelineno-3-9#>  15.0
    #__codelineno-3-10#>  20.0
    #__codelineno-3-11#>  25.0
    #__codelineno-3-12#> ]
    #__codelineno-3-13
    #__codelineno-3-14pl$DataFrame(a = 1:5, b = letters[1:5])
    #__codelineno-3-15#> shape: (5, 2)
    #__codelineno-3-16#> ┌─────┬─────┐
    #__codelineno-3-17#> │ a   ┆ b   │
    #__codelineno-3-18#> │ --- ┆ --- │
    #__codelineno-3-19#> │ i32 ┆ str │
    #__codelineno-3-20#> ╞═════╪═════╡
    #__codelineno-3-21#> │ 1   ┆ a   │
    #__codelineno-3-22#> │ 2   ┆ b   │
    #__codelineno-3-23#> │ 3   ┆ c   │
    #__codelineno-3-24#> │ 4   ┆ d   │
    #__codelineno-3-25#> │ 5   ┆ e   │
    #__codelineno-3-26#> └─────┴─────┘
Or, to convert existing R objects to Polars objects, we can use the `as_polars_series()` and `as_polars_df()` generic functions.  

    #__codelineno-4-1ser = as_polars_series((1:5) * 5)
    #__codelineno-4-2ser
    #__codelineno-4-3#> polars Series: shape: (5,)
    #__codelineno-4-4#> Series: '' [f64]
    #__codelineno-4-5#> [
    #__codelineno-4-6#>  5.0
    #__codelineno-4-7#>  10.0
    #__codelineno-4-8#>  15.0
    #__codelineno-4-9#>  20.0
    #__codelineno-4-10#>  25.0
    #__codelineno-4-11#> ]
    #__codelineno-4-12
    #__codelineno-4-13dat = as_polars_df(mtcars)
    #__codelineno-4-14dat
    #__codelineno-4-15#> shape: (32, 11)
    #__codelineno-4-16#> ┌──────┬─────┬───────┬───────┬───┬─────┬─────┬──────┬──────┐
    #__codelineno-4-17#> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ vs  ┆ am  ┆ gear ┆ carb │
    #__codelineno-4-18#> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ --- ┆ --- ┆ ---  ┆ ---  │
    #__codelineno-4-19#> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64 ┆ f64 ┆ f64  ┆ f64  │
    #__codelineno-4-20#> ╞══════╪═════╪═══════╪═══════╪═══╪═════╪═════╪══════╪══════╡
    #__codelineno-4-21#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 4.0  ┆ 4.0  │
    #__codelineno-4-22#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 4.0  ┆ 4.0  │
    #__codelineno-4-23#> │ 22.8 ┆ 4.0 ┆ 108.0 ┆ 93.0  ┆ … ┆ 1.0 ┆ 1.0 ┆ 4.0  ┆ 1.0  │
    #__codelineno-4-24#> │ 21.4 ┆ 6.0 ┆ 258.0 ┆ 110.0 ┆ … ┆ 1.0 ┆ 0.0 ┆ 3.0  ┆ 1.0  │
    #__codelineno-4-25#> │ 18.7 ┆ 8.0 ┆ 360.0 ┆ 175.0 ┆ … ┆ 0.0 ┆ 0.0 ┆ 3.0  ┆ 2.0  │
    #__codelineno-4-26#> │ …    ┆ …   ┆ …     ┆ …     ┆ … ┆ …   ┆ …   ┆ …    ┆ …    │
    #__codelineno-4-27#> │ 30.4 ┆ 4.0 ┆ 95.1  ┆ 113.0 ┆ … ┆ 1.0 ┆ 1.0 ┆ 5.0  ┆ 2.0  │
    #__codelineno-4-28#> │ 15.8 ┆ 8.0 ┆ 351.0 ┆ 264.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 5.0  ┆ 4.0  │
    #__codelineno-4-29#> │ 19.7 ┆ 6.0 ┆ 145.0 ┆ 175.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 5.0  ┆ 6.0  │
    #__codelineno-4-30#> │ 15.0 ┆ 8.0 ┆ 301.0 ┆ 335.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 5.0  ┆ 8.0  │
    #__codelineno-4-31#> │ 21.4 ┆ 4.0 ┆ 121.0 ┆ 109.0 ┆ … ┆ 1.0 ┆ 1.0 ┆ 4.0  ┆ 2.0  │
    #__codelineno-4-32#> └──────┴─────┴───────┴───────┴───┴─────┴─────┴──────┴──────┘
`pl$DataFrame()` is similar to `data.frame()`, and `as_polars_df()` is similar to `as.data.frame()`.

Both Polars and R are column-orientated. So you can think of `DataFrames` (data.frames) as being made up of a collection of `Series` (vectors). In fact, you can create a new Polars `DataFrame` as a mix of `Series` and/or regular R vectors.  

    #__codelineno-5-1pl$DataFrame(
    #__codelineno-5-2  a = as_polars_series((1:5) * 5),
    #__codelineno-5-3  b = as_polars_series(letters[1:5]),
    #__codelineno-5-4  c = as_polars_series(c(1, 2, 3, 4, 5)),
    #__codelineno-5-5  d = c(15, 14, 13, 12, 11),
    #__codelineno-5-6  c(5, 4, 3, 2, 1),
    #__codelineno-5-7  1:5
    #__codelineno-5-8)
    #__codelineno-5-9#> shape: (5, 6)
    #__codelineno-5-10#> ┌──────┬─────┬─────┬──────┬────────────┬──────────────┐
    #__codelineno-5-11#> │ a    ┆ b   ┆ c   ┆ d    ┆ new_column ┆ new_column_1 │
    #__codelineno-5-12#> │ ---  ┆ --- ┆ --- ┆ ---  ┆ ---        ┆ ---          │
    #__codelineno-5-13#> │ f64  ┆ str ┆ f64 ┆ f64  ┆ f64        ┆ i32          │
    #__codelineno-5-14#> ╞══════╪═════╪═════╪══════╪════════════╪══════════════╡
    #__codelineno-5-15#> │ 5.0  ┆ a   ┆ 1.0 ┆ 15.0 ┆ 5.0        ┆ 1            │
    #__codelineno-5-16#> │ 10.0 ┆ b   ┆ 2.0 ┆ 14.0 ┆ 4.0        ┆ 2            │
    #__codelineno-5-17#> │ 15.0 ┆ c   ┆ 3.0 ┆ 13.0 ┆ 3.0        ┆ 3            │
    #__codelineno-5-18#> │ 20.0 ┆ d   ┆ 4.0 ┆ 12.0 ┆ 2.0        ┆ 4            │
    #__codelineno-5-19#> │ 25.0 ┆ e   ┆ 5.0 ┆ 11.0 ┆ 1.0        ┆ 5            │
    #__codelineno-5-20#> └──────┴─────┴─────┴──────┴────────────┴──────────────┘
`Series` and `DataFrame` can be operated on using many standard R functions. For example:  

    #__codelineno-6-1# Series
    #__codelineno-6-2length(ser)
    #__codelineno-6-3#> [1] 5
    #__codelineno-6-4
    #__codelineno-6-5max(ser)
    #__codelineno-6-6#> [1] 25
    #__codelineno-6-7
    #__codelineno-6-8# DataFrame
    #__codelineno-6-9dat[c(1:3, 12), c("mpg", "hp")]
    #__codelineno-6-10#> shape: (4, 2)
    #__codelineno-6-11#> ┌──────┬───────┐
    #__codelineno-6-12#> │ mpg  ┆ hp    │
    #__codelineno-6-13#> │ ---  ┆ ---   │
    #__codelineno-6-14#> │ f64  ┆ f64   │
    #__codelineno-6-15#> ╞══════╪═══════╡
    #__codelineno-6-16#> │ 21.0 ┆ 110.0 │
    #__codelineno-6-17#> │ 21.0 ┆ 110.0 │
    #__codelineno-6-18#> │ 22.8 ┆ 93.0  │
    #__codelineno-6-19#> │ 16.4 ┆ 180.0 │
    #__codelineno-6-20#> └──────┴───────┘
    #__codelineno-6-21
    #__codelineno-6-22names(dat)
    #__codelineno-6-23#>  [1] "mpg"  "cyl"  "disp" "hp"   "drat" "wt"   "qsec" "vs"   "am"   "gear"
    #__codelineno-6-24#> [11] "carb"
    #__codelineno-6-25
    #__codelineno-6-26dim(dat)
    #__codelineno-6-27#> [1] 32 11
    #__codelineno-6-28
    #__codelineno-6-29head(dat, n = 2)
    #__codelineno-6-30#> shape: (2, 11)
    #__codelineno-6-31#> ┌──────┬─────┬───────┬───────┬───┬─────┬─────┬──────┬──────┐
    #__codelineno-6-32#> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ vs  ┆ am  ┆ gear ┆ carb │
    #__codelineno-6-33#> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ --- ┆ --- ┆ ---  ┆ ---  │
    #__codelineno-6-34#> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64 ┆ f64 ┆ f64  ┆ f64  │
    #__codelineno-6-35#> ╞══════╪═════╪═══════╪═══════╪═══╪═════╪═════╪══════╪══════╡
    #__codelineno-6-36#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 4.0  ┆ 4.0  │
    #__codelineno-6-37#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 4.0  ┆ 4.0  │
    #__codelineno-6-38#> └──────┴─────┴───────┴───────┴───┴─────┴─────┴──────┴──────┘
## Methods and pipelines

Although some simple R functions work out of the box on **polars** objects, the full power of Polars is realized via *methods* . Polars methods are accessed using the `$` syntax. For example, to sort a `Series` object, we use the `$sort()` method:  

    #__codelineno-7-1ser$sort()
    #__codelineno-7-2#> polars Series: shape: (5,)
    #__codelineno-7-3#> Series: '' [f64]
    #__codelineno-7-4#> [
    #__codelineno-7-5#>  5.0
    #__codelineno-7-6#>  10.0
    #__codelineno-7-7#>  15.0
    #__codelineno-7-8#>  20.0
    #__codelineno-7-9#>  25.0
    #__codelineno-7-10#> ]
There are numerous methods designed to accomplish various tasks:  

    #__codelineno-8-1ser$max()
    #__codelineno-8-2#> [1] 25
    #__codelineno-8-3
    #__codelineno-8-4dat$slice(offset = 2, length = 3)
    #__codelineno-8-5#> shape: (3, 11)
    #__codelineno-8-6#> ┌──────┬─────┬───────┬───────┬───┬─────┬─────┬──────┬──────┐
    #__codelineno-8-7#> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ vs  ┆ am  ┆ gear ┆ carb │
    #__codelineno-8-8#> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ --- ┆ --- ┆ ---  ┆ ---  │
    #__codelineno-8-9#> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64 ┆ f64 ┆ f64  ┆ f64  │
    #__codelineno-8-10#> ╞══════╪═════╪═══════╪═══════╪═══╪═════╪═════╪══════╪══════╡
    #__codelineno-8-11#> │ 22.8 ┆ 4.0 ┆ 108.0 ┆ 93.0  ┆ … ┆ 1.0 ┆ 1.0 ┆ 4.0  ┆ 1.0  │
    #__codelineno-8-12#> │ 21.4 ┆ 6.0 ┆ 258.0 ┆ 110.0 ┆ … ┆ 1.0 ┆ 0.0 ┆ 3.0  ┆ 1.0  │
    #__codelineno-8-13#> │ 18.7 ┆ 8.0 ┆ 360.0 ┆ 175.0 ┆ … ┆ 0.0 ┆ 0.0 ┆ 3.0  ┆ 2.0  │
    #__codelineno-8-14#> └──────┴─────┴───────┴───────┴───┴─────┴─────┴──────┴──────┘
One advantage of using methods is that many more operations are possible on Polars objects using methods than through base R functions.

A second advantage is *Methods Chaining*, a core part of the Polars workflow. If you are coming from one of the other popular data wrangling libraries in R, then you probably already have an innate sense of what this means. For instance,

* In **dplyr** we use a pipe operator, e.g. `dat |> filter(...) |> select(...)`
* In **data.table** we use its indexing syntax, e.g. `DT[i, j, by][...]`
* Etc.

In **polars** our method chaining syntax takes the form `object$m1()$m2()`, where `object` is our data object, and `$m1()` and `$m2()` are appropriate methods, like subsetting or aggregation expressions.

This might all seem a little abstract, so let’s walk through some quick examples to help make things concrete. We continue with the `mtcars` dataset that we coerced to a `DataFrame` in the introduction.[1]

To start, say we compute the maximum value in each column. We can use the `$max()` method:  

    #__codelineno-9-1dat$max()
    #__codelineno-9-2#> shape: (1, 11)
    #__codelineno-9-3#> ┌──────┬─────┬───────┬───────┬───┬─────┬─────┬──────┬──────┐
    #__codelineno-9-4#> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ vs  ┆ am  ┆ gear ┆ carb │
    #__codelineno-9-5#> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ --- ┆ --- ┆ ---  ┆ ---  │
    #__codelineno-9-6#> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64 ┆ f64 ┆ f64  ┆ f64  │
    #__codelineno-9-7#> ╞══════╪═════╪═══════╪═══════╪═══╪═════╪═════╪══════╪══════╡
    #__codelineno-9-8#> │ 33.9 ┆ 8.0 ┆ 472.0 ┆ 335.0 ┆ … ┆ 1.0 ┆ 1.0 ┆ 5.0  ┆ 8.0  │
    #__codelineno-9-9#> └──────┴─────┴───────┴───────┴───┴─────┴─────┴──────┴──────┘
Now, we first use the `$tail()` method to select the last 10 rows of the dataset, and then use the `$max()` method to compute the maximums in those 10 rows:  

    #__codelineno-10-1dat$tail(10)$max()
    #__codelineno-10-2#> shape: (1, 11)
    #__codelineno-10-3#> ┌──────┬─────┬───────┬───────┬───┬─────┬─────┬──────┬──────┐
    #__codelineno-10-4#> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ vs  ┆ am  ┆ gear ┆ carb │
    #__codelineno-10-5#> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ --- ┆ --- ┆ ---  ┆ ---  │
    #__codelineno-10-6#> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64 ┆ f64 ┆ f64  ┆ f64  │
    #__codelineno-10-7#> ╞══════╪═════╪═══════╪═══════╪═══╪═════╪═════╪══════╪══════╡
    #__codelineno-10-8#> │ 30.4 ┆ 8.0 ┆ 400.0 ┆ 335.0 ┆ … ┆ 1.0 ┆ 1.0 ┆ 5.0  ┆ 8.0  │
    #__codelineno-10-9#> └──────┴─────┴───────┴───────┴───┴─────┴─────┴──────┴──────┘
Finally, we convert the result to a standard R data frame:  

    #__codelineno-11-1dat$tail(10)$max() |>
    #__codelineno-11-2  as.data.frame()
    #__codelineno-11-3#>    mpg cyl disp  hp drat    wt qsec vs am gear carb
    #__codelineno-11-4#> 1 30.4   8  400 335 4.43 3.845 18.9  1  1    5    8
Below, we will introduce several other methods, including `$select()`, `$filter()`, and `$group_by()` which allow us to do powerful data manipulations easily. To give you a small taste, we now take group-wise means:  

    #__codelineno-12-1dat$group_by("cyl")$mean()
    #__codelineno-12-2#> shape: (3, 11)
    #__codelineno-12-3#> ┌─────┬───────────┬────────────┬────────────┬───┬──────────┬──────────┬──────────┬──────────┐
    #__codelineno-12-4#> │ cyl ┆ mpg       ┆ disp       ┆ hp         ┆ … ┆ vs       ┆ am       ┆ gear     ┆ carb     │
    #__codelineno-12-5#> │ --- ┆ ---       ┆ ---        ┆ ---        ┆   ┆ ---      ┆ ---      ┆ ---      ┆ ---      │
    #__codelineno-12-6#> │ f64 ┆ f64       ┆ f64        ┆ f64        ┆   ┆ f64      ┆ f64      ┆ f64      ┆ f64      │
    #__codelineno-12-7#> ╞═════╪═══════════╪════════════╪════════════╪═══╪══════════╪══════════╪══════════╪══════════╡
    #__codelineno-12-8#> │ 8.0 ┆ 15.1      ┆ 353.1      ┆ 209.214286 ┆ … ┆ 0.0      ┆ 0.142857 ┆ 3.285714 ┆ 3.5      │
    #__codelineno-12-9#> │ 4.0 ┆ 26.663636 ┆ 105.136364 ┆ 82.636364  ┆ … ┆ 0.909091 ┆ 0.727273 ┆ 4.090909 ┆ 1.545455 │
    #__codelineno-12-10#> │ 6.0 ┆ 19.742857 ┆ 183.314286 ┆ 122.285714 ┆ … ┆ 0.571429 ┆ 0.428571 ┆ 3.857143 ┆ 3.428571 │
    #__codelineno-12-11#> └─────┴───────────┴────────────┴────────────┴───┴──────────┴──────────┴──────────┴──────────┘
## Subset

We can now start chaining together various methods (expressions) to manipulate it in different ways. For example, we can subset the data by rows ([`$filter()`](https://pola-rs.github.io/r-polars/man/DataFrame_filter.html)) and also columns ([`$select()`](https://pola-rs.github.io/r-polars/man/DataFrame_select.html)):  

    #__codelineno-13-1dat$filter(pl$col("cyl") == 6)
    #__codelineno-13-2#> shape: (7, 11)
    #__codelineno-13-3#> ┌──────┬─────┬───────┬───────┬───┬─────┬─────┬──────┬──────┐
    #__codelineno-13-4#> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ vs  ┆ am  ┆ gear ┆ carb │
    #__codelineno-13-5#> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ --- ┆ --- ┆ ---  ┆ ---  │
    #__codelineno-13-6#> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64 ┆ f64 ┆ f64  ┆ f64  │
    #__codelineno-13-7#> ╞══════╪═════╪═══════╪═══════╪═══╪═════╪═════╪══════╪══════╡
    #__codelineno-13-8#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 4.0  ┆ 4.0  │
    #__codelineno-13-9#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 4.0  ┆ 4.0  │
    #__codelineno-13-10#> │ 21.4 ┆ 6.0 ┆ 258.0 ┆ 110.0 ┆ … ┆ 1.0 ┆ 0.0 ┆ 3.0  ┆ 1.0  │
    #__codelineno-13-11#> │ 18.1 ┆ 6.0 ┆ 225.0 ┆ 105.0 ┆ … ┆ 1.0 ┆ 0.0 ┆ 3.0  ┆ 1.0  │
    #__codelineno-13-12#> │ 19.2 ┆ 6.0 ┆ 167.6 ┆ 123.0 ┆ … ┆ 1.0 ┆ 0.0 ┆ 4.0  ┆ 4.0  │
    #__codelineno-13-13#> │ 17.8 ┆ 6.0 ┆ 167.6 ┆ 123.0 ┆ … ┆ 1.0 ┆ 0.0 ┆ 4.0  ┆ 4.0  │
    #__codelineno-13-14#> │ 19.7 ┆ 6.0 ┆ 145.0 ┆ 175.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 5.0  ┆ 6.0  │
    #__codelineno-13-15#> └──────┴─────┴───────┴───────┴───┴─────┴─────┴──────┴──────┘
    #__codelineno-13-16
    #__codelineno-13-17dat$filter(pl$col("cyl") == 6 & pl$col("am") == 1)
    #__codelineno-13-18#> shape: (3, 11)
    #__codelineno-13-19#> ┌──────┬─────┬───────┬───────┬───┬─────┬─────┬──────┬──────┐
    #__codelineno-13-20#> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ vs  ┆ am  ┆ gear ┆ carb │
    #__codelineno-13-21#> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ --- ┆ --- ┆ ---  ┆ ---  │
    #__codelineno-13-22#> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64 ┆ f64 ┆ f64  ┆ f64  │
    #__codelineno-13-23#> ╞══════╪═════╪═══════╪═══════╪═══╪═════╪═════╪══════╪══════╡
    #__codelineno-13-24#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 4.0  ┆ 4.0  │
    #__codelineno-13-25#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 4.0  ┆ 4.0  │
    #__codelineno-13-26#> │ 19.7 ┆ 6.0 ┆ 145.0 ┆ 175.0 ┆ … ┆ 0.0 ┆ 1.0 ┆ 5.0  ┆ 6.0  │
    #__codelineno-13-27#> └──────┴─────┴───────┴───────┴───┴─────┴─────┴──────┴──────┘
    #__codelineno-13-28
    #__codelineno-13-29dat$select(pl$col(c("mpg", "hp")))
    #__codelineno-13-30#> shape: (32, 2)
    #__codelineno-13-31#> ┌──────┬───────┐
    #__codelineno-13-32#> │ mpg  ┆ hp    │
    #__codelineno-13-33#> │ ---  ┆ ---   │
    #__codelineno-13-34#> │ f64  ┆ f64   │
    #__codelineno-13-35#> ╞══════╪═══════╡
    #__codelineno-13-36#> │ 21.0 ┆ 110.0 │
    #__codelineno-13-37#> │ 21.0 ┆ 110.0 │
    #__codelineno-13-38#> │ 22.8 ┆ 93.0  │
    #__codelineno-13-39#> │ 21.4 ┆ 110.0 │
    #__codelineno-13-40#> │ 18.7 ┆ 175.0 │
    #__codelineno-13-41#> │ …    ┆ …     │
    #__codelineno-13-42#> │ 30.4 ┆ 113.0 │
    #__codelineno-13-43#> │ 15.8 ┆ 264.0 │
    #__codelineno-13-44#> │ 19.7 ┆ 175.0 │
    #__codelineno-13-45#> │ 15.0 ┆ 335.0 │
    #__codelineno-13-46#> │ 21.4 ┆ 109.0 │
    #__codelineno-13-47#> └──────┴───────┘
Of course, we can chain those methods to create a pipeline:  

    #__codelineno-14-1dat$filter(
    #__codelineno-14-2  pl$col("cyl") == 6
    #__codelineno-14-3)$select(
    #__codelineno-14-4  pl$col(c("mpg", "hp", "cyl"))
    #__codelineno-14-5)
    #__codelineno-14-6#> shape: (7, 3)
    #__codelineno-14-7#> ┌──────┬───────┬─────┐
    #__codelineno-14-8#> │ mpg  ┆ hp    ┆ cyl │
    #__codelineno-14-9#> │ ---  ┆ ---   ┆ --- │
    #__codelineno-14-10#> │ f64  ┆ f64   ┆ f64 │
    #__codelineno-14-11#> ╞══════╪═══════╪═════╡
    #__codelineno-14-12#> │ 21.0 ┆ 110.0 ┆ 6.0 │
    #__codelineno-14-13#> │ 21.0 ┆ 110.0 ┆ 6.0 │
    #__codelineno-14-14#> │ 21.4 ┆ 110.0 ┆ 6.0 │
    #__codelineno-14-15#> │ 18.1 ┆ 105.0 ┆ 6.0 │
    #__codelineno-14-16#> │ 19.2 ┆ 123.0 ┆ 6.0 │
    #__codelineno-14-17#> │ 17.8 ┆ 123.0 ┆ 6.0 │
    #__codelineno-14-18#> │ 19.7 ┆ 175.0 ┆ 6.0 │
    #__codelineno-14-19#> └──────┴───────┴─────┘
## Aggregate and modify

The `$select()` method that we introduced above also supports data modification, so you can simultaneously transform it while you are subsetting. However, the result will exclude any columns that weren’t specified as part of the expression. To modify or add some columns—whilst preserving all others in the dataset—it is therefore better to use the [`$with_columns()`](https://pola-rs.github.io/r-polars/man/DataFrame_with_columns.html) method. This next code chunk is equivalent to `mtcars |> dplyr::mutate(sum_mpg = sum(mpg), sum_hp = sum(hp), .by = cyl)`.  

    #__codelineno-15-1# Add the grouped sums of some selected columns.
    #__codelineno-15-2dat$with_columns(
    #__codelineno-15-3  pl$col("mpg")$sum()$over("cyl")$alias("sum_mpg"),
    #__codelineno-15-4  pl$col("hp")$sum()$over("cyl")$alias("sum_hp")
    #__codelineno-15-5)
    #__codelineno-15-6#> shape: (32, 13)
    #__codelineno-15-7#> ┌──────┬─────┬───────┬───────┬───┬──────┬──────┬─────────┬────────┐
    #__codelineno-15-8#> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ gear ┆ carb ┆ sum_mpg ┆ sum_hp │
    #__codelineno-15-9#> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ ---  ┆ ---  ┆ ---     ┆ ---    │
    #__codelineno-15-10#> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64  ┆ f64  ┆ f64     ┆ f64    │
    #__codelineno-15-11#> ╞══════╪═════╪═══════╪═══════╪═══╪══════╪══════╪═════════╪════════╡
    #__codelineno-15-12#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 4.0  ┆ 4.0  ┆ 138.2   ┆ 856.0  │
    #__codelineno-15-13#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 4.0  ┆ 4.0  ┆ 138.2   ┆ 856.0  │
    #__codelineno-15-14#> │ 22.8 ┆ 4.0 ┆ 108.0 ┆ 93.0  ┆ … ┆ 4.0  ┆ 1.0  ┆ 293.3   ┆ 909.0  │
    #__codelineno-15-15#> │ 21.4 ┆ 6.0 ┆ 258.0 ┆ 110.0 ┆ … ┆ 3.0  ┆ 1.0  ┆ 138.2   ┆ 856.0  │
    #__codelineno-15-16#> │ 18.7 ┆ 8.0 ┆ 360.0 ┆ 175.0 ┆ … ┆ 3.0  ┆ 2.0  ┆ 211.4   ┆ 2929.0 │
    #__codelineno-15-17#> │ …    ┆ …   ┆ …     ┆ …     ┆ … ┆ …    ┆ …    ┆ …       ┆ …      │
    #__codelineno-15-18#> │ 30.4 ┆ 4.0 ┆ 95.1  ┆ 113.0 ┆ … ┆ 5.0  ┆ 2.0  ┆ 293.3   ┆ 909.0  │
    #__codelineno-15-19#> │ 15.8 ┆ 8.0 ┆ 351.0 ┆ 264.0 ┆ … ┆ 5.0  ┆ 4.0  ┆ 211.4   ┆ 2929.0 │
    #__codelineno-15-20#> │ 19.7 ┆ 6.0 ┆ 145.0 ┆ 175.0 ┆ … ┆ 5.0  ┆ 6.0  ┆ 138.2   ┆ 856.0  │
    #__codelineno-15-21#> │ 15.0 ┆ 8.0 ┆ 301.0 ┆ 335.0 ┆ … ┆ 5.0  ┆ 8.0  ┆ 211.4   ┆ 2929.0 │
    #__codelineno-15-22#> │ 21.4 ┆ 4.0 ┆ 121.0 ┆ 109.0 ┆ … ┆ 4.0  ┆ 2.0  ┆ 293.3   ┆ 909.0  │
    #__codelineno-15-23#> └──────┴─────┴───────┴───────┴───┴──────┴──────┴─────────┴────────┘
For what it’s worth, the previous query could have been written more concisely as:  

    #__codelineno-16-1dat$with_columns(
    #__codelineno-16-2  pl$col(c("mpg", "hp"))$sum()$over("cyl")$name$prefix("sum_")
    #__codelineno-16-3)
    #__codelineno-16-4#> shape: (32, 13)
    #__codelineno-16-5#> ┌──────┬─────┬───────┬───────┬───┬──────┬──────┬─────────┬────────┐
    #__codelineno-16-6#> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ gear ┆ carb ┆ sum_mpg ┆ sum_hp │
    #__codelineno-16-7#> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ ---  ┆ ---  ┆ ---     ┆ ---    │
    #__codelineno-16-8#> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64  ┆ f64  ┆ f64     ┆ f64    │
    #__codelineno-16-9#> ╞══════╪═════╪═══════╪═══════╪═══╪══════╪══════╪═════════╪════════╡
    #__codelineno-16-10#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 4.0  ┆ 4.0  ┆ 138.2   ┆ 856.0  │
    #__codelineno-16-11#> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 4.0  ┆ 4.0  ┆ 138.2   ┆ 856.0  │
    #__codelineno-16-12#> │ 22.8 ┆ 4.0 ┆ 108.0 ┆ 93.0  ┆ … ┆ 4.0  ┆ 1.0  ┆ 293.3   ┆ 909.0  │
    #__codelineno-16-13#> │ 21.4 ┆ 6.0 ┆ 258.0 ┆ 110.0 ┆ … ┆ 3.0  ┆ 1.0  ┆ 138.2   ┆ 856.0  │
    #__codelineno-16-14#> │ 18.7 ┆ 8.0 ┆ 360.0 ┆ 175.0 ┆ … ┆ 3.0  ┆ 2.0  ┆ 211.4   ┆ 2929.0 │
    #__codelineno-16-15#> │ …    ┆ …   ┆ …     ┆ …     ┆ … ┆ …    ┆ …    ┆ …       ┆ …      │
    #__codelineno-16-16#> │ 30.4 ┆ 4.0 ┆ 95.1  ┆ 113.0 ┆ … ┆ 5.0  ┆ 2.0  ┆ 293.3   ┆ 909.0  │
    #__codelineno-16-17#> │ 15.8 ┆ 8.0 ┆ 351.0 ┆ 264.0 ┆ … ┆ 5.0  ┆ 4.0  ┆ 211.4   ┆ 2929.0 │
    #__codelineno-16-18#> │ 19.7 ┆ 6.0 ┆ 145.0 ┆ 175.0 ┆ … ┆ 5.0  ┆ 6.0  ┆ 138.2   ┆ 856.0  │
    #__codelineno-16-19#> │ 15.0 ┆ 8.0 ┆ 301.0 ┆ 335.0 ┆ … ┆ 5.0  ┆ 8.0  ┆ 211.4   ┆ 2929.0 │
    #__codelineno-16-20#> │ 21.4 ┆ 4.0 ┆ 121.0 ┆ 109.0 ┆ … ┆ 4.0  ┆ 2.0  ┆ 293.3   ┆ 909.0  │
    #__codelineno-16-21#> └──────┴─────┴───────┴───────┴───┴──────┴──────┴─────────┴────────┘
Similarly, here’s how we could have aggregated (i.e., collapsed) the dataset by groups instead of modifying them. We need simply invoke the `$group_by()` and [`$agg()`](https://pola-rs.github.io/r-polars/man/Expr_agg_groups.html) methods.  

    #__codelineno-17-1dat$group_by(
    #__codelineno-17-2  "cyl",
    #__codelineno-17-3  maintain_order = TRUE
    #__codelineno-17-4)$agg(
    #__codelineno-17-5  pl$col(c("mpg", "hp"))$sum()
    #__codelineno-17-6)
    #__codelineno-17-7#> shape: (3, 3)
    #__codelineno-17-8#> ┌─────┬───────┬────────┐
    #__codelineno-17-9#> │ cyl ┆ mpg   ┆ hp     │
    #__codelineno-17-10#> │ --- ┆ ---   ┆ ---    │
    #__codelineno-17-11#> │ f64 ┆ f64   ┆ f64    │
    #__codelineno-17-12#> ╞═════╪═══════╪════════╡
    #__codelineno-17-13#> │ 6.0 ┆ 138.2 ┆ 856.0  │
    #__codelineno-17-14#> │ 4.0 ┆ 293.3 ┆ 909.0  │
    #__codelineno-17-15#> │ 8.0 ┆ 211.4 ┆ 2929.0 │
    #__codelineno-17-16#> └─────┴───────┴────────┘
(arg `maintain_order = TRUE` is optional, since **polars** doesn’t sort the results of grouped operations by default. This is similar to what **data.table** does and is also true for newer versions of **dplyr**.)

The same principles of method chaining can be combined very flexibly to group by multiple variables and aggregation types.  

    #__codelineno-18-1dat$group_by(
    #__codelineno-18-2  "cyl",
    #__codelineno-18-3  manual = pl$col("am")$cast(pl$Boolean)
    #__codelineno-18-4)$agg(
    #__codelineno-18-5  mean_mpg = pl$col("mpg")$mean(),
    #__codelineno-18-6  med_hp = pl$col("hp")$median()
    #__codelineno-18-7)
    #__codelineno-18-8#> shape: (6, 4)
    #__codelineno-18-9#> ┌─────┬────────┬───────────┬────────┐
    #__codelineno-18-10#> │ cyl ┆ manual ┆ mean_mpg  ┆ med_hp │
    #__codelineno-18-11#> │ --- ┆ ---    ┆ ---       ┆ ---    │
    #__codelineno-18-12#> │ f64 ┆ bool   ┆ f64       ┆ f64    │
    #__codelineno-18-13#> ╞═════╪════════╪═══════════╪════════╡
    #__codelineno-18-14#> │ 6.0 ┆ true   ┆ 20.566667 ┆ 110.0  │
    #__codelineno-18-15#> │ 6.0 ┆ false  ┆ 19.125    ┆ 116.5  │
    #__codelineno-18-16#> │ 8.0 ┆ false  ┆ 15.05     ┆ 180.0  │
    #__codelineno-18-17#> │ 8.0 ┆ true   ┆ 15.4      ┆ 299.5  │
    #__codelineno-18-18#> │ 4.0 ┆ false  ┆ 22.9      ┆ 95.0   │
    #__codelineno-18-19#> │ 4.0 ┆ true   ┆ 28.075    ┆ 78.5   │
    #__codelineno-18-20#> └─────┴────────┴───────────┴────────┘
Note that we used the `$cast()` method to convert the data type of the `am` column. See the section below for more details on data types.

## Reshape

Polars supports data reshaping, going from both long to wide (a.k.a. “pivotting”, or `pivot_wider()` in `tidyr`), and from wide to long (a.k.a. “unpivotting”, “melting”, or `pivot_longer()` in `tidyr`). Let’s switch to the `Indometh` dataset to demonstrate some basic examples. Note that the data are currently in long format.  

    #__codelineno-19-1indo = as_polars_df(Indometh)
    #__codelineno-19-2indo
    #__codelineno-19-3#> shape: (66, 3)
    #__codelineno-19-4#> ┌─────────┬──────┬──────┐
    #__codelineno-19-5#> │ Subject ┆ time ┆ conc │
    #__codelineno-19-6#> │ ---     ┆ ---  ┆ ---  │
    #__codelineno-19-7#> │ cat     ┆ f64  ┆ f64  │
    #__codelineno-19-8#> ╞═════════╪══════╪══════╡
    #__codelineno-19-9#> │ 1       ┆ 0.25 ┆ 1.5  │
    #__codelineno-19-10#> │ 1       ┆ 0.5  ┆ 0.94 │
    #__codelineno-19-11#> │ 1       ┆ 0.75 ┆ 0.78 │
    #__codelineno-19-12#> │ 1       ┆ 1.0  ┆ 0.48 │
    #__codelineno-19-13#> │ 1       ┆ 1.25 ┆ 0.37 │
    #__codelineno-19-14#> │ …       ┆ …    ┆ …    │
    #__codelineno-19-15#> │ 6       ┆ 3.0  ┆ 0.24 │
    #__codelineno-19-16#> │ 6       ┆ 4.0  ┆ 0.17 │
    #__codelineno-19-17#> │ 6       ┆ 5.0  ┆ 0.13 │
    #__codelineno-19-18#> │ 6       ┆ 6.0  ┆ 0.1  │
    #__codelineno-19-19#> │ 6       ┆ 8.0  ┆ 0.09 │
    #__codelineno-19-20#> └─────────┴──────┴──────┘
To go from long to wide, we use the `$pivot()` method. Here we pivot the data so that every subject takes its own column.  

    #__codelineno-20-1indo_wide = indo$pivot(values = "conc", index = "time", on = "Subject")
    #__codelineno-20-2indo_wide
    #__codelineno-20-3#> shape: (11, 7)
    #__codelineno-20-4#> ┌──────┬──────┬──────┬──────┬──────┬──────┬──────┐
    #__codelineno-20-5#> │ time ┆ 1    ┆ 2    ┆ 3    ┆ 4    ┆ 5    ┆ 6    │
    #__codelineno-20-6#> │ ---  ┆ ---  ┆ ---  ┆ ---  ┆ ---  ┆ ---  ┆ ---  │
    #__codelineno-20-7#> │ f64  ┆ f64  ┆ f64  ┆ f64  ┆ f64  ┆ f64  ┆ f64  │
    #__codelineno-20-8#> ╞══════╪══════╪══════╪══════╪══════╪══════╪══════╡
    #__codelineno-20-9#> │ 0.25 ┆ 1.5  ┆ 2.03 ┆ 2.72 ┆ 1.85 ┆ 2.05 ┆ 2.31 │
    #__codelineno-20-10#> │ 0.5  ┆ 0.94 ┆ 1.63 ┆ 1.49 ┆ 1.39 ┆ 1.04 ┆ 1.44 │
    #__codelineno-20-11#> │ 0.75 ┆ 0.78 ┆ 0.71 ┆ 1.16 ┆ 1.02 ┆ 0.81 ┆ 1.03 │
    #__codelineno-20-12#> │ 1.0  ┆ 0.48 ┆ 0.7  ┆ 0.8  ┆ 0.89 ┆ 0.39 ┆ 0.84 │
    #__codelineno-20-13#> │ 1.25 ┆ 0.37 ┆ 0.64 ┆ 0.8  ┆ 0.59 ┆ 0.3  ┆ 0.64 │
    #__codelineno-20-14#> │ …    ┆ …    ┆ …    ┆ …    ┆ …    ┆ …    ┆ …    │
    #__codelineno-20-15#> │ 3.0  ┆ 0.12 ┆ 0.32 ┆ 0.22 ┆ 0.16 ┆ 0.13 ┆ 0.24 │
    #__codelineno-20-16#> │ 4.0  ┆ 0.11 ┆ 0.2  ┆ 0.12 ┆ 0.11 ┆ 0.11 ┆ 0.17 │
    #__codelineno-20-17#> │ 5.0  ┆ 0.08 ┆ 0.25 ┆ 0.11 ┆ 0.1  ┆ 0.08 ┆ 0.13 │
    #__codelineno-20-18#> │ 6.0  ┆ 0.07 ┆ 0.12 ┆ 0.08 ┆ 0.07 ┆ 0.1  ┆ 0.1  │
    #__codelineno-20-19#> │ 8.0  ┆ 0.05 ┆ 0.08 ┆ 0.08 ┆ 0.07 ┆ 0.06 ┆ 0.09 │
    #__codelineno-20-20#> └──────┴──────┴──────┴──────┴──────┴──────┴──────┘
To go from wide to long, we use the `$melt()` method.  

    #__codelineno-21-1# indo_wide$melt(id_vars = "time") # default column names are "variable" and "value"
    #__codelineno-21-2indo_wide$unpivot(index = "time", variable_name = "subject", value_name = "conc")
    #__codelineno-21-3#> shape: (66, 3)
    #__codelineno-21-4#> ┌──────┬─────────┬──────┐
    #__codelineno-21-5#> │ time ┆ subject ┆ conc │
    #__codelineno-21-6#> │ ---  ┆ ---     ┆ ---  │
    #__codelineno-21-7#> │ f64  ┆ str     ┆ f64  │
    #__codelineno-21-8#> ╞══════╪═════════╪══════╡
    #__codelineno-21-9#> │ 0.25 ┆ 1       ┆ 1.5  │
    #__codelineno-21-10#> │ 0.5  ┆ 1       ┆ 0.94 │
    #__codelineno-21-11#> │ 0.75 ┆ 1       ┆ 0.78 │
    #__codelineno-21-12#> │ 1.0  ┆ 1       ┆ 0.48 │
    #__codelineno-21-13#> │ 1.25 ┆ 1       ┆ 0.37 │
    #__codelineno-21-14#> │ …    ┆ …       ┆ …    │
    #__codelineno-21-15#> │ 3.0  ┆ 6       ┆ 0.24 │
    #__codelineno-21-16#> │ 4.0  ┆ 6       ┆ 0.17 │
    #__codelineno-21-17#> │ 5.0  ┆ 6       ┆ 0.13 │
    #__codelineno-21-18#> │ 6.0  ┆ 6       ┆ 0.1  │
    #__codelineno-21-19#> │ 8.0  ┆ 6       ┆ 0.09 │
    #__codelineno-21-20#> └──────┴─────────┴──────┘
Basic functionality aside, it should be noted that `$pivot()` can perform aggregations as part of the reshaping operation. This is useful when you have multiple observations per ID variable that need to be collapsed into unique values. The aggregating functions can be arbitrarily complex, but let’s consider a relatively simple example using our `dat` (“mtcars”) DataFrame from earlier: what is the median MPG value (`mpg`) across cylinders (`cyl`), cut by different combinations of transmission type (`am`) and engine shape (`vs`)?  

    #__codelineno-22-1dat$pivot(
    #__codelineno-22-2  values = "mpg",
    #__codelineno-22-3  index = c("am", "vs"),
    #__codelineno-22-4  on = "cyl",
    #__codelineno-22-5  aggregate_function = "median" # aggregating function
    #__codelineno-22-6)
    #__codelineno-22-7#> shape: (4, 5)
    #__codelineno-22-8#> ┌─────┬─────┬───────┬──────┬──────┐
    #__codelineno-22-9#> │ am  ┆ vs  ┆ 6.0   ┆ 4.0  ┆ 8.0  │
    #__codelineno-22-10#> │ --- ┆ --- ┆ ---   ┆ ---  ┆ ---  │
    #__codelineno-22-11#> │ f64 ┆ f64 ┆ f64   ┆ f64  ┆ f64  │
    #__codelineno-22-12#> ╞═════╪═════╪═══════╪══════╪══════╡
    #__codelineno-22-13#> │ 1.0 ┆ 0.0 ┆ 21.0  ┆ 26.0 ┆ 15.4 │
    #__codelineno-22-14#> │ 1.0 ┆ 1.0 ┆ null  ┆ 30.4 ┆ null │
    #__codelineno-22-15#> │ 0.0 ┆ 1.0 ┆ 18.65 ┆ 22.8 ┆ null │
    #__codelineno-22-16#> │ 0.0 ┆ 0.0 ┆ null  ┆ null ┆ 15.2 │
    #__codelineno-22-17#> └─────┴─────┴───────┴──────┴──────┘
Here, `"median"` is a convenience string that is equivalent to the more verbose `pl$element()$median()`. Other convenience strings include `"first"`, `"last"`, `"min"`, `"max"`, `"mean"`, `"sum"`, and `"count"`.

## Join

As a final example of how **polars** can be used for standard data wrangling tasks, let’s implement a (left) join. For this example, we’ll borrow some datasets from the **nycflights13** package.  

    #__codelineno-23-1data("flights", "planes", package = "nycflights13")
    #__codelineno-23-2flights = as_polars_df(flights)
    #__codelineno-23-3planes = as_polars_df(planes)
    #__codelineno-23-4
    #__codelineno-23-5flights$join(
    #__codelineno-23-6  planes,
    #__codelineno-23-7  on = "tailnum",
    #__codelineno-23-8  how = "left"
    #__codelineno-23-9)
    #__codelineno-23-10#> shape: (336_776, 27)
    #__codelineno-23-11#> ┌──────┬───────┬─────┬──────────┬───┬─────────┬───────┬───────┬───────────┐
    #__codelineno-23-12#> │ year ┆ month ┆ day ┆ dep_time ┆ … ┆ engines ┆ seats ┆ speed ┆ engine    │
    #__codelineno-23-13#> │ ---  ┆ ---   ┆ --- ┆ ---      ┆   ┆ ---     ┆ ---   ┆ ---   ┆ ---       │
    #__codelineno-23-14#> │ i32  ┆ i32   ┆ i32 ┆ i32      ┆   ┆ i32     ┆ i32   ┆ i32   ┆ str       │
    #__codelineno-23-15#> ╞══════╪═══════╪═════╪══════════╪═══╪═════════╪═══════╪═══════╪═══════════╡
    #__codelineno-23-16#> │ 2013 ┆ 1     ┆ 1   ┆ 517      ┆ … ┆ 2       ┆ 149   ┆ null  ┆ Turbo-fan │
    #__codelineno-23-17#> │ 2013 ┆ 1     ┆ 1   ┆ 533      ┆ … ┆ 2       ┆ 149   ┆ null  ┆ Turbo-fan │
    #__codelineno-23-18#> │ 2013 ┆ 1     ┆ 1   ┆ 542      ┆ … ┆ 2       ┆ 178   ┆ null  ┆ Turbo-fan │
    #__codelineno-23-19#> │ 2013 ┆ 1     ┆ 1   ┆ 544      ┆ … ┆ 2       ┆ 200   ┆ null  ┆ Turbo-fan │
    #__codelineno-23-20#> │ 2013 ┆ 1     ┆ 1   ┆ 554      ┆ … ┆ 2       ┆ 178   ┆ null  ┆ Turbo-fan │
    #__codelineno-23-21#> │ …    ┆ …     ┆ …   ┆ …        ┆ … ┆ …       ┆ …     ┆ …     ┆ …         │
    #__codelineno-23-22#> │ 2013 ┆ 9     ┆ 30  ┆ null     ┆ … ┆ null    ┆ null  ┆ null  ┆ null      │
    #__codelineno-23-23#> │ 2013 ┆ 9     ┆ 30  ┆ null     ┆ … ┆ null    ┆ null  ┆ null  ┆ null      │
    #__codelineno-23-24#> │ 2013 ┆ 9     ┆ 30  ┆ null     ┆ … ┆ null    ┆ null  ┆ null  ┆ null      │
    #__codelineno-23-25#> │ 2013 ┆ 9     ┆ 30  ┆ null     ┆ … ┆ null    ┆ null  ┆ null  ┆ null      │
    #__codelineno-23-26#> │ 2013 ┆ 9     ┆ 30  ┆ null     ┆ … ┆ null    ┆ null  ┆ null  ┆ null      │
    #__codelineno-23-27#> └──────┴───────┴─────┴──────────┴───┴─────────┴───────┴───────┴───────────┘
More information on the **polars** joining method can be found in the [reference manual](https://pola-rs.github.io/r-polars/man/DataFrame_join.html).

The package supports many other data manipulation operations, which we won’t cover here. Hopefully, you will already have a sense of the key syntax features. We now turn to another core idea of the Polars ecosystem: *lazy execution*.

## Lazy execution

While the “eager” execution engine of **polars** works perfectly well—as evidenced by all of the previous examples—to get the most out of the package you need to go *lazy*. Lazy execution enables several benefits, but the most important is that it improves performance. Delaying execution until the last possible moment allows Polars to apply automatic optimization to every query. Let’s take a quick look.

To create a so-called “[LazyFrame](https://pola-rs.github.io/r-polars/man/LazyFrame_class.html)” from an existing object in memory, we can invoke the `$lazy()` method.  

    #__codelineno-24-1ldat = dat$lazy()
    #__codelineno-24-2ldat
    #__codelineno-24-3#> polars LazyFrame
    #__codelineno-24-4#>  $explain(): Show the optimized query plan.
    #__codelineno-24-5#> 
    #__codelineno-24-6#> Naive plan:
    #__codelineno-24-7#> DF ["mpg", "cyl", "disp", "hp"]; PROJECT */11 COLUMNS; SELECTION: None
Or, use the `as_polars_lf()` generic function.  

    #__codelineno-25-1as_polars_lf(dat)
    #__codelineno-25-2#> polars LazyFrame
    #__codelineno-25-3#>  $explain(): Show the optimized query plan.
    #__codelineno-25-4#> 
    #__codelineno-25-5#> Naive plan:
    #__codelineno-25-6#> DF ["mpg", "cyl", "disp", "hp"]; PROJECT */11 COLUMNS; SELECTION: None
Now consider what happens when we run our subsetting query from earlier on this LazyFrame.  

    #__codelineno-26-1subset_query = ldat$filter(
    #__codelineno-26-2  pl$col("cyl") == 6
    #__codelineno-26-3)$select(
    #__codelineno-26-4  pl$col(c("mpg", "hp", "cyl"))
    #__codelineno-26-5)
    #__codelineno-26-6
    #__codelineno-26-7subset_query
    #__codelineno-26-8#> polars LazyFrame
    #__codelineno-26-9#>  $explain(): Show the optimized query plan.
    #__codelineno-26-10#> 
    #__codelineno-26-11#> Naive plan:
    #__codelineno-26-12#>  SELECT [col("mpg"), col("hp"), col("cyl")] FROM
    #__codelineno-26-13#>   FILTER [(col("cyl")) == (6.0)] FROM
    #__codelineno-26-14#>     DF ["mpg", "cyl", "disp", "hp"]; PROJECT */11 COLUMNS; SELECTION: None
Right now we only have a tree of instructions. But underneath the hood, Polars has already worked out a more optimized version of the query. We can view this optimized plan this by requesting it.  

    #__codelineno-27-1cat(subset_query$explain())
    #__codelineno-27-2#> DF ["mpg", "cyl", "disp", "hp"]; PROJECT 3/11 COLUMNS; SELECTION: [(col("cyl")) == (6.0)]
Here we see a simple, but surprisingly effective component in query optimization: *projection*. Changing the order in which our subsetting operations occurs—in this case, subsetting on columns first—reduces the memory overhead of the overall query and leads to a downstream speedup. Of course, you would hardly notice a difference for this small dataset. But the same principles carry over to much bigger datasets and more complex queries.

To actually execute the plan, we just need to invoke the `$collect()` method. This should feel very familiar if you have previously used other lazy execution engines like those provided by **arrow** or **dbplyr**.  

    #__codelineno-28-1subset_query$collect()
    #__codelineno-28-2#> shape: (7, 3)
    #__codelineno-28-3#> ┌──────┬───────┬─────┐
    #__codelineno-28-4#> │ mpg  ┆ hp    ┆ cyl │
    #__codelineno-28-5#> │ ---  ┆ ---   ┆ --- │
    #__codelineno-28-6#> │ f64  ┆ f64   ┆ f64 │
    #__codelineno-28-7#> ╞══════╪═══════╪═════╡
    #__codelineno-28-8#> │ 21.0 ┆ 110.0 ┆ 6.0 │
    #__codelineno-28-9#> │ 21.0 ┆ 110.0 ┆ 6.0 │
    #__codelineno-28-10#> │ 21.4 ┆ 110.0 ┆ 6.0 │
    #__codelineno-28-11#> │ 18.1 ┆ 105.0 ┆ 6.0 │
    #__codelineno-28-12#> │ 19.2 ┆ 123.0 ┆ 6.0 │
    #__codelineno-28-13#> │ 17.8 ┆ 123.0 ┆ 6.0 │
    #__codelineno-28-14#> │ 19.7 ┆ 175.0 ┆ 6.0 │
    #__codelineno-28-15#> └──────┴───────┴─────┘
## Data import

**polars** supports data import of both CSV and Parquet files formats. Here we demonstrate using the `airquality` dataset that also comes bundled with base R.  

    #__codelineno-29-1write.csv(airquality, "airquality.csv", row.names = FALSE)
    #__codelineno-29-2
    #__codelineno-29-3pl$read_csv("airquality.csv")
    #__codelineno-29-4#> shape: (153, 6)
    #__codelineno-29-5#> ┌───────┬─────────┬──────┬──────┬───────┬─────┐
    #__codelineno-29-6#> │ Ozone ┆ Solar.R ┆ Wind ┆ Temp ┆ Month ┆ Day │
    #__codelineno-29-7#> │ ---   ┆ ---     ┆ ---  ┆ ---  ┆ ---   ┆ --- │
    #__codelineno-29-8#> │ str   ┆ str     ┆ f64  ┆ i64  ┆ i64   ┆ i64 │
    #__codelineno-29-9#> ╞═══════╪═════════╪══════╪══════╪═══════╪═════╡
    #__codelineno-29-10#> │ 41    ┆ 190     ┆ 7.4  ┆ 67   ┆ 5     ┆ 1   │
    #__codelineno-29-11#> │ 36    ┆ 118     ┆ 8.0  ┆ 72   ┆ 5     ┆ 2   │
    #__codelineno-29-12#> │ 12    ┆ 149     ┆ 12.6 ┆ 74   ┆ 5     ┆ 3   │
    #__codelineno-29-13#> │ 18    ┆ 313     ┆ 11.5 ┆ 62   ┆ 5     ┆ 4   │
    #__codelineno-29-14#> │ NA    ┆ NA      ┆ 14.3 ┆ 56   ┆ 5     ┆ 5   │
    #__codelineno-29-15#> │ …     ┆ …       ┆ …    ┆ …    ┆ …     ┆ …   │
    #__codelineno-29-16#> │ 30    ┆ 193     ┆ 6.9  ┆ 70   ┆ 9     ┆ 26  │
    #__codelineno-29-17#> │ NA    ┆ 145     ┆ 13.2 ┆ 77   ┆ 9     ┆ 27  │
    #__codelineno-29-18#> │ 14    ┆ 191     ┆ 14.3 ┆ 75   ┆ 9     ┆ 28  │
    #__codelineno-29-19#> │ 18    ┆ 131     ┆ 8.0  ┆ 76   ┆ 9     ┆ 29  │
    #__codelineno-29-20#> │ 20    ┆ 223     ┆ 11.5 ┆ 68   ┆ 9     ┆ 30  │
    #__codelineno-29-21#> └───────┴─────────┴──────┴──────┴───────┴─────┘
Again, however, the package works best if we take the lazy approach.  

    #__codelineno-30-1pl$scan_csv("airquality.csv")
    #__codelineno-30-2#> polars LazyFrame
    #__codelineno-30-3#>  $explain(): Show the optimized query plan.
    #__codelineno-30-4#> 
    #__codelineno-30-5#> Naive plan:
    #__codelineno-30-6#> Csv SCAN [airquality.csv]
    #__codelineno-30-7#> PROJECT */6 COLUMNS
We could obviously append a set of query operators to the above LazyFrame and then collect the results. However, this workflow is even better suited to Parquet files, since we can leverage their efficient storage format on disk. Let’s see an example.  

    #__codelineno-31-1as_polars_df(airquality)$write_parquet("airquality.parquet")
    #__codelineno-31-2
    #__codelineno-31-3# eager version (okay)
    #__codelineno-31-4aq_collected = pl$read_parquet("airquality.parquet")
    #__codelineno-31-5
    #__codelineno-31-6# lazy version (better)
    #__codelineno-31-7aq = pl$scan_parquet("airquality.parquet")
    #__codelineno-31-8
    #__codelineno-31-9aq$filter(
    #__codelineno-31-10  pl$col("Month") <= 6
    #__codelineno-31-11)$group_by(
    #__codelineno-31-12  "Month"
    #__codelineno-31-13)$agg(
    #__codelineno-31-14  pl$col(c("Ozone", "Temp"))$mean()
    #__codelineno-31-15)$collect()
    #__codelineno-31-16#> shape: (2, 3)
    #__codelineno-31-17#> ┌───────┬───────────┬───────────┐
    #__codelineno-31-18#> │ Month ┆ Ozone     ┆ Temp      │
    #__codelineno-31-19#> │ ---   ┆ ---       ┆ ---       │
    #__codelineno-31-20#> │ i32   ┆ f64       ┆ f64       │
    #__codelineno-31-21#> ╞═══════╪═══════════╪═══════════╡
    #__codelineno-31-22#> │ 6     ┆ 29.444444 ┆ 79.1      │
    #__codelineno-31-23#> │ 5     ┆ 23.615385 ┆ 65.548387 │
    #__codelineno-31-24#> └───────┴───────────┴───────────┘
Finally, we can read/scan multiple files in the same directory through pattern globbing.  

    #__codelineno-32-1dir.create("airquality-ds")
    #__codelineno-32-2
    #__codelineno-32-3# Create a hive-partitioned dataset with the function from the arrow package
    #__codelineno-32-4arrow::write_dataset(airquality, "airquality-ds", partitioning = "Month")
    #__codelineno-32-5
    #__codelineno-32-6# Use pattern globbing to scan all parquet files in the folder
    #__codelineno-32-7aq2 = pl$scan_parquet("airquality-ds/**/*.parquet")
    #__codelineno-32-8
    #__codelineno-32-9# Scan the first two rows
    #__codelineno-32-10aq2$fetch(2)
    #__codelineno-32-11#> shape: (2, 5)
    #__codelineno-32-12#> ┌───────┬─────────┬──────┬──────┬─────┐
    #__codelineno-32-13#> │ Ozone ┆ Solar.R ┆ Wind ┆ Temp ┆ Day │
    #__codelineno-32-14#> │ ---   ┆ ---     ┆ ---  ┆ ---  ┆ --- │
    #__codelineno-32-15#> │ i32   ┆ i32     ┆ f64  ┆ i32  ┆ i32 │
    #__codelineno-32-16#> ╞═══════╪═════════╪══════╪══════╪═════╡
    #__codelineno-32-17#> │ 41    ┆ 190     ┆ 7.4  ┆ 67   ┆ 1   │
    #__codelineno-32-18#> │ 36    ┆ 118     ┆ 8.0  ┆ 72   ┆ 2   │
    #__codelineno-32-19#> └───────┴─────────┴──────┴──────┴─────┘
Before continuing, don’t forget to clean up by removing the newly created temp files and directory on disk.  

    #__codelineno-33-1# Remove LazyFrames which reference the files
    #__codelineno-33-2rm(aq, aq2)
    #__codelineno-33-3
    #__codelineno-33-4file.remove(c("airquality.csv", "airquality.parquet"))
    #__codelineno-33-5#> [1] TRUE TRUE
    #__codelineno-33-6unlink("airquality-ds", recursive = TRUE)
## Execute R functions within a Polars query

It is possible to mix R code with Polars by passing R functions to **polars**. This can unlock a lot of flexibility, but note that it can inhibit performance. R functions are typically slower, so we recommend using native Polars functions and expressions wherever possible.  

    #__codelineno-34-1as_polars_df(iris)$select(
    #__codelineno-34-2  pl$col("Sepal.Length")$map_batches(\(s) { # map with a R function
    #__codelineno-34-3    x = as.vector(s) # convert from Polars Series to a native R vector
    #__codelineno-34-4    x[x >= 5] = 10
    #__codelineno-34-5    x[1:10] # if return is R vector, it will automatically be converted to Polars Series again
    #__codelineno-34-6  })
    #__codelineno-34-7) |>
    #__codelineno-34-8  as.data.frame()
    #__codelineno-34-9#>    Sepal.Length
    #__codelineno-34-10#> 1          10.0
    #__codelineno-34-11#> 2           4.9
    #__codelineno-34-12#> 3           4.7
    #__codelineno-34-13#> 4           4.6
    #__codelineno-34-14#> 5          10.0
    #__codelineno-34-15#> 6          10.0
    #__codelineno-34-16#> 7           4.6
    #__codelineno-34-17#> 8          10.0
    #__codelineno-34-18#> 9           4.4
    #__codelineno-34-19#> 10          4.9
## Data types

Polars is [strongly typed](https://en.wikipedia.org/wiki/Strong_and_weak_typing) and new types can be created with the `dtypes` constructor. For example:  

    #__codelineno-35-1pl$dtypes$Float64
    #__codelineno-35-2#> DataType: Float64
The full list of valid Polars types can be found by typing `pl$dtypes` into your R console. These include *Boolean* , *Float32(64)* , *Int32(64)* , *Utf8* , *Categorical* , *Date* , etc. Note that some type names differ from what they are called in R (e.g., *Boolean* in Polars is equivalent to `logical()` in R). This might occasionally require you to look up a specific type. But the good news is that **polars** generally does a good job of inferring types automatically.

[1] Similar to how (most) **data.table** operations are limited to objects of class `data.table`, we can only perform polars operations on objects that have been converted to an appropriate **polars** class. Later on, we’ll see how to read data from disk directly in Polars format.
