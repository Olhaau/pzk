---
title: User Guide - Polars R Package
---

# User Guide - Polars R Package

url:: https://pola-rs.github.io/r-polars/vignettes/userguide.html
up: [[sources]]

#source

# Polars - User Guide for R

    #__codelineno-0-1These functions/methods are either missing, broken, or Vincent can't figure out how to use them.
    #__codelineno-0-2
    #__codelineno-0-3* `Series_shift`
    #__codelineno-0-4* `pl$exclude()`
    #__codelineno-0-5* `LazyGroupBy$filter()`
    #__codelineno-0-6* `LazyGroupBy$collect()`
    #__codelineno-0-7* `pl$concat_str`
    #__codelineno-0-8* `pl$col("Defense")$mean()$over(c("Type 1", "Type 2"))$alias("avg_defense_by_type_combination"),`
    #__codelineno-0-9* `pl$fold`
    #__codelineno-0-10* `DataFrame.explode`
    #__codelineno-0-11* Custom R function in an agg on group_by
    #__codelineno-0-12
    #__codelineno-0-13Requires new Polars version:
    #__codelineno-0-14
    #__codelineno-0-15* `df$sample()`
    #__codelineno-0-16* `df$describe()`
[The Polars User Guide](https://pola-rs.github.io/polars-book/user-guide/) is a detailed tutorial about the Polars DataFrame library. Its goal is to introduce you to Polars by going through examples and comparing it to other solutions. Some design choices are introduced there. The guide also introduces you to optimal usage of Polars. The Polars User Guide is available at this link:

https://pola-rs.github.io/polars-book/user-guide/

Currently, the User Guide includes code examples in Python and Rust. This page complements the guide with examples in R. The R examples are not complete yet; when they are complete, our goal is to merge them into the main User Guide. If you want to help, please submit a pull request to the [R polars Github repository.](https://github.com/pola-rs/r-polars)

The current page works as a reference document, for side-by-side comparisons with the Python and Rust examples in the main User Guide.

# Introduction

# Getting started

    #__codelineno-2-1df = pl$read_csv("https://j.mp/iriscsv")

    #__codelineno-3-1df$filter(pl$col("sepal_length") > 5)$
    #__codelineno-3-2  group_by("species", maintain_order = TRUE)$
    #__codelineno-3-3  agg(pl$all()$sum())
    #__codelineno-3-4#> shape: (3, 5)
    #__codelineno-3-5#> ┌────────────┬──────────────┬─────────────┬──────────────┬─────────────┐
    #__codelineno-3-6#> │ species    ┆ sepal_length ┆ sepal_width ┆ petal_length ┆ petal_width │
    #__codelineno-3-7#> │ ---        ┆ ---          ┆ ---         ┆ ---          ┆ ---         │
    #__codelineno-3-8#> │ str        ┆ f64          ┆ f64         ┆ f64          ┆ f64         │
    #__codelineno-3-9#> ╞════════════╪══════════════╪═════════════╪══════════════╪═════════════╡
    #__codelineno-3-10#> │ setosa     ┆ 116.9        ┆ 81.7        ┆ 33.2         ┆ 6.1         │
    #__codelineno-3-11#> │ versicolor ┆ 281.9        ┆ 131.8       ┆ 202.9        ┆ 63.3        │
    #__codelineno-3-12#> │ virginica  ┆ 324.5        ┆ 146.2       ┆ 273.1        ┆ 99.6        │
    #__codelineno-3-13#> └────────────┴──────────────┴─────────────┴──────────────┴─────────────┘
    #__codelineno-3-14
    #__codelineno-3-15df$
    #__codelineno-3-16  lazy()$
    #__codelineno-3-17  filter(pl$col("sepal_length") > 5)$
    #__codelineno-3-18  group_by("species", maintain_order = TRUE)$
    #__codelineno-3-19  agg(pl$all()$sum())$
    #__codelineno-3-20  collect()
    #__codelineno-3-21#> shape: (3, 5)
    #__codelineno-3-22#> ┌────────────┬──────────────┬─────────────┬──────────────┬─────────────┐
    #__codelineno-3-23#> │ species    ┆ sepal_length ┆ sepal_width ┆ petal_length ┆ petal_width │
    #__codelineno-3-24#> │ ---        ┆ ---          ┆ ---         ┆ ---          ┆ ---         │
    #__codelineno-3-25#> │ str        ┆ f64          ┆ f64         ┆ f64          ┆ f64         │
    #__codelineno-3-26#> ╞════════════╪══════════════╪═════════════╪══════════════╪═════════════╡
    #__codelineno-3-27#> │ setosa     ┆ 116.9        ┆ 81.7        ┆ 33.2         ┆ 6.1         │
    #__codelineno-3-28#> │ versicolor ┆ 281.9        ┆ 131.8       ┆ 202.9        ┆ 63.3        │
    #__codelineno-3-29#> │ virginica  ┆ 324.5        ┆ 146.2       ┆ 273.1        ┆ 99.6        │
    #__codelineno-3-30#> └────────────┴──────────────┴─────────────┴──────────────┴─────────────┘
# Polars quick exploration guide

    #__codelineno-4-1series = as_polars_series(c(1, 2, 3, 4, 5))
    #__codelineno-4-2series
    #__codelineno-4-3#> polars Series: shape: (5,)
    #__codelineno-4-4#> Series: '' [f64]
    #__codelineno-4-5#> [
    #__codelineno-4-6#>  1.0
    #__codelineno-4-7#>  2.0
    #__codelineno-4-8#>  3.0
    #__codelineno-4-9#>  4.0
    #__codelineno-4-10#>  5.0
    #__codelineno-4-11#> ]
    #__codelineno-4-12
    #__codelineno-4-13df = pl$DataFrame(
    #__codelineno-4-14  "integer" = c(1, 2, 3),
    #__codelineno-4-15  "date" = as.Date(c("2022-1-1", "2022-1-2", "2022-1-3")),
    #__codelineno-4-16  "float" = c(4.0, 5.0, 6.0)
    #__codelineno-4-17)
    #__codelineno-4-18df
    #__codelineno-4-19#> shape: (3, 3)
    #__codelineno-4-20#> ┌─────────┬────────────┬───────┐
    #__codelineno-4-21#> │ integer ┆ date       ┆ float │
    #__codelineno-4-22#> │ ---     ┆ ---        ┆ ---   │
    #__codelineno-4-23#> │ f64     ┆ date       ┆ f64   │
    #__codelineno-4-24#> ╞═════════╪════════════╪═══════╡
    #__codelineno-4-25#> │ 1.0     ┆ 2022-01-01 ┆ 4.0   │
    #__codelineno-4-26#> │ 2.0     ┆ 2022-01-02 ┆ 5.0   │
    #__codelineno-4-27#> │ 3.0     ┆ 2022-01-03 ┆ 6.0   │
    #__codelineno-4-28#> └─────────┴────────────┴───────┘
    #__codelineno-4-29
    #__codelineno-4-30# df$write_csv('output.csv')
    #__codelineno-4-31# df_csv_with_dates = pl$read_csv("output.csv", parse_dates=True)
    #__codelineno-4-32# print(df_csv_with_dates)
    #__codelineno-4-33
    #__codelineno-4-34# dataframe$write_json('output.json')
    #__codelineno-4-35# df_json = pl$read_json("output.json")
    #__codelineno-4-36# print(df_json)
    #__codelineno-4-37
    #__codelineno-4-38# dataframe$write_parquet('output.parquet')
    #__codelineno-4-39# df_parquet = pl$read_parquet("output.parquet")
    #__codelineno-4-40# print(df_parquet)
    #__codelineno-4-41
    #__codelineno-4-42df = pl$DataFrame(
    #__codelineno-4-43  "a" = as.numeric(0:7),
    #__codelineno-4-44  "b" = runif(8),
    #__codelineno-4-45  "c" = as.Date(sprintf("2022-12-%s", 1:8)),
    #__codelineno-4-46  "d" = c(1, 2.0, NaN, NaN, 0, -5, -42, NA)
    #__codelineno-4-47)
    #__codelineno-4-48df$head(5)
    #__codelineno-4-49#> shape: (5, 4)
    #__codelineno-4-50#> ┌─────┬──────────┬────────────┬─────┐
    #__codelineno-4-51#> │ a   ┆ b        ┆ c          ┆ d   │
    #__codelineno-4-52#> │ --- ┆ ---      ┆ ---        ┆ --- │
    #__codelineno-4-53#> │ f64 ┆ f64      ┆ date       ┆ f64 │
    #__codelineno-4-54#> ╞═════╪══════════╪════════════╪═════╡
    #__codelineno-4-55#> │ 0.0 ┆ 0.419703 ┆ 2022-12-01 ┆ 1.0 │
    #__codelineno-4-56#> │ 1.0 ┆ 0.194936 ┆ 2022-12-02 ┆ 2.0 │
    #__codelineno-4-57#> │ 2.0 ┆ 0.847618 ┆ 2022-12-03 ┆ NaN │
    #__codelineno-4-58#> │ 3.0 ┆ 0.287933 ┆ 2022-12-04 ┆ NaN │
    #__codelineno-4-59#> │ 4.0 ┆ 0.10014  ┆ 2022-12-05 ┆ 0.0 │
    #__codelineno-4-60#> └─────┴──────────┴────────────┴─────┘
    #__codelineno-4-61
    #__codelineno-4-62df$tail(5)
    #__codelineno-4-63#> shape: (5, 4)
    #__codelineno-4-64#> ┌─────┬──────────┬────────────┬───────┐
    #__codelineno-4-65#> │ a   ┆ b        ┆ c          ┆ d     │
    #__codelineno-4-66#> │ --- ┆ ---      ┆ ---        ┆ ---   │
    #__codelineno-4-67#> │ f64 ┆ f64      ┆ date       ┆ f64   │
    #__codelineno-4-68#> ╞═════╪══════════╪════════════╪═══════╡
    #__codelineno-4-69#> │ 3.0 ┆ 0.287933 ┆ 2022-12-04 ┆ NaN   │
    #__codelineno-4-70#> │ 4.0 ┆ 0.10014  ┆ 2022-12-05 ┆ 0.0   │
    #__codelineno-4-71#> │ 5.0 ┆ 0.302791 ┆ 2022-12-06 ┆ -5.0  │
    #__codelineno-4-72#> │ 6.0 ┆ 0.203715 ┆ 2022-12-07 ┆ -42.0 │
    #__codelineno-4-73#> │ 7.0 ┆ 0.796292 ┆ 2022-12-08 ┆ null  │
    #__codelineno-4-74#> └─────┴──────────┴────────────┴───────┘
    #__codelineno-4-75
    #__codelineno-4-76## not implemented yet
    #__codelineno-4-77# df$sample(3)
    #__codelineno-4-78
    #__codelineno-4-79## not implemented yet
    #__codelineno-4-80# df$describe()
    #__codelineno-4-81
    #__codelineno-4-82df$select(pl$col("*"))
    #__codelineno-4-83#> shape: (8, 4)
    #__codelineno-4-84#> ┌─────┬──────────┬────────────┬───────┐
    #__codelineno-4-85#> │ a   ┆ b        ┆ c          ┆ d     │
    #__codelineno-4-86#> │ --- ┆ ---      ┆ ---        ┆ ---   │
    #__codelineno-4-87#> │ f64 ┆ f64      ┆ date       ┆ f64   │
    #__codelineno-4-88#> ╞═════╪══════════╪════════════╪═══════╡
    #__codelineno-4-89#> │ 0.0 ┆ 0.419703 ┆ 2022-12-01 ┆ 1.0   │
    #__codelineno-4-90#> │ 1.0 ┆ 0.194936 ┆ 2022-12-02 ┆ 2.0   │
    #__codelineno-4-91#> │ 2.0 ┆ 0.847618 ┆ 2022-12-03 ┆ NaN   │
    #__codelineno-4-92#> │ 3.0 ┆ 0.287933 ┆ 2022-12-04 ┆ NaN   │
    #__codelineno-4-93#> │ 4.0 ┆ 0.10014  ┆ 2022-12-05 ┆ 0.0   │
    #__codelineno-4-94#> │ 5.0 ┆ 0.302791 ┆ 2022-12-06 ┆ -5.0  │
    #__codelineno-4-95#> │ 6.0 ┆ 0.203715 ┆ 2022-12-07 ┆ -42.0 │
    #__codelineno-4-96#> │ 7.0 ┆ 0.796292 ┆ 2022-12-08 ┆ null  │
    #__codelineno-4-97#> └─────┴──────────┴────────────┴───────┘
    #__codelineno-4-98
    #__codelineno-4-99df$select(pl$col(c("a", "b")))
    #__codelineno-4-100#> shape: (8, 2)
    #__codelineno-4-101#> ┌─────┬──────────┐
    #__codelineno-4-102#> │ a   ┆ b        │
    #__codelineno-4-103#> │ --- ┆ ---      │
    #__codelineno-4-104#> │ f64 ┆ f64      │
    #__codelineno-4-105#> ╞═════╪══════════╡
    #__codelineno-4-106#> │ 0.0 ┆ 0.419703 │
    #__codelineno-4-107#> │ 1.0 ┆ 0.194936 │
    #__codelineno-4-108#> │ 2.0 ┆ 0.847618 │
    #__codelineno-4-109#> │ 3.0 ┆ 0.287933 │
    #__codelineno-4-110#> │ 4.0 ┆ 0.10014  │
    #__codelineno-4-111#> │ 5.0 ┆ 0.302791 │
    #__codelineno-4-112#> │ 6.0 ┆ 0.203715 │
    #__codelineno-4-113#> │ 7.0 ┆ 0.796292 │
    #__codelineno-4-114#> └─────┴──────────┘
    #__codelineno-4-115
    #__codelineno-4-116df$select(pl$col(c("a", "b")))
    #__codelineno-4-117#> shape: (8, 2)
    #__codelineno-4-118#> ┌─────┬──────────┐
    #__codelineno-4-119#> │ a   ┆ b        │
    #__codelineno-4-120#> │ --- ┆ ---      │
    #__codelineno-4-121#> │ f64 ┆ f64      │
    #__codelineno-4-122#> ╞═════╪══════════╡
    #__codelineno-4-123#> │ 0.0 ┆ 0.419703 │
    #__codelineno-4-124#> │ 1.0 ┆ 0.194936 │
    #__codelineno-4-125#> │ 2.0 ┆ 0.847618 │
    #__codelineno-4-126#> │ 3.0 ┆ 0.287933 │
    #__codelineno-4-127#> │ 4.0 ┆ 0.10014  │
    #__codelineno-4-128#> │ 5.0 ┆ 0.302791 │
    #__codelineno-4-129#> │ 6.0 ┆ 0.203715 │
    #__codelineno-4-130#> │ 7.0 ┆ 0.796292 │
    #__codelineno-4-131#> └─────┴──────────┘
    #__codelineno-4-132
    #__codelineno-4-133df$select(pl$col("a"), pl$col("b"))$limit(3)
    #__codelineno-4-134#> shape: (3, 2)
    #__codelineno-4-135#> ┌─────┬──────────┐
    #__codelineno-4-136#> │ a   ┆ b        │
    #__codelineno-4-137#> │ --- ┆ ---      │
    #__codelineno-4-138#> │ f64 ┆ f64      │
    #__codelineno-4-139#> ╞═════╪══════════╡
    #__codelineno-4-140#> │ 0.0 ┆ 0.419703 │
    #__codelineno-4-141#> │ 1.0 ┆ 0.194936 │
    #__codelineno-4-142#> │ 2.0 ┆ 0.847618 │
    #__codelineno-4-143#> └─────┴──────────┘
    #__codelineno-4-144
    #__codelineno-4-145df$select(pl$all()$exclude("a"))
    #__codelineno-4-146#> shape: (8, 3)
    #__codelineno-4-147#> ┌──────────┬────────────┬───────┐
    #__codelineno-4-148#> │ b        ┆ c          ┆ d     │
    #__codelineno-4-149#> │ ---      ┆ ---        ┆ ---   │
    #__codelineno-4-150#> │ f64      ┆ date       ┆ f64   │
    #__codelineno-4-151#> ╞══════════╪════════════╪═══════╡
    #__codelineno-4-152#> │ 0.419703 ┆ 2022-12-01 ┆ 1.0   │
    #__codelineno-4-153#> │ 0.194936 ┆ 2022-12-02 ┆ 2.0   │
    #__codelineno-4-154#> │ 0.847618 ┆ 2022-12-03 ┆ NaN   │
    #__codelineno-4-155#> │ 0.287933 ┆ 2022-12-04 ┆ NaN   │
    #__codelineno-4-156#> │ 0.10014  ┆ 2022-12-05 ┆ 0.0   │
    #__codelineno-4-157#> │ 0.302791 ┆ 2022-12-06 ┆ -5.0  │
    #__codelineno-4-158#> │ 0.203715 ┆ 2022-12-07 ┆ -42.0 │
    #__codelineno-4-159#> │ 0.796292 ┆ 2022-12-08 ┆ null  │
    #__codelineno-4-160#> └──────────┴────────────┴───────┘
    #__codelineno-4-161
    #__codelineno-4-162df$filter(
    #__codelineno-4-163  pl$col("c")$is_between(as.Date("2022-12-2"), as.Date("2022-12-8"))
    #__codelineno-4-164)
    #__codelineno-4-165#> shape: (7, 4)
    #__codelineno-4-166#> ┌─────┬──────────┬────────────┬───────┐
    #__codelineno-4-167#> │ a   ┆ b        ┆ c          ┆ d     │
    #__codelineno-4-168#> │ --- ┆ ---      ┆ ---        ┆ ---   │
    #__codelineno-4-169#> │ f64 ┆ f64      ┆ date       ┆ f64   │
    #__codelineno-4-170#> ╞═════╪══════════╪════════════╪═══════╡
    #__codelineno-4-171#> │ 1.0 ┆ 0.194936 ┆ 2022-12-02 ┆ 2.0   │
    #__codelineno-4-172#> │ 2.0 ┆ 0.847618 ┆ 2022-12-03 ┆ NaN   │
    #__codelineno-4-173#> │ 3.0 ┆ 0.287933 ┆ 2022-12-04 ┆ NaN   │
    #__codelineno-4-174#> │ 4.0 ┆ 0.10014  ┆ 2022-12-05 ┆ 0.0   │
    #__codelineno-4-175#> │ 5.0 ┆ 0.302791 ┆ 2022-12-06 ┆ -5.0  │
    #__codelineno-4-176#> │ 6.0 ┆ 0.203715 ┆ 2022-12-07 ┆ -42.0 │
    #__codelineno-4-177#> │ 7.0 ┆ 0.796292 ┆ 2022-12-08 ┆ null  │
    #__codelineno-4-178#> └─────┴──────────┴────────────┴───────┘
    #__codelineno-4-179
    #__codelineno-4-180df$filter((pl$col("a") <= 3) & (pl$col("d")$is_not_nan()))
    #__codelineno-4-181#> shape: (2, 4)
    #__codelineno-4-182#> ┌─────┬──────────┬────────────┬─────┐
    #__codelineno-4-183#> │ a   ┆ b        ┆ c          ┆ d   │
    #__codelineno-4-184#> │ --- ┆ ---      ┆ ---        ┆ --- │
    #__codelineno-4-185#> │ f64 ┆ f64      ┆ date       ┆ f64 │
    #__codelineno-4-186#> ╞═════╪══════════╪════════════╪═════╡
    #__codelineno-4-187#> │ 0.0 ┆ 0.419703 ┆ 2022-12-01 ┆ 1.0 │
    #__codelineno-4-188#> │ 1.0 ┆ 0.194936 ┆ 2022-12-02 ┆ 2.0 │
    #__codelineno-4-189#> └─────┴──────────┴────────────┴─────┘
    #__codelineno-4-190
    #__codelineno-4-191df$with_columns(pl$col("b")$sum()$alias("e"), (pl$col("b") + 42)$alias("b+42"))
    #__codelineno-4-192#> shape: (8, 6)
    #__codelineno-4-193#> ┌─────┬──────────┬────────────┬───────┬──────────┬───────────┐
    #__codelineno-4-194#> │ a   ┆ b        ┆ c          ┆ d     ┆ e        ┆ b+42      │
    #__codelineno-4-195#> │ --- ┆ ---      ┆ ---        ┆ ---   ┆ ---      ┆ ---       │
    #__codelineno-4-196#> │ f64 ┆ f64      ┆ date       ┆ f64   ┆ f64      ┆ f64       │
    #__codelineno-4-197#> ╞═════╪══════════╪════════════╪═══════╪══════════╪═══════════╡
    #__codelineno-4-198#> │ 0.0 ┆ 0.419703 ┆ 2022-12-01 ┆ 1.0   ┆ 3.153128 ┆ 42.419703 │
    #__codelineno-4-199#> │ 1.0 ┆ 0.194936 ┆ 2022-12-02 ┆ 2.0   ┆ 3.153128 ┆ 42.194936 │
    #__codelineno-4-200#> │ 2.0 ┆ 0.847618 ┆ 2022-12-03 ┆ NaN   ┆ 3.153128 ┆ 42.847618 │
    #__codelineno-4-201#> │ 3.0 ┆ 0.287933 ┆ 2022-12-04 ┆ NaN   ┆ 3.153128 ┆ 42.287933 │
    #__codelineno-4-202#> │ 4.0 ┆ 0.10014  ┆ 2022-12-05 ┆ 0.0   ┆ 3.153128 ┆ 42.10014  │
    #__codelineno-4-203#> │ 5.0 ┆ 0.302791 ┆ 2022-12-06 ┆ -5.0  ┆ 3.153128 ┆ 42.302791 │
    #__codelineno-4-204#> │ 6.0 ┆ 0.203715 ┆ 2022-12-07 ┆ -42.0 ┆ 3.153128 ┆ 42.203715 │
    #__codelineno-4-205#> │ 7.0 ┆ 0.796292 ┆ 2022-12-08 ┆ null  ┆ 3.153128 ┆ 42.796292 │
    #__codelineno-4-206#> └─────┴──────────┴────────────┴───────┴──────────┴───────────┘
    #__codelineno-4-207
    #__codelineno-4-208df$with_columns((pl$col("a") * pl$col("b"))$alias("a * b"))$select(pl$all()$exclude(c("c", "d")))
    #__codelineno-4-209#> shape: (8, 3)
    #__codelineno-4-210#> ┌─────┬──────────┬──────────┐
    #__codelineno-4-211#> │ a   ┆ b        ┆ a * b    │
    #__codelineno-4-212#> │ --- ┆ ---      ┆ ---      │
    #__codelineno-4-213#> │ f64 ┆ f64      ┆ f64      │
    #__codelineno-4-214#> ╞═════╪══════════╪══════════╡
    #__codelineno-4-215#> │ 0.0 ┆ 0.419703 ┆ 0.0      │
    #__codelineno-4-216#> │ 1.0 ┆ 0.194936 ┆ 0.194936 │
    #__codelineno-4-217#> │ 2.0 ┆ 0.847618 ┆ 1.695236 │
    #__codelineno-4-218#> │ 3.0 ┆ 0.287933 ┆ 0.8638   │
    #__codelineno-4-219#> │ 4.0 ┆ 0.10014  ┆ 0.400558 │
    #__codelineno-4-220#> │ 5.0 ┆ 0.302791 ┆ 1.513957 │
    #__codelineno-4-221#> │ 6.0 ┆ 0.203715 ┆ 1.222287 │
    #__codelineno-4-222#> │ 7.0 ┆ 0.796292 ┆ 5.574044 │
    #__codelineno-4-223#> └─────┴──────────┴──────────┘
    #__codelineno-4-224
    #__codelineno-4-225df = pl$DataFrame("x" = 0:7, "y" = c("A", "A", "A", "B", "B", "C", "X", "X"))
    #__codelineno-4-226
    #__codelineno-4-227df$
    #__codelineno-4-228  group_by("y", maintain_order = FALSE)$
    #__codelineno-4-229  agg(
    #__codelineno-4-230  pl$col("*")$count()$alias("count"),
    #__codelineno-4-231  pl$col("*")$sum()$alias("sum")
    #__codelineno-4-232)
    #__codelineno-4-233#> shape: (4, 3)
    #__codelineno-4-234#> ┌─────┬───────┬─────┐
    #__codelineno-4-235#> │ y   ┆ count ┆ sum │
    #__codelineno-4-236#> │ --- ┆ ---   ┆ --- │
    #__codelineno-4-237#> │ str ┆ u32   ┆ i32 │
    #__codelineno-4-238#> ╞═════╪═══════╪═════╡
    #__codelineno-4-239#> │ C   ┆ 1     ┆ 5   │
    #__codelineno-4-240#> │ X   ┆ 2     ┆ 13  │
    #__codelineno-4-241#> │ B   ┆ 2     ┆ 7   │
    #__codelineno-4-242#> │ A   ┆ 3     ┆ 3   │
    #__codelineno-4-243#> └─────┴───────┴─────┘
    #__codelineno-4-244
    #__codelineno-4-245df1 = pl$DataFrame(
    #__codelineno-4-246  "a" = 0:7,
    #__codelineno-4-247  "b" = runif(8),
    #__codelineno-4-248  "c" = as.Date(sprintf("2022-12-%s", 1:8)),
    #__codelineno-4-249  "d" = c(1, 2.0, NaN, NaN, 0, -5, -42, NA)
    #__codelineno-4-250)
    #__codelineno-4-251df2 = pl$DataFrame("x" = 0:7, "y" = c("A", "A", "A", "B", "B", "C", "X", "X"))
    #__codelineno-4-252
    #__codelineno-4-253pl$concat(c(df1, df2), how = "horizontal")
    #__codelineno-4-254#> shape: (8, 6)
    #__codelineno-4-255#> ┌─────┬──────────┬────────────┬───────┬─────┬─────┐
    #__codelineno-4-256#> │ a   ┆ b        ┆ c          ┆ d     ┆ x   ┆ y   │
    #__codelineno-4-257#> │ --- ┆ ---      ┆ ---        ┆ ---   ┆ --- ┆ --- │
    #__codelineno-4-258#> │ i32 ┆ f64      ┆ date       ┆ f64   ┆ i32 ┆ str │
    #__codelineno-4-259#> ╞═════╪══════════╪════════════╪═══════╪═════╪═════╡
    #__codelineno-4-260#> │ 0   ┆ 0.278717 ┆ 2022-12-01 ┆ 1.0   ┆ 0   ┆ A   │
    #__codelineno-4-261#> │ 1   ┆ 0.999991 ┆ 2022-12-02 ┆ 2.0   ┆ 1   ┆ A   │
    #__codelineno-4-262#> │ 2   ┆ 0.693839 ┆ 2022-12-03 ┆ NaN   ┆ 2   ┆ A   │
    #__codelineno-4-263#> │ 3   ┆ 0.890582 ┆ 2022-12-04 ┆ NaN   ┆ 3   ┆ B   │
    #__codelineno-4-264#> │ 4   ┆ 0.711113 ┆ 2022-12-05 ┆ 0.0   ┆ 4   ┆ B   │
    #__codelineno-4-265#> │ 5   ┆ 0.999317 ┆ 2022-12-06 ┆ -5.0  ┆ 5   ┆ C   │
    #__codelineno-4-266#> │ 6   ┆ 0.138401 ┆ 2022-12-07 ┆ -42.0 ┆ 6   ┆ X   │
    #__codelineno-4-267#> │ 7   ┆ 0.473887 ┆ 2022-12-08 ┆ null  ┆ 7   ┆ X   │
    #__codelineno-4-268#> └─────┴──────────┴────────────┴───────┴─────┴─────┘
# Polars expressions

## Expressions

    #__codelineno-5-1df = pl$DataFrame(
    #__codelineno-5-2  "nrs" = c(1, 2, 3, NA, 5),
    #__codelineno-5-3  "names" = c("foo", "ham", "spam", "egg", NA),
    #__codelineno-5-4  "random" = runif(5),
    #__codelineno-5-5  "groups" = c("A", "A", "B", "C", "B")
    #__codelineno-5-6)
    #__codelineno-5-7df
    #__codelineno-5-8#> shape: (5, 4)
    #__codelineno-5-9#> ┌──────┬───────┬──────────┬────────┐
    #__codelineno-5-10#> │ nrs  ┆ names ┆ random   ┆ groups │
    #__codelineno-5-11#> │ ---  ┆ ---   ┆ ---      ┆ ---    │
    #__codelineno-5-12#> │ f64  ┆ str   ┆ f64      ┆ str    │
    #__codelineno-5-13#> ╞══════╪═══════╪══════════╪════════╡
    #__codelineno-5-14#> │ 1.0  ┆ foo   ┆ 0.809688 ┆ A      │
    #__codelineno-5-15#> │ 2.0  ┆ ham   ┆ 0.261185 ┆ A      │
    #__codelineno-5-16#> │ 3.0  ┆ spam  ┆ 0.647752 ┆ B      │
    #__codelineno-5-17#> │ null ┆ egg   ┆ 0.44197  ┆ C      │
    #__codelineno-5-18#> │ 5.0  ┆ null  ┆ 0.505604 ┆ B      │
    #__codelineno-5-19#> └──────┴───────┴──────────┴────────┘
    #__codelineno-5-20
    #__codelineno-5-21df$select(
    #__codelineno-5-22  pl$col("names")$n_unique()$alias("unique_names_1"),
    #__codelineno-5-23  pl$col("names")$unique()$count()$alias("unique_names_2")
    #__codelineno-5-24)
    #__codelineno-5-25#> shape: (1, 2)
    #__codelineno-5-26#> ┌────────────────┬────────────────┐
    #__codelineno-5-27#> │ unique_names_1 ┆ unique_names_2 │
    #__codelineno-5-28#> │ ---            ┆ ---            │
    #__codelineno-5-29#> │ u32            ┆ u32            │
    #__codelineno-5-30#> ╞════════════════╪════════════════╡
    #__codelineno-5-31#> │ 5              ┆ 4              │
    #__codelineno-5-32#> └────────────────┴────────────────┘
    #__codelineno-5-33
    #__codelineno-5-34df$select(
    #__codelineno-5-35  pl$sum("random")$alias("sum"),
    #__codelineno-5-36  pl$min("random")$alias("min"),
    #__codelineno-5-37  pl$max("random")$alias("max"),
    #__codelineno-5-38  pl$col("random")$max()$alias("other_max"),
    #__codelineno-5-39  pl$std("random")$alias("std dev"),
    #__codelineno-5-40  pl$var("random")$alias("variance")
    #__codelineno-5-41)
    #__codelineno-5-42#> shape: (1, 6)
    #__codelineno-5-43#> ┌──────────┬──────────┬──────────┬───────────┬──────────┬──────────┐
    #__codelineno-5-44#> │ sum      ┆ min      ┆ max      ┆ other_max ┆ std dev  ┆ variance │
    #__codelineno-5-45#> │ ---      ┆ ---      ┆ ---      ┆ ---       ┆ ---      ┆ ---      │
    #__codelineno-5-46#> │ f64      ┆ f64      ┆ f64      ┆ f64       ┆ f64      ┆ f64      │
    #__codelineno-5-47#> ╞══════════╪══════════╪══════════╪═══════════╪══════════╪══════════╡
    #__codelineno-5-48#> │ 2.666197 ┆ 0.261185 ┆ 0.809688 ┆ 0.809688  ┆ 0.207752 ┆ 0.043161 │
    #__codelineno-5-49#> └──────────┴──────────┴──────────┴───────────┴──────────┴──────────┘
    #__codelineno-5-50
    #__codelineno-5-51df$select(
    #__codelineno-5-52  pl$col("names")$filter(pl$col("names")$str$contains("am$"))$count()
    #__codelineno-5-53)
    #__codelineno-5-54#> shape: (1, 1)
    #__codelineno-5-55#> ┌───────┐
    #__codelineno-5-56#> │ names │
    #__codelineno-5-57#> │ ---   │
    #__codelineno-5-58#> │ u32   │
    #__codelineno-5-59#> ╞═══════╡
    #__codelineno-5-60#> │ 2     │
    #__codelineno-5-61#> └───────┘
    #__codelineno-5-62
    #__codelineno-5-63df$select(
    #__codelineno-5-64  pl$when(pl$col("random") > 0.5)$then(0)$otherwise(pl$col("random")) * pl$sum("nrs")
    #__codelineno-5-65)
    #__codelineno-5-66#> shape: (5, 1)
    #__codelineno-5-67#> ┌──────────┐
    #__codelineno-5-68#> │ literal  │
    #__codelineno-5-69#> │ ---      │
    #__codelineno-5-70#> │ f64      │
    #__codelineno-5-71#> ╞══════════╡
    #__codelineno-5-72#> │ 0.0      │
    #__codelineno-5-73#> │ 2.873031 │
    #__codelineno-5-74#> │ 0.0      │
    #__codelineno-5-75#> │ 4.861666 │
    #__codelineno-5-76#> │ 0.0      │
    #__codelineno-5-77#> └──────────┘
    #__codelineno-5-78
    #__codelineno-5-79df$select(
    #__codelineno-5-80  pl$when(pl$col("groups") == "A")$then(1)$when(pl$col("random") > 0.5)$then(0)$otherwise(pl$col("random"))
    #__codelineno-5-81)
    #__codelineno-5-82#> shape: (5, 1)
    #__codelineno-5-83#> ┌─────────┐
    #__codelineno-5-84#> │ literal │
    #__codelineno-5-85#> │ ---     │
    #__codelineno-5-86#> │ f64     │
    #__codelineno-5-87#> ╞═════════╡
    #__codelineno-5-88#> │ 1.0     │
    #__codelineno-5-89#> │ 1.0     │
    #__codelineno-5-90#> │ 0.0     │
    #__codelineno-5-91#> │ 0.44197 │
    #__codelineno-5-92#> │ 0.0     │
    #__codelineno-5-93#> └─────────┘
    #__codelineno-5-94
    #__codelineno-5-95df$select(
    #__codelineno-5-96  pl$col("*"), # select all
    #__codelineno-5-97  pl$col("random")$sum()$over("groups")$alias("sumc(random)/groups"),
    #__codelineno-5-98  pl$col("random")$implode()$over("names")$alias("random/name")
    #__codelineno-5-99)
    #__codelineno-5-100#> shape: (5, 6)
    #__codelineno-5-101#> ┌──────┬───────┬──────────┬────────┬─────────────────────┬─────────────┐
    #__codelineno-5-102#> │ nrs  ┆ names ┆ random   ┆ groups ┆ sumc(random)/groups ┆ random/name │
    #__codelineno-5-103#> │ ---  ┆ ---   ┆ ---      ┆ ---    ┆ ---                 ┆ ---         │
    #__codelineno-5-104#> │ f64  ┆ str   ┆ f64      ┆ str    ┆ f64                 ┆ list[f64]   │
    #__codelineno-5-105#> ╞══════╪═══════╪══════════╪════════╪═════════════════════╪═════════════╡
    #__codelineno-5-106#> │ 1.0  ┆ foo   ┆ 0.809688 ┆ A      ┆ 1.070872            ┆ [0.809688]  │
    #__codelineno-5-107#> │ 2.0  ┆ ham   ┆ 0.261185 ┆ A      ┆ 1.070872            ┆ [0.261185]  │
    #__codelineno-5-108#> │ 3.0  ┆ spam  ┆ 0.647752 ┆ B      ┆ 1.153356            ┆ [0.647752]  │
    #__codelineno-5-109#> │ null ┆ egg   ┆ 0.44197  ┆ C      ┆ 0.44197             ┆ [0.44197]   │
    #__codelineno-5-110#> │ 5.0  ┆ null  ┆ 0.505604 ┆ B      ┆ 1.153356            ┆ [0.505604]  │
    #__codelineno-5-111#> └──────┴───────┴──────────┴────────┴─────────────────────┴─────────────┘
## Contexts

    #__codelineno-6-1df$select(
    #__codelineno-6-2  pl$sum("nrs"),
    #__codelineno-6-3  pl$col("names")$sort(),
    #__codelineno-6-4  pl$col("names")$first()$alias("first name"),
    #__codelineno-6-5  (pl$mean("nrs") * 10)$alias("10xnrs")
    #__codelineno-6-6)
    #__codelineno-6-7#> shape: (5, 4)
    #__codelineno-6-8#> ┌──────┬───────┬────────────┬────────┐
    #__codelineno-6-9#> │ nrs  ┆ names ┆ first name ┆ 10xnrs │
    #__codelineno-6-10#> │ ---  ┆ ---   ┆ ---        ┆ ---    │
    #__codelineno-6-11#> │ f64  ┆ str   ┆ str        ┆ f64    │
    #__codelineno-6-12#> ╞══════╪═══════╪════════════╪════════╡
    #__codelineno-6-13#> │ 11.0 ┆ null  ┆ foo        ┆ 27.5   │
    #__codelineno-6-14#> │ 11.0 ┆ egg   ┆ foo        ┆ 27.5   │
    #__codelineno-6-15#> │ 11.0 ┆ foo   ┆ foo        ┆ 27.5   │
    #__codelineno-6-16#> │ 11.0 ┆ ham   ┆ foo        ┆ 27.5   │
    #__codelineno-6-17#> │ 11.0 ┆ spam  ┆ foo        ┆ 27.5   │
    #__codelineno-6-18#> └──────┴───────┴────────────┴────────┘
    #__codelineno-6-19
    #__codelineno-6-20df$with_columns(
    #__codelineno-6-21  pl$sum("nrs")$alias("nrs_sum"),
    #__codelineno-6-22  pl$col("random")$count()$alias("count")
    #__codelineno-6-23)
    #__codelineno-6-24#> shape: (5, 6)
    #__codelineno-6-25#> ┌──────┬───────┬──────────┬────────┬─────────┬───────┐
    #__codelineno-6-26#> │ nrs  ┆ names ┆ random   ┆ groups ┆ nrs_sum ┆ count │
    #__codelineno-6-27#> │ ---  ┆ ---   ┆ ---      ┆ ---    ┆ ---     ┆ ---   │
    #__codelineno-6-28#> │ f64  ┆ str   ┆ f64      ┆ str    ┆ f64     ┆ u32   │
    #__codelineno-6-29#> ╞══════╪═══════╪══════════╪════════╪═════════╪═══════╡
    #__codelineno-6-30#> │ 1.0  ┆ foo   ┆ 0.809688 ┆ A      ┆ 11.0    ┆ 5     │
    #__codelineno-6-31#> │ 2.0  ┆ ham   ┆ 0.261185 ┆ A      ┆ 11.0    ┆ 5     │
    #__codelineno-6-32#> │ 3.0  ┆ spam  ┆ 0.647752 ┆ B      ┆ 11.0    ┆ 5     │
    #__codelineno-6-33#> │ null ┆ egg   ┆ 0.44197  ┆ C      ┆ 11.0    ┆ 5     │
    #__codelineno-6-34#> │ 5.0  ┆ null  ┆ 0.505604 ┆ B      ┆ 11.0    ┆ 5     │
    #__codelineno-6-35#> └──────┴───────┴──────────┴────────┴─────────┴───────┘
    #__codelineno-6-36
    #__codelineno-6-37df$group_by("groups")$agg(
    #__codelineno-6-38  pl$sum("nrs"), # sum nrs by groups
    #__codelineno-6-39  pl$col("random")$count()$alias("count"), # count group members
    #__codelineno-6-40  # sum random where name != null
    #__codelineno-6-41  pl$col("random")$filter(pl$col("names")$is_not_null())$sum()$name$suffix("_sum"),
    #__codelineno-6-42  pl$col("names")$reverse()$alias(("reversed names"))
    #__codelineno-6-43)
    #__codelineno-6-44#> shape: (3, 5)
    #__codelineno-6-45#> ┌────────┬─────┬───────┬────────────┬────────────────┐
    #__codelineno-6-46#> │ groups ┆ nrs ┆ count ┆ random_sum ┆ reversed names │
    #__codelineno-6-47#> │ ---    ┆ --- ┆ ---   ┆ ---        ┆ ---            │
    #__codelineno-6-48#> │ str    ┆ f64 ┆ u32   ┆ f64        ┆ list[str]      │
    #__codelineno-6-49#> ╞════════╪═════╪═══════╪════════════╪════════════════╡
    #__codelineno-6-50#> │ A      ┆ 3.0 ┆ 2     ┆ 1.070872   ┆ ["ham", "foo"] │
    #__codelineno-6-51#> │ B      ┆ 8.0 ┆ 2     ┆ 0.647752   ┆ [null, "spam"] │
    #__codelineno-6-52#> │ C      ┆ 0.0 ┆ 1     ┆ 0.44197    ┆ ["egg"]        │
    #__codelineno-6-53#> └────────┴─────┴───────┴────────────┴────────────────┘
## GroupBy

    #__codelineno-7-1url = "https://unitedstates.github.io/congress-legislators/legislators-historical.csv"
    #__codelineno-7-2
    #__codelineno-7-3dtypes = list(
    #__codelineno-7-4  "first_name" = pl$Categorical(),
    #__codelineno-7-5  "gender" = pl$Categorical(),
    #__codelineno-7-6  "type" = pl$Categorical(),
    #__codelineno-7-7  "state" = pl$Categorical(),
    #__codelineno-7-8  "party" = pl$Categorical()
    #__codelineno-7-9)
    #__codelineno-7-10
    #__codelineno-7-11# dtypes argument
    #__codelineno-7-12dataset = pl$read_csv(url)$with_columns(pl$col("birthday")$str$strptime(pl$Date, "%Y-%m-%d"))
    #__codelineno-7-13#> tmp file placed in 
    #__codelineno-7-14#>  /tmp/RtmpreSHIh/filebcb2133ae81f
    #__codelineno-7-15
    #__codelineno-7-16dataset$
    #__codelineno-7-17  lazy()$
    #__codelineno-7-18  group_by("first_name")$
    #__codelineno-7-19  agg(
    #__codelineno-7-20  pl$len(),
    #__codelineno-7-21  pl$col("gender"),
    #__codelineno-7-22  pl$first("last_name")
    #__codelineno-7-23)$
    #__codelineno-7-24  sort("len", descending = TRUE)$
    #__codelineno-7-25  limit(5)$
    #__codelineno-7-26  collect()
    #__codelineno-7-27#> shape: (5, 4)
    #__codelineno-7-28#> ┌────────────┬──────┬───────────────────┬───────────┐
    #__codelineno-7-29#> │ first_name ┆ len  ┆ gender            ┆ last_name │
    #__codelineno-7-30#> │ ---        ┆ ---  ┆ ---               ┆ ---       │
    #__codelineno-7-31#> │ str        ┆ u32  ┆ list[str]         ┆ str       │
    #__codelineno-7-32#> ╞════════════╪══════╪═══════════════════╪═══════════╡
    #__codelineno-7-33#> │ John       ┆ 1258 ┆ ["M", "M", … "M"] ┆ Walker    │
    #__codelineno-7-34#> │ William    ┆ 1022 ┆ ["M", "M", … "M"] ┆ Few       │
    #__codelineno-7-35#> │ James      ┆ 714  ┆ ["M", "M", … "M"] ┆ Armstrong │
    #__codelineno-7-36#> │ Thomas     ┆ 454  ┆ ["M", "M", … "M"] ┆ Tucker    │
    #__codelineno-7-37#> │ Charles    ┆ 439  ┆ ["M", "M", … "M"] ┆ Carroll   │
    #__codelineno-7-38#> └────────────┴──────┴───────────────────┴───────────┘
    #__codelineno-7-39
    #__codelineno-7-40dataset$lazy()$
    #__codelineno-7-41  group_by("state")$
    #__codelineno-7-42  agg(
    #__codelineno-7-43  (pl$col("party") == "Anti-Administration")$sum()$alias("anti"),
    #__codelineno-7-44  (pl$col("party") == "Pro-Administration")$sum()$alias("pro")
    #__codelineno-7-45)$
    #__codelineno-7-46  sort("pro", descending = TRUE)$
    #__codelineno-7-47  limit(5)$
    #__codelineno-7-48  collect()
    #__codelineno-7-49#> shape: (5, 3)
    #__codelineno-7-50#> ┌───────┬──────┬─────┐
    #__codelineno-7-51#> │ state ┆ anti ┆ pro │
    #__codelineno-7-52#> │ ---   ┆ ---  ┆ --- │
    #__codelineno-7-53#> │ str   ┆ u32  ┆ u32 │
    #__codelineno-7-54#> ╞═══════╪══════╪═════╡
    #__codelineno-7-55#> │ NJ    ┆ 0    ┆ 3   │
    #__codelineno-7-56#> │ CT    ┆ 0    ┆ 3   │
    #__codelineno-7-57#> │ NC    ┆ 1    ┆ 2   │
    #__codelineno-7-58#> │ MA    ┆ 0    ┆ 1   │
    #__codelineno-7-59#> │ SC    ┆ 0    ┆ 1   │
    #__codelineno-7-60#> └───────┴──────┴─────┘
    #__codelineno-7-61
    #__codelineno-7-62dataset$
    #__codelineno-7-63  lazy()$
    #__codelineno-7-64  group_by(c("state", "party"))$
    #__codelineno-7-65  agg(pl$count("party")$alias("count"))$
    #__codelineno-7-66  filter((pl$col("party") == "Anti-Administration") | (pl$col("party") == "Pro-Administration"))$
    #__codelineno-7-67  sort("count", descending = TRUE)$
    #__codelineno-7-68  head(5)$
    #__codelineno-7-69  collect()
    #__codelineno-7-70#> shape: (5, 3)
    #__codelineno-7-71#> ┌───────┬─────────────────────┬───────┐
    #__codelineno-7-72#> │ state ┆ party               ┆ count │
    #__codelineno-7-73#> │ ---   ┆ ---                 ┆ ---   │
    #__codelineno-7-74#> │ str   ┆ str                 ┆ u32   │
    #__codelineno-7-75#> ╞═══════╪═════════════════════╪═══════╡
    #__codelineno-7-76#> │ VA    ┆ Anti-Administration ┆ 3     │
    #__codelineno-7-77#> │ NJ    ┆ Pro-Administration  ┆ 3     │
    #__codelineno-7-78#> │ CT    ┆ Pro-Administration  ┆ 3     │
    #__codelineno-7-79#> │ NC    ┆ Pro-Administration  ┆ 2     │
    #__codelineno-7-80#> │ VA    ┆ Pro-Administration  ┆ 1     │
    #__codelineno-7-81#> └───────┴─────────────────────┴───────┘
    #__codelineno-8-1compute_age = function() 2021 - pl$col("birthday")$dt$year()
    #__codelineno-8-2
    #__codelineno-8-3avg_birthday = function(gender) {
    #__codelineno-8-4  compute_age()$filter(pl$col("gender") == gender)$mean()$alias(sprintf("avg %s birthday", gender))
    #__codelineno-8-5}
    #__codelineno-8-6
    #__codelineno-8-7q = (
    #__codelineno-8-8  dataset$lazy()$
    #__codelineno-8-9    group_by("state")$
    #__codelineno-8-10    agg(
    #__codelineno-8-11    avg_birthday("M"),
    #__codelineno-8-12    avg_birthday("F"),
    #__codelineno-8-13    (pl$col("gender") == "M")$sum()$alias("# male"),
    #__codelineno-8-14    (pl$col("gender") == "F")$sum()$alias("# female")
    #__codelineno-8-15  )$
    #__codelineno-8-16    limit(5)
    #__codelineno-8-17)
    #__codelineno-8-18q$collect()
    #__codelineno-8-19
    #__codelineno-8-20#
    #__codelineno-8-21# get_person <- function() pl$col("first_name") + pl$lit(" ") + pl$col("last_name")
    #__codelineno-8-22# q = (
    #__codelineno-8-23#     dataset$lazy()
    #__codelineno-8-24#     $sort("birthday", descending=True)
    #__codelineno-8-25#     $group_by(["state"])
    #__codelineno-8-26#     $agg(
    #__codelineno-8-27#         [
    #__codelineno-8-28#             get_person()$first()$alias("youngest"),
    #__codelineno-8-29#             get_person()$last()$alias("oldest"),
    #__codelineno-8-30#         ]
    #__codelineno-8-31#     )
    #__codelineno-8-32#     $limit(5)
    #__codelineno-8-33# )
    #__codelineno-8-34# q$collect()
    #__codelineno-8-35#
    #__codelineno-8-36# get_person <- function() pl$col("first_name") + pl$lit(" ") + pl$col("last_name")
    #__codelineno-8-37# q = (
    #__codelineno-8-38#     dataset$lazy()
    #__codelineno-8-39#     $sort("birthday", descending=True)
    #__codelineno-8-40#     $group_by(["state"])
    #__codelineno-8-41#     $agg(
    #__codelineno-8-42#         [
    #__codelineno-8-43#             get_person()$first()$alias("youngest"),
    #__codelineno-8-44#             get_person()$last()$alias("oldest"),
    #__codelineno-8-45#             get_person()$sort()$first()$alias("alphabetical_first"),
    #__codelineno-8-46#         ]
    #__codelineno-8-47#     )
    #__codelineno-8-48#     $limit(5)
    #__codelineno-8-49# )
    #__codelineno-8-50# q$collect()
    #__codelineno-8-51#
    #__codelineno-8-52# q = (
    #__codelineno-8-53#     dataset$lazy()
    #__codelineno-8-54#     $sort("birthday", descending=True)
    #__codelineno-8-55#     $group_by(["state"])
    #__codelineno-8-56#     $agg(
    #__codelineno-8-57#         [
    #__codelineno-8-58#             get_person()$first()$alias("youngest"),
    #__codelineno-8-59#             get_person()$last()$alias("oldest"),
    #__codelineno-8-60#             get_person()$sort()$first()$alias("alphabetical_first"),
    #__codelineno-8-61#             pl$col("gender")$sort_by("first_name")$first()$alias("gender"),
    #__codelineno-8-62#         ]
    #__codelineno-8-63#     )
    #__codelineno-8-64#     $sort("state")
    #__codelineno-8-65#     $limit(5)
    #__codelineno-8-66# )
    #__codelineno-8-67# q$collect()
## Folds

    #__codelineno-9-1df = pl$DataFrame(
    #__codelineno-9-2  "a" = c(1, 2, 3),
    #__codelineno-9-3  "b" = c(10, 20, 30)
    #__codelineno-9-4)
    #__codelineno-9-5
    #__codelineno-9-6df$select(
    #__codelineno-9-7  pl$fold(acc=pl$lit(0), function (x) acc, x = acc + x, exprs=pl$all())$alias("sum")
    #__codelineno-9-8)
    #__codelineno-9-9
    #__codelineno-9-10df = pl$DataFrame(
    #__codelineno-9-11  "a" = c(1, 2, 3),
    #__codelineno-9-12  "b" = c(0, 1, 2)
    #__codelineno-9-13)
    #__codelineno-9-14
    #__codelineno-9-15out = df$
    #__codelineno-9-16  filter(
    #__codelineno-9-17    pl$fold(
    #__codelineno-9-18        acc=pl$lit(TRUE),
    #__codelineno-9-19        function=lambda acc, x = acc & x,
    #__codelineno-9-20        exprs=pl$col("*") > 1,
    #__codelineno-9-21    )
    #__codelineno-9-22)
    #__codelineno-9-23print(out)
    #__codelineno-9-24
    #__codelineno-9-25df = pl$DataFrame(
    #__codelineno-9-26  "a" = c("a", "b", "c"),
    #__codelineno-9-27  "b" = c(1, 2, 3)
    #__codelineno-9-28)
    #__codelineno-9-29
    #__codelineno-9-30df$select(
    #__codelineno-9-31  pl$concat_str("a", "b")
    #__codelineno-9-32)
## Window functions

    #__codelineno-10-1df = pl$read_csv(
    #__codelineno-10-2  "https://gist.githubusercontent.com/ritchie46/cac6b337ea52281aa23c049250a4ff03/raw/89a957ff3919d90e6ef2d34235e6bf22304f3366/pokemon.csv"
    #__codelineno-10-3)
    #__codelineno-11-1df$select(
    #__codelineno-11-2  "Type 1",
    #__codelineno-11-3  "Type 2",
    #__codelineno-11-4  pl$col("Attack")$mean()$over("Type 1")$alias("avg_attack_by_type"),
    #__codelineno-11-5  pl$col("Defense")$mean()$over(c("Type 1", "Type 2"))$alias("avg_defense_by_type_combination"),
    #__codelineno-11-6  pl$col("Attack")$mean()$alias("avg_attack")
    #__codelineno-11-7)
    #__codelineno-12-1filtered = df$
    #__codelineno-12-2  filter(pl$col("Type 2") == "Psychic")$
    #__codelineno-12-3  select(c("Name", "Type 1", "Speed"))
    #__codelineno-12-4filtered
    #__codelineno-12-5
    #__codelineno-12-6filtered$with_columns(
    #__codelineno-12-7  pl$col(c("Name", "Speed"))$sort()$over("Type 1")
    #__codelineno-12-8)
    #__codelineno-12-9
    #__codelineno-12-10# aggregate and broadcast within a group
    #__codelineno-12-11# output type: -> Int32
    #__codelineno-12-12pl$sum("foo")$over("groups")
    #__codelineno-12-13
    #__codelineno-12-14# sum within a group and multiply with group elements
    #__codelineno-12-15# output type: -> Int32
    #__codelineno-12-16(pl$col("x")$sum() * pl$col("y"))$over("groups")
    #__codelineno-12-17
    #__codelineno-12-18# sum within a group and multiply with group elements
    #__codelineno-12-19# and aggregate/implode the group to a list
    #__codelineno-12-20# output type: -> List(Int32)
    #__codelineno-12-21(pl$col("x")$sum() * pl$col("y"))$implode()$over("groups")
    #__codelineno-12-22
    #__codelineno-12-23# note that it will require an explicit `implode()` call
    #__codelineno-12-24# sum within a group and multiply with group elements
    #__codelineno-12-25# and aggregate/implode the group to a list
    #__codelineno-12-26# the explode call unpack the list and combine inner elements to one column
    #__codelineno-12-27
    #__codelineno-12-28# This is the fastest method to do things over groups when the groups are sorted
    #__codelineno-12-29(pl$col("x")$sum() * pl$col("y"))$implode()$over("groups")$explode()
    #__codelineno-12-30
    #__codelineno-12-31df$sort("Type 1")$select(
    #__codelineno-12-32  pl$col("Type 1")$head(3)$implode()$over("Type 1")$explode(),
    #__codelineno-12-33  pl$col("Name")$sort_by(pl$col("Speed"))$head(3)$implode()$over("Type 1")$explode()$alias("fastest/group"),
    #__codelineno-12-34  pl$col("Name")$sort_by(pl$col("Attack"))$head(3)$implode()$over("Type 1")$explode()$alias("strongest/group"),
    #__codelineno-12-35  pl$col("Name")$sort()$head(3)$implode()$over("Type 1")$explode()$alias("sorted_by_alphabet")
    #__codelineno-12-36)
# List context and row wise computations

    #__codelineno-13-1grades = pl$DataFrame(
    #__codelineno-13-2        "student" = c("bas", "laura", "tim", "jenny"),
    #__codelineno-13-3        "arithmetic" = c(10, 5, 6, 8),
    #__codelineno-13-4        "biology" = c(4, 6, 2, 7),
    #__codelineno-13-5        "geography" = c(8, 4, 9, 7)
    #__codelineno-13-6)
    #__codelineno-13-7grades
    #__codelineno-13-8
    #__codelineno-13-9# grades$select(c(pl$concat_list(pl$all()$exclude("student"))$alias("all_grades")))
    #__codelineno-13-10
    #__codelineno-13-11# the percentage rank expression
    #__codelineno-13-12# rank_pct = pl$element()$rank(descending = TRUE) / pl$col("")$count()
    #__codelineno-13-13rank_pct = pl$element()$rank() / pl$col("")$count()
    #__codelineno-13-14
    #__codelineno-13-15grades$with_columns(
    #__codelineno-13-16    # create the list of homogeneous data
    #__codelineno-13-17    pl$concat_list(pl$all()$exclude("student"))$alias("all_grades")
    #__codelineno-13-18)$select(c(
    #__codelineno-13-19    # select all columns except the intermediate list
    #__codelineno-13-20    pl$all()$exclude("all_grades"),
    #__codelineno-13-21    # compute the rank by calling `arr$eval`
    #__codelineno-13-22    pl$col("all_grades")$list$eval(rank_pct, parallel = TRUE)$alias("grades_rank")
    #__codelineno-13-23))
# Custom functions

    #__codelineno-14-1df = pl$DataFrame(
    #__codelineno-14-2  "keys" = c("a", "a", "b"),
    #__codelineno-14-3  "values" = c(10, 7, 1)
    #__codelineno-14-4)
    #__codelineno-14-5
    #__codelineno-14-6df$group_by("keys")$agg(
    #__codelineno-14-7        pl$col("values")$map_batches(function(s) s$shift())$alias("shift_map"),
    #__codelineno-14-8        pl$col("values")$shift()$alias("shift_expression")
    #__codelineno-14-9)
    #__codelineno-14-10
    #__codelineno-14-11df$group_by("keys")$agg(
    #__codelineno-14-12        pl$col("values")$map_batches(function(s) s$shift())$alias("shift_map"),
    #__codelineno-14-13        pl$col("values")$shift()$alias("shift_expression")
    #__codelineno-14-14)
    #__codelineno-14-15
    #__codelineno-14-16df$group_by("keys")$agg(
    #__codelineno-14-17  pl$col("values")$apply(function(s) s$shift())$alias("shift_map"),
    #__codelineno-14-18  pl$col("values")$shift()$alias("shift_expression")
    #__codelineno-14-19)
    #__codelineno-14-20
    #__codelineno-14-21counter = 0
    #__codelineno-14-22add_counter = function(val) {
    #__codelineno-14-23  counter <<- counter + 1
    #__codelineno-14-24  counter + val
    #__codelineno-14-25}
    #__codelineno-14-26
    #__codelineno-14-27out = df$select(
    #__codelineno-14-28  pl$col("values")$apply(add_counter)$alias("solution_apply"),
    #__codelineno-14-29  (pl$col("values") + pl$arange(1, pl$count() + 1))$alias("solution_expr")
    #__codelineno-14-30)
    #__codelineno-14-31
    #__codelineno-14-32print(out)
    #__codelineno-14-33
    #__codelineno-14-34out = df$select(
    #__codelineno-14-35  pl$struct(c("keys", "values"))$apply(lambda x = len(xc("keys")) + xc("values"))$alias("solution_apply"),
    #__codelineno-14-36  (pl$col("keys")$str$len_bytes() + pl$col("values"))$alias("solution_expr")
    #__codelineno-14-37)
    #__codelineno-14-38print(out)
# R examples

    #__codelineno-15-1df = pl$DataFrame(
    #__codelineno-15-2  "A" = c(1, 2, 3, 4, 5),
    #__codelineno-15-3  "fruits" = c("banana", "banana", "apple", "apple", "banana"),
    #__codelineno-15-4  "B" = c(5, 4, 3, 2, 1),
    #__codelineno-15-5  "cars" = c("beetle", "audi", "beetle", "beetle", "beetle"),
    #__codelineno-15-6  "optional" = c(28, 300, NA, 2, -30)
    #__codelineno-15-7)
    #__codelineno-15-8df
    #__codelineno-15-9#> shape: (5, 5)
    #__codelineno-15-10#> ┌─────┬────────┬─────┬────────┬──────────┐
    #__codelineno-15-11#> │ A   ┆ fruits ┆ B   ┆ cars   ┆ optional │
    #__codelineno-15-12#> │ --- ┆ ---    ┆ --- ┆ ---    ┆ ---      │
    #__codelineno-15-13#> │ f64 ┆ str    ┆ f64 ┆ str    ┆ f64      │
    #__codelineno-15-14#> ╞═════╪════════╪═════╪════════╪══════════╡
    #__codelineno-15-15#> │ 1.0 ┆ banana ┆ 5.0 ┆ beetle ┆ 28.0     │
    #__codelineno-15-16#> │ 2.0 ┆ banana ┆ 4.0 ┆ audi   ┆ 300.0    │
    #__codelineno-15-17#> │ 3.0 ┆ apple  ┆ 3.0 ┆ beetle ┆ null     │
    #__codelineno-15-18#> │ 4.0 ┆ apple  ┆ 2.0 ┆ beetle ┆ 2.0      │
    #__codelineno-15-19#> │ 5.0 ┆ banana ┆ 1.0 ┆ beetle ┆ -30.0    │
    #__codelineno-15-20#> └─────┴────────┴─────┴────────┴──────────┘
    #__codelineno-15-21
    #__codelineno-15-22# Within select, we can use the col function to refer to columns$
    #__codelineno-15-23# If we are not applying any function to the column, we can also use the column name as a string$
    #__codelineno-15-24df$select(
    #__codelineno-15-25  pl$col("A"),
    #__codelineno-15-26  "B", # the col part is inferred
    #__codelineno-15-27  pl$lit("B") # the pl$lit functions tell polars we mean the literal "B"
    #__codelineno-15-28)
    #__codelineno-15-29#> shape: (5, 3)
    #__codelineno-15-30#> ┌─────┬─────┬─────────┐
    #__codelineno-15-31#> │ A   ┆ B   ┆ literal │
    #__codelineno-15-32#> │ --- ┆ --- ┆ ---     │
    #__codelineno-15-33#> │ f64 ┆ f64 ┆ str     │
    #__codelineno-15-34#> ╞═════╪═════╪═════════╡
    #__codelineno-15-35#> │ 1.0 ┆ 5.0 ┆ B       │
    #__codelineno-15-36#> │ 2.0 ┆ 4.0 ┆ B       │
    #__codelineno-15-37#> │ 3.0 ┆ 3.0 ┆ B       │
    #__codelineno-15-38#> │ 4.0 ┆ 2.0 ┆ B       │
    #__codelineno-15-39#> │ 5.0 ┆ 1.0 ┆ B       │
    #__codelineno-15-40#> └─────┴─────┴─────────┘
    #__codelineno-15-41
    #__codelineno-15-42# We can use a list within select (example above) or a comma-separated list of expressions (this example)$
    #__codelineno-15-43df$select(
    #__codelineno-15-44  pl$col("A"),
    #__codelineno-15-45  "B",
    #__codelineno-15-46  pl$lit("B")
    #__codelineno-15-47)
    #__codelineno-15-48#> shape: (5, 3)
    #__codelineno-15-49#> ┌─────┬─────┬─────────┐
    #__codelineno-15-50#> │ A   ┆ B   ┆ literal │
    #__codelineno-15-51#> │ --- ┆ --- ┆ ---     │
    #__codelineno-15-52#> │ f64 ┆ f64 ┆ str     │
    #__codelineno-15-53#> ╞═════╪═════╪═════════╡
    #__codelineno-15-54#> │ 1.0 ┆ 5.0 ┆ B       │
    #__codelineno-15-55#> │ 2.0 ┆ 4.0 ┆ B       │
    #__codelineno-15-56#> │ 3.0 ┆ 3.0 ┆ B       │
    #__codelineno-15-57#> │ 4.0 ┆ 2.0 ┆ B       │
    #__codelineno-15-58#> │ 5.0 ┆ 1.0 ┆ B       │
    #__codelineno-15-59#> └─────┴─────┴─────────┘
    #__codelineno-15-60
    #__codelineno-15-61# We can select columns with a regex if the regex starts with '^' and ends with '$'
    #__codelineno-15-62df$select(
    #__codelineno-15-63  pl$col("^A|B$")$sum()
    #__codelineno-15-64)
    #__codelineno-15-65#> shape: (1, 2)
    #__codelineno-15-66#> ┌──────┬──────┐
    #__codelineno-15-67#> │ A    ┆ B    │
    #__codelineno-15-68#> │ ---  ┆ ---  │
    #__codelineno-15-69#> │ f64  ┆ f64  │
    #__codelineno-15-70#> ╞══════╪══════╡
    #__codelineno-15-71#> │ 15.0 ┆ 15.0 │
    #__codelineno-15-72#> └──────┴──────┘
    #__codelineno-15-73
    #__codelineno-15-74# We can select multiple columns by name
    #__codelineno-15-75df$select(
    #__codelineno-15-76  pl$col(c("A", "B"))$sum()
    #__codelineno-15-77)
    #__codelineno-15-78#> shape: (1, 2)
    #__codelineno-15-79#> ┌──────┬──────┐
    #__codelineno-15-80#> │ A    ┆ B    │
    #__codelineno-15-81#> │ ---  ┆ ---  │
    #__codelineno-15-82#> │ f64  ┆ f64  │
    #__codelineno-15-83#> ╞══════╪══════╡
    #__codelineno-15-84#> │ 15.0 ┆ 15.0 │
    #__codelineno-15-85#> └──────┴──────┘
    #__codelineno-15-86
    #__codelineno-15-87# We select everything in normal order
    #__codelineno-15-88# Then we select everything in reversed order
    #__codelineno-15-89df$select(
    #__codelineno-15-90  pl$all(),
    #__codelineno-15-91  pl$all()$reverse()$name$suffix("_reverse")
    #__codelineno-15-92)
    #__codelineno-15-93#> shape: (5, 10)
    #__codelineno-15-94#> ┌─────┬────────┬─────┬────────┬───┬────────────────┬───────────┬──────────────┬──────────────────┐
    #__codelineno-15-95#> │ A   ┆ fruits ┆ B   ┆ cars   ┆ … ┆ fruits_reverse ┆ B_reverse ┆ cars_reverse ┆ optional_reverse │
    #__codelineno-15-96#> │ --- ┆ ---    ┆ --- ┆ ---    ┆   ┆ ---            ┆ ---       ┆ ---          ┆ ---              │
    #__codelineno-15-97#> │ f64 ┆ str    ┆ f64 ┆ str    ┆   ┆ str            ┆ f64       ┆ str          ┆ f64              │
    #__codelineno-15-98#> ╞═════╪════════╪═════╪════════╪═══╪════════════════╪═══════════╪══════════════╪══════════════════╡
    #__codelineno-15-99#> │ 1.0 ┆ banana ┆ 5.0 ┆ beetle ┆ … ┆ banana         ┆ 1.0       ┆ beetle       ┆ -30.0            │
    #__codelineno-15-100#> │ 2.0 ┆ banana ┆ 4.0 ┆ audi   ┆ … ┆ apple          ┆ 2.0       ┆ beetle       ┆ 2.0              │
    #__codelineno-15-101#> │ 3.0 ┆ apple  ┆ 3.0 ┆ beetle ┆ … ┆ apple          ┆ 3.0       ┆ beetle       ┆ null             │
    #__codelineno-15-102#> │ 4.0 ┆ apple  ┆ 2.0 ┆ beetle ┆ … ┆ banana         ┆ 4.0       ┆ audi         ┆ 300.0            │
    #__codelineno-15-103#> │ 5.0 ┆ banana ┆ 1.0 ┆ beetle ┆ … ┆ banana         ┆ 5.0       ┆ beetle       ┆ 28.0             │
    #__codelineno-15-104#> └─────┴────────┴─────┴────────┴───┴────────────────┴───────────┴──────────────┴──────────────────┘
    #__codelineno-15-105
    #__codelineno-15-106# All expressions run in parallel
    #__codelineno-15-107# Single valued `Series` are broadcasted to the shape of the `DataFrame`
    #__codelineno-15-108df$select(
    #__codelineno-15-109  pl$all(),
    #__codelineno-15-110  pl$col(pl$Float64)$sum()$name$suffix("_sum") # This is a single valued Series broadcasted to the shape of the DataFrame
    #__codelineno-15-111)
    #__codelineno-15-112#> shape: (5, 8)
    #__codelineno-15-113#> ┌─────┬────────┬─────┬────────┬──────────┬───────┬───────┬──────────────┐
    #__codelineno-15-114#> │ A   ┆ fruits ┆ B   ┆ cars   ┆ optional ┆ A_sum ┆ B_sum ┆ optional_sum │
    #__codelineno-15-115#> │ --- ┆ ---    ┆ --- ┆ ---    ┆ ---      ┆ ---   ┆ ---   ┆ ---          │
    #__codelineno-15-116#> │ f64 ┆ str    ┆ f64 ┆ str    ┆ f64      ┆ f64   ┆ f64   ┆ f64          │
    #__codelineno-15-117#> ╞═════╪════════╪═════╪════════╪══════════╪═══════╪═══════╪══════════════╡
    #__codelineno-15-118#> │ 1.0 ┆ banana ┆ 5.0 ┆ beetle ┆ 28.0     ┆ 15.0  ┆ 15.0  ┆ 300.0        │
    #__codelineno-15-119#> │ 2.0 ┆ banana ┆ 4.0 ┆ audi   ┆ 300.0    ┆ 15.0  ┆ 15.0  ┆ 300.0        │
    #__codelineno-15-120#> │ 3.0 ┆ apple  ┆ 3.0 ┆ beetle ┆ null     ┆ 15.0  ┆ 15.0  ┆ 300.0        │
    #__codelineno-15-121#> │ 4.0 ┆ apple  ┆ 2.0 ┆ beetle ┆ 2.0      ┆ 15.0  ┆ 15.0  ┆ 300.0        │
    #__codelineno-15-122#> │ 5.0 ┆ banana ┆ 1.0 ┆ beetle ┆ -30.0    ┆ 15.0  ┆ 15.0  ┆ 300.0        │
    #__codelineno-15-123#> └─────┴────────┴─────┴────────┴──────────┴───────┴───────┴──────────────┘
    #__codelineno-15-124
    #__codelineno-15-125# Filters can also be applied within an expression
    #__codelineno-15-126df$select(
    #__codelineno-15-127  # Sum the values of A where the fruits column starts with 'b'
    #__codelineno-15-128  pl$col("A")$filter(pl$col("fruits")$str$contains("^b$*"))$sum()
    #__codelineno-15-129)
    #__codelineno-15-130#> shape: (1, 1)
    #__codelineno-15-131#> ┌─────┐
    #__codelineno-15-132#> │ A   │
    #__codelineno-15-133#> │ --- │
    #__codelineno-15-134#> │ f64 │
    #__codelineno-15-135#> ╞═════╡
    #__codelineno-15-136#> │ 8.0 │
    #__codelineno-15-137#> └─────┘
    #__codelineno-15-138
    #__codelineno-15-139# We can do arithmetic on columns
    #__codelineno-15-140df$select(
    #__codelineno-15-141  ((pl$col("A") / 124.0 * pl$col("B")) / pl$sum("B"))$alias("computed")
    #__codelineno-15-142)
    #__codelineno-15-143#> shape: (5, 1)
    #__codelineno-15-144#> ┌──────────┐
    #__codelineno-15-145#> │ computed │
    #__codelineno-15-146#> │ ---      │
    #__codelineno-15-147#> │ f64      │
    #__codelineno-15-148#> ╞══════════╡
    #__codelineno-15-149#> │ 0.002688 │
    #__codelineno-15-150#> │ 0.004301 │
    #__codelineno-15-151#> │ 0.004839 │
    #__codelineno-15-152#> │ 0.004301 │
    #__codelineno-15-153#> │ 0.002688 │
    #__codelineno-15-154#> └──────────┘
    #__codelineno-15-155
    #__codelineno-15-156# We can combine columns by a predicate
    #__codelineno-15-157# For example when the `fruits` column is 'banana' we set the value equal to the
    #__codelineno-15-158# value in `B` column for that row, otherwise we set the value to be -1
    #__codelineno-15-159df$select(
    #__codelineno-15-160  "fruits",
    #__codelineno-15-161  "B",
    #__codelineno-15-162  pl$when(pl$col("fruits") == "banana")$then(pl$col("B"))$otherwise(-1)$alias("b")
    #__codelineno-15-163)
    #__codelineno-15-164#> shape: (5, 3)
    #__codelineno-15-165#> ┌────────┬─────┬──────┐
    #__codelineno-15-166#> │ fruits ┆ B   ┆ b    │
    #__codelineno-15-167#> │ ---    ┆ --- ┆ ---  │
    #__codelineno-15-168#> │ str    ┆ f64 ┆ f64  │
    #__codelineno-15-169#> ╞════════╪═════╪══════╡
    #__codelineno-15-170#> │ banana ┆ 5.0 ┆ 5.0  │
    #__codelineno-15-171#> │ banana ┆ 4.0 ┆ 4.0  │
    #__codelineno-15-172#> │ apple  ┆ 3.0 ┆ -1.0 │
    #__codelineno-15-173#> │ apple  ┆ 2.0 ┆ -1.0 │
    #__codelineno-15-174#> │ banana ┆ 1.0 ┆ 1.0  │
    #__codelineno-15-175#> └────────┴─────┴──────┘
    #__codelineno-15-176
    #__codelineno-15-177# We can combine columns by a fold operation on column level$
    #__codelineno-15-178# For example we do a horizontal sum where we:
    #__codelineno-15-179# - start with 0
    #__codelineno-15-180# - add the value in the `A` column
    #__codelineno-15-181# - add the value in the `B` column
    #__codelineno-15-182# - add the value in the `B` column squared
    #__codelineno-15-183# df$select(
    #__codelineno-15-184#   "A",
    #__codelineno-15-185#   "B",
    #__codelineno-15-186#   pl$fold(0, function(a, b) a + b, c( pl$col("A"), "B", pl$col("B")**2,))$alias("fold")
    #__codelineno-15-187# )
    #__codelineno-15-188
    #__codelineno-15-189df$group_by("fruits")$
    #__codelineno-15-190  agg(
    #__codelineno-15-191  pl$col("B")$count()$alias("B_count"),
    #__codelineno-15-192  pl$col("B")$sum()$alias("B_sum")
    #__codelineno-15-193)
    #__codelineno-15-194#> shape: (2, 3)
    #__codelineno-15-195#> ┌────────┬─────────┬───────┐
    #__codelineno-15-196#> │ fruits ┆ B_count ┆ B_sum │
    #__codelineno-15-197#> │ ---    ┆ ---     ┆ ---   │
    #__codelineno-15-198#> │ str    ┆ u32     ┆ f64   │
    #__codelineno-15-199#> ╞════════╪═════════╪═══════╡
    #__codelineno-15-200#> │ banana ┆ 3       ┆ 10.0  │
    #__codelineno-15-201#> │ apple  ┆ 2       ┆ 5.0   │
    #__codelineno-15-202#> └────────┴─────────┴───────┘
    #__codelineno-15-203
    #__codelineno-15-204# We can aggregate many expressions at once
    #__codelineno-15-205df$group_by("fruits")$
    #__codelineno-15-206  agg(
    #__codelineno-15-207  pl$col("B")$sum()$alias("B_sum"), # Sum of B
    #__codelineno-15-208  # pl$first("fruits")$alias("fruits_first"),# First value of fruits
    #__codelineno-15-209  # pl$count("A")$alias("count"),# Count of A
    #__codelineno-15-210  pl$col("cars")$reverse() # Reverse the cars column - not an aggregation
    #__codelineno-15-211  # so the output is a pl$List
    #__codelineno-15-212)
    #__codelineno-15-213#> shape: (2, 3)
    #__codelineno-15-214#> ┌────────┬───────┬──────────────────────────────┐
    #__codelineno-15-215#> │ fruits ┆ B_sum ┆ cars                         │
    #__codelineno-15-216#> │ ---    ┆ ---   ┆ ---                          │
    #__codelineno-15-217#> │ str    ┆ f64   ┆ list[str]                    │
    #__codelineno-15-218#> ╞════════╪═══════╪══════════════════════════════╡
    #__codelineno-15-219#> │ apple  ┆ 5.0   ┆ ["beetle", "beetle"]         │
    #__codelineno-15-220#> │ banana ┆ 10.0  ┆ ["beetle", "audi", "beetle"] │
    #__codelineno-15-221#> └────────┴───────┴──────────────────────────────┘
    #__codelineno-16-1# We can explode the list column "cars" to a new row for each element in the list
    #__codelineno-16-2df$
    #__codelineno-16-3  # sort("cars")$
    #__codelineno-16-4  group_by("fruits")$
    #__codelineno-16-5  agg(
    #__codelineno-16-6    # pl$col("B")$sum()$alias("B_sum"),
    #__codelineno-16-7    # pl$col("B")$sum()$alias("B_sum2"),  # syntactic sugar for the first
    #__codelineno-16-8    pl$col("fruits")$first()$alias("fruits_first"),
    #__codelineno-16-9    # pl$first("fruits")$alias("fruits_first"),
    #__codelineno-16-10    pl$col("A")$count()$alias("count"),
    #__codelineno-16-11    # pl$count("A")$alias("count"),
    #__codelineno-16-12    pl$col("cars")$reverse()
    #__codelineno-16-13    )$
    #__codelineno-16-14  explode("cars")
    #__codelineno-17-1# We can also get a list of the row indices for each group with `agg_groups()`
    #__codelineno-17-2df$
    #__codelineno-17-3  group_by("fruits")$
    #__codelineno-17-4  agg(pl$col("B")$agg_groups()$alias("group_row_indices"))
    #__codelineno-17-5#> shape: (2, 2)
    #__codelineno-17-6#> ┌────────┬───────────────────┐
    #__codelineno-17-7#> │ fruits ┆ group_row_indices │
    #__codelineno-17-8#> │ ---    ┆ ---               │
    #__codelineno-17-9#> │ str    ┆ list[u32]         │
    #__codelineno-17-10#> ╞════════╪═══════════════════╡
    #__codelineno-17-11#> │ apple  ┆ [2, 3]            │
    #__codelineno-17-12#> │ banana ┆ [0, 1, 4]         │
    #__codelineno-17-13#> └────────┴───────────────────┘
    #__codelineno-17-14
    #__codelineno-17-15# We can also do filter predicates in group_by
    #__codelineno-17-16# In this example we do not include values of B that are smaller than 1
    #__codelineno-17-17# in the sum
    #__codelineno-17-18df$
    #__codelineno-17-19  group_by("fruits")$
    #__codelineno-17-20  agg(pl$col("B")$filter(pl$col("B") > 1)$sum())
    #__codelineno-17-21#> shape: (2, 2)
    #__codelineno-17-22#> ┌────────┬─────┐
    #__codelineno-17-23#> │ fruits ┆ B   │
    #__codelineno-17-24#> │ ---    ┆ --- │
    #__codelineno-17-25#> │ str    ┆ f64 │
    #__codelineno-17-26#> ╞════════╪═════╡
    #__codelineno-17-27#> │ apple  ┆ 5.0 │
    #__codelineno-17-28#> │ banana ┆ 9.0 │
    #__codelineno-17-29#> └────────┴─────┘
    #__codelineno-17-30
    #__codelineno-17-31
    #__codelineno-17-32# Here we add a new column with the sum of B grouped by fruits
    #__codelineno-17-33df$
    #__codelineno-17-34  select(
    #__codelineno-17-35  "fruits",
    #__codelineno-17-36  "cars",
    #__codelineno-17-37  "B",
    #__codelineno-17-38  pl$col("B")$sum()$over("fruits")$alias("B_sum_by_fruits")
    #__codelineno-17-39)
    #__codelineno-17-40#> shape: (5, 4)
    #__codelineno-17-41#> ┌────────┬────────┬─────┬─────────────────┐
    #__codelineno-17-42#> │ fruits ┆ cars   ┆ B   ┆ B_sum_by_fruits │
    #__codelineno-17-43#> │ ---    ┆ ---    ┆ --- ┆ ---             │
    #__codelineno-17-44#> │ str    ┆ str    ┆ f64 ┆ f64             │
    #__codelineno-17-45#> ╞════════╪════════╪═════╪═════════════════╡
    #__codelineno-17-46#> │ banana ┆ beetle ┆ 5.0 ┆ 10.0            │
    #__codelineno-17-47#> │ banana ┆ audi   ┆ 4.0 ┆ 10.0            │
    #__codelineno-17-48#> │ apple  ┆ beetle ┆ 3.0 ┆ 5.0             │
    #__codelineno-17-49#> │ apple  ┆ beetle ┆ 2.0 ┆ 5.0             │
    #__codelineno-17-50#> │ banana ┆ beetle ┆ 1.0 ┆ 10.0            │
    #__codelineno-17-51#> └────────┴────────┴─────┴─────────────────┘
    #__codelineno-17-52
    #__codelineno-17-53# We can also use window functions to do group_by over multiple columns
    #__codelineno-17-54df$
    #__codelineno-17-55  select(
    #__codelineno-17-56  "fruits",
    #__codelineno-17-57  "cars",
    #__codelineno-17-58  "B",
    #__codelineno-17-59  pl$col("B")$sum()$over("fruits")$alias("B_sum_by_fruits"),
    #__codelineno-17-60  pl$col("B")$sum()$over("cars")$alias("B_sum_by_cars")
    #__codelineno-17-61)
    #__codelineno-17-62#> shape: (5, 5)
    #__codelineno-17-63#> ┌────────┬────────┬─────┬─────────────────┬───────────────┐
    #__codelineno-17-64#> │ fruits ┆ cars   ┆ B   ┆ B_sum_by_fruits ┆ B_sum_by_cars │
    #__codelineno-17-65#> │ ---    ┆ ---    ┆ --- ┆ ---             ┆ ---           │
    #__codelineno-17-66#> │ str    ┆ str    ┆ f64 ┆ f64             ┆ f64           │
    #__codelineno-17-67#> ╞════════╪════════╪═════╪═════════════════╪═══════════════╡
    #__codelineno-17-68#> │ banana ┆ beetle ┆ 5.0 ┆ 10.0            ┆ 11.0          │
    #__codelineno-17-69#> │ banana ┆ audi   ┆ 4.0 ┆ 10.0            ┆ 4.0           │
    #__codelineno-17-70#> │ apple  ┆ beetle ┆ 3.0 ┆ 5.0             ┆ 11.0          │
    #__codelineno-17-71#> │ apple  ┆ beetle ┆ 2.0 ┆ 5.0             ┆ 11.0          │
    #__codelineno-17-72#> │ banana ┆ beetle ┆ 1.0 ┆ 10.0            ┆ 11.0          │
    #__codelineno-17-73#> └────────┴────────┴─────┴─────────────────┴───────────────┘
    #__codelineno-17-74
    #__codelineno-17-75# Here we use a window function to lag column B within "fruits"
    #__codelineno-17-76df$
    #__codelineno-17-77  select(
    #__codelineno-17-78  "fruits",
    #__codelineno-17-79  "B",
    #__codelineno-17-80  pl$col("B")$shift()$over("fruits")$alias("lag_B_by_fruits")
    #__codelineno-17-81)
    #__codelineno-17-82#> shape: (5, 3)
    #__codelineno-17-83#> ┌────────┬─────┬─────────────────┐
    #__codelineno-17-84#> │ fruits ┆ B   ┆ lag_B_by_fruits │
    #__codelineno-17-85#> │ ---    ┆ --- ┆ ---             │
    #__codelineno-17-86#> │ str    ┆ f64 ┆ f64             │
    #__codelineno-17-87#> ╞════════╪═════╪═════════════════╡
    #__codelineno-17-88#> │ banana ┆ 5.0 ┆ null            │
    #__codelineno-17-89#> │ banana ┆ 4.0 ┆ 5.0             │
    #__codelineno-17-90#> │ apple  ┆ 3.0 ┆ null            │
    #__codelineno-17-91#> │ apple  ┆ 2.0 ┆ 3.0             │
    #__codelineno-17-92#> │ banana ┆ 1.0 ┆ 4.0             │
    #__codelineno-17-93#> └────────┴─────┴─────────────────┘
