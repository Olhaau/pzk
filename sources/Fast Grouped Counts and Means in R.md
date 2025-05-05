---
title: Fast Grouped Counts and Means in R
---

# Fast Grouped Counts and Means in R

url:: https://www.r-bloggers.com/2025/04/fast-grouped-counts-and-means-in-r/?utm_source=phpList&utm_medium=email&utm_campaign=R-bloggers-daily&utm_content=HTML
up: [[sources]]

#source

From time to time, the following questions pop up:

1. How to calculate grouped counts and (weighted) means?
2. What are fast ways to do it in R?

This blog post presents a couple of approaches and then compares their speed with a naive benchmark.
![](https://i1.wp.com/lorentzen.ch/wp-content/uploads/2025/03/image-10.png?w=450&ssl=1)

## Base R

There are many ways to calculate grouped counts and means in base R, e.g., `aggregate()`, `tapply()`, `by()`, `split()` + `lapply()`. In my experience, the fastest way is a combination of `tabulate()` and `rowsum()`.  

```
# Make data
set.seed(1)

n <- 1e6

y <- rexp(n)
w <- runif(n)
g <- factor(sample(LETTERS[1:3], n, TRUE))
df <- data.frame(y = y, g = g, w = w)

# Grouped counts
tabulate(g)
# 333469 333569 332962

# Grouped means
rowsum(y, g) / tabulate(g)
      [,1]
# A 1.000869
# B 1.001043
# C 1.000445

# Grouped weighted mean
ws <- rowsum(data.frame(y = y * w, w), g)
ws[, 1L] / ws[, 2L]
# 1.0022749 1.0017816 0.9997058
```

But: `tabulate()` ignores missing values. To avoid problems, create an explicit missing level via `factor(x, exclude = NULL`).

Let’s turn to some other approaches.

## dplyr

Not optimized for speed or memory, but the de-facto standard in data processing with R. I love its syntax.  

```
library(tidyverse)

df <- tibble(df)

# Grouped counts
dplyr::count(df, g)

# Grouped means
df |>
  group_by(g) |>
  summarize(mean(y))

# Grouped weighted means
df |>
  group_by(g) |>
  summarize(sum(w * y) / sum(w))
```

## [data.table](https://github.com/Rdatatable/data.table)

Does not need an introduction. Since 2006 *the* package for fast data manipulation written in C.  

```
library(data.table)

dt <- data.table(df)

# Grouped counts (use keyby for sorted output)
dt[, .N, by = g]
#         g      N
#    <fctr>  <int>
# 1:      C 332962
# 2:      B 333569
# 3:      A 333469

# Grouped means
dt[, mean(y), by = g]

# Grouped weighted means
dt[, sum(w * y) / sum(w), by = g]
dt[, weighted.mean(y, w), by = g]
```

## DuckDB

Extremely powerful query engine / database system written in C++, with initial release in 2019, and R bindings since 2020. Allows larger-than-RAM calculations.  

```
library(duckdb)

con <- dbConnect(duckdb())

duckdb_register(con, name = "df", df = df)

dbGetQuery(con, "SELECT g, COUNT(*) N FROM df GROUP BY g")
dbGetQuery(con, "SELECT g, AVG(y) AS mean FROM df GROUP BY g")
con |> 
  dbGetQuery(
  "
  SELECT g, SUM(y * w) / sum(w) as wmean
  FROM df
  GROUP BY g
  "
  )
#   g     wmean
# 1 A 1.0022749
# 2 B 1.0017816
# 3 C 0.9997058
```

## collapse

C/C++-based package for data transformation and statistical computing. {collapse} was initially released on CRAN in 2020. It can do much more than grouped calculations, check it out!  

```
library(collapse)

fcount(g)
fnobs(g, g) # Faster and does not need memory, but ignores missing values
fmean(y, g = g)
fmean(y, g = g, w = w)
#         A         B         C
# 1.0022749 1.0017816 0.9997058
```

## Polars

R bindings of the fantastic [Polars project](https://github.com/pola-rs/r-polars) that started in 2020. First R release in 2022. About to be overhauled into the R package {neopandas} .  

```
# Sys.setenv(NOT_CRAN = "true")
# install.packages("polars", repos = "https://community.r-multiverse.org")
library(polars)

dfp <- as_polars_df(df)

# Grouped counts
dfp$get_column("g")$value_counts()
# Faster, but eats more memory
dfp$select("g")$with_columns(pl$lit(1L))$group_by("g")$sum()

# Grouped means
dfp$select(c("g", "y"))$group_by("g")$mean()

# Grouped weighted means
(
  dfp
  $with_columns(pl$col("y") * pl$col("w"))
  $group_by("g")
  $sum()
  $with_columns(pl$col("y") / pl$col("w"))
  $drop("w")
)
# shape: (3, 2)
# ┌─────┬──────────┐
# │ g   ┆ y        │
# │ --- ┆ ---      │
# │ cat ┆ f64      │
# ╞═════╪══════════╡
# │ A   ┆ 1.002275 │
# │ B   ┆ 1.001782 │
# │ C   ┆ 0.999706 │
# └─────┴──────────┘
```

## Naive Benchmark

Let’s compare the speed of these approaches for sample sizes up to 10^8 using a Windows system with an Intel i7-13700H CPU.  

```
# We run the code in a fresh session
library(tidyverse)
library(duckdb)
library(data.table)
library(collapse)
library(polars)

polars_info() # 8 threads
setDTthreads(8)
con <- dbConnect(duckdb(config = list(threads = "8")))

set.seed(1)

N <- 10^(5:8)
m_queries <- 3
results <- vector("list", length(N) * m_queries)

for (i in seq_along(N)) {
  n <- N[i]

  # Create data
  y <- rexp(n)
  w <- runif(n)
  g <- factor(sample(LETTERS, n, TRUE))

  df <- tibble(y = y, g = g, w = w)
  dt <- data.table(df)
  dfp <- as_polars_df(df)
  duckdb_register(con, name = "df", df = df, overwrite = TRUE)

  # Grouped counts
  results[[1 + (i - 1) * m_queries]] <- bench::mark(
    base = tabulate(g),
    dplyr = dplyr::count(df, g),
    data.table = dt[, .N, by = g],
    polars = dfp$get_column("g")$value_counts(),
    collapse = fcount(g),
    duckdb = dbGetQuery(con, "SELECT g, COUNT(*) N FROM df GROUP BY g"),
    check = FALSE,
    min_iterations = 3,
  ) |>
    bind_cols(n = n, query = "counts")

  results[[2 + (i - 1) * m_queries]] <- bench::mark(
    base = rowsum(y, g) / tabulate(g),
    dplyr = df |> group_by(g) |> summarize(mean(y)),
    data.table = dt[, mean(y), by = g],
    polars = dfp$select(c("g", "y"))$group_by("g")$mean(),
    collapse = fmean(y, g = g),
    duckdb = dbGetQuery(con, "SELECT g, AVG(y) AS mean FROM df GROUP BY g"),
    check = FALSE,
    min_iterations = 3
  ) |>
    bind_cols(n = n, query = "means")

  results[[3 + (i - 1) * m_queries]] <- bench::mark(
    base = {
      ws <- rowsum(data.frame(y = y * w, w), g)
      ws[, 1L] / ws[, 2L]
    },
    dplyr = df |> group_by(g) |> summarize(sum(w * y) / sum(w)),
    data.table = dt[, sum(w * y) / sum(w), by = g],
    polars = (
      dfp
      $with_columns(pl$col("y") * pl$col("w"))
      $group_by("g")
      $sum()
      $with_columns(pl$col("y") / pl$col("w"))
      $drop("w")
    ),
    collapse = fmean(y, g = g, w = w),
    duckdb = dbGetQuery(
      con,
      "SELECT g, SUM(y * w) / sum(w) as wmean FROM df GROUP BY g"
    ),
    check = FALSE,
    min_iterations = 3
  ) |>
    bind_cols(n = n, query = "weighted means")
}

results_df <- bind_rows(results) |>
  group_by(n, query) |>
  mutate(
    time = median,
    approach = as.character(expression),
    relative = as.numeric(time / min(time))
  ) |>
  ungroup()

ggplot(results_df, aes(y = relative, x = query, group = approach, color = approach)) +
  geom_point() +
  geom_line() +
  facet_wrap("n", scales = "free_y") +
  labs(x = element_blank(), y = "Relative timings") +
  theme_gray(base_size = 14)

ggplot(results_df, aes(y = time, x = query, group = approach, color = approach)) +
  geom_point() +
  geom_line() +
  facet_wrap("n", scales = "free_y") +
  labs(x = element_blank(), y = "Absolute time in seconds") +
  theme_gray(base_size = 14)
```

![](https://i2.wp.com/lorentzen.ch/wp-content/uploads/2025/03/image-11.png?w=450&ssl=1) Absolute time in seconds. For relative time, check the plot at the top.

## Memory

What about memory? {dplyr}, {data.table}, and `rowsum()` require a lot of it, as does `collapse::fcount()`. For the other approaches, almost no memory is required, or profmem can’ t measure it.

## Final words

* {collapse} is increadibly fast for all sample sizes and tasks. In [other benchmarks](https://h2oai.github.io/db-benchmark/), it is slower because there, the grouping has to be a string rather than a factor.
* {duckdb} is increadibly fast for large data.
* {polars} looks really cool.
* `rowsum()` and `tabulate()` provide fast solutions with base R.
* Don’t trust my benchmarks!

[R scrip](https://github.com/lorentzenchr/notebooks/blob/master/blogposts/2025-04-30%20grouped_sums_r.R)t
