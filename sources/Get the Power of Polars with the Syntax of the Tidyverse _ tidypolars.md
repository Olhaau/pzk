---
title: Get the Power of Polars with the Syntax of the Tidyverse • tidypolars
---

# Get the Power of Polars with the Syntax of the Tidyverse • tidypolars

url:: https://tidypolars.etiennebacher.com/
up: [[sources]]

#source

---

ℹ️ This is the R package “tidypolars”. The Python one is here: [markfairbanks/tidypolars](https://github.com/markfairbanks/tidypolars)

---

## Overview

`tidypolars` provides a [`polars`](https://rpolars.github.io/) backend for the `tidyverse`. The aim of `tidypolars` is to enable users to keep their existing `tidyverse` code while using `polars` in the background to benefit from large performance gains. The only thing that needs to change is the way data is imported in the R session.

See the [“Getting started” vignette](https://tidypolars.etiennebacher.com/articles/tidypolars) for a gentle introduction to `tidypolars`.

Since most of the work is rewriting `tidyverse` code into `polars` syntax, `tidypolars` and `polars` have very similar performance.
Click to see a small benchmark

The main purpose of this benchmark is to show that `polars` and `tidypolars` are close and to give an idea of the performance. For more thorough, representative benchmarks about `polars`, take a look at [DuckDB benchmarks](https://duckdblabs.github.io/db-benchmark/) instead.  

    https://rdrr.io/r/base/library.html(https://sebkrantz.github.io/collapse/, warn.conflicts = FALSE)
    #> collapse 2.0.16, see ?`collapse-package` or ?`collapse-documentation`
    https://rdrr.io/r/base/library.html(https://dplyr.tidyverse.org, warn.conflicts = FALSE)
    https://rdrr.io/r/base/library.html(https://dtplyr.tidyverse.org)
    https://rdrr.io/r/base/library.html(https://pola-rs.github.io/r-polars/)
    https://rdrr.io/r/base/library.html(https://tidypolars.etiennebacher.com)

    large_iris <- data.table::https://rdatatable.gitlab.io/data.table/reference/rbindlist.html(https://rdrr.io/r/base/rep.html(https://rdrr.io/r/base/list.html(iris), 100000))
    large_iris_pl <- https://pola-rs.github.io/r-polars/man/as_polars_lf.html(large_iris)
    large_iris_dt <- lazy_dt(large_iris)

    https://rdrr.io/r/base/format.html(https://rdrr.io/r/base/nrow.html(large_iris), big.mark = ",")
    #> [1] "15,000,000"

    bench::https://bench.r-lib.org/reference/mark.html(
      polars = {
        large_iris_pl$
          select(https://rdrr.io/r/base/c.html("Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width"))$
          with_columns(
            pl$when(
              (pl$col("Petal.Length") / pl$col("Petal.Width") > 3)
            )$then(pl$lit("long"))$
              otherwise(pl$lit("large"))$
              alias("petal_type")
          )$
          filter(pl$col("Sepal.Length")$is_between(4.5, 5.5))$
          collect()
      },
      tidypolars = {
        large_iris_pl |>
          https://dplyr.tidyverse.org/reference/select.html(https://tidyselect.r-lib.org/reference/starts_with.html(https://rdrr.io/r/base/c.html("Sep", "Pet"))) |>
          https://dplyr.tidyverse.org/reference/mutate.html(
            petal_type = https://rdrr.io/r/base/ifelse.html((Petal.Length / Petal.Width) > 3, "long", "large")
          ) |> 
          https://dplyr.tidyverse.org/reference/filter.html(https://dplyr.tidyverse.org/reference/between.html(Sepal.Length, 4.5, 5.5)) |> 
          https://dplyr.tidyverse.org/reference/compute.html()
      },
      dplyr = {
        large_iris |>
          https://dplyr.tidyverse.org/reference/select.html(https://tidyselect.r-lib.org/reference/starts_with.html(https://rdrr.io/r/base/c.html("Sep", "Pet"))) |>
          https://dplyr.tidyverse.org/reference/mutate.html(
            petal_type = https://rdrr.io/r/base/ifelse.html((Petal.Length / Petal.Width) > 3, "long", "large")
          ) |>
          https://dplyr.tidyverse.org/reference/filter.html(https://dplyr.tidyverse.org/reference/between.html(Sepal.Length, 4.5, 5.5))
      },
      dtplyr = {
        large_iris_dt |>
          https://dplyr.tidyverse.org/reference/select.html(https://tidyselect.r-lib.org/reference/starts_with.html(https://rdrr.io/r/base/c.html("Sep", "Pet"))) |>
          https://dplyr.tidyverse.org/reference/mutate.html(
            petal_type = https://rdrr.io/r/base/ifelse.html((Petal.Length / Petal.Width) > 3, "long", "large")
          ) |>
          https://dplyr.tidyverse.org/reference/filter.html(https://dplyr.tidyverse.org/reference/between.html(Sepal.Length, 4.5, 5.5)) |> 
          https://rdrr.io/r/base/as.data.frame.html()
      },
      collapse = {
        large_iris |>
          fselect(https://rdrr.io/r/base/c.html("Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width")) |>
          fmutate(
            petal_type = data.table::https://rdatatable.gitlab.io/data.table/reference/fifelse.html((Petal.Length / Petal.Width) > 3, "long", "large")
          ) |>
          fsubset(Sepal.Length >= 4.5 & Sepal.Length <= 5.5)
      },
      check = FALSE,
      iterations = 40
    )
    #> Warning: Some expressions had a GC in every iteration;
    #> so filtering is disabled.
    #> # A tibble: 5 × 6
    #>   expression      min   median `itr/sec` mem_alloc
    #>   <bch:expr> <bch:tm> <bch:tm>     <dbl> <bch:byt>
    #> 1 polars     277.93ms 304.59ms     3.17     1.99MB
    #> 2 tidypolars 286.78ms 362.65ms     2.35      1.2MB
    #> 3 dplyr         3.16s    3.63s     0.257    1.79GB
    #> 4 dtplyr        1.58s    1.92s     0.461    1.72GB
    #> 5 collapse   702.79ms 948.19ms     0.987  745.96MB
    #> # ℹ 1 more variable: `gc/sec` <dbl>

    # NOTE: do NOT take the "mem_alloc" results into account.
    # `bench::mark()` doesn't report the accurate memory usage for packages calling
    # Rust code.
## Installation

`tidypolars` is built on `polars`, which is not available on CRAN. This means that `tidypolars` also can’t be on CRAN. However, you can install it from R-universe.  

    https://rdrr.io/r/base/Sys.setenv.html(NOT_CRAN = "true")
    https://rdrr.io/r/utils/install.packages.html("tidypolars", repos = https://rdrr.io/r/base/c.html("https://community.r-multiverse.org", 'https://cloud.r-project.org'))
## Contributing

Did you find some bugs or some errors in the documentation? Do you want `tidypolars` to support more functions?

Take a look at the [contributing guide](https://tidypolars.etiennebacher.com/contributing) for instructions on bug report and pull requests.  

## Acknowledgements

The website theme was heavily inspired by Matthew Kay’s `ggblend` package: <https://mjskay.github.io/ggblend/>.

The package hex logo was created by Hubert Hałun as part of the Appsilon Hex Contest.
