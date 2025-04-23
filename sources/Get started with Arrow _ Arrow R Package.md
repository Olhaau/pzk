Apache Arrow is a software development platform for building high performance applications that process and transport large data sets. It is designed to improve the performance of data analysis methods, and to increase the efficiency of moving data from one system or programming language to another.

The arrow package provides a standard way to use Apache Arrow in R. It provides a low-level interface to the [Arrow C++ library](https://arrow.apache.org/docs/cpp), and some higher-level tools for working with it in a way designed to feel natural to R users. This article provides an overview of how the pieces fit together, and it describes the conventions that the classes and methods follow in R.  

## Package conventions

The arrow R package builds on top of the Arrow C++ library, and C++ is an object oriented language. As a consequence, the core logic of the Arrow C++ library is encapsulated in classes and methods. In the arrow R package these are implemented as [`R6`](https://r6.r-lib.org) classes that all adopt “TitleCase” naming conventions. Some examples of these include:

* Two-dimensional, tabular data structures such as `Table`, `RecordBatch`, and `Dataset`
* One-dimensional, vector-like data structures such as `Array` and `ChunkedArray`
* Classes for reading, writing, and streaming data such as `ParquetFileReader` and `CsvTableReader`

This low-level interface allows you to interact with the Arrow C++ library in a very flexible way, but in many common situations you may never need to use it at all, because arrow also supplies a high-level interface using functions that follow a “snake_case” naming convention. Some examples of this include:

* [arrow_table()](https://arrow.apache.org/docs/r/articles/../reference/table.html) allows you to create Arrow tables without directly using the `Table` object
* [read_parquet()](https://arrow.apache.org/docs/r/articles/../reference/read_parquet.html) allows you to open Parquet files without directly using the `ParquetFileReader` object

All the examples used in this article rely on this high-level interface.

For developers interested in learning more about the package structure, see the [developer guide](https://arrow.apache.org/docs/r/articles/developing.html).  

## Tabular data in Arrow

A critical component of Apache Arrow is its in-memory columnar format, a standardized, language-agnostic specification for representing structured, table-like datasets in-memory. In the arrow R package, the `Table` class is used to store these objects. Tables are roughly analogous to data frames and have similar behavior. The [arrow_table()](https://arrow.apache.org/docs/r/articles/../reference/table.html) function allows you to generate new Arrow Tables in much the same way that [data.frame()](https://rdrr.io/r/base/data.frame.html) is used to create new data frames:

    ## Table
    ## 3 rows x 2 columns
    ## $x <int32>
    ## $y <string>
You can use `[` to specify subsets of Arrow Table in the same way you would for a data frame:

    ## Table
    ## 2 rows x 2 columns
    ## $x <int32>
    ## $y <string>
Along the same lines, the `$` operator can be used to extract named columns:

    ## ChunkedArray
    ## <string>
    ## [
    ##   [
    ##     "a",
    ##     "b",
    ##     "c"
    ##   ]
    ## ]
Note the output: individual columns in an Arrow Table are represented as Chunked Arrays, which are one-dimensional data structures in Arrow that are roughly analogous to vectors in R.

Tables are the primary way to represent rectangular data in-memory using Arrow, but they are not the only rectangular data structure used by the Arrow C++ library: there are also Datasets which are used for data stored on-disk rather than in-memory, and Record Batches which are fundamental building blocks but not typically used in data analysis.

To learn more about the different data object classes in arrow, see the article on [data objects](https://arrow.apache.org/docs/r/articles/data_objects.html).  

## Converting Tables to data frames

Tables are a data structure used to represent rectangular data within memory allocated by the Arrow C++ library, but they can be coerced to native R data frames (or tibbles) using [as.data.frame()](https://rdrr.io/r/base/as.data.frame.html)

    ##   x y
    ## 1 1 a
    ## 2 2 b
    ## 3 3 c
When this coercion takes place, each of the columns in the original Arrow Table must be converted to native R data objects. In the `dat` Table, for instance, `dat$x` is stored as the Arrow data type int32 inherited from C++, which becomes an R integer type when [as.data.frame()](https://rdrr.io/r/base/as.data.frame.html) is called.

It is possible to exercise fine-grained control over this conversion process. To learn more about the different types and how they are converted, see the [data types](https://arrow.apache.org/docs/r/articles/data_types.html) article.  

## Reading and writing data

One of the main ways to use arrow is to read and write data files in several common formats. The arrow package supplies extremely fast CSV reading and writing capabilities, but in addition supports data formats like Parquet and Arrow (also called Feather) that are not widely supported in other packages. In addition, the arrow package supports multi-file data sets in which a single rectangular data set is stored across multiple files.  

### Individual files

When the goal is to read a single data file into memory, there are several functions you can use:

* [read_parquet()](https://arrow.apache.org/docs/r/articles/../reference/read_parquet.html): read a file in Parquet format
* [read_feather()](https://arrow.apache.org/docs/r/articles/../reference/read_feather.html): read a file in Arrow/Feather format
* [read_delim_arrow()](https://arrow.apache.org/docs/r/articles/../reference/read_delim_arrow.html): read a delimited text file
* [read_csv_arrow()](https://arrow.apache.org/docs/r/articles/../reference/read_delim_arrow.html): read a comma-separated values (CSV) file
* [read_tsv_arrow()](https://arrow.apache.org/docs/r/articles/../reference/read_delim_arrow.html): read a tab-separated values (TSV) file
* [read_json_arrow()](https://arrow.apache.org/docs/r/articles/../reference/read_json_arrow.html): read a JSON data file

In every case except JSON, there is a corresponding `write_*()` function that allows you to write data files in the appropriate format.

By default, the `read_*()` functions will return a data frame or tibble, but you can also use them to read data into an Arrow Table. To do this, you need to set the `as_data_frame` argument to `FALSE`.

In the example below, we take the `starwars` data provided by the dplyr package and write it to a Parquet file using [write_parquet()](https://arrow.apache.org/docs/r/articles/../reference/write_parquet.html)

We can then use [read_parquet()](https://arrow.apache.org/docs/r/articles/../reference/read_parquet.html) to load the data from this file. As shown below, the default behavior is to return a data frame (`sw_frame`) but when we set `as_data_frame = FALSE` the data are read as an Arrow Table (`sw_table`):

    ## Table
    ## 87 rows x 14 columns
    ## $name <string>
    ## $height <int32>
    ## $mass <double>
    ## $hair_color <string>
    ## $skin_color <string>
    ## $eye_color <string>
    ## $birth_year <double>
    ## $sex <string>
    ## $gender <string>
    ## $homeworld <string>
    ## $species <string>
    ## $films: list<element <string>>
    ## $vehicles: list<element <string>>
    ## $starships: list<element <string>>
To learn more about reading and writing individual data files, see the [read/write article](https://arrow.apache.org/docs/r/articles/read_write.html).  

### Multi-file data sets

When a tabular data set becomes large, it is often good practice to partition the data into meaningful subsets and store each one in a separate file. Among other things, this means that if only one subset of the data are relevant to an analysis, only one (smaller) file needs to be read. The arrow package provides the Dataset interface, a convenient way to read, write, and analyze a single data file that is larger-than-memory and multi-file data sets.

To illustrate the concepts, we’ll create a nonsense data set with 100000 rows that can be split into 10 subsets:

What we might like to do is partition this data and then write it to 10 separate Parquet files, one corresponding to each value of the `subset` column. To do this we first specify the path to a folder into which we will write the data files:

We can then use [group_by()](https://dplyr.tidyverse.org/reference/group_by.html) function from dplyr to specify that the data will be partitioned using the `subset` column, and then pass the grouped data to [write_dataset()](https://arrow.apache.org/docs/r/articles/../reference/write_dataset.html):

This creates a set of 10 files, one for each subset. These files are named according to the “hive partitioning” format as shown below:

    ##  [1] "subset=1/part-0.parquet"  "subset=10/part-0.parquet"
    ##  [3] "subset=2/part-0.parquet"  "subset=3/part-0.parquet" 
    ##  [5] "subset=4/part-0.parquet"  "subset=5/part-0.parquet" 
    ##  [7] "subset=6/part-0.parquet"  "subset=7/part-0.parquet" 
    ##  [9] "subset=8/part-0.parquet"  "subset=9/part-0.parquet"
Each of these Parquet files can be opened individually using [read_parquet()](https://arrow.apache.org/docs/r/articles/../reference/read_parquet.html) but is often more convenient – especially for very large data sets – to scan the folder and “connect” to the data set without loading it into memory. We can do this using [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html):

    ## FileSystemDataset with 10 Parquet files
    ## 3 columns
    ## x: double
    ## y: double
    ## subset: int32
This `dset` object does not store the data in-memory, only some metadata. However, as discussed in the next section, it is possible to analyze the data referred to be `dset` as if it had been loaded.

To learn more about Arrow Datasets, see the [dataset article](https://arrow.apache.org/docs/r/articles/dataset.html).  

## Analyzing Arrow data with dplyr

Arrow Tables and Datasets can be analyzed using dplyr syntax. This is possible because the arrow R package supplies a backend that translates dplyr verbs into commands that are understood by the Arrow C++ library, and will similarly translate R expressions that appear within a call to a dplyr verb. For example, although the `dset` Dataset is not a data frame (and does not store the data values in memory), you can still pass it to a dplyr pipeline like the one shown below:

    ## # A tibble: 6 x 3
    ##   subset  mean_x min_y
    ##    <int>   <dbl> <dbl>
    ## 1      2 0.00486 -4.00
    ## 2      3 0.00440 -3.86
    ## 3      4 0.0125  -3.65
    ## 4      6 0.0234  -3.88
    ## 5      7 0.00477 -4.65
    ## 6      9 0.00557 -3.50
Notice that we call [collect()](https://dplyr.tidyverse.org/reference/compute.html) at the end of the pipeline. No actual computations are performed until [collect()](https://dplyr.tidyverse.org/reference/compute.html) (or the related [compute()](https://dplyr.tidyverse.org/reference/compute.html) function) is called. This “lazy evaluation” makes it possible for the Arrow C++ compute engine to optimize how the computations are performed.

To learn more about analyzing Arrow data, see the [data wrangling article](https://arrow.apache.org/docs/r/articles/data_wrangling.html). The [list of functions available in dplyr queries](https://arrow.apache.org/docs/r/reference/acero.html) page may also be useful.  

## Connecting to cloud storage

Another use for the arrow R package is to read, write, and analyze data sets stored remotely on cloud services. The package currently supports both Amazon Simple Storage Service (S3) and Google Cloud Storage (GCS). The example below illustrates how you can use [s3_bucket()](https://arrow.apache.org/docs/r/articles/../reference/s3_bucket.html) to refer to a an S3 bucket, and use [open_dataset()](https://arrow.apache.org/docs/r/articles/../reference/open_dataset.html) to connect to the data set stored there:

To learn more about the support for cloud services in arrow, see the [cloud storage](https://arrow.apache.org/docs/r/articles/fs.html) article.  

## Efficient data interchange between R and Python

The [reticulate](https://rstudio.github.io/reticulate/) package provides an interface that allows you to call Python code from R. The arrow package is designed to be interoperable with reticulate. If the Python environment has the pyarrow library installed (the Python equivalent to the arrow package), you can pass an Arrow Table from R to Python using the [r_to_py()](https://rstudio.github.io/reticulate/reference/r-py-conversion.html) function in reticulate as shown below:

The `sw_table_python` object is now stored as a pyarrow Table: the Python equivalent of the Table class. You can see this when you print the object:

    ## pyarrow.Table
    ## name: string
    ## height: int32
    ## mass: double
    ## hair_color: string
    ## skin_color: string
    ## eye_color: string
    ## birth_year: double
    ## sex: string
    ## gender: string
    ## homeworld: string
    ## species: string
    ## films: list<element: string>
    ##   child 0, element: string
    ## vehicles: list<element: string>
    ##   child 0, element: string
    ## starships: list<element: string>
    ##   child 0, element: string
    ## ----
    ## name: [["Luke Skywalker","C-3PO","R2-D2","Darth Vader","Leia Organa",...,"Finn","Rey","Poe Dameron","BB8","Captain Phasma"]]
    ## height: [[172,167,96,202,150,...,null,null,null,null,null]]
    ## mass: [[77,75,32,136,49,...,null,null,null,null,null]]
    ## hair_color: [["blond",null,null,"none","brown",...,"black","brown","brown","none","none"]]
    ## skin_color: [["fair","gold","white, blue","white","light",...,"dark","light","light","none","none"]]
    ## eye_color: [["blue","yellow","red","yellow","brown",...,"dark","hazel","brown","black","unknown"]]
    ## birth_year: [[19,112,33,41.9,19,...,null,null,null,null,null]]
    ## sex: [["male","none","none","male","female",...,"male","female","male","none","female"]]
    ## gender: [["masculine","masculine","masculine","masculine","feminine",...,"masculine","feminine","masculine","masculine","feminine"]]
    ## homeworld: [["Tatooine","Tatooine","Naboo","Tatooine","Alderaan",...,null,null,null,null,null]]
    ## ...
It is important to recognize that when this transfer takes place, only the C++ pointer (i.e., metadata referring to the underlying data object stored by the Arrow C++ library) is copied. The data values themselves in the same place within memory. The consequence of this is that it is much faster to pass an Arrow Table from R to Python than to copy a data frame in R to a Pandas DataFrame in Python.

To learn more about passing Arrow data between R and Python, see the article on [python integrations](https://arrow.apache.org/docs/r/articles/python.html).  

## Access to Arrow messages, buffers, and streams

The arrow package also provides many lower-level bindings to the C++ library, which enable you to access and manipulate Arrow objects. You can use these to build connectors to other applications and services that use Arrow. One example is Spark: the [`sparklyr`](https://spark.rstudio.com/) package has support for using Arrow to move data to and from Spark, yielding [significant performance gains](https://arrow.apache.org/blog/2019/01/25/r-spark-improvements/).  

## Contributing to arrow

Apache Arrow is an extensive project spanning multiple languages, and the arrow R package is only one part of this large project. Because of this there are a number of special considerations for developers who would like to contribute to the package. To help make this process easier, there are several articles in the arrow documentation that discuss topics that are relevant to arrow developers, but are very unlikely to be needed by users.

For an overview of the development process and a list of related articles for developers, see the [developer guide](https://arrow.apache.org/docs/r/articles/developing.html).
