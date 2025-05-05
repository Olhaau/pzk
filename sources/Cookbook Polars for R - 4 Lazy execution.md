---
title: Cookbook Polars for R - 4 Lazy execution
---

# Cookbook Polars for R - 4 Lazy execution

url:: https://ddotta.github.io/cookbook-rpolars/lazy_execution.html
up: [[sources]]

#source

Below is the code used to create the fake data needed to compare the `eager` and `lazy` modes in this document.  
Click to expand it! 👇  
Code  

    #cb1-1library(polars)
    #cb1-2library(arrow)
    #cb1-3library(dplyr)
    #cb1-4library(fakir)
    #cb1-5library(tictoc)
    #cb1-6
    #cb1-7# Creation the "Datasets" folder
    #cb1-8dir.create(normalizePath("Datasets"))
    #cb1-9
    #cb1-10# Creation of large example R data.frame
    #cb1-11fake_data <- fake_ticket_client(vol = 100000)
    #cb1-12
    #cb1-13# Creation of large example csv dataset
    #cb1-14write.csv(
    #cb1-15  x = fake_data,
    #cb1-16  file = normalizePath("Datasets/fakir_file.csv"))
    #cb1-17
    #cb1-18# Creation of large example parquet dataset
    #cb1-19write_parquet(
    #cb1-20  x = fake_data,
    #cb1-21  sink = normalizePath("Datasets/fakir_file.parquet"))
This chapter only deals with the **lazy execution** of Polars. It does not include a comparison with R base, dplyr and data.table

## Introduction to lazy mode

Polars supports two modes of operation: **lazy** and **eager**.

Let’s start this chapter by citing the official documentation:
> In the eager API the query is executed immediately while in the lazy API the query is only evaluated once it is ‘needed’. Deferring the execution to the last minute can have significant performance advantages that is why the Lazy API is preferred in most cases. Delaying execution until the last possible moment allows Polars to apply automatic optimization to every query.

As you can see, with **lazy mode** , you give the engine the chance to analyse what you want to do in order to propose optimal execution (for both reading and transforming datasets). **Lazy evaluation** is a fairly common method of improving processing speed and is used by [Spark](https://spark.apache.org/docs/latest/index.html), among others.

So far in this book, we have only used the **eager mode** but fortunately all the syntax we’ve seen applies to **lazy mode** too. Whatever mode is used, queries will be executed transparently for users.

### Creation of a `LazyFrame` with lazy()

To convert a `DataFrame` to a `LazyFrame` we can use the `lazy()` contructor.  

    #cb2-1pl$DataFrame(iris)$lazy()

    polars LazyFrame
     $describe_optimized_plan() : Show the optimized query plan.

    Naive plan:
    DF ["Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width"]; PROJECT */5 COLUMNS; SELECTION: "None"
We are no longer working on a `DataFrame` but on a `LazyFrame`.

### First query passed on `LazyFrame`

Let’s look at what happens when we request this `LazyFrame`:  

    #cb4-1myquery <- pl$DataFrame(iris)$lazy()$filter(
    #cb4-2  pl$col("Species") == "setosa"
    #cb4-3)$select(
    #cb4-4  pl$col(c("Species", "Petal.Length"))
    #cb4-5)
    #cb4-6myquery
    polars LazyFrame
     $describe_optimized_plan() : Show the optimized query plan.

    Naive plan:
     SELECT [col("Species"), col("Petal.Length")] FROM
      FILTER [(col("Species")) == (String(setosa))] FROM

      DF ["Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width"]; PROJECT */5 COLUMNS; SELECTION: "None"
This way, we can display the **naive plan** (which means it is an non-optimized plan). Let’s see what it contains for our example:

* `FILTER [(col("Species")) == (Utf8(setosa))] FROM DF ["Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width"]` means that **once the entire datasets has been read into memory** , this `DataFrame` will be filtered for rows with Species equals to “setosa”;  
* `PROJECT */5 COLUMNS` selects all 5 of the columns (* is the wildcard meaning all);  
* `SELECTION: "None"` means no rows will be filtered out.

As indicated in the console, we can use the `describe_optimized_plan()` method to see the **optimized plan**.  

    #cb6-1myquery$describe_optimized_plan()

    DF ["Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width"]; PROJECT 2/5 COLUMNS; SELECTION: "[(col(\"Species\")) == (String(setosa))]"

This example shows a simple but surprisingly effective element of query optimisation: **projection**.

Let’s see what changed in this **optimized plan**:

* `PROJECT 2/5 COLUMNS` selects only 2 columns;  
* `SELECTION: "[(col(\"Species\")) == (Utf8(setosa))]` means that Polars will apply the filter conditions on the `Species` column **as the datasets is being read.**

We can see that Polars has identified that only 2 columns are needed to execute our query which is **memory efficient** ! And Polars did this without me having to select these variables myself (for example with a `select` method).  
**The added value of Polars is that it applies some optimizations that I/you might not even have known about. 💪**

### Execute the plan

To actually execute the plan, we just need to invoke the **collect()** method.  

    shape: (50, 2)
    ┌─────────┬──────────────┐
    │ Species ┆ Petal.Length │
    │ ---     ┆ ---          │
    │ cat     ┆ f64          │
    ╞═════════╪══════════════╡
    │ setosa  ┆ 1.4          │
    │ setosa  ┆ 1.4          │
    │ setosa  ┆ 1.3          │
    │ setosa  ┆ 1.5          │
    │ setosa  ┆ 1.4          │
    │ …       ┆ …            │
    │ setosa  ┆ 1.4          │
    │ setosa  ┆ 1.6          │
    │ setosa  ┆ 1.4          │
    │ setosa  ┆ 1.5          │
    │ setosa  ┆ 1.4          │
    └─────────┴──────────────┘
## `Lazy` vs `eager` mode comparison

### General principles

In this first example we use the **eager API**:  

    #cb10-1df <- pl$read_csv("examples/iris.csv")
    #cb10-2df_small = df$filter(pl$col("Petal.Length") > 5)
    #cb10-3df_agg = df_small$group_by("Species")$agg(pl$col("Petal.Width")$median())
    #cb10-4df_agg
    shape: (2, 2)
    ┌────────────┬─────────────┐
    │ Species    ┆ Petal.Width │
    │ ---        ┆ ---         │
    │ str        ┆ f64         │
    ╞════════════╪═════════════╡
    │ virginica  ┆ 2.1         │
    │ versicolor ┆ 1.6         │
    └────────────┴─────────────┘
This example:

* Read the iris dataset.
* Filter the dataset based on Petal.Length
* Calculate the median of the Petal.Width per Species

**Every step is executed immediately returning the intermediate results** . This can be very **wastefull** as we might do work or load extra data that is not being used. If we instead used the **lazy API** and waited on execution untill all the steps are defined then the query planner could perform various optimizations. In this case:

* `Predicate pushdown`: Apply filters as early as possible while reading the dataset, thus only reading rows with sepal length greater than 5.
* `Projection pushdown`: Select only the columns that are needed while reading the dataset, thus removing the need to load additional columns

To consult the list of **optimisations** made by `Polars` on queries in **lazy mode** , see [this page](https://pola-rs.github.io/polars-book/user-guide/lazy/optimizations/)..

Here is the equivalent code using the lazy API. At the end of the query, don’t forget to use the `collect()` method to inform Polars that you want to execute it.  

    #cb12-1pl$scan_csv("examples/iris.csv")$
    #cb12-2  filter(
    #cb12-3    pl$col("Petal.Length") > 5)$
    #cb12-4  group_by("Species")$
    #cb12-5  agg(pl$col("Petal.Width")$median())$
    #cb12-6  collect() # <- don't forget collect() here!
    shape: (2, 2)
    ┌────────────┬─────────────┐
    │ Species    ┆ Petal.Width │
    │ ---        ┆ ---         │
    │ str        ┆ f64         │
    ╞════════════╪═════════════╡
    │ versicolor ┆ 1.6         │
    │ virginica  ┆ 2.1         │
    └────────────┴─────────────┘
Use **lazy execution** will signficantly lower the load on memory & CPU thus allowing you to fit bigger datasets in memory and process faster.

The next section will demonstrate this time saving. 👇

### Limits of lazy mode

There are some operations that cannot be performed in lazy mode (whether in polars or other lazy frameworks such as SQL database). One limitation is that Polars needs to know the column names and dtypes at each step of the query plan.

For example, we can’t `pivot()` (see [here](https://ddotta.github.io/cookbook-rpolars/data_manipulation.html#pivot-a-dataframe)) in lazy mode as the column names are data-dependant following a pivot. Indeed, when you have to `pivot()` a `DataFrame` your future columns names cannot be predicted because it depends on what it is actually in your datasets!

When you have to do operations that can be done in lazy mode, the recommandation is: - Running your query in lazy mode as far as possible; - Evaluating this lazy query with `collect()` when you need a non-lazy method; - Running the non-lazy methods; - Calling `lazy()` on the output to continue in lazy mode.

Here’s an example:  

    #cb14-1pl$scan_parquet("Datasets/fakir_file.parquet")$
    #cb14-2  # Call collect() because I need to pivot()
    #cb14-3  collect()$
    #cb14-4  pivot(
    #cb14-5    index = "region",
    #cb14-6    columns = "priority",
    #cb14-7    values = "age",
    #cb14-8    aggregate_function = "mean"
    #cb14-9  )$
    #cb14-10  # Continue in lazy mode
    #cb14-11  lazy()$
    #cb14-12  select(
    #cb14-13    pl$col(c("region","Gold","Platinium"))
    #cb14-14  )$
    #cb14-15  collect()
    shape: (21, 3)
    ┌────────────────────────────┬───────────┬───────────┐
    │ region                     ┆ Gold      ┆ Platinium │
    │ ---                        ┆ ---       ┆ ---       │
    │ str                        ┆ f64       ┆ f64       │
    ╞════════════════════════════╪═══════════╪═══════════╡
    │ Île-de-France              ┆ 71.209059 ┆ null      │
    │ Poitou-Charentes           ┆ 60.0      ┆ 73.172524 │
    │ Aquitaine                  ┆ null      ┆ null      │
    │ Centre                     ┆ null      ┆ null      │
    │ Midi-Pyrénées              ┆ 54.445931 ┆ null      │
    │ …                          ┆ …         ┆ …         │
    │ Lorraine                   ┆ null      ┆ 74.0      │
    │ Provence-Alpes-Côte d'Azur ┆ 66.0      ┆ null      │
    │ Alsace                     ┆ 40.0      ┆ null      │
    │ Bourgogne                  ┆ null      ┆ null      │
    │ Haute-Normandie            ┆ null      ┆ null      │
    └────────────────────────────┴───────────┴───────────┘
## `Lazy` vs `eager` mode : fight! ⚔️

For this fight, we’re going to use a fake dataset with 1 000 000 rows and 25 columns created with the {fakir} package. The code for creating this dataset is available at the beginning of this document.

This fight will take place over 3 rounds :

1. With an eager query versus a lazy query **from a DataFrame**
2. With an eager query versus a lazy query **from a csv file**
3. With an eager query versus a lazy query **from a parquet file**

### From a DataFrame

For this first round and as seen above, let’s start with a simple query from a `DataFrame`:  

    #cb16-1tic()
    #cb16-2#| label: fight-eager_dataframe
    #cb16-3pl$DataFrame(fake_data)$select(
    #cb16-4    pl$col(c("region","departement","priority"))
    #cb16-5  )$
    #cb16-6  filter(
    #cb16-7    pl$col("region") == "Aquitaine")
    shape: (5_862, 3)
    ┌───────────┬─────────────┬──────────┐
    │ region    ┆ departement ┆ priority │
    │ ---       ┆ ---         ┆ ---      │
    │ str       ┆ str         ┆ cat      │
    ╞═══════════╪═════════════╪══════════╡
    │ Aquitaine ┆ Dordogne    ┆ Silver   │
    │ Aquitaine ┆ Dordogne    ┆ Silver   │
    │ Aquitaine ┆ Dordogne    ┆ Silver   │
    │ Aquitaine ┆ Dordogne    ┆ Silver   │
    │ Aquitaine ┆ Dordogne    ┆ Silver   │
    │ …         ┆ …           ┆ …        │
    │ Aquitaine ┆ Dordogne    ┆ Bronze   │
    │ Aquitaine ┆ Dordogne    ┆ Bronze   │
    │ Aquitaine ┆ Dordogne    ┆ Bronze   │
    │ Aquitaine ┆ Landes      ┆ Silver   │
    │ Aquitaine ┆ Dordogne    ┆ Bronze   │
    └───────────┴─────────────┴──────────┘
As seen above, we’re going to use the `lazy()` method to convert a `DataFrame` to a `LazyFrame`:  

    #cb20-1tic()
    #cb20-2#| label: fight-lazy_lazyframe
    #cb20-3pl$DataFrame(fake_data)$lazy()$
    #cb20-4  select(
    #cb20-5    pl$col(c("region","departement","priority"))
    #cb20-6  )$
    #cb20-7  filter(
    #cb20-8    pl$col("region") == "Aquitaine")$
    #cb20-9  collect() # don't forget collect() here!
    shape: (5_862, 3)
    ┌───────────┬─────────────┬──────────┐
    │ region    ┆ departement ┆ priority │
    │ ---       ┆ ---         ┆ ---      │
    │ str       ┆ str         ┆ cat      │
    ╞═══════════╪═════════════╪══════════╡
    │ Aquitaine ┆ Dordogne    ┆ Silver   │
    │ Aquitaine ┆ Dordogne    ┆ Silver   │
    │ Aquitaine ┆ Dordogne    ┆ Silver   │
    │ Aquitaine ┆ Dordogne    ┆ Silver   │
    │ Aquitaine ┆ Dordogne    ┆ Silver   │
    │ …         ┆ …           ┆ …        │
    │ Aquitaine ┆ Dordogne    ┆ Bronze   │
    │ Aquitaine ┆ Dordogne    ┆ Bronze   │
    │ Aquitaine ┆ Dordogne    ┆ Bronze   │
    │ Aquitaine ┆ Landes      ┆ Silver   │
    │ Aquitaine ┆ Dordogne    ┆ Bronze   │
    └───────────┴─────────────┴──────────┘
### From a csv file

Now, the `eager` mode is represented here by the `read_csv()` method…  

    #cb24-1tic()
    #cb24-2#| label: fight-eager_read_csv
    #cb24-3pl$read_csv("Datasets/fakir_file.csv", infer_schema_length=0)$
    #cb24-4  select(
    #cb24-5    pl$col(c("region","departement","priority","age")))$
    #cb24-6  with_columns(
    #cb24-7    pl$col("age")$cast(pl$Int32,strict = FALSE))$
    #cb24-8  filter(
    #cb24-9    pl$col("region") == "Bretagne")$
    #cb24-10  group_by("departement","priority")$
    #cb24-11  agg(pl$col("age")$mean())
    shape: (4, 3)
    ┌───────────────┬──────────┬───────────┐
    │ departement   ┆ priority ┆ age       │
    │ ---           ┆ ---      ┆ ---       │
    │ str           ┆ str      ┆ f64       │
    ╞═══════════════╪══════════╪═══════════╡
    │ Morbihan      ┆ Bronze   ┆ 18.832957 │
    │ Côtes-d'Armor ┆ Bronze   ┆ 23.0      │
    │ Côtes-d'Armor ┆ Silver   ┆ 61.0      │
    │ Finistère     ┆ Silver   ┆ 23.0      │
    └───────────────┴──────────┴───────────┘
… while the `lazy` method is represented by the `pl$scan_csv()`:  

    #cb28-1tic()
    #cb28-2pl$scan_csv("Datasets/fakir_file.csv", infer_schema_length=0)$
    #cb28-3  select(
    #cb28-4    pl$col(c("region","departement","priority","age")))$
    #cb28-5  with_columns(
    #cb28-6    pl$col("age")$cast(pl$Int32,strict = FALSE))$
    #cb28-7  filter(
    #cb28-8    pl$col("region") == "Bretagne")$
    #cb28-9  group_by("departement","priority")$
    #cb28-10  agg(pl$col("age")$mean())$
    #cb28-11  collect()
    shape: (4, 3)
    ┌───────────────┬──────────┬───────────┐
    │ departement   ┆ priority ┆ age       │
    │ ---           ┆ ---      ┆ ---       │
    │ str           ┆ str      ┆ f64       │
    ╞═══════════════╪══════════╪═══════════╡
    │ Côtes-d'Armor ┆ Silver   ┆ 61.0      │
    │ Côtes-d'Armor ┆ Bronze   ┆ 23.0      │
    │ Morbihan      ┆ Bronze   ┆ 18.832957 │
    │ Finistère     ┆ Silver   ┆ 23.0      │
    └───────────────┴──────────┴───────────┘
We can clearly see that we save a lot of time when executing the `lazy` version of the code!

### From a parquet file

The `read_parquet()` method has not been implemented in the R Polars package, but for this fight we will use `arrow::read_parquet()` and {dplyr} syntax, which will compete with `pl$scan_parquet()`.  

    #cb32-1tic()
    #cb32-2arrow::read_parquet("Datasets/fakir_file.parquet", as_data_frame = FALSE) |>
    #cb32-3  filter(region == "Bretagne") |>
    #cb32-4  group_by(departement,priority) |>
    #cb32-5  summarise(mymean=mean(age, na.rm = TRUE)) |>
    #cb32-6  arrange(departement) |>
    #cb32-7  collect()
    # A tibble: 4 × 3
    # Groups:   departement [3]
      departement   priority mymean
      <chr>         <fct>     <dbl>
    1 Côtes-d'Armor Bronze     23  
    2 Côtes-d'Armor Silver     61  
    3 Finistère     Silver     23  
    4 Morbihan      Bronze     18.8
    #cb36-1tic()
    #cb36-2pl$scan_parquet("Datasets/fakir_file.parquet")$
    #cb36-3  filter(
    #cb36-4    pl$col("region") == "Bretagne")$
    #cb36-5  group_by(c("departement","priority"))$
    #cb36-6  agg(
    #cb36-7    pl$col(c("age"))$mean()
    #cb36-8)$sort("departement")$
    #cb36-9  collect()
    shape: (4, 3)
    ┌───────────────┬──────────┬───────────┐
    │ departement   ┆ priority ┆ age       │
    │ ---           ┆ ---      ┆ ---       │
    │ str           ┆ cat      ┆ f64       │
    ╞═══════════════╪══════════╪═══════════╡
    │ Côtes-d'Armor ┆ Silver   ┆ 61.0      │
    │ Côtes-d'Armor ┆ Bronze   ┆ 23.0      │
    │ Finistère     ┆ Silver   ┆ 23.0      │
    │ Morbihan      ┆ Bronze   ┆ 18.832957 │
    └───────────────┴──────────┴───────────┘
And it’s another victory for the `lazy` execution!  
Note that the {arrow} package also have ability to scan parquet files in a lazy way with the `arrow::open_dataset` function.  

    #cb40-1tic()
    #cb40-2arrow::open_dataset("Datasets/fakir_file.parquet") |>
    #cb40-3  filter(region == "Bretagne") |>
    #cb40-4  group_by(departement,priority) |>
    #cb40-5  summarise(mymean=mean(age, na.rm = TRUE)) |>
    #cb40-6  arrange(departement) |>
    #cb40-7  collect()
    # A tibble: 4 × 3
    # Groups:   departement [3]
      departement   priority mymean
      <chr>         <fct>     <dbl>
    1 Côtes-d'Armor Bronze     23  
    2 Côtes-d'Armor Silver     61  
    3 Finistère     Silver     23  
    4 Morbihan      Bronze     18.8


![[/attachments/Attachment-20250502.jpg]]