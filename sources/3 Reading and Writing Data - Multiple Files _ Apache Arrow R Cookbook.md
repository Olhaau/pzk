# Reading and Writing Data - Multiple Files

## Introduction

When reading files into R using Apache Arrow, you can read:

* a single file into memory as a data frame or an Arrow Table
* a single file that is too large to fit in memory as an Arrow Dataset
* multiple and partitioned files as an Arrow Dataset

This chapter contains recipes related to using Apache Arrow to read and write files too large for memory and multiple or partitioned files as an Arrow Dataset. There are a number of circumstances in which you may want to read in the data as an Arrow Dataset:

* your single data file is too large to load into memory
* your data are partitioned among numerous files
* you want faster performance from your `dplyr` queries
* you want to be able to take advantage of Arrow’s compute functions

It is possible to read in partitioned data in Parquet, Feather (also known as Arrow IPC), and CSV or other text-delimited formats. If you are choosing a partitioned multiple file format, we recommend Parquet or Feather (Arrow IPC ), both of which can have improved performance when compared to CSVs due to their capabilities around metadata and compression.  

## Write data to disk - Parquet

You want to write data to disk in a single Parquet file.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb31-1write_dataset(dataset = airquality, path = "airquality_data")

### Discussion

The default format for `open_dataset()` and `write_dataset()` is Parquet.  

## Write partitioned data - Parquet

You want to save multiple Parquet data files to disk in partitions based on columns in the data.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb32-1write_dataset(airquality, "airquality_partitioned", partitioning = c("Month"))

As you can see, this has created folders based on the supplied partition variable `Month`.  

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb33-1list.files("airquality_partitioned")

    ## [1] "Month=5" "Month=6" "Month=7" "Month=8" "Month=9"

### Discussion

The data is written to separate folders based on the values in the `Month` column. The default behaviour is to use Hive-style (i.e. “col_name=value” folder names) partitions.  

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb35-1# Take a look at the files in this directory
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb35-2list.files("airquality_partitioned", recursive = TRUE)
    ## [1] "Month=5/part-0.parquet" "Month=6/part-0.parquet" "Month=7/part-0.parquet"
    ## [4] "Month=8/part-0.parquet" "Month=9/part-0.parquet"
You can specify multiple partitioning variables to add extra levels of partitioning.  

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb37-1write_dataset(airquality, "airquality_partitioned_deeper", partitioning = c("Month", "Day"))
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb37-2list.files("airquality_partitioned_deeper")
    ## [1] "Month=5" "Month=6" "Month=7" "Month=8" "Month=9"

If you take a look in one of these folders, you will see that the data is then partitioned by the second partition variable, `Day`.  

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb39-1# Take a look at the files in this directory
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb39-2list.files("airquality_partitioned_deeper/Month=5", recursive = TRUE)
    ##  [1] "Day=1/part-0.parquet"  "Day=10/part-0.parquet" "Day=11/part-0.parquet"
    ##  [4] "Day=12/part-0.parquet" "Day=13/part-0.parquet" "Day=14/part-0.parquet"
    ##  [7] "Day=15/part-0.parquet" "Day=16/part-0.parquet" "Day=17/part-0.parquet"
    ## [10] "Day=18/part-0.parquet" "Day=19/part-0.parquet" "Day=2/part-0.parquet" 
    ## [13] "Day=20/part-0.parquet" "Day=21/part-0.parquet" "Day=22/part-0.parquet"
    ## [16] "Day=23/part-0.parquet" "Day=24/part-0.parquet" "Day=25/part-0.parquet"
    ## [19] "Day=26/part-0.parquet" "Day=27/part-0.parquet" "Day=28/part-0.parquet"
    ## [22] "Day=29/part-0.parquet" "Day=3/part-0.parquet"  "Day=30/part-0.parquet"
    ## [25] "Day=31/part-0.parquet" "Day=4/part-0.parquet"  "Day=5/part-0.parquet" 
    ## [28] "Day=6/part-0.parquet"  "Day=7/part-0.parquet"  "Day=8/part-0.parquet" 
    ## [31] "Day=9/part-0.parquet"
There are two different ways to specify variables to use for partitioning - either via the `partitioning` variable as above, or by using `dplyr::group_by()` on your data - the group variables will form the partitions.  

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb41-1write_dataset(dataset = group_by(airquality, Month, Day),
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb41-2  path = "airquality_groupby")
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb42-1# Take a look at the files in this directory
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb42-2list.files("airquality_groupby", recursive = TRUE)
    ##   [1] "Month=5/Day=1/part-0.parquet"  "Month=5/Day=10/part-0.parquet"
    ##   [3] "Month=5/Day=11/part-0.parquet" "Month=5/Day=12/part-0.parquet"
    ##   [5] "Month=5/Day=13/part-0.parquet" "Month=5/Day=14/part-0.parquet"
    ##   [7] "Month=5/Day=15/part-0.parquet" "Month=5/Day=16/part-0.parquet"
    ##   [9] "Month=5/Day=17/part-0.parquet" "Month=5/Day=18/part-0.parquet"
    ##  [11] "Month=5/Day=19/part-0.parquet" "Month=5/Day=2/part-0.parquet" 
    ##  [13] "Month=5/Day=20/part-0.parquet" "Month=5/Day=21/part-0.parquet"
    ##  [15] "Month=5/Day=22/part-0.parquet" "Month=5/Day=23/part-0.parquet"
    ##  [17] "Month=5/Day=24/part-0.parquet" "Month=5/Day=25/part-0.parquet"
    ##  [19] "Month=5/Day=26/part-0.parquet" "Month=5/Day=27/part-0.parquet"
    ##  [21] "Month=5/Day=28/part-0.parquet" "Month=5/Day=29/part-0.parquet"
    ##  [23] "Month=5/Day=3/part-0.parquet"  "Month=5/Day=30/part-0.parquet"
    ##  [25] "Month=5/Day=31/part-0.parquet" "Month=5/Day=4/part-0.parquet" 
    ##  [27] "Month=5/Day=5/part-0.parquet"  "Month=5/Day=6/part-0.parquet" 
    ##  [29] "Month=5/Day=7/part-0.parquet"  "Month=5/Day=8/part-0.parquet" 
    ##  [31] "Month=5/Day=9/part-0.parquet"  "Month=6/Day=1/part-0.parquet" 
    ##  [33] "Month=6/Day=10/part-0.parquet" "Month=6/Day=11/part-0.parquet"
    ##  [35] "Month=6/Day=12/part-0.parquet" "Month=6/Day=13/part-0.parquet"
    ##  [37] "Month=6/Day=14/part-0.parquet" "Month=6/Day=15/part-0.parquet"
    ##  [39] "Month=6/Day=16/part-0.parquet" "Month=6/Day=17/part-0.parquet"
    ##  [41] "Month=6/Day=18/part-0.parquet" "Month=6/Day=19/part-0.parquet"
    ##  [43] "Month=6/Day=2/part-0.parquet"  "Month=6/Day=20/part-0.parquet"
    ##  [45] "Month=6/Day=21/part-0.parquet" "Month=6/Day=22/part-0.parquet"
    ##  [47] "Month=6/Day=23/part-0.parquet" "Month=6/Day=24/part-0.parquet"
    ##  [49] "Month=6/Day=25/part-0.parquet" "Month=6/Day=26/part-0.parquet"
    ##  [51] "Month=6/Day=27/part-0.parquet" "Month=6/Day=28/part-0.parquet"
    ##  [53] "Month=6/Day=29/part-0.parquet" "Month=6/Day=3/part-0.parquet" 
    ##  [55] "Month=6/Day=30/part-0.parquet" "Month=6/Day=4/part-0.parquet" 
    ##  [57] "Month=6/Day=5/part-0.parquet"  "Month=6/Day=6/part-0.parquet" 
    ##  [59] "Month=6/Day=7/part-0.parquet"  "Month=6/Day=8/part-0.parquet" 
    ##  [61] "Month=6/Day=9/part-0.parquet"  "Month=7/Day=1/part-0.parquet" 
    ##  [63] "Month=7/Day=10/part-0.parquet" "Month=7/Day=11/part-0.parquet"
    ##  [65] "Month=7/Day=12/part-0.parquet" "Month=7/Day=13/part-0.parquet"
    ##  [67] "Month=7/Day=14/part-0.parquet" "Month=7/Day=15/part-0.parquet"
    ##  [69] "Month=7/Day=16/part-0.parquet" "Month=7/Day=17/part-0.parquet"
    ##  [71] "Month=7/Day=18/part-0.parquet" "Month=7/Day=19/part-0.parquet"
    ##  [73] "Month=7/Day=2/part-0.parquet"  "Month=7/Day=20/part-0.parquet"
    ##  [75] "Month=7/Day=21/part-0.parquet" "Month=7/Day=22/part-0.parquet"
    ##  [77] "Month=7/Day=23/part-0.parquet" "Month=7/Day=24/part-0.parquet"
    ##  [79] "Month=7/Day=25/part-0.parquet" "Month=7/Day=26/part-0.parquet"
    ##  [81] "Month=7/Day=27/part-0.parquet" "Month=7/Day=28/part-0.parquet"
    ##  [83] "Month=7/Day=29/part-0.parquet" "Month=7/Day=3/part-0.parquet" 
    ##  [85] "Month=7/Day=30/part-0.parquet" "Month=7/Day=31/part-0.parquet"
    ##  [87] "Month=7/Day=4/part-0.parquet"  "Month=7/Day=5/part-0.parquet" 
    ##  [89] "Month=7/Day=6/part-0.parquet"  "Month=7/Day=7/part-0.parquet" 
    ##  [91] "Month=7/Day=8/part-0.parquet"  "Month=7/Day=9/part-0.parquet" 
    ##  [93] "Month=8/Day=1/part-0.parquet"  "Month=8/Day=10/part-0.parquet"
    ##  [95] "Month=8/Day=11/part-0.parquet" "Month=8/Day=12/part-0.parquet"
    ##  [97] "Month=8/Day=13/part-0.parquet" "Month=8/Day=14/part-0.parquet"
    ##  [99] "Month=8/Day=15/part-0.parquet" "Month=8/Day=16/part-0.parquet"
    ## [101] "Month=8/Day=17/part-0.parquet" "Month=8/Day=18/part-0.parquet"
    ## [103] "Month=8/Day=19/part-0.parquet" "Month=8/Day=2/part-0.parquet" 
    ## [105] "Month=8/Day=20/part-0.parquet" "Month=8/Day=21/part-0.parquet"
    ## [107] "Month=8/Day=22/part-0.parquet" "Month=8/Day=23/part-0.parquet"
    ## [109] "Month=8/Day=24/part-0.parquet" "Month=8/Day=25/part-0.parquet"
    ## [111] "Month=8/Day=26/part-0.parquet" "Month=8/Day=27/part-0.parquet"
    ## [113] "Month=8/Day=28/part-0.parquet" "Month=8/Day=29/part-0.parquet"
    ## [115] "Month=8/Day=3/part-0.parquet"  "Month=8/Day=30/part-0.parquet"
    ## [117] "Month=8/Day=31/part-0.parquet" "Month=8/Day=4/part-0.parquet" 
    ## [119] "Month=8/Day=5/part-0.parquet"  "Month=8/Day=6/part-0.parquet" 
    ## [121] "Month=8/Day=7/part-0.parquet"  "Month=8/Day=8/part-0.parquet" 
    ## [123] "Month=8/Day=9/part-0.parquet"  "Month=9/Day=1/part-0.parquet" 
    ## [125] "Month=9/Day=10/part-0.parquet" "Month=9/Day=11/part-0.parquet"
    ## [127] "Month=9/Day=12/part-0.parquet" "Month=9/Day=13/part-0.parquet"
    ## [129] "Month=9/Day=14/part-0.parquet" "Month=9/Day=15/part-0.parquet"
    ## [131] "Month=9/Day=16/part-0.parquet" "Month=9/Day=17/part-0.parquet"
    ## [133] "Month=9/Day=18/part-0.parquet" "Month=9/Day=19/part-0.parquet"
    ## [135] "Month=9/Day=2/part-0.parquet"  "Month=9/Day=20/part-0.parquet"
    ## [137] "Month=9/Day=21/part-0.parquet" "Month=9/Day=22/part-0.parquet"
    ## [139] "Month=9/Day=23/part-0.parquet" "Month=9/Day=24/part-0.parquet"
    ## [141] "Month=9/Day=25/part-0.parquet" "Month=9/Day=26/part-0.parquet"
    ## [143] "Month=9/Day=27/part-0.parquet" "Month=9/Day=28/part-0.parquet"
    ## [145] "Month=9/Day=29/part-0.parquet" "Month=9/Day=3/part-0.parquet" 
    ## [147] "Month=9/Day=30/part-0.parquet" "Month=9/Day=4/part-0.parquet" 
    ## [149] "Month=9/Day=5/part-0.parquet"  "Month=9/Day=6/part-0.parquet" 
    ## [151] "Month=9/Day=7/part-0.parquet"  "Month=9/Day=8/part-0.parquet" 
    ## [153] "Month=9/Day=9/part-0.parquet"
Each of these folders contains 1 or more Parquet files containing the relevant partition of the data.  

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb44-1list.files("airquality_groupby/Month=5/Day=10")

    ## [1] "part-0.parquet"

Note that when there was an `NA` value in the partition column, these values are written to the `col_name=__HIVE_DEFAULT_PARTITION__` directory.  

## Read partitioned data

You want to read partitioned data files as an Arrow Dataset.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb46-1# Read data from directory
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb46-2air_data <- open_dataset("airquality_partitioned_deeper")
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb46-3
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb46-4# View data
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb46-5air_data
    ## FileSystemDataset with 153 Parquet files
    ## Ozone: int32
    ## Solar.R: int32
    ## Wind: double
    ## Temp: int32
    ## Month: int32
    ## Day: int32
    ## 
    ## See $metadata for additional Schema metadata
### Discussion

Partitioning allows you to split data across multiple files and folders, avoiding problems associated with storing all your data in a single file. This can provide further advantages when using Arrow, as Arrow will only read in the necessary partitioned files needed for any given analysis.  

## Write data to disk - Feather/Arrow IPC format

You want to write data to disk in a single Feather/Arrow IPC file.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb48-1write_dataset(dataset = airquality,
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb48-2  path = "airquality_data_feather",
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb48-3  format = "feather")
## Read in Feather/Arrow IPC data as an Arrow Dataset

You want to read in Feather/Arrow IPC data as an Arrow Dataset  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb49-1# write Arrow file to use in this example
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb49-2write_dataset(dataset = airquality,
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb49-3  path = "airquality_data_arrow",
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb49-4  format = "arrow")
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb49-5
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb49-6# read into R
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb49-7open_dataset("airquality_data_arrow", format = "arrow")
    ## FileSystemDataset with 1 Feather file
    ## Ozone: int32
    ## Solar.R: int32
    ## Wind: double
    ## Temp: int32
    ## Month: int32
    ## Day: int32
    ## 
    ## See $metadata for additional Schema metadata
## Write data to disk - CSV format

You want to write data to disk in a single CSV file.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb51-1write_dataset(dataset = airquality,
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb51-2  path = "airquality_data_csv",
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb51-3  format = "csv")
## Read in CSV data as an Arrow Dataset

You want to read in CSV data as an Arrow Dataset  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb52-1# write CSV file to use in this example
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb52-2write_dataset(dataset = airquality,
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb52-3  path = "airquality_data_csv",
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb52-4  format = "csv")
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb52-5
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb52-6# read into R
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb52-7open_dataset("airquality_data_csv", format = "csv")
    ## FileSystemDataset with 1 csv file
    ## Ozone: int64
    ## Solar.R: int64
    ## Wind: double
    ## Temp: int64
    ## Month: int64
    ## Day: int64
## Write compressed partitioned data

You want to save partitioned files, compressed with a specified compression algorithm.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb58-1# Create a temporary directory
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb58-2td <- tempfile()
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb58-3dir.create(td)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb58-4
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb58-5# Write dataset to file
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb58-6write_dataset(iris, path = td, compression = "gzip")
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb59-1# View files in the directory
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb59-2list.files(td, recursive = TRUE)
    ## [1] "part-0.parquet"

### Discussion

You can supply the `compression` argument to `write_dataset()` as long as the compression algorithm is compatible with the chosen format. See `?write_dataset()` for more information on supported compression algorithms and default settings.  

## Read compressed data

You want to read in data which has been compressed.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-1# Create a temporary directory
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-2td <- tempfile()
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-3dir.create(td)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-4
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-5# Write dataset to file
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-6write_dataset(iris, path = td, compression = "gzip")
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-7
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-8# Read in data
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-9ds <- open_dataset(td) %>%
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-10  collect()
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-11
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---multiple-files.html#cb61-12ds
    ## # A tibble: 150 × 5
    ##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ##           <dbl>       <dbl>        <dbl>       <dbl> <fct>  
    ##  1          5.1         3.5          1.4         0.2 setosa 
    ##  2          4.9         3            1.4         0.2 setosa 
    ##  3          4.7         3.2          1.3         0.2 setosa 
    ##  4          4.6         3.1          1.5         0.2 setosa 
    ##  5          5           3.6          1.4         0.2 setosa 
    ##  6          5.4         3.9          1.7         0.4 setosa 
    ##  7          4.6         3.4          1.4         0.3 setosa 
    ##  8          5           3.4          1.5         0.2 setosa 
    ##  9          4.4         2.9          1.4         0.2 setosa 
    ## 10          4.9         3.1          1.5         0.1 setosa 
    ## # ℹ 140 more rows
### Discussion

Note that Arrow automatically detects the compression and you do not have to supply it in the call to `open_dataset()` or the `read_*()` functions.
