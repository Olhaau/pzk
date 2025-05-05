---
title: Converting arbitrarily large CSVs to Parquet with R
---

# Converting arbitrarily large CSVs to Parquet with R

url:: https://www.r-bloggers.com/2025/03/converting-arbitrarily-large-csvs-to-parquet-with-r/
up: [[sources]]

#source

In this [recent post](https://lorentzen.ch/index.php/2025/03/22/converting-csv-to-parquet/), we have used [**Polars**](https://pola.rs/) and [**DuckDB**](https://duckdb.org/) to convert a large CSV file to Parquet in steaming mode – and Python.

Different people have contacted me and asked: “and in R?”

Simple answer: We have DuckDB, and we have different Polars bindings. Here, we are using {[polars}](https://github.com/pola-rs/r-polars) which is currently being overhauled into {neopandas}.

So let’s not wait any longer!

![](https://i2.wp.com/lorentzen.ch/wp-content/uploads/2025/03/image-3.png?w=578&ssl=1)   
![](https://i0.wp.com/lorentzen.ch/wp-content/uploads/2025/03/image-2.png?w=578&ssl=1)

Run times are on a Windows system with an Intel i7-13700H CPU.

## Generate 2.2 GB csv file

We use {data.table} to dump a randomly generated dataset with 100 Mio rows into a csv file.  

```
library(data.table)

set.seed(1)

n <- 1e8

df <- data.frame(
  X = sample(letters[1:3], n, TRUE),
  Y = runif(n),
  Z = sample(1:5, n, TRUE)
)

fwrite(df, "data.csv")
```

## DuckDB

Then, we use [DuckDB](https://duckdb.org/) to fire a query to the file and stream the result into Parquet.

Threads and RAM can be set on the fly, which is very convenient. Setting a low memory limit (e.g., 500 MB) will work – try it out!  

```
library(duckdb)

con <- dbConnect(duckdb(config = list(threads = "8", memory_limit = "4GB")))

system.time( # 3.5s
  dbSendQuery(
    con,
    "
    COPY (
      SELECT Y, Z
      FROM 'data.csv'
      WHERE X == 'a'
      ORDER BY Y
    ) TO 'data.parquet' (FORMAT parquet, COMPRESSION zstd)
    "
  )
)

# Check
dbGetQuery(con, "SELECT COUNT(*) N FROM 'data.parquet'") # 33329488
dbGetQuery(con, "SELECT * FROM 'data.parquet' LIMIT 5")
#              Y Z
# 1 5.355105e-09 4
# 2 9.080395e-09 5
# 3 2.258457e-08 2
# 4 3.445894e-08 2
# 5 6.891787e-08 1
```

3.5 seconds – wow! The resulting file looks good. It is 125 MB large.

## Polars

Let’s do the same with Polars.

```
# Sys.setenv(NOT_CRAN = "true")
# install.packages("polars", repos = "https://community.r-multiverse.org")
library(polars)

polars_info()

system.time( # 9s
  (
    pl$scan_csv("data.csv")
    $filter(pl$col("X") == "a")
    $drop("X")
    $sort("Y")
    $sink_parquet("data.parquet", row_group_size = 1e5)
  )
)

# Check
pl$scan_parquet("data.parquet")$head()$collect()
# shape: (5, 2)
# ┌───────────┬─────┐
# │ Y         ┆ Z   │
# │ ---       ┆ --- │
# │ f64       ┆ i64 │
# ╞═══════════╪═════╡
# │ 5.3551e-9 ┆ 4   │
# │ 9.0804e-9 ┆ 5   │
# │ 2.2585e-8 ┆ 2   │
# │ 3.4459e-8 ┆ 2   │
# │ 6.8918e-8 ┆ 1   │
# └───────────┴─────┘
```

With nine seconds, it is slower than DuckDB. But the output looks as expected and has the same size as with DuckDB.

## Final words

* With DuckDB or Polars, conversion of CSVs to Parquet is easy and fast, even in larger-than-RAM situations.
* We can apply filters, selects, sorts etc. on the fly.
* Let’s keep an eye on Polars in R. It looks really interesting.

[R script](https://github.com/lorentzenchr/notebooks/blob/master/blogposts/2025-03-30%20csv%20to%20parquet.R)
