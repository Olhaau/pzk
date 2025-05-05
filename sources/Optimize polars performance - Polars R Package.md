---
title: Optimize polars performance - Polars R Package
---

# Optimize polars performance - Polars R Package

url:: https://pola-rs.github.io/r-polars/vignettes/performance.html
up: [[sources]]

#source

# Optimize `polars` performance

As highlighted by the [DuckDB benchmarks](https://duckdblabs.github.io/db-benchmark/), `polars` is very efficient to deal with large datasets. Still, one can make `polars` even faster by following some good practices.

# Lazy vs eager execution

## Order of operations

In the “Get Started” vignette, we mostly used *eager* execution. This is the classic type of execution in R: write some code, link functions with pipes, run the code and it is executed line by line.

Most of the time, this kind of execution is perfectly fine. With datasets of a reasonable size (let’s say up to a few hundreds of thousands of observations and a few dozens columns), we don’t really have to worry about whether our code is optimized to save memory and time.

However, when we start dealing with much larger datasets, the order in which functions are applied is extremely important. Indeed, some functions are much more memory intensive than others.

For instance, let’s say we have a dataset with 1M observations containing country names and a few numeric columns. We would like to keep just a few of these countries and sort them alphabetically. Here, we have two operations: filtering and sorting. Sorting is much harder to do than filtering. To filter data, we simply check whether each row fills some conditions, but to sort data we have to compare rows between them and rearrange them as we go. If we don’t take this into account, sorting data before filtering it can slow down our pipeline significantly.  

    #__codelineno-0-1countries = c(
    #__codelineno-0-2  "France", "Germany", "United Kingdom", "Japan", "Columbia",
    #__codelineno-0-3  "South Korea", "Vietnam", "South Africa", "Senegal", "Iran"
    #__codelineno-0-4)
    #__codelineno-0-5
    #__codelineno-0-6set.seed(123)
    #__codelineno-0-7test = data.frame(
    #__codelineno-0-8  country = sample(countries, 1e6, TRUE),
    #__codelineno-0-9  x = sample(1:100, 1e6, TRUE),
    #__codelineno-0-10  y = sample(1:1000, 1e6, TRUE)
    #__codelineno-0-11)
    #__codelineno-1-1bench::mark(
    #__codelineno-1-2  sort_filter = {
    #__codelineno-1-3    tmp = test[order(test$country), ]
    #__codelineno-1-4    subset(tmp, country %in% c("United Kingdom", "Japan", "Vietnam"))
    #__codelineno-1-5  },
    #__codelineno-1-6  filter_sort = {
    #__codelineno-1-7    tmp = subset(test, country %in% c("United Kingdom", "Japan", "Vietnam"))
    #__codelineno-1-8    tmp[order(tmp$country), ]
    #__codelineno-1-9  }
    #__codelineno-1-10)
    #__codelineno-1-11
    #__codelineno-1-12#> Warning: Some expressions had a GC in every iteration; so filtering is
    #__codelineno-1-13#> disabled.
    #__codelineno-1-14#> # A tibble: 2 × 6
    #__codelineno-1-15#>   expression       min   median `itr/sec` mem_alloc `gc/sec`
    #__codelineno-1-16#>   <bch:expr>  <bch:tm> <bch:tm>     <dbl> <bch:byt>    <dbl>
    #__codelineno-1-17#> 1 sort_filter    10.4s    10.4s    0.0964    87.2MB    0.193
    #__codelineno-1-18#> 2 filter_sort     2.4s     2.4s    0.417     67.1MB    0.835
## How does `polars` help?

We have seen that the order in which functions are applied matters a lot. But it already takes a long time to deal with large data, we’re not going to spend even more time trying to optimize our pipeline, right?

This is where *lazy* execution comes into play. The idea is that we write our code as usual, but this time, we won’t apply it directly on a dataset but on a lazy dataset, i.e a dataset that is not loaded in memory yet (in `polars` terms, these are `DataFrame`s and `LazyFrame`s). Once our code is ready, we call `collect()` at the end of the pipeline. Before executing our code, `polars` will internally check whether it can be optimized, for example by reordering some operations.

Let’s re-use the example above but this time with `polars` syntax and 10M observations. For the purpose of this vignette, we can create a `LazyFrame` directly in our session, but if the data was stored in a CSV file for instance, we would have to scan it first with `pl$scan_csv()`:  

    #__codelineno-2-1library(polars)
    #__codelineno-2-2
    #__codelineno-2-3set.seed(123)
    #__codelineno-2-4df_test = pl$DataFrame(
    #__codelineno-2-5  country = sample(countries, 1e7, TRUE),
    #__codelineno-2-6  x = sample(1:100, 1e7, TRUE),
    #__codelineno-2-7  y = sample(1:1000, 1e7, TRUE)
    #__codelineno-2-8)
    #__codelineno-2-9
    #__codelineno-2-10lf_test = df_test$lazy()
Now, we can convert the base R code above to a `polars` query:  

    #__codelineno-3-1df_test$
    #__codelineno-3-2  sort(pl$col("country"))$
    #__codelineno-3-3  filter(
    #__codelineno-3-4  pl$col("country")$is_in(pl$lit(c("United Kingdom", "Japan", "Vietnam")))
    #__codelineno-3-5)
    #__codelineno-3-6#> shape: (3_000_835, 3)
    #__codelineno-3-7#> ┌─────────┬─────┬─────┐
    #__codelineno-3-8#> │ country ┆ x   ┆ y   │
    #__codelineno-3-9#> │ ---     ┆ --- ┆ --- │
    #__codelineno-3-10#> │ str     ┆ i32 ┆ i32 │
    #__codelineno-3-11#> ╞═════════╪═════╪═════╡
    #__codelineno-3-12#> │ Japan   ┆ 97  ┆ 7   │
    #__codelineno-3-13#> │ Japan   ┆ 96  ┆ 672 │
    #__codelineno-3-14#> │ Japan   ┆ 17  ┆ 710 │
    #__codelineno-3-15#> │ Japan   ┆ 68  ┆ 41  │
    #__codelineno-3-16#> │ Japan   ┆ 100 ┆ 109 │
    #__codelineno-3-17#> │ …       ┆ …   ┆ …   │
    #__codelineno-3-18#> │ Vietnam ┆ 89  ┆ 352 │
    #__codelineno-3-19#> │ Vietnam ┆ 62  ┆ 8   │
    #__codelineno-3-20#> │ Vietnam ┆ 52  ┆ 988 │
    #__codelineno-3-21#> │ Vietnam ┆ 85  ┆ 982 │
    #__codelineno-3-22#> │ Vietnam ┆ 74  ┆ 692 │
    #__codelineno-3-23#> └─────────┴─────┴─────┘
This works for the `DataFrame`, that uses eager execution. For the `LazyFrame`, we can write the same query:  

    #__codelineno-4-1lazy_query = lf_test$
    #__codelineno-4-2  sort(pl$col("country"))$
    #__codelineno-4-3  filter(
    #__codelineno-4-4  pl$col("country")$is_in(pl$lit(c("United Kingdom", "Japan", "Vietnam")))
    #__codelineno-4-5)
    #__codelineno-4-6
    #__codelineno-4-7lazy_query
    #__codelineno-4-8#> polars LazyFrame
    #__codelineno-4-9#>  $explain(): Show the optimized query plan.
    #__codelineno-4-10#> 
    #__codelineno-4-11#> Naive plan:
    #__codelineno-4-12#> FILTER col("country").is_in([Series]) FROM
    #__codelineno-4-13#>   SORT BY [col("country")]
    #__codelineno-4-14#>     DF ["country", "x", "y"]; PROJECT */3 COLUMNS; SELECTION: None
However, this doesn’t do anything to the data until we call `collect()` at the end. We can now compare the two approaches (in the `lazy` timing, calling `collect()` both reads the data and process it, so we include the data loading part in the `eager` timing as well):  

    #__codelineno-5-1bench::mark(
    #__codelineno-5-2  eager = df_test$
    #__codelineno-5-3    sort(pl$col("country"))$
    #__codelineno-5-4    filter(
    #__codelineno-5-5      pl$col("country")$is_in(pl$lit(c("United Kingdom", "Japan", "Vietnam")))
    #__codelineno-5-6    ),
    #__codelineno-5-7  lazy = lazy_query$collect(),
    #__codelineno-5-8  iterations = 10
    #__codelineno-5-9)
    #__codelineno-5-10
    #__codelineno-5-11#> # A tibble: 2 × 6
    #__codelineno-5-12#>   expression      min   median `itr/sec` mem_alloc `gc/sec`
    #__codelineno-5-13#>   <bch:expr> <bch:tm> <bch:tm>     <dbl> <bch:byt>    <dbl>
    #__codelineno-5-14#> 1 eager         1.18s    1.28s     0.754    9.95KB        0
    #__codelineno-5-15#> 2 lazy       526.98ms 577.35ms     1.72       864B        0
On this very simple query, using lazy execution instead of eager execution lead to a 1.7-2.2x decrease in time.

So what happened? Under the hood, `polars` reorganized the query so that it filters rows while reading the csv into memory, and then sorts the remaining data. This can be seen by comparing the original query with the optimized one:  

    #__codelineno-6-1lazy_query$explain(optimized = FALSE) |>
    #__codelineno-6-2  cat()
    #__codelineno-6-3#> FILTER col("country").is_in([Series]) FROM
    #__codelineno-6-4#>   SORT BY [col("country")]
    #__codelineno-6-5#>     DF ["country", "x", "y"]; PROJECT */3 COLUMNS; SELECTION: None
    #__codelineno-6-6
    #__codelineno-6-7lazy_query$explain() |>
    #__codelineno-6-8  cat()
    #__codelineno-6-9#> SORT BY [col("country")]
    #__codelineno-6-10#>   DF ["country", "x", "y"]; PROJECT */3 COLUMNS; SELECTION: col("country").is_in([Series])
Note that queries must be read from bottom to top, i.e the optimized query is “select the dataset where the column ‘country’ matches these values, then sort the data by the values of ‘country’”.

# Use `polars` functions

`polars` comes with a large number of built-in, optimized, basic functions that should cover most aspects of data wrangling. These functions are designed to be very memory efficient. Therefore, using R functions or converting data back and forth between `polars` and R is discouraged as it can lead to a large decrease in efficiency.

Let’s use the test data from the previous section and let’s say that we only want to check whether each country contains “na”. This can be done in (at least) two ways: with the built-in function `contains()` and with the base R function `grepl()`. However, using the built-in function is much faster:  

    #__codelineno-7-1bench::mark(
    #__codelineno-7-2  contains = df_test$with_columns(
    #__codelineno-7-3    pl$col("country")$str$contains("na")
    #__codelineno-7-4  ),
    #__codelineno-7-5  grepl = df_test$with_columns(
    #__codelineno-7-6    pl$col("country")$map_batches(\(s) { # map with a R function
    #__codelineno-7-7      grepl("na", s)
    #__codelineno-7-8    })
    #__codelineno-7-9  ),
    #__codelineno-7-10  grepl_nv = df_test$limit(1e6)$with_columns(
    #__codelineno-7-11    pl$col("country")$apply(\(str) {
    #__codelineno-7-12      grepl("na", str)
    #__codelineno-7-13    }, return_type = pl$Boolean)
    #__codelineno-7-14  ),
    #__codelineno-7-15  iterations = 10
    #__codelineno-7-16)
    #__codelineno-7-17
    #__codelineno-7-18
    #__codelineno-7-19#> Warning: Some expressions had a GC in every iteration; so filtering is
    #__codelineno-7-20#> disabled.
    #__codelineno-7-21#> # A tibble: 3 × 6
    #__codelineno-7-22#>   expression      min   median `itr/sec` mem_alloc `gc/sec`
    #__codelineno-7-23#>   <bch:expr> <bch:tm> <bch:tm>     <dbl> <bch:byt>    <dbl>
    #__codelineno-7-24#> 1 contains   387.02ms 432.12ms     2.27   401.86KB    0
    #__codelineno-7-25#> 2 grepl         2.06s    2.11s     0.466  114.79MB    0.512
    #__codelineno-7-26#> 3 grepl_nv      6.42s    6.52s     0.153    7.65MB   10.3
Using custom R functions can be useful, but when possible, you should use the functions provided by `polars`. See the Reference tab for a complete list of functions.

# Streaming data

Finally, quoting [Polars User Guide](https://pola-rs.github.io/polars-book/user-guide/concepts/streaming/):
> One additional benefit of the lazy API is that it allows queries to be executed in a streaming manner. Instead of processing the data all-at-once Polars can execute the query in batches allowing you to process datasets that are larger-than-memory.

To use streaming mode, we can just add `streaming = TRUE` in `collect()` (note that this is still an alpha feature):  

    #__codelineno-8-1bench::mark(
    #__codelineno-8-2  lazy = lazy_query$collect(),
    #__codelineno-8-3  lazy_streaming = lazy_query$collect(streaming = TRUE),
    #__codelineno-8-4  iterations = 20
    #__codelineno-8-5)
    #__codelineno-8-6
    #__codelineno-8-7#> # A tibble: 2 × 6
    #__codelineno-8-8#>   expression          min   median `itr/sec` mem_alloc `gc/sec`
    #__codelineno-8-9#>   <bch:expr>     <bch:tm> <bch:tm>     <dbl> <bch:byt>    <dbl>
    #__codelineno-8-10#> 1 lazy              482ms    546ms      1.85      864B        0
    #__codelineno-8-11#> 2 lazy_streaming    292ms    346ms      2.90      864B        0
