---
title: Working with multi-file data sets • Arrow R Package
keywords: source
---

# Working with multi-file data sets • Arrow R Package


url:: https://arrow.apache.org/docs/r/articles/dataset.html
up: [[sources]]

Apache Arrow lets you work efficiently with single and multi-file data sets even when that data set is too large to be loaded into memory. With the help of Arrow Dataset objects you can analyze this kind of data using familiar [dplyr](https://dplyr.tidyverse.org/) syntax. This article introduces Datasets and shows you how to analyze them with dplyr and arrow: we’ll start by ensuring both packages are loaded  

## Example: NYC taxi data

The primary motivation for Arrow’s Datasets object is to allow users to analyze extremely large datasets. As an example, consider the [New York City taxi trip record data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page) that is widely used in big data exercises and competitions. To demonstrate the capabilities of Apache Arrow we host a Parquet-formatted version this data in a public Amazon S3 bucket: in its full form, our version of the data set is one very large table with about 1.7 billion rows and 24 columns, where each row corresponds to a single taxi ride sometime between 2009 and 2022. A [data dictionary](https://arrow-user2022.netlify.app/packages-and-data.html#data) for this version of the NYC taxi data is also available.

This multi-file data set is comprised of 158 distinct Parquet files, each corresponding to a month of data. A single file is typically around 400-500MB in size, and the full data set is about 70GB in size. It is not a small data set – it is slow to download and does not fit in memory on a typical machine 🙂 – so we also host a “tiny” version of the NYC taxi data that is formatted in exactly the same way but includes only one out of every thousand entries in the original data set (i.e., individual files are <1MB in size, and the “tiny” data set is only 70MB)

If you have Amazon S3 support enabled in arrow (true for most users; see links at the end of this article if you need to troubleshoot this), you can connect to a copy of the “tiny taxi data” stored on S3 with this command:  

    bucket <- https://arrow.apache.org/docs/r/articles/../reference/s3_bucket.html("voltrondata-labs-datasets/nyc-taxi-tiny")

Alternatively you could connect to a copy of the data on Google Cloud Storage (GCS) using the following command:  

    bucket <- https://arrow.apache.org/docs/r/articles/../reference/gs_bucket.html("voltrondata-labs-datasets/nyc-taxi-tiny", anonymous = TRUE)

If you want to use the full data set, replace `nyc-taxi-tiny` with `nyc-taxi` in the code above. Apart from size – and with it the cost in time, bandwidth usage, and CPU cycles – there is no difference in the two versions of the data: you can test your code using the tiny taxi data and then check how it scales using the full data set.

To make a local copy of the data set stored in the `bucket` to a folder called `"nyc-taxi"`, use the [copy_files()](https://arrow.apache.org/docs/r/articles/../reference/copy_files.html) function:

For the purposes of this article, we assume that the NYC taxi dataset (either the full data or the tiny version) has been downloaded locally and exists in an `"nyc-taxi"` directory.  

## Opening Datasets

The first step in the process is to create a Dataset object that points at the data directory:

It is important to note that when we do this, the data values are not loaded into memory. Instead, Arrow scans the data directory to find relevant files, parses the file paths looking for a “Hive-style partitioning” (see below), and reads headers of the data files to construct a Schema that contains metadata describing the structure of the data. For more information about Schemas see the [metadata article](https://arrow.apache.org/docs/r/articles/metadata.html).

Two questions naturally follow from this: what kind of files does [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) look for, and what structure does it expect to find in the file paths? Let’s start by looking at the file types.

By default [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) looks for Parquet files but you can override this using the `format` argument. For example if the data were encoded as CSV files we could set `format = "csv"` to connect to the data. The Arrow Dataset interface supports several file formats including:

* `"parquet"` (the default)
* `"feather"` or `"ipc"` (aliases for `"arrow"`; as Feather version 2 is the Arrow file format)
* `"csv"` (comma-delimited files) and `"tsv"` (tab-delimited files)
* `"text"` (generic text-delimited files - use the `delimiter` argument to specify which to use)

In the case of text files, you can pass the following parsing options to [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) to ensure that files are read correctly:

* `delim`
* `quote`
* `escape_double`
* `escape_backslash`
* `skip_empty_rows`

An alternative when working with text files is to use [open_delim_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_delim_dataset.html), [open_csv_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_delim_dataset.html), or [open_tsv_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_delim_dataset.html). These functions are wrappers around [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) but with parameters that mirror [read_csv_arrow()](https://arrow.apache.org/docs/r/articles/../reference/read_delim_arrow.html), [read_delim_arrow()](https://arrow.apache.org/docs/r/articles/../reference/read_delim_arrow.html), and [read_tsv_arrow()](https://arrow.apache.org/docs/r/articles/../reference/read_delim_arrow.html) to allow for easy switching between functions for opening single files and functions for opening datasets.

For example:

For more information on these arguments and on parsing delimited text files generally, see the help documentation for [read_delim_arrow()](https://arrow.apache.org/docs/r/articles/../reference/read_delim_arrow.html) and [open_delim_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_delim_dataset.html).

Next, what information does [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) expect to find in the file paths? By default, the Dataset interface looks for [Hive](https://hive.apache.org/)-style partitioning structure in which folders are named using a “key=value” convention, and data files in a folder contain the subset of the data for which the key has the relevant value. For example, in the NYC taxi data file paths look like this:

    year=2009/month=1/part-0.parquet
    year=2009/month=2/part-0.parquet
    ...
From this, [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) infers that the first listed Parquet file contains the data for January 2009. In that sense, a hive-style partitioning is self-describing: the folder names state explicitly how the Dataset has been split across files.

Sometimes the directory partitioning isn’t self describing; that is, it doesn’t contain field names. For example, suppose the NYC taxi data used file paths like these:

    2009/01/part-0.parquet
    2009/02/part-0.parquet
    ...
In that case, [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) would need some hints as to how to use the file paths. In this case, you could provide `c("year", "month")` to the `partitioning` argument, saying that the first path segment gives the value for `year`, and the second segment is `month`. Every row in `2009/01/part-0.parquet` has a value of 2009 for `year` and 1 for `month`, even though those columns may not be present in the file. In other words, we would open the data like this:  

    ds <- https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html("nyc-taxi", partitioning = https://rdrr.io/r/base/c.html("year", "month"))

Either way, when you look at the Dataset, you can see that in addition to the columns present in every file, there are also columns `year` and `month`. These columns are not present in the files themselves: they are inferred from the partitioning structure.

    ## 
    ## FileSystemDataset with 158 Parquet files
    ## vendor_name: string
    ## pickup_datetime: timestamp[ms]
    ## dropoff_datetime: timestamp[ms]
    ## passenger_count: int64
    ## trip_distance: double
    ## pickup_longitude: double
    ## pickup_latitude: double
    ## rate_code: string
    ## store_and_fwd: string
    ## dropoff_longitude: double
    ## dropoff_latitude: double
    ## payment_type: string
    ## fare_amount: double
    ## extra: double
    ## mta_tax: double
    ## tip_amount: double
    ## tolls_amount: double
    ## total_amount: double
    ## improvement_surcharge: double
    ## congestion_surcharge: double
    ## pickup_location_id: int64
    ## dropoff_location_id: int64
    ## year: int32
    ## month: int32
## Querying Datasets

Now that we have a Dataset object that refers to our data, we can construct dplyr-style queries. This is possible because arrow supplies a back end that allows users to manipulate tabular Arrow data using dplyr verbs. Here’s an example: suppose you are curious about tipping behavior in the longest taxi rides. Let’s find the median tip percentage for rides with fares greater than $100 in 2015, broken down by the number of passengers:

    ## 
    ## # A tibble: 10 x 3
    ##    passenger_count median_tip_pct      n
    ##              <int>          <dbl>  <int>
    ##  1               1           16.6 143087
    ##  2               2           16.2  34418
    ##  3               5           16.7   5806
    ##  4               4           11.4   4771
    ##  5               6           16.7   3338
    ##  6               3           14.6   8922
    ##  7               0           10.1    380
    ##  8               8           16.7     32
    ##  9               9           16.7     42
    ## 10               7           16.7     11
    ## 
    ##    user  system elapsed
    ##   4.436   1.012   1.402
You’ve just selected a subset from a Dataset that contains around 2 billion rows, computed a new column, and aggregated it. All within a few seconds on a modern laptop. How does this work?

There are three reasons arrow can accomplish this task so quickly:

First, arrow adopts a lazy evaluation approach to queries: when dplyr verbs are called on the Dataset, they record their actions but do not evaluate those actions on the data until you run [collect()](https://dplyr.tidyverse.org/reference/compute.html). We can see this by taking the same code as before and leaving off the final step:

    ## 
    ## FileSystemDataset (query)
    ## passenger_count: int64
    ## median_tip_pct: double
    ## n: int32
    ## 
    ## See $.data for the source Arrow object
This version of the code returns an output instantly and shows the manipulations you’ve made, without loading data from the files. Because the evaluation of these queries is deferred, you can build up a query that selects down to a small subset without generating intermediate data sets that could potentially be large.

Second, all work is pushed down to the individual data files, and depending on the file format, chunks of data within files. As a result, you can select a subset of data from a much larger data set by collecting the smaller slices from each file: you don’t have to load the whole data set in memory to slice from it.

Third, because of partitioning, you can ignore some files entirely. In this example, by filtering `year == 2015`, all files corresponding to other years are immediately excluded: you don’t have to load them in order to find that no rows match the filter. For Parquet files – which contain row groups with statistics on the data contained within groups – there may be entire chunks of data you can avoid scanning because they have no rows where `total_amount > 100`.

One final thing to note about querying Datasets. Suppose you attempt to call unsupported dplyr verbs or unimplemented functions in your query on an Arrow Dataset. In that case, the arrow package raises an error. However, for dplyr queries on Arrow Table objects (which are already in-memory), the package automatically calls [collect()](https://dplyr.tidyverse.org/reference/compute.html) before processing that dplyr verb. To learn more about the dplyr back end, see the [data wrangling article](https://arrow.apache.org/docs/r/articles/data_wrangling.html).  

## Batch processing (experimental)

Sometimes you want to run R code on the entire Dataset, but that Dataset is much larger than memory. You can use `map_batches` on a Dataset query to process it batch-by-batch.

**Note** : `map_batches` is experimental and not recommended for production use.

As an example, to randomly sample a Dataset, use `map_batches` to sample a percentage of rows from each batch:

    ## 
    ## tibble [10,918 <U+00D7> 4] (S3: tbl_df/tbl/data.frame)
    ##  $ tip_amount     : num [1:10918] 3 0 4 1 1 6 0 1.35 0 5.9 ...
    ##  $ total_amount   : num [1:10918] 18.8 13.3 20.3 15.8 13.3 ...
    ##  $ passenger_count: int [1:10918] 3 2 1 1 1 1 1 1 1 3 ...
    ##  $ tip_pct        : num [1:10918] 0.1596 0 0.197 0.0633 0.0752 ...
This function can also be used to aggregate summary statistics over a Dataset by computing partial results for each batch and then aggregating those partial results. Extending the example above, you could fit a model to the sample data and then use `map_batches` to compute the MSE on the full Dataset.

    ## 
    ## [1] 0.1304284
## Dataset options

There are a few ways you can control the Dataset creation to adapt to special use cases.  

### Work with files in a directory

If you are working with a single file or a set of files that are not all in the same directory, you can provide a file path or a vector of multiple file paths to [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html). This is useful if, for example, you have a single CSV file that is too big to read into memory. You could pass the file path to [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html), use [group_by()](https://dplyr.tidyverse.org/reference/group_by.html) to partition the Dataset into manageable chunks, then use [write_dataset()](https://arrow.apache.org/docs/r/articles/../reference/write_dataset.html) to write each chunk to a separate Parquet file—all without needing to read the full CSV file into R.  

### Explicitly declare column names and data types

You can specify the `schema` argument to [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) to declare the columns and their data types. This is useful if you have data files that have different storage schema (for example, a column could be `int32` in one and `int8` in another) and you want to ensure that the resulting Dataset has a specific type.

To be clear, it’s not necessary to specify a schema, even in this example of mixed integer types, because the Dataset constructor will reconcile differences like these. The schema specification just lets you declare what you want the result to be.  

### Explicitly declare partition format

Similarly, you can provide a Schema in the `partitioning` argument of [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) in order to declare the types of the virtual columns that define the partitions. This would be useful, in the NYC taxi data example, if you wanted to keep `month` as a string instead of an integer.  

### Work with multiple data sources

Another feature of Datasets is that they can be composed of multiple data sources. That is, you may have a directory of partitioned Parquet files in one location, and in another directory, files that haven’t been partitioned. Or, you could point to an S3 bucket of Parquet data and a directory of CSVs on the local file system and query them together as a single Dataset. To create a multi-source Dataset, provide a list of Datasets to [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) instead of a file path, or concatenate them with a command like `big_dataset <- c(ds1, ds2)`.  

## Writing Datasets

As you can see, querying a large Dataset can be made quite fast by storage in an efficient binary columnar format like Parquet or Feather and partitioning based on columns commonly used for filtering. However, data isn’t always stored that way. Sometimes you might start with one giant CSV. The first step in analyzing data is cleaning is up and reshaping it into a more usable form.

The [write_dataset()](https://arrow.apache.org/docs/r/articles/../reference/write_dataset.html) function allows you to take a Dataset or another tabular data object—an Arrow Table or RecordBatch, or an R data frame—and write it to a different file format, partitioned into multiple files.

Assume that you have a version of the NYC Taxi data as CSV:

You can write it to a new location and translate the files to the Feather format by calling [write_dataset()](https://arrow.apache.org/docs/r/articles/../reference/write_dataset.html) on it:

Next, let’s imagine that the `payment_type` column is something you often filter on, so you want to partition the data by that variable. By doing so you ensure that a filter like `payment_type == "Cash"` will touch only a subset of files where `payment_type` is always `"Cash"`.

One natural way to express the columns you want to partition on is to use the [group_by()](https://dplyr.tidyverse.org/reference/group_by.html) method:

This will write files to a directory tree that looks like this:  

    https://rdrr.io/r/base/system.html("tree nyc-taxi/feather")

    ## feather
    ## ├── payment_type=1
    ## │   └── part-18.arrow
    ## ├── payment_type=2
    ## │   └── part-19.arrow
    ## ...
    ## └── payment_type=UNK
    ##     └── part-17.arrow
    ##
    ## 18 directories, 23 files
Note that the directory names are `payment_type=Cash` and similar: this is the Hive-style partitioning described above. This means that when you call [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) on this directory, you don’t have to declare what the partitions are because they can be read from the file paths. (To instead write bare values for partition segments, i.e. `Cash` rather than `payment_type=Cash`, call [write_dataset()](https://arrow.apache.org/docs/r/articles/../reference/write_dataset.html) with `hive_style = FALSE`.)

Perhaps, though, `payment_type == "Cash"` is the only data you ever care about, and you just want to drop the rest and have a smaller working set. For this, you can [filter()](https://dplyr.tidyverse.org/reference/filter.html) them out when writing:

The other thing you can do when writing Datasets is select a subset of columns or reorder them. Suppose you never care about `vendor_id`, and being a string column, it can take up a lot of space when you read it in, so let’s drop it:

Note that while you can select a subset of columns, you cannot currently rename columns when writing a Dataset.  

## Partitioning performance considerations

Partitioning Datasets has two aspects that affect performance: it increases the number of files and it creates a directory structure around the files. Both of these have benefits as well as costs. Depending on the configuration and the size of your Dataset, the costs can outweigh the benefits.

Because partitions split up the Dataset into multiple files, partitioned Datasets can be read and written with parallelism. However, each additional file adds a little overhead in processing for filesystem interaction. It also increases the overall Dataset size since each file has some shared metadata. For example, each parquet file contains the schema and group-level statistics. The number of partitions is a floor for the number of files. If you partition a Dataset by date with a year of data, you will have at least 365 files. If you further partition by another dimension with 1,000 unique values, you will have up to 365,000 files. This fine of partitioning often leads to small files that mostly consist of metadata.

Partitioned Datasets create nested folder structures, and those allow us to prune which files are loaded in a scan. However, this adds overhead to discovering files in the Dataset, as we’ll need to recursively “list directory” to find the data files. Too fine partitions can cause problems here: Partitioning a dataset by date for a years worth of data will require 365 list calls to find all the files; adding another column with cardinality 1,000 will make that 365,365 calls.

The most optimal partitioning layout will depend on your data, access patterns, and which systems will be reading the data. Most systems, including Arrow, should work across a range of file sizes and partitioning layouts, but there are extremes you should avoid. These guidelines can help avoid some known worst cases:

* Avoid files smaller than 20MB and larger than 2GB.
* Avoid partitioning layouts with more than 10,000 distinct partitions.

For file formats that have a notion of groups within a file, such as Parquet, similar guidelines apply. Row groups can provide parallelism when reading and allow data skipping based on statistics, but very small groups can cause metadata to be a significant portion of file size. Arrow’s file writer provides sensible defaults for group sizing in most cases.  

## Transactions / ACID guarantees

The Dataset API offers no transaction support or any ACID guarantees. This affects both reading and writing. Concurrent reads are fine. Concurrent writes or writes concurring with reads may have unexpected behavior. Various approaches can be used to avoid operating on the same files such as using a unique basename template for each writer, a temporary directory for new files, or separate storage of the file list instead of relying on directory discovery.

Unexpectedly killing the process while a write is in progress can leave the system in an inconsistent state. Write calls generally return as soon as the bytes to be written have been completely delivered to the OS page cache. Even though a write operation has been completed it is possible for part of the file to be lost if there is a sudden power loss immediately after the write call.

Most file formats have magic numbers which are written at the end. This means a partial file write can safely be detected and discarded. The CSV file format does not have any such concept and a partially written CSV file may be detected as valid.
