---
title: Apache Arrow DataFusion 34.0.0 Released, Looking Forward to 2024
keywords: source
---

# Apache Arrow DataFusion 34.0.0 Released, Looking Forward to 2024

url:: https://arrow.apache.org/blog/2024/01/19/datafusion-34.0.0/
up: [[sources]]

# Apache Arrow DataFusion 34.0.0 Released, Looking Forward to 2024

---

Published 19 Jan 2024   
By [The Apache Arrow PMC (pmc)](https://arrow.apache.org)

## Introduction

We recently [released DataFusion 34.0.0](https://crates.io/crates/datafusion/34.0.0). This blog highlights some of the major improvements since we [released DataFusion 26.0.0](https://arrow.apache.org/blog/2023/06/24/datafusion-25.0.0/.) (spoiler alert there are many) and a preview of where the community plans to focus in the next 6 months.

[Apache Arrow DataFusion](https://arrow.apache.org/datafusion/) is an extensible query engine, written in [Rust](https://www.rust-lang.org/), that uses [Apache Arrow](https://arrow.apache.org) as its in-memory format. DataFusion is used by developers to create new, fast data centric systems such as databases, dataframe libraries, machine learning and streaming applications. While [DataFusion’s primary design goal](https://arrow.apache.org/datafusion/user-guide/introduction.html#project-goals) is to accelerate creating other data centric systems, it has a reasonable experience directly out of the box as a [dataframe library](https://arrow.apache.org/datafusion-python/) and [command line SQL tool](https://arrow.apache.org/datafusion/user-guide/cli.html).

This may also be our last update on the Apache Arrow Site. Future updates will likely be on the DataFusion website as we are working to [graduate to a top level project](https://github.com/apache/arrow-datafusion/discussions/6475) (Apache Arrow DataFusion → Apache DataFusion!) which will help focus governance and project growth. Also exciting, our [first DataFusion in person meetup](https://github.com/apache/arrow-datafusion/discussions/8522) is planned for March 2024.

DataFusion is very much a community endeavor. Our core thesis is that as a community we can build much more advanced technology than any of us as individuals or companies could alone. In the last 6 months between `26.0.0` and `34.0.0`, community growth has been strong. We accepted and reviewed over a thousand PRs from 124 different committers, created over 650 issues and closed 517 of them. You can find a list of all changes in the detailed [CHANGELOG](https://github.com/apache/arrow-datafusion/blob/main/datafusion/CHANGELOG.md).

# Improved Performance 🚀

Performance is a key feature of DataFusion, DataFusion is more than 2x faster on [ClickBench](https://benchmark.clickhouse.com/) compared to version `25.0.0`, as shown below:
![Fig 1: Adaptive Arrow schema architecture overview.](https://arrow.apache.org/img/datafusion-34.0.0/compare-new.png) **Figure 1** : Performance improvement between `25.0.0` and `34.0.0` on ClickBench. Note that DataFusion `25.0.0`, could not run several queries due to unsupported SQL (Q9, Q11, Q12, Q14) or memory requirements (Q33). ![Fig 1: Adaptive Arrow schema architecture overview.](https://arrow.apache.org/img/datafusion-34.0.0/compare.png) **Figure 2** : Total query runtime for DataFusion `34.0.0` and DataFusion `25.0.0`.

Here are some specific enhancements we have made to improve performance:

* [2-3x better aggregation performance with many distinct groups](https://arrow.apache.org/blog/2023/08/05/datafusion_fast_grouping/)
* Partially ordered grouping / streaming grouping
* [Specialized operator for “TopK” `ORDER BY LIMIT XXX`](https://github.com/apache/arrow-datafusion/pull/7721)
* [Specialized operator for `min(col) GROUP BY .. ORDER by min(col) LIMIT XXX`](https://github.com/apache/arrow-datafusion/pull/7192)
* [Improved join performance](https://github.com/apache/arrow-datafusion/pull/8126)
* Eliminate redundant sorting with sort order aware optimizers

# New Features ✨

## DML / Insert / Creating Files

DataFusion now supports writing data in parallel, to individual or multiple files, using `Parquet`, `CSV`, `JSON`, `ARROW` and user defined formats. [Benchmark results](https://github.com/apache/arrow-datafusion/pull/7655) show improvements up to 5x in some cases.

Similarly to reading, data can now be written to any [`ObjectStore`](https://docs.rs/object_store/0.9.0/object_store/index.html) implementation, including AWS S3, Azure Blob Storage, GCP Cloud Storage, local files, and user defined implementations. While reading from [hive style partitioned tables](https://docs.rs/datafusion/latest/datafusion/datasource/listing/struct.ListingTable.html#features) has long been supported, it is now possible to write to such tables as well.

For example, to write to a local file:  

    ❯ CREATE EXTERNAL TABLE awesome_table(x INT) STORED AS PARQUET LOCATION '/tmp/my_awesome_table';
    0 rows in set. Query took 0.003 seconds.

    ❯ INSERT INTO awesome_table SELECT x * 10 FROM my_source_table;
    +-------+
    | count |
    +-------+
    | 3     |
    +-------+
    1 row in set. Query took 0.024 seconds.
You can also write to files with the [`COPY`](https://arrow.apache.org/datafusion/user-guide/sql/dml.html#copy), similarly to [DuckDB’s `COPY`](https://duckdb.org/docs/sql/statements/copy.html):  

    ❯ COPY (SELECT x + 1 FROM my_source_table) TO '/tmp/output.json';
    +-------+
    | count |
    +-------+
    | 3     |
    +-------+
    1 row in set. Query took 0.014 seconds.
    $ cat /tmp/output.json
    {"x":1}
    {"x":2}
    {"x":3}
## Improved `STRUCT` and `ARRAY` support

DataFusion `34.0.0` has much improved `STRUCT` and `ARRAY` support, including a full range of [struct functions](https://arrow.apache.org/datafusion/user-guide/sql/scalar_functions.html#struct-functions) and [array functions](https://arrow.apache.org/datafusion/user-guide/sql/scalar_functions.html#array-functions).

For example, you can now use `[]` syntax and `array_length` to access and inspect arrays:  

    ❯ SELECT column1, 
             column1[1] AS first_element, 
             array_length(column1) AS len 
      FROM my_table;
    +-----------+---------------+-----+
    | column1   | first_element | len |
    +-----------+---------------+-----+
    | [1, 2, 3] | 1             | 3   |
    | [2]       | 2             | 1   |
    | [4, 5]    | 4             | 2   |
    +-----------+---------------+-----+
    ❯ SELECT column1, column1['c0'] FROM  my_table;
    +------------------+----------------------+
    | column1          | my_table.column1[c0] |
    +------------------+----------------------+
    | {c0: foo, c1: 1} | foo                  |
    | {c0: bar, c1: 2} | bar                  |
    +------------------+----------------------+
    2 rows in set. Query took 0.002 seconds.
## Other Features

Other notable features include:

* Support aggregating datasets that exceed memory size, with [group by spill to disk](https://github.com/apache/arrow-datafusion/pull/7400)
* All operators now track and limit their memory consumption, including Joins

# Building Systems is Easier with DataFusion 🛠️

## Documentation

It is easier than ever to get started using DataFusion with the new [Library Users Guide](https://arrow.apache.org/datafusion/library-user-guide/index.html) as well as significantly improved the [API documentation](https://docs.rs/datafusion/latest/datafusion/index.html).

## User Defined Window and Table Functions

In addition to DataFusion’s [User Defined Scalar Functions](https://arrow.apache.org/datafusion/library-user-guide/adding-udfs.html#adding-a-scalar-udf), and [User Defined Aggregate Functions](https://arrow.apache.org/datafusion/library-user-guide/adding-udfs.html#adding-an-aggregate-udf), DataFusion now supports [User Defined Window Functions](https://arrow.apache.org/datafusion/library-user-guide/adding-udfs.html#adding-a-window-udf) and [User Defined Table Functions](https://arrow.apache.org/datafusion/library-user-guide/adding-udfs.html#adding-a-user-defined-table-function).

For example, [the `datafusion-cli`](https://arrow.apache.org/datafusion/user-guide/cli.html) implements a DuckDB style [`parquet_metadata`](https://arrow.apache.org/datafusion/user-guide/cli.html#supported-sql) function as a user defined table function ([source code here](https://github.com/apache/arrow-datafusion/blob/3f219bc929cfd418b0e3d3501f8eba1d5a2c87ae/datafusion-cli/src/functions.rs#L222-L248)):  

    ❯ SELECT 
          path_in_schema, row_group_id, row_group_num_rows, stats_min, stats_max, total_compressed_size 
    FROM 
          parquet_metadata('hits.parquet')
    WHERE path_in_schema = '"WatchID"' 
    LIMIT 3;

    +----------------+--------------+--------------------+---------------------+---------------------+-----------------------+
    | path_in_schema | row_group_id | row_group_num_rows | stats_min           | stats_max           | total_compressed_size |
    +----------------+--------------+--------------------+---------------------+---------------------+-----------------------+
    | "WatchID"      | 0            | 450560             | 4611687214012840539 | 9223369186199968220 | 3883759               |
    | "WatchID"      | 1            | 612174             | 4611689135232456464 | 9223371478009085789 | 5176803               |
    | "WatchID"      | 2            | 344064             | 4611692774829951781 | 9223363791697310021 | 3031680               |
    +----------------+--------------+--------------------+---------------------+---------------------+-----------------------+
    3 rows in set. Query took 0.053 seconds.
### Growth of DataFusion 📈

DataFusion has been appearing more publically in the wild. For example

* New projects built using DataFusion such as [lancedb](https://lancedb.com/), [GlareDB](https://glaredb.com/), [Arroyo](https://www.arroyo.dev/), and [optd](https://github.com/cmu-db/optd).
* Public talks such as [Apache Arrow Datafusion: Vectorized Execution Framework For Maximum Performance](https://www.youtube.com/watch?v=AJU9rdRNk9I) in [CommunityOverCode Asia 2023](https://www.bagevent.com/event/8432178)
* Blogs posts such as [Apache Arrow, Arrow/DataFusion, AI-native Data Infra](https://www.synnada.ai/blog/apache-arrow-arrow-datafusion-ai-native-data-infra-an-interview-with-our-ceo-ozan), [Flight, DataFusion, Arrow, and Parquet: Using the FDAP Architecture to build InfluxDB 3.0](https://www.influxdata.com/blog/flight-datafusion-arrow-parquet-fdap-architecture-influxdb/), and [A Guide to User-Defined Functions in Apache Arrow DataFusion](https://www.linkedin.com/pulse/guide-user-defined-functions-apache-arrow-datafusion-dade-aderemi/)

We have also [submitted a paper](https://github.com/apache/arrow-datafusion/issues/6782) to [SIGMOD 2024](https://2024.sigmod.org/), one of the premiere database conferences, describing DataFusion in a technically formal style and making the case that it is possible to create a modular and extensive query engine without sacrificing performance. We hope this paper helps people evaluating DataFusion for their needs understand it better.

# DataFusion in 2024 🥳

Some major initiatives from contributors we know of this year are:

1. *Modularity* : Make DataFusion even more modular, such as [unifying built in and user functions](https://github.com/apache/arrow-datafusion/issues/8045), making it easier to customize DataFusion’s behavior.

2. *Community Growth*: Graduate to our own top level Apache project, and subsequently add more committers and PMC members to keep pace with project growth.

3. *Use case white papers*: Write blog posts and videos explaining how to use DataFusion for real-world use cases.

4. *Testing*: Improve CI infrastructure and test coverage, more fuzz testing, and better functional and performance regression testing.

5. *Planning Time* : Reduce the time taken to plan queries, both [wide tables of 1000s of columns](https://github.com/apache/arrow-datafusion/issues/7698), and in [general](https://github.com/apache/arrow-datafusion/issues/5637).

6. *Aggregate Performance* : Improve the speed of [aggregating “high cardinality”](https://github.com/apache/arrow-datafusion/issues/7000) data when there are many (e.g. millions) of distinct groups.

7. *Statistics* : [Improved statistics handling](https://github.com/apache/arrow-datafusion/issues/8227) with an eye towards more sophisticated expression analysis and cost models.

# How to Get Involved

If you are interested in contributing to DataFusion we would love to have you join us. You can try out DataFusion on some of your own data and projects and let us know how it goes, contribute suggestions, documentation, bug reports, or a PR with documentation, tests or code. A list of open issues suitable for beginners is [here](https://github.com/apache/arrow-datafusion/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22).

As the community grows, we are also looking to restart biweekly calls / meetings. Timezones are always a challenge for such meetings, but we hope to have two calls that can work for most attendees. If you are interested in helping, or just want to say hi, please drop us a note via one of the methods listed in our [Communication Doc](https://arrow.apache.org/datafusion/contributor-guide/communication.html).
