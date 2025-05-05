___
created: 20250501
summary:
___

# polars

#zettel

## refs

### up

- [[data-efficiency]]
- [[polars]]

### down

## content

install.packages("polars", repos = "https://rpolars.r-universe.dev")

Polars’ main functions are stored in the “pl” namespace and can be accessed using the “pl$” prefix to prevent conflicts with other packages and base R function names. For more, see here.
1.2.1 Convert a R data.frame to a polars DataFrame

First example to convert the most famous R data frame (iris) to a Polars DataFrame:

iris_polars <- pl$DataFrame(iris)
iris_polars

## What is Polars?

Polars is [a lightning fast](https://duckdblabs.github.io/db-benchmark/) Data Frame library. Its embarrassingly parallel execution, cache efficient algorithms and expressive API makes it perfect for efficient data wrangling, data pipelines, snappy APIs, and much more besides. Polars also supports “streaming mode” for out-of-memory operations. This allows users to analyze datasets many times larger than RAM.

The underlying computation engine is written in Rust and is built on the Apache Arrow columnar memory format.

as of 0.17.0, the first argument will correspond