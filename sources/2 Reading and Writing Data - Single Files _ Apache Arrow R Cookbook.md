# Reading and Writing Data - Single Files

## Introduction

When reading files into R using Apache Arrow, you can read:

* a single file into memory as a data frame or an Arrow Table
* a single file that is too large to fit in memory as an Arrow Dataset
* multiple and partitioned files as an Arrow Dataset

This chapter contains recipes related to using Apache Arrow to read and write single file data into memory as an Arrow Table. There are a number of circumstances in which you may want to read in single file data as an Arrow Table:

* your data file is large and having performance issues
* you want faster performance from your `dplyr` queries
* you want to be able to take advantage of Arrow’s compute functions

If a single data file is too large to load into memory, you can use the Arrow Dataset API. Recipes for using `open_dataset()` and `write_dataset()` are in the Reading and Writing Data - Multiple Files chapter.  

## Convert data from a data frame to an Arrow Table

You want to convert an existing `data.frame` or `tibble` object into an Arrow Table.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb1-1air_table <- arrow_table(airquality)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb1-2air_table
    ## Table
    ## 153 rows x 6 columns
    ## $Ozone <int32>
    ## $Solar.R <int32>
    ## $Wind <double>
    ## $Temp <int32>
    ## $Month <int32>
    ## $Day <int32>
    ## 
    ## See $metadata for additional Schema metadata
## Convert data from an Arrow Table to a data frame

You want to convert an Arrow Table to a data frame to view the data or work with it in your usual analytics pipeline.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb3-1air_df <- as.data.frame(air_table)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb3-2air_df
    ##     Ozone Solar.R Wind Temp Month Day
    ## 1      41     190  7.4   67     5   1
    ## 2      36     118  8.0   72     5   2
    ## 3      12     149 12.6   74     5   3
    ## 4      18     313 11.5   62     5   4
    ## 5      NA      NA 14.3   56     5   5
    ## 6      28      NA 14.9   66     5   6
    ## 7      23     299  8.6   65     5   7
    ## 8      19      99 13.8   59     5   8
    ## 9       8      19 20.1   61     5   9
    ## 10     NA     194  8.6   69     5  10
    ## 11      7      NA  6.9   74     5  11
    ## 12     16     256  9.7   69     5  12
    ## 13     11     290  9.2   66     5  13
    ## 14     14     274 10.9   68     5  14
    ## 15     18      65 13.2   58     5  15
    ## 16     14     334 11.5   64     5  16
    ## 17     34     307 12.0   66     5  17
    ## 18      6      78 18.4   57     5  18
    ## 19     30     322 11.5   68     5  19
    ## 20     11      44  9.7   62     5  20
    ## 21      1       8  9.7   59     5  21
    ## 22     11     320 16.6   73     5  22
    ## 23      4      25  9.7   61     5  23
    ## 24     32      92 12.0   61     5  24
    ## 25     NA      66 16.6   57     5  25
    ## 26     NA     266 14.9   58     5  26
    ## 27     NA      NA  8.0   57     5  27
    ## 28     23      13 12.0   67     5  28
    ## 29     45     252 14.9   81     5  29
    ## 30    115     223  5.7   79     5  30
    ## 31     37     279  7.4   76     5  31
    ## 32     NA     286  8.6   78     6   1
    ## 33     NA     287  9.7   74     6   2
    ## 34     NA     242 16.1   67     6   3
    ## 35     NA     186  9.2   84     6   4
    ## 36     NA     220  8.6   85     6   5
    ## 37     NA     264 14.3   79     6   6
    ## 38     29     127  9.7   82     6   7
    ## 39     NA     273  6.9   87     6   8
    ## 40     71     291 13.8   90     6   9
    ## 41     39     323 11.5   87     6  10
    ## 42     NA     259 10.9   93     6  11
    ## 43     NA     250  9.2   92     6  12
    ## 44     23     148  8.0   82     6  13
    ## 45     NA     332 13.8   80     6  14
    ## 46     NA     322 11.5   79     6  15
    ## 47     21     191 14.9   77     6  16
    ## 48     37     284 20.7   72     6  17
    ## 49     20      37  9.2   65     6  18
    ## 50     12     120 11.5   73     6  19
    ## 51     13     137 10.3   76     6  20
    ## 52     NA     150  6.3   77     6  21
    ## 53     NA      59  1.7   76     6  22
    ## 54     NA      91  4.6   76     6  23
    ## 55     NA     250  6.3   76     6  24
    ## 56     NA     135  8.0   75     6  25
    ## 57     NA     127  8.0   78     6  26
    ## 58     NA      47 10.3   73     6  27
    ## 59     NA      98 11.5   80     6  28
    ## 60     NA      31 14.9   77     6  29
    ## 61     NA     138  8.0   83     6  30
    ## 62    135     269  4.1   84     7   1
    ## 63     49     248  9.2   85     7   2
    ## 64     32     236  9.2   81     7   3
    ## 65     NA     101 10.9   84     7   4
    ## 66     64     175  4.6   83     7   5
    ## 67     40     314 10.9   83     7   6
    ## 68     77     276  5.1   88     7   7
    ## 69     97     267  6.3   92     7   8
    ## 70     97     272  5.7   92     7   9
    ## 71     85     175  7.4   89     7  10
    ## 72     NA     139  8.6   82     7  11
    ## 73     10     264 14.3   73     7  12
    ## 74     27     175 14.9   81     7  13
    ## 75     NA     291 14.9   91     7  14
    ## 76      7      48 14.3   80     7  15
    ## 77     48     260  6.9   81     7  16
    ## 78     35     274 10.3   82     7  17
    ## 79     61     285  6.3   84     7  18
    ## 80     79     187  5.1   87     7  19
    ## 81     63     220 11.5   85     7  20
    ## 82     16       7  6.9   74     7  21
    ## 83     NA     258  9.7   81     7  22
    ## 84     NA     295 11.5   82     7  23
    ## 85     80     294  8.6   86     7  24
    ## 86    108     223  8.0   85     7  25
    ## 87     20      81  8.6   82     7  26
    ## 88     52      82 12.0   86     7  27
    ## 89     82     213  7.4   88     7  28
    ## 90     50     275  7.4   86     7  29
    ## 91     64     253  7.4   83     7  30
    ## 92     59     254  9.2   81     7  31
    ## 93     39      83  6.9   81     8   1
    ## 94      9      24 13.8   81     8   2
    ## 95     16      77  7.4   82     8   3
    ## 96     78      NA  6.9   86     8   4
    ## 97     35      NA  7.4   85     8   5
    ## 98     66      NA  4.6   87     8   6
    ## 99    122     255  4.0   89     8   7
    ## 100    89     229 10.3   90     8   8
    ## 101   110     207  8.0   90     8   9
    ## 102    NA     222  8.6   92     8  10
    ## 103    NA     137 11.5   86     8  11
    ## 104    44     192 11.5   86     8  12
    ## 105    28     273 11.5   82     8  13
    ## 106    65     157  9.7   80     8  14
    ## 107    NA      64 11.5   79     8  15
    ## 108    22      71 10.3   77     8  16
    ## 109    59      51  6.3   79     8  17
    ## 110    23     115  7.4   76     8  18
    ## 111    31     244 10.9   78     8  19
    ## 112    44     190 10.3   78     8  20
    ## 113    21     259 15.5   77     8  21
    ## 114     9      36 14.3   72     8  22
    ## 115    NA     255 12.6   75     8  23
    ## 116    45     212  9.7   79     8  24
    ## 117   168     238  3.4   81     8  25
    ## 118    73     215  8.0   86     8  26
    ## 119    NA     153  5.7   88     8  27
    ## 120    76     203  9.7   97     8  28
    ## 121   118     225  2.3   94     8  29
    ## 122    84     237  6.3   96     8  30
    ## 123    85     188  6.3   94     8  31
    ## 124    96     167  6.9   91     9   1
    ## 125    78     197  5.1   92     9   2
    ## 126    73     183  2.8   93     9   3
    ## 127    91     189  4.6   93     9   4
    ## 128    47      95  7.4   87     9   5
    ## 129    32      92 15.5   84     9   6
    ## 130    20     252 10.9   80     9   7
    ## 131    23     220 10.3   78     9   8
    ## 132    21     230 10.9   75     9   9
    ## 133    24     259  9.7   73     9  10
    ## 134    44     236 14.9   81     9  11
    ## 135    21     259 15.5   76     9  12
    ## 136    28     238  6.3   77     9  13
    ## 137     9      24 10.9   71     9  14
    ## 138    13     112 11.5   71     9  15
    ## 139    46     237  6.9   78     9  16
    ## 140    18     224 13.8   67     9  17
    ## 141    13      27 10.3   76     9  18
    ## 142    24     238 10.3   68     9  19
    ## 143    16     201  8.0   82     9  20
    ## 144    13     238 12.6   64     9  21
    ## 145    23      14  9.2   71     9  22
    ## 146    36     139 10.3   81     9  23
    ## 147     7      49 10.3   69     9  24
    ## 148    14      20 16.6   63     9  25
    ## 149    30     193  6.9   70     9  26
    ## 150    NA     145 13.2   77     9  27
    ## 151    14     191 14.3   75     9  28
    ## 152    18     131  8.0   76     9  29
    ## 153    20     223 11.5   68     9  30
### Discussion

You can `dplyr::collect()` to return a tibble or `as.data.frame()` to return a `data.frame`.  

## Write a Parquet file

You want to write a single Parquet file to disk.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb5-1# Create table
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb5-2my_table <- arrow_table(tibble::tibble(group = c("A", "B", "C"), score = c(99, 97, 99)))
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb5-3# Write to Parquet
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb5-4write_parquet(my_table, "my_table.parquet")
## Read a Parquet file

You want to read a single Parquet file into memory.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb6-1parquet_tbl <- read_parquet("my_table.parquet")
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb6-2parquet_tbl
    ## # A tibble: 3 × 2
    ##   group score
    ##   <chr> <dbl>
    ## 1 A        99
    ## 2 B        97
    ## 3 C        99
As the argument `as_data_frame` was left set to its default value of `TRUE`, the file was read in as a tibble.

    ## [1] "tbl_df"     "tbl"        "data.frame"

### Discussion

If you set `as_data_frame` to `FALSE`, the file will be read in as an Arrow Table.  

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb10-1my_table_arrow <- read_parquet("my_table.parquet", as_data_frame = FALSE)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb10-2my_table_arrow
    ## Table
    ## 3 rows x 2 columns
    ## $group <string>
    ## $score <double>
    ## [1] "Table"        "ArrowTabular" "ArrowObject"  "R6"

## Read a Parquet file from S3

You want to read a single Parquet file from S3 into memory.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb14-1df <- read_parquet(file = "s3://voltrondata-labs-datasets/nyc-taxi/year=2019/month=6/part-0.parquet")

## Filter columns while reading a Parquet file

You want to specify which columns to include when reading in a single Parquet file into memory.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb15-1# Create table to read back in
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb15-2dist_time <- arrow_table(data.frame(distance = c(12.2, 15.7, 14.2), time = c(43, 44, 40)))
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb15-3# Write to Parquet
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb15-4write_parquet(dist_time, "dist_time.parquet")
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb15-5
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb15-6# Read in only the "time" column
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb15-7time_only <- read_parquet("dist_time.parquet", col_select = "time")
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb15-8time_only
    ## # A tibble: 3 × 1
    ##    time
    ##   <dbl>
    ## 1    43
    ## 2    44
    ## 3    40
## Write a Feather V2/Arrow IPC file

You want to write a single Feather V2 file (also called Arrow IPC file).  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb17-1my_table <- arrow_table(data.frame(group = c("A", "B", "C"), score = c(99, 97, 99)))
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb17-2write_feather(my_table, "my_table.arrow")
### Discussion

For legacy support, you can write data in the original Feather format by setting the `version` parameter to `1`.  

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb18-1# Create table
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb18-2my_table <- arrow_table(data.frame(group = c("A", "B", "C"), score = c(99, 97, 99)))
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb18-3# Write to Feather format V1
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb18-4write_feather(mtcars, "my_table.feather", version = 1)
## Read a Feather/Arrow IPC file

You want to read a single Feather V1 or V2 file into memory (also called Arrow IPC file).  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb19-1my_feather_tbl <- read_feather("my_table.arrow")

## Write streaming Arrow IPC files

You want to write to the Arrow IPC stream format.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb20-1# Create table
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb20-2my_table <- arrow_table(
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb20-3  data.frame(
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb20-4    group = c("A", "B", "C"),
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb20-5    score = c(99, 97, 99)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb20-6    )
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb20-7)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb20-8# Write to IPC stream format
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb20-9write_ipc_stream(my_table, "my_table.arrows")
## Read streaming Arrow IPC files

You want to read from the Arrow IPC stream format.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb21-1my_ipc_stream <- arrow::read_ipc_stream("my_table.arrows")

## Write a CSV file

You want to write Arrow data to a single CSV file.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb22-1write_csv_arrow(cars, "cars.csv")

## Read a CSV file

You want to read a single CSV file into memory.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb23-1my_csv <- read_csv_arrow("cars.csv", as_data_frame = FALSE)

## Read a JSON file

You want to read a JSON file into memory.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-1# Create a file to read back in
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-2tf <- tempfile()
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-3writeLines('
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-4    {"country": "United Kingdom", "code": "GB", "long": -3.44, "lat": 55.38}
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-5    {"country": "France", "code": "FR", "long": 2.21, "lat": 46.23}
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-6    {"country": "Germany", "code": "DE", "long": 10.45, "lat": 51.17}
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-7  ', tf, useBytes = TRUE)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-8
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-9# Read in the data
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-10countries <- read_json_arrow(tf, col_select = c("country", "long", "lat"))
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb24-11countries
    ## # A tibble: 3 × 3
    ##   country         long   lat
    ##   <chr>          <dbl> <dbl>
    ## 1 United Kingdom -3.44  55.4
    ## 2 France          2.21  46.2
    ## 3 Germany        10.4   51.2
## Write a compressed single data file

You want to save a single file, compressed with a specified compression algorithm.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb26-1# Create a temporary directory
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb26-2td <- tempfile()
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb26-3dir.create(td)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb26-4
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb26-5# Write data compressed with the gzip algorithm instead of the default
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb26-6write_parquet(iris, file.path(td, "iris.parquet"), compression = "gzip")
### See also

Some formats write compressed data by default. For more information on the supported compression algorithms and default settings, see:

* `?write_parquet()`
* `?write_feather()`  

## Read compressed data

You want to read in a single data file which has been compressed.  

### Solution

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb27-1# Create a temporary directory
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb27-2td <- tempfile()
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb27-3dir.create(td)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb27-4
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb27-5# Write data which is to be read back in
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb27-6write_parquet(iris, file.path(td, "iris.parquet"), compression = "gzip")
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb27-7
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb27-8# Read in data
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb27-9ds <- read_parquet(file.path(td, "iris.parquet"))
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb27-10ds
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

Note that Arrow automatically detects the compression and you do not have to supply it in the call to the `read_*()` or the `open_dataset()` functions.

Although the CSV format does not support compression itself, Arrow supports reading in CSV data which has been compressed, if the file extension is `.gz`.  

    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb29-1# Create a temporary directory
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb29-2td <- tempfile()
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb29-3dir.create(td)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb29-4
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb29-5# Write data which is to be read back in
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb29-6write.csv(iris, gzfile(file.path(td, "iris.csv.gz")), row.names = FALSE, quote = FALSE)
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb29-7
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb29-8# Read in data
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb29-9ds <- read_csv_arrow(file.path(td, "iris.csv.gz"))
    https://arrow.apache.org/cookbook/r/reading-and-writing-data---single-files.html#cb29-10ds
    ## # A tibble: 150 × 5
    ##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ##           <dbl>       <dbl>        <dbl>       <dbl> <chr>  
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
