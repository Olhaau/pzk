---
title: R and Polars expressions • tidypolars
---

# R and Polars expressions • tidypolars

url:: https://www.tidypolars.etiennebacher.com/articles/r-and-polars-expressions
up: [[sources]]

#source

When we use the `tidyverse`, we use R expressions in mainly three places: [filter()](https://dplyr.tidyverse.org/reference/filter.html), [mutate()](https://dplyr.tidyverse.org/reference/mutate.html), and [summarize()](https://dplyr.tidyverse.org/reference/summarise.html).

This is very convenient but creates a challenge for `tidypolars`. While it is possible to convert a Polars Data/LazyFrame to an R `data.frame`, apply functions on it and then convert it back to Polars, it is strongly discouraged to do so because it doesn’t take advantage of Polars optimizations.

Indeed, Polars comes with dozens of built-in functions for maths (`median`, `var`, `arccos`, …), string manipulation (`len_chars`, `starts`, …), and date-time (`hour`, `quarter`, `ordinal_day`, …). All of these functions are optimized internally and are ran in parallel under the hood, which will not be the case if we pass R functions.

However, using these Polars expressions would imply that we need to learn these new functions and this new syntax. To avoid doing that, `tidypolars` will automatically translate R expressions into Polars ones. Basically, **you can keep writing R expressions in most situations**, and they will automatically be translated to Polars syntax.

However, there are some situations where this might not work, so this vignette explains the process and the limitations.  

## How does `tidypolars` translate R expressions into Polars expressions?

When `tidypolars` receives an expression, it runs a function `translate()` several times until all components are translated to their Polars equivalent. There are four possible components: single values, column names, external objects, and functions.  

### Single values, column names, and external objects

If you pass a single value, like `x = 1` or `x = "a"`, it is wrapped into `pl$lit()`. This is also the case for external objects with the difference that these need to be wrapped in `{{ }}` and are evaluated before being wrapped into `pl$lit()`.

Column names, like `x = mpg`, are wrapped into `pl$col()`.

    x = "a"               ->  x = pl$lit("a")
    x = {{ some_value }}  ->  x = pl$lit(*value*)
    x = mpg               ->  x = pl$col("mpg")
### Functions

Functions are split into two categories: built-in functions (i.e functions provided by base R or by other packages), and user-defined functions (UDF) that are written by the user (you).  

#### Built-in functions

In the first case, `tidypolars` checks the function name and whether it has already been translated internally. For example, if we call the R function `mean(x, trim = 2)`, then it looks for a translation of [mean()](https://rdrr.io/r/base/mean.html). You can see the list of supported R functions at the bottom of this vignette. Note that most of essential base R functions are supported, as well as many functions from `dplyr` or from `stringr` for example.

Now that `tidypolars` knows that a translation of [mean()](https://rdrr.io/r/base/mean.html) exists, it parses the arguments in the call to translate them to the Polars syntax: internally, `x` is converted to `pl$col("x")` if there is a column `"x"` in the data. Sometimes, additional arguments do not have an equivalent in Polars. This is the case for the argument `trim` here. In this case, `tidypolars` ignores this argument and warns the user:  

    https://rdrr.io/r/base/library.html(https://tidypolars.etiennebacher.com)
    https://rdrr.io/r/base/library.html(https://pola-rs.github.io/r-polars/)

    mtcars |>
      https://pola-rs.github.io/r-polars/man/as_polars_df.html() |>
      https://dplyr.tidyverse.org/reference/mutate.html(x = https://rdrr.io/r/base/mean.html(mpg, trim = 2))
    #> Warning: 
    #> Package tidypolars doesn't know how to use some arguments of `mean()`.
    #> The following argument(s) will be ignored: `trim`.
    #> shape: (32, 12)
    #> ┌──────┬─────┬───────┬───────┬───┬─────┬──────┬──────┬───────────┐
    #> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ am  ┆ gear ┆ carb ┆ x         │
    #> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ --- ┆ ---  ┆ ---  ┆ ---       │
    #> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64 ┆ f64  ┆ f64  ┆ f64       │
    #> ╞══════╪═════╪═══════╪═══════╪═══╪═════╪══════╪══════╪═══════════╡
    #> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 1.0 ┆ 4.0  ┆ 4.0  ┆ 20.090625 │
    #> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 1.0 ┆ 4.0  ┆ 4.0  ┆ 20.090625 │
    #> │ 22.8 ┆ 4.0 ┆ 108.0 ┆ 93.0  ┆ … ┆ 1.0 ┆ 4.0  ┆ 1.0  ┆ 20.090625 │
    #> │ 21.4 ┆ 6.0 ┆ 258.0 ┆ 110.0 ┆ … ┆ 0.0 ┆ 3.0  ┆ 1.0  ┆ 20.090625 │
    #> │ 18.7 ┆ 8.0 ┆ 360.0 ┆ 175.0 ┆ … ┆ 0.0 ┆ 3.0  ┆ 2.0  ┆ 20.090625 │
    #> │ …    ┆ …   ┆ …     ┆ …     ┆ … ┆ …   ┆ …    ┆ …    ┆ …         │
    #> │ 30.4 ┆ 4.0 ┆ 95.1  ┆ 113.0 ┆ … ┆ 1.0 ┆ 5.0  ┆ 2.0  ┆ 20.090625 │
    #> │ 15.8 ┆ 8.0 ┆ 351.0 ┆ 264.0 ┆ … ┆ 1.0 ┆ 5.0  ┆ 4.0  ┆ 20.090625 │
    #> │ 19.7 ┆ 6.0 ┆ 145.0 ┆ 175.0 ┆ … ┆ 1.0 ┆ 5.0  ┆ 6.0  ┆ 20.090625 │
    #> │ 15.0 ┆ 8.0 ┆ 301.0 ┆ 335.0 ┆ … ┆ 1.0 ┆ 5.0  ┆ 8.0  ┆ 20.090625 │
    #> │ 21.4 ┆ 4.0 ┆ 121.0 ┆ 109.0 ┆ … ┆ 1.0 ┆ 4.0  ┆ 2.0  ┆ 20.090625 │
    #> └──────┴─────┴───────┴───────┴───┴─────┴──────┴──────┴───────────┘
This behavior [can be changed](https://tidypolars.etiennebacher.com/reference/tidypolars-options) to throw an error instead.  

#### User-defined functions

User-defined functions (UDF) are more challenging. Indeed, it is technically possible to inspect the code inside a UDF, but rewriting it to match Polars syntax would be extremely complicated. In this situation, you will have to rewrite your custom function using Polars syntax so that it returns a Polars expression. For example, we could make a function to standardize a column like this:  

    pl_standardize <- function(x) {
      (x - x$mean()) / x$std()
    }
Remember that the column name used as `x` will end up wrapped into `pl$col()`, so to check that your function returns a Polars expression, you have to provide a `pl$col()` call:  

    pl_standardize(pl$col("mpg"))
    #> polars Expr: [([(col("mpg")) - (col("mpg").mean())]) // (col("mpg").std())]
This function correctly returns a Polars expression, so we can now use it like any other function:  

    mtcars |>
      https://pola-rs.github.io/r-polars/man/as_polars_df.html() |>
      https://dplyr.tidyverse.org/reference/mutate.html(x = pl_standardize(mpg))
    #> shape: (32, 12)
    #> ┌──────┬─────┬───────┬───────┬───┬─────┬──────┬──────┬───────────┐
    #> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ am  ┆ gear ┆ carb ┆ x         │
    #> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ --- ┆ ---  ┆ ---  ┆ ---       │
    #> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64 ┆ f64  ┆ f64  ┆ f64       │
    #> ╞══════╪═════╪═══════╪═══════╪═══╪═════╪══════╪══════╪═══════════╡
    #> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 1.0 ┆ 4.0  ┆ 4.0  ┆ 0.150885  │
    #> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 1.0 ┆ 4.0  ┆ 4.0  ┆ 0.150885  │
    #> │ 22.8 ┆ 4.0 ┆ 108.0 ┆ 93.0  ┆ … ┆ 1.0 ┆ 4.0  ┆ 1.0  ┆ 0.449543  │
    #> │ 21.4 ┆ 6.0 ┆ 258.0 ┆ 110.0 ┆ … ┆ 0.0 ┆ 3.0  ┆ 1.0  ┆ 0.217253  │
    #> │ 18.7 ┆ 8.0 ┆ 360.0 ┆ 175.0 ┆ … ┆ 0.0 ┆ 3.0  ┆ 2.0  ┆ -0.230735 │
    #> │ …    ┆ …   ┆ …     ┆ …     ┆ … ┆ …   ┆ …    ┆ …    ┆ …         │
    #> │ 30.4 ┆ 4.0 ┆ 95.1  ┆ 113.0 ┆ … ┆ 1.0 ┆ 5.0  ┆ 2.0  ┆ 1.710547  │
    #> │ 15.8 ┆ 8.0 ┆ 351.0 ┆ 264.0 ┆ … ┆ 1.0 ┆ 5.0  ┆ 4.0  ┆ -0.711907 │
    #> │ 19.7 ┆ 6.0 ┆ 145.0 ┆ 175.0 ┆ … ┆ 1.0 ┆ 5.0  ┆ 6.0  ┆ -0.064813 │
    #> │ 15.0 ┆ 8.0 ┆ 301.0 ┆ 335.0 ┆ … ┆ 1.0 ┆ 5.0  ┆ 8.0  ┆ -0.844644 │
    #> │ 21.4 ┆ 4.0 ┆ 121.0 ┆ 109.0 ┆ … ┆ 1.0 ┆ 4.0  ┆ 2.0  ┆ 0.217253  │
    #> └──────┴─────┴───────┴───────┴───┴─────┴──────┴──────┴───────────┘
#### Special case: `across()`

[`across()`](https://dplyr.tidyverse.org/reference/across.html) is a very useful function that applies a function (or a list of functions) to a selection of columns. It accepts built-in functions, UDFs, and anonymous functions.  

    mtcars |>
      https://pola-rs.github.io/r-polars/man/as_polars_df.html() |>
      https://dplyr.tidyverse.org/reference/mutate.html(
        https://dplyr.tidyverse.org/reference/across.html(
          .cols = https://tidyselect.r-lib.org/reference/starts_with.html("a"),
          https://rdrr.io/r/base/list.html(mean = mean, stand = pl_standardize, ~ https://rdrr.io/r/stats/sd.html(.x))
        )
      )
    #> shape: (32, 23)
    #> ┌──────┬─────┬───────┬───────┬───┬──────────┬───────────┬────────────┬────────┐
    #> │ mpg  ┆ cyl ┆ disp  ┆ hp    ┆ … ┆ gear_3   ┆ carb_mean ┆ carb_stand ┆ carb_3 │
    #> │ ---  ┆ --- ┆ ---   ┆ ---   ┆   ┆ ---      ┆ ---       ┆ ---        ┆ ---    │
    #> │ f64  ┆ f64 ┆ f64   ┆ f64   ┆   ┆ f64      ┆ f64       ┆ f64        ┆ f64    │
    #> ╞══════╪═════╪═══════╪═══════╪═══╪══════════╪═══════════╪════════════╪════════╡
    #> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 0.737804 ┆ 2.8125    ┆ 0.735203   ┆ 1.6152 │
    #> │ 21.0 ┆ 6.0 ┆ 160.0 ┆ 110.0 ┆ … ┆ 0.737804 ┆ 2.8125    ┆ 0.735203   ┆ 1.6152 │
    #> │ 22.8 ┆ 4.0 ┆ 108.0 ┆ 93.0  ┆ … ┆ 0.737804 ┆ 2.8125    ┆ -1.122152  ┆ 1.6152 │
    #> │ 21.4 ┆ 6.0 ┆ 258.0 ┆ 110.0 ┆ … ┆ 0.737804 ┆ 2.8125    ┆ -1.122152  ┆ 1.6152 │
    #> │ 18.7 ┆ 8.0 ┆ 360.0 ┆ 175.0 ┆ … ┆ 0.737804 ┆ 2.8125    ┆ -0.503034  ┆ 1.6152 │
    #> │ …    ┆ …   ┆ …     ┆ …     ┆ … ┆ …        ┆ …         ┆ …          ┆ …      │
    #> │ 30.4 ┆ 4.0 ┆ 95.1  ┆ 113.0 ┆ … ┆ 0.737804 ┆ 2.8125    ┆ -0.503034  ┆ 1.6152 │
    #> │ 15.8 ┆ 8.0 ┆ 351.0 ┆ 264.0 ┆ … ┆ 0.737804 ┆ 2.8125    ┆ 0.735203   ┆ 1.6152 │
    #> │ 19.7 ┆ 6.0 ┆ 145.0 ┆ 175.0 ┆ … ┆ 0.737804 ┆ 2.8125    ┆ 1.97344    ┆ 1.6152 │
    #> │ 15.0 ┆ 8.0 ┆ 301.0 ┆ 335.0 ┆ … ┆ 0.737804 ┆ 2.8125    ┆ 3.211677   ┆ 1.6152 │
    #> │ 21.4 ┆ 4.0 ┆ 121.0 ┆ 109.0 ┆ … ┆ 0.737804 ┆ 2.8125    ┆ -0.503034  ┆ 1.6152 │
    #> └──────┴─────┴───────┴───────┴───┴──────────┴───────────┴────────────┴────────┘
Similarly, UDFs and anonymous functions will error if they don’t return a Polars expression:  

    mtcars |>
      https://pola-rs.github.io/r-polars/man/as_polars_df.html() |>
      https://dplyr.tidyverse.org/reference/mutate.html(
        https://dplyr.tidyverse.org/reference/across.html(
          .cols = https://tidyselect.r-lib.org/reference/starts_with.html("a"),
          .fns = https://rdrr.io/r/base/list.html(
            mean = mean,
            function(x) {
              (x - https://rdrr.io/r/base/mean.html(x)) / https://rdrr.io/r/stats/sd.html(x)
            },
            ~ https://rdrr.io/r/stats/sd.html(.x)
          )
        )
      )
    #> Error in `mutate()`:
    #> ! Could not evaluate an anonymous function in `across()`.
    #> ℹ Are you sure the anonymous function returns a Polars expression?
#### Non-translated functions

When a function provided in base R or in another package cannot be translated, `tidypolars` usually throws an error (it’s not necessary to understand what [agrep()](https://rdrr.io/r/base/agrep.html) does here, you should only know that it is not translated by `tidypolars`):  

    dat <- pl$DataFrame(a = https://rdrr.io/r/base/c.html("d", "e", "f"), foo = https://rdrr.io/r/base/c.html(2, 1, 2))

    dat |>
      https://dplyr.tidyverse.org/reference/filter.html(foo >= https://rdrr.io/r/base/agrep.html("a", a))
    #> Error in `filter()`:
    #> ! `tidypolars` doesn't know how to translate this function: `agrep()`.
This is because `foo >= agrep("a", a)` uses the values from the columns `a` and `foo`, which are stored in the `polars` DataFrame. Therefore, `polars` needs a translation of [agrep()](https://rdrr.io/r/base/agrep.html), which doesn’t exist.

Now, let’s assume that we have this data instead:  

    dat <- pl$DataFrame(foo = https://rdrr.io/r/base/c.html(2, 1, 2))
    a <- https://rdrr.io/r/base/c.html("d", "e", "f")

    dat |>
      https://dplyr.tidyverse.org/reference/filter.html(foo >= https://rdrr.io/r/base/agrep.html("a", a))
    #> shape: (1, 1)
    #> ┌─────┐
    #> │ foo │
    #> │ --- │
    #> │ f64 │
    #> ╞═════╡
    #> │ 2.0 │
    #> └─────┘
Starting from `tidypolars` 0.14.0, this doesn’t error anymore. Why is that? The reason is that while `foo >= agrep("a", a)` uses a column from the data (`foo`), `agrep("a", a)` no longer does since `a` is an object in the environment and not in the data anymore. Therefore, we can evaluate `agrep("a", a)` first and then use its result when evaluating `foo >= agrep("a", a)`.

More generally, `tidypolars` checks whether a function uses columns from the data and skips the translation part if it doesn’t, allowing us to use more functions than what is translated by `tidypolars` (only if they don’t use columns as inputs).

Note that expressions that don’t use the data are evaluated before running `polars` in the background so they don’t benefit from `polars` parallel evaluation for instance.  

## A note on performance

As we saw above, a large part of the work of `tidypolars` is to translate existing R code to its `polars` equivalent. This takes some time, especially when translating R expressions used in [mutate()](https://dplyr.tidyverse.org/reference/mutate.html) or [filter()](https://dplyr.tidyverse.org/reference/filter.html) for instance, and it might be noticeable if you run `tidypolars` many times on small datasets.

This translation process takes more time as the number of expressions increases but it doesn’t depend on the size of the data. In other words, translating the code from R to `polars` takes about the same time when applied on a dataset of 1M or 10M observations. Therefore, this drawback should become less and less noticeable as the size of the dataset increases since most of the time will then be taken by the actual computation performed by `polars`.  

### List of base R and `tidyverse` functions supported by `tidypolars`

|-------------|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `base`      | `abs`             |                                                                                                                                                                                                                                                                      |
| `base`      | `acos`            |                                                                                                                                                                                                                                                                      |
| `base`      | `acosh`           |                                                                                                                                                                                                                                                                      |
| `base`      | `all`             |                                                                                                                                                                                                                                                                      |
| `base`      | `any`             |                                                                                                                                                                                                                                                                      |
| `base`      | `asin`            |                                                                                                                                                                                                                                                                      |
| `base`      | `asinh`           |                                                                                                                                                                                                                                                                      |
| `base`      | `atan`            |                                                                                                                                                                                                                                                                      |
| `base`      | `atanh`           |                                                                                                                                                                                                                                                                      |
| `base`      | `ceiling`         |                                                                                                                                                                                                                                                                      |
| `base`      | `cos`             |                                                                                                                                                                                                                                                                      |
| `base`      | `cosh`            |                                                                                                                                                                                                                                                                      |
| `base`      | `cummin`          |                                                                                                                                                                                                                                                                      |
| `base`      | `cumsum`          |                                                                                                                                                                                                                                                                      |
| `base`      | `diff`            |                                                                                                                                                                                                                                                                      |
| `base`      | `exp`             |                                                                                                                                                                                                                                                                      |
| `base`      | `floor`           |                                                                                                                                                                                                                                                                      |
| `base`      | `grepl`           |                                                                                                                                                                                                                                                                      |
| `base`      | `ifelse`          |                                                                                                                                                                                                                                                                      |
| `base`      | `ISOdatetime`     |                                                                                                                                                                                                                                                                      |
| `base`      | `length`          |                                                                                                                                                                                                                                                                      |
| `base`      | `log`             |                                                                                                                                                                                                                                                                      |
| `base`      | `log10`           |                                                                                                                                                                                                                                                                      |
| `base`      | `max`             |                                                                                                                                                                                                                                                                      |
| `base`      | `mean`            |                                                                                                                                                                                                                                                                      |
| `base`      | `min`             |                                                                                                                                                                                                                                                                      |
| `base`      | `nchar`           |                                                                                                                                                                                                                                                                      |
| `base`      | `paste0`          |                                                                                                                                                                                                                                                                      |
| `base`      | `paste`           |                                                                                                                                                                                                                                                                      |
| `base`      | `rank`            |                                                                                                                                                                                                                                                                      |
| `base`      | `rev`             |                                                                                                                                                                                                                                                                      |
| `base`      | `round`           |                                                                                                                                                                                                                                                                      |
| `base`      | `seq`             |                                                                                                                                                                                                                                                                      |
| `base`      | `seq_len`         |                                                                                                                                                                                                                                                                      |
| `base`      | `sin`             |                                                                                                                                                                                                                                                                      |
| `base`      | `sinh`            |                                                                                                                                                                                                                                                                      |
| `base`      | `sort`            |                                                                                                                                                                                                                                                                      |
| `base`      | `sqrt`            |                                                                                                                                                                                                                                                                      |
| `base`      | `strptime`        |                                                                                                                                                                                                                                                                      |
| `base`      | `substr`          |                                                                                                                                                                                                                                                                      |
| `base`      | `tan`             |                                                                                                                                                                                                                                                                      |
| `base`      | `tanh`            |                                                                                                                                                                                                                                                                      |
| `base`      | `tolower`         |                                                                                                                                                                                                                                                                      |
| `base`      | `toupper`         |                                                                                                                                                                                                                                                                      |
| `base`      | `unique`          |                                                                                                                                                                                                                                                                      |
| `base`      | `which.min`       |                                                                                                                                                                                                                                                                      |
| `base`      | `which.max`       |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `between`         |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `case_match`      |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `case_when`       |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `coalesce`        |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `consecutive_id`  |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `dense_rank`      |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `first`           |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `group_keys`      |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `group_vars`      |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `if_else`         |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `lag`             |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `lead`            |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `last`            |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `min_rank`        |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `n`               |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `nth`             |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `n_distinct`      |                                                                                                                                                                                                                                                                      |
| `dplyr`     | `row_number`      | Doesn’t work when `x` is missing.                                                                                                                                                                                                                                    |
| `lubridate` | `am`              | Contrarily to `lubridate`, this doesn’t work on dates, only datetimes.                                                                                                                                                                                               |
| `lubridate` | `day`             |                                                                                                                                                                                                                                                                      |
| `lubridate` | `days`            |                                                                                                                                                                                                                                                                      |
| `lubridate` | `days_in_month`   |                                                                                                                                                                                                                                                                      |
| `lubridate` | `date`            |                                                                                                                                                                                                                                                                      |
| `lubridate` | `ddays`           |                                                                                                                                                                                                                                                                      |
| `lubridate` | `dhours`          |                                                                                                                                                                                                                                                                      |
| `lubridate` | `dmilliseconds`   |                                                                                                                                                                                                                                                                      |
| `lubridate` | `dminutes`        |                                                                                                                                                                                                                                                                      |
| `lubridate` | `dseconds`        |                                                                                                                                                                                                                                                                      |
| `lubridate` | `dweeks`          |                                                                                                                                                                                                                                                                      |
| `lubridate` | `force_tz`        | `tidypolars` cannot use several timezones in a single column, while [lubridate::force_tz()](https://lubridate.tidyverse.org/reference/force_tz.html) can.                                                                                                            |
| `lubridate` | `hours`           |                                                                                                                                                                                                                                                                      |
| `lubridate` | `leap_year`       |                                                                                                                                                                                                                                                                      |
| `lubridate` | `make_date`       |                                                                                                                                                                                                                                                                      |
| `lubridate` | `make_datetime`   | In [lubridate::make_datetime()](https://lubridate.tidyverse.org/reference/make_datetime.html), when there is an overflow (for example `hours = 25`), then it is automatically converted to the higher unit (for example 1 day and 1h). In Polars, this returns `NA`. |
| `lubridate` | `mday`            |                                                                                                                                                                                                                                                                      |
| `lubridate` | `microseconds`    |                                                                                                                                                                                                                                                                      |
| `lubridate` | `milliseconds`    |                                                                                                                                                                                                                                                                      |
| `lubridate` | `minutes`         |                                                                                                                                                                                                                                                                      |
| `lubridate` | `month`           |                                                                                                                                                                                                                                                                      |
| `lubridate` | `nanoseconds`     |                                                                                                                                                                                                                                                                      |
| `lubridate` | `now`             |                                                                                                                                                                                                                                                                      |
| `lubridate` | `pm`              | Contrarily to `lubridate`, this doesn’t work on dates, only datetimes.                                                                                                                                                                                               |
| `lubridate` | `quarter`         |                                                                                                                                                                                                                                                                      |
| `lubridate` | `seconds`         |                                                                                                                                                                                                                                                                      |
| `lubridate` | `today`           |                                                                                                                                                                                                                                                                      |
| `lubridate` | `wday`            | Requires `week_start == 7`. If `label = TRUE`, it returns a string variable and not a factor as in `lubridate`.                                                                                                                                                      |
| `lubridate` | `weeks`           |                                                                                                                                                                                                                                                                      |
| `lubridate` | `with_tz`         | `tidypolars` cannot use several timezones in a single column, while [lubridate::with_tz()](https://lubridate.tidyverse.org/reference/with_tz.html) can. Unrecognized timezones and/or NULL are not supported and will throw an error.                                |
| `lubridate` | `yday`            |                                                                                                                                                                                                                                                                      |
| `lubridate` | `year`            |                                                                                                                                                                                                                                                                      |
| `stats`     | `median`          |                                                                                                                                                                                                                                                                      |
| `stats`     | `lag`             |                                                                                                                                                                                                                                                                      |
| `stats`     | `sd`              |                                                                                                                                                                                                                                                                      |
| `stats`     | `var`             |                                                                                                                                                                                                                                                                      |
| `stringr`   | `regex`           |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_count`       |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_detect`      |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_dup`         |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_ends`        |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_extract`     |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_extract_all` |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_length`      |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_pad`         |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_remove`      |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_remove_all`  |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_replace`     |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_replace_all` |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_replace_na`  |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_split`       |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_split_i`     |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_squish`      |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_starts`      |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_sub`         |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_trim`        |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_to_lower`    |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_to_title`    | Letters following apostrophe will be capitalized as well, which differs from the `stringr` implementation.                                                                                                                                                           |
| `stringr`   | `str_to_upper`    |                                                                                                                                                                                                                                                                      |
| `stringr`   | `str_trunc`       |                                                                                                                                                                                                                                                                      |
| `stringr`   | `word`            |                                                                                                                                                                                                                                                                      |
| `tidyr`     | `replace_na`      |                                                                                                                                                                                                                                                                      |
| `tools`     | `toTitleCase`     | Letters following apostrophe will be capitalized as well, which differs from the `tools` implementation.                                                                                                                                                             |

| ## On this page
|
