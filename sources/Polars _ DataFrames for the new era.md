---
title: Polars — DataFrames for the new era
---

# Polars — DataFrames for the new era

url:: https://pola.rs/
up: [[sources]]

#source

# DataFrames for
the new ++era++

[Get started](https://docs.pola.rs/user-guide/getting-started/)
![Data swooshes](https://pola.rs/_astro/hero-bg.W3EmM7Vb_16iaFC.svg) Polars is an open-source library for data manipulation, known for being one of the fastest data processing solutions on a single machine. It features a well-structured, typed API that is both expressive and easy to use.  
![Polars Cloud](https://pola.rs/_astro/polars-cloud.DDYQK_KH_2qE7dT.png)

## Benefits

![Fast](https://pola.rs/_astro/icon-1.DUJfifOP_1dbwup.webp) 01 Fast

Polars is written from the ground up with performance in mind. Its multi-threaded query engine is written in Rust and designed for effective parallelism. Its vectorized and columnar processing enables cache-coherent algorithms and high performance on modern processors.  
![Easy to use](https://pola.rs/_astro/icon-2.CXJ3cZv3_XIs12.svg) 02 Easy to use

You will feel right at home with Polars if you are familiar with data wrangling. Its expressions are intuitive and empower you to write code which is readable and performant at the same time.  
![Open source](https://pola.rs/_astro/icon-3.CMcWLZ2U_1ptfFB.svg) 03 Open source

Polars is and always will be open source. Driven by an active community of developers, everyone is encouraged to add new features and contribute. Polars is free to use under the MIT license.

## Built by developers for developers to achieve up to 50x performance



Why use Polars

Polars was benchmarked in a derived version of the independent TPC-H benchmark against several other solutions. This benchmark aims to replicate data wrangling operations used in practice. Polars easily trumps other solutions due to its parallel execution engine, efficient algorithms and use of vectorization with SIMD (Single Instruction, Multiple Data). Compared to pandas, it can achieve more than 30x performance gains.
[Learn more →](https://pola.rs/posts/benchmarks)

The benchmark was performed on a [c3-highmem-22](https://cloud.google.com/compute/docs/general-purpose-machines#n2-high-mem) with scale factor 10 including I/O. The queries are [open source](https://github.com/pola-rs/polars-benchmark).

### Quick install

Polars is easy to install using your standard package manager. Select your programming language and get started!  
`[dependencies]`  
`polars = { version = "x", features = ["lazy", ...]}`  
`const pl = require('nodejs-polars');`

#### Leading companies using Polars

> "Polars revolutionizes data analysis, completely replacing pandas in my setup. It offers massive performance boosts, effortlessly handling data frames with millions of rows. Allowing us to focus on crafting simpler, cleaner code. This efficiency drastically cuts down iteration time, driving improved trading decisions. And speed? Polars isn't just fast—it's lightning-fast."
> Matt Whitehead
>
Quantitative Researcher @ Optiver  
> " Migrating from pandas to Polars was surprisingly easy. For us, the results speak for themselves. Polars not only solved our initial problem but opened the door to new possibilities. We are excited to use Polars on future data engineering projects. "
> Paul Duvenage
>
Senior Data Engineer @ Check  
> "The speedup of Polars compared to Pandas is massively noticeable. I generally enjoy writing code that I know is fast."
> Casey H.
>
> Machine Learning Engineer @ G-Research
Support

### Support for all common data formats

Polars supports reading and writing to all common data formats. This allows you to easily integrate Polars with your existing data stack.

* Text: CSV & JSON
* Binary: Parquet, Delta Lake, AVRO & Excel
* IPC: Feather, Arrow
* Databases: MySQL, Postgres, SQL Server, Sqlite, Redshift & Oracle
* Cloud storage: S3, Azure Blob & Azure File

## How ++Polars++ will
make your life ++easier++



01

#### Easy
to use

Write your queries the way they were intended. Polars will determine the most efficient way to execute them using its query optimizer.  
02

#### Embarrassingly parallel

Complete your queries faster! Polars fully utilizes the power of your machine by dividing the workload among the available CPU cores without any additional configuration or serialization overhead.  
03

#### Apache
Arrow

Polars utilizes the Apache Arrow memory model allowing you to easily integrate with existing tools in the data landscape. It supports zero-copy data sharing for efficient collaboration.  
04

#### Close to
the metal

Polars is written from the ground up, designed close to the machine and without external dependencies. This allows for full control of the ecosystem (API, memory & execution).  
05

#### Written
in Rust

The core of Polars is written in Rust, one of the fastest growing programming languages in the world. Rust allows for high performance with fine-grained control over memory.  
06

#### Out
of core

Want to process large data sets that are bigger than your memory? Our streaming API allows you to process your results efficiently, eliminating the need to keep all data in memory.
