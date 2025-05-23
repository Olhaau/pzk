---
title: Polars R Package
---

# Polars R Package

url:: https://pola-rs.github.io/r-polars/
up: [[sources]]

#source

# polars

[![R-multiverse
status](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fcommunity.r-multiverse.org%2Fapi%2Fpackages%2Fpolars&query=%24.Version&label=r-multiverse)](https://community.r-multiverse.org/polars) [![R-universe status
badge](https://rpolars.r-universe.dev/badges/polars)](https://rpolars.r-universe.dev) [![CRAN
status](https://www.r-pkg.org/badges/version/polars)](https://CRAN.R-project.org/package=polars) [![Dev
R-CMD-check](https://github.com/pola-rs/r-polars/actions/workflows/check.yaml/badge.svg)](https://github.com/pola-rs/r-polars/actions/workflows/check.yaml) [![Docs dev
version](https://img.shields.io/badge/docs-dev-blue.svg)](https://pola-rs.github.io/r-polars)

---

IMPORTANT:

This package is now in maintenance mode, meaning that important bugs will be fixed if possible but we won’t adding new features in the next few weeks / months.

The focus of future development is on a completely rewritten version (`neopolars`) that currently exists in the `next` branch.

The package in that branch will become the new `polars` package once the rewrite is complete (so the installation method will not change).

Check <https://github.com/pola-rs/r-polars/issues/1152> for more info.

---

The **polars** package for R gives users access to [a lightning fast](https://duckdblabs.github.io/db-benchmark/) Data Frame library written in Rust. [Polars](https://www.pola.rs/)’ embarrassingly parallel execution, cache efficient algorithms and expressive API makes it perfect for efficient data wrangling, data pipelines, snappy APIs, and much more besides. Polars also supports “streaming mode” for out-of-memory operations. This allows users to analyze datasets many times larger than RAM.

Examples of common operations:

* read CSV, JSON, Parquet, and other file formats;
* filter rows and select columns;
* modify and create new columns;
* group by and aggregate;
* reshape data;
* join and concatenate different datasets;
* sort data;
* work with dates and times;
* handle missing values;
* use the lazy execution engine for maximum performance and memory-efficient operations

Note that this package is rapidly evolving and there are a number of breaking changes at each version. Be sure to check the [changelog](https://pola-rs.github.io/r-polars/NEWS.html) when updating `polars`.

## Install

The recommended way to install this package is via R-multiverse:  

    #__codelineno-0-1Sys.setenv(NOT_CRAN = "true")
    #__codelineno-0-2install.packages("polars", repos = "https://community.r-multiverse.org")
[The “Install” vignette](https://pola-rs.github.io/r-polars/vignettes/install.html) (`vignette("install", "polars")`) gives more details on how to install this package and other ways to install it.

## Quickstart example

To avoid conflicts with other packages and base R function names, **polars** ’s top level functions are hosted in the `pl` namespace, and accessible via the `pl$` prefix. This means that `polars` queries written in Python and in R are very similar.

For example, rewriting the Python example from <https://github.com/pola-rs/polars> in R:  

    #__codelineno-1-1library(polars)
    #__codelineno-1-2
    #__codelineno-1-3df = pl$DataFrame(
    #__codelineno-1-4  A = 1:5,
    #__codelineno-1-5  fruits = c("banana", "banana", "apple", "apple", "banana"),
    #__codelineno-1-6  B = 5:1,
    #__codelineno-1-7  cars = c("beetle", "audi", "beetle", "beetle", "beetle")
    #__codelineno-1-8)
    #__codelineno-1-9
    #__codelineno-1-10# embarrassingly parallel execution & very expressive query language
    #__codelineno-1-11df$sort("fruits")$select(
    #__codelineno-1-12  "fruits",
    #__codelineno-1-13  "cars",
    #__codelineno-1-14  pl$lit("fruits")$alias("literal_string_fruits"),
    #__codelineno-1-15  pl$col("B")$filter(pl$col("cars") == "beetle")$sum(),
    #__codelineno-1-16  pl$col("A")$filter(pl$col("B") > 2)$sum()$over("cars")$alias("sum_A_by_cars"),
    #__codelineno-1-17  pl$col("A")$sum()$over("fruits")$alias("sum_A_by_fruits"),
    #__codelineno-1-18  pl$col("A")$reverse()$over("fruits")$alias("rev_A_by_fruits"),
    #__codelineno-1-19  pl$col("A")$sort_by("B")$over("fruits")$alias("sort_A_by_B_by_fruits")
    #__codelineno-1-20)
    #__codelineno-1-21#> shape: (5, 8)
    #__codelineno-1-22#> ┌────────┬────────┬──────────────┬─────┬──────────────┬──────────────┬──────────────┬──────────────┐
    #__codelineno-1-23#> │ fruits ┆ cars   ┆ literal_stri ┆ B   ┆ sum_A_by_car ┆ sum_A_by_fru ┆ rev_A_by_fru ┆ sort_A_by_B_ │
    #__codelineno-1-24#> │ ---    ┆ ---    ┆ ng_fruits    ┆ --- ┆ s            ┆ its          ┆ its          ┆ by_fruits    │
    #__codelineno-1-25#> │ str    ┆ str    ┆ ---          ┆ i32 ┆ ---          ┆ ---          ┆ ---          ┆ ---          │
    #__codelineno-1-26#> │        ┆        ┆ str          ┆     ┆ i32          ┆ i32          ┆ i32          ┆ i32          │
    #__codelineno-1-27#> ╞════════╪════════╪══════════════╪═════╪══════════════╪══════════════╪══════════════╪══════════════╡
    #__codelineno-1-28#> │ apple  ┆ beetle ┆ fruits       ┆ 11  ┆ 4            ┆ 7            ┆ 4            ┆ 4            │
    #__codelineno-1-29#> │ apple  ┆ beetle ┆ fruits       ┆ 11  ┆ 4            ┆ 7            ┆ 3            ┆ 3            │
    #__codelineno-1-30#> │ banana ┆ beetle ┆ fruits       ┆ 11  ┆ 4            ┆ 8            ┆ 5            ┆ 5            │
    #__codelineno-1-31#> │ banana ┆ audi   ┆ fruits       ┆ 11  ┆ 2            ┆ 8            ┆ 2            ┆ 2            │
    #__codelineno-1-32#> │ banana ┆ beetle ┆ fruits       ┆ 11  ┆ 4            ┆ 8            ┆ 1            ┆ 1            │
    #__codelineno-1-33#> └────────┴────────┴──────────────┴─────┴──────────────┴──────────────┴──────────────┴──────────────┘
The [Get Started vignette](https://pola-rs.github.io/r-polars/vignettes/polars.html) (`vignette("polars")`) provides a more detailed introduction to **polars**.

## Extensions

While one can use **polars** as-is, other packages build on it to provide different syntaxes:

* [polarssql](https://rpolars.github.io/r-polarssql/) provides a **polars** backend for [DBI](https://dbi.r-dbi.org/) and [dbplyr](https://dbplyr.tidyverse.org/).
* [tidypolars](https://tidypolars.etiennebacher.com/) allows one to use the [tidyverse](https://www.tidyverse.org/) syntax while using the power of **polars**.

## Getting help

The online documentation can be found at <https://pola-rs.github.io/r-polars/>.

If you encounter a bug, please file an issue with a minimal reproducible example on [GitHub](https://github.com/pola-rs/r-polars/issues).

Consider joining our [Discord](https://discord.com/invite/4UfP5cfBE7) subchannel for additional help and discussion.
