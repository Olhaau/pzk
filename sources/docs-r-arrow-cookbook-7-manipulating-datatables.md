---
title: 7 Manipulating Data - Tables
keywords: source
---

url:: https://arrow.apache.org/cookbook/r/manipulating-data---tables.html
up: [[sources]]

# Manipulating Data - Tables

## Introduction

One of the aims of the Arrow project is to reduce duplication between different data frame implementations. The underlying implementation of a data frame is a conceptually different thing to the code- or the application programming interface (API)-that you write to work with it.

You may have seen this before in packages like dbplyr which allow you to use the dplyr API to interact with SQL databases.

The Arrow R package has been written so that the underlying Arrow Table-like objects can be manipulated using the dplyr API, which allows you to use dplyr verbs.

For example, here’s a short pipeline of data manipulation which uses dplyr exclusively:  

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb103-1library(dplyr)
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb103-2starwars %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb103-3  filter(species == "Human") %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb103-4  mutate(height_ft = height/30.48) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb103-5  select(name, height_ft)
    ## # A tibble: 35 × 2
    ##    name               height_ft
    ##    <chr>                  <dbl>
    ##  1 Luke Skywalker          5.64
    ##  2 Darth Vader             6.63
    ##  3 Leia Organa             4.92
    ##  4 Owen Lars               5.84
    ##  5 Beru Whitesun lars      5.41
    ##  6 Biggs Darklighter       6.00
    ##  7 Obi-Wan Kenobi          5.97
    ##  8 Anakin Skywalker        6.17
    ##  9 Wilhuff Tarkin          5.91
    ## 10 Han Solo                5.91
    ## # ℹ 25 more rows
And the same results as using Arrow with dplyr syntax:  

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb105-1arrow_table(starwars) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb105-2  filter(species == "Human") %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb105-3  mutate(height_ft = height/30.48) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb105-4  select(name, height_ft) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb105-5  collect()
    ## # A tibble: 35 × 2
    ##    name               height_ft
    ##    <chr>                  <dbl>
    ##  1 Luke Skywalker          5.64
    ##  2 Darth Vader             6.63
    ##  3 Leia Organa             4.92
    ##  4 Owen Lars               5.84
    ##  5 Beru Whitesun lars      5.41
    ##  6 Biggs Darklighter       6.00
    ##  7 Obi-Wan Kenobi          5.97
    ##  8 Anakin Skywalker        6.17
    ##  9 Wilhuff Tarkin          5.91
    ## 10 Han Solo                5.91
    ## # ℹ 25 more rows
You’ll notice we’ve used `collect()` in the Arrow pipeline above. That’s because one of the ways in which Arrow is efficient is that it works out the instructions for the calculations it needs to perform (*expressions* ) and only runs them using Arrow once you actually pull the data into your R session. This means instead of doing lots of separate operations, it does them all at once in a more optimised way. This is called *lazy evaluation*.

It also means that you are able to manipulate data that is larger than you can fit into memory on the machine you’re running your code on, if you only pull data into R when you have selected the desired subset, or when using functions which can operate on chunks of data.

You can also have data which is split across multiple files. For example, you might have files which are stored in multiple Parquet or Feather files, partitioned across different directories. You can open partitioned or multi-file datasets using `open_dataset()` as discussed in a previous chapter, and then manipulate this data using Arrow before even reading any of the data into R.  

## Use dplyr verbs in Arrow

You want to use a dplyr verb in Arrow.  

### Solution

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb107-1library(dplyr)
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb107-2arrow_table(starwars) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb107-3  filter(species == "Human", homeworld == "Tatooine") %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb107-4  collect()
    ## # A tibble: 8 × 14
    ##   name      height  mass hair_color skin_color eye_color birth_year sex   gender
    ##   <chr>      <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
    ## 1 Luke Sky…    172    77 blond      fair       blue            19   male  mascu…
    ## 2 Darth Va…    202   136 none       white      yellow          41.9 male  mascu…
    ## 3 Owen Lars    178   120 brown, gr… light      blue            52   male  mascu…
    ## 4 Beru Whi…    165    75 brown      light      blue            47   fema… femin…
    ## 5 Biggs Da…    183    84 black      light      brown           24   male  mascu…
    ## 6 Anakin S…    188    84 blond      fair       blue            41.9 male  mascu…
    ## 7 Shmi Sky…    163    NA black      fair       brown           72   fema… femin…
    ## 8 Cliegg L…    183    NA brown      fair       blue            82   male  mascu…
    ## # ℹ 5 more variables: homeworld <chr>, species <chr>, films <list<character>>,
    ## #   vehicles <list<character>>, starships <list<character>>
### Discussion

You can use most of the dplyr verbs directly from Arrow.  

### See also

You can find examples of the various dplyr verbs in “Introduction to dplyr” - run `vignette("dplyr", package = "dplyr")` or view on the [pkgdown site](https://dplyr.tidyverse.org/articles/dplyr.html).

You can see more information about using `arrow_table()` to create Arrow Tables and `collect()` to view them as R data frames in [Creating Arrow Objects](https://arrow.apache.org/cookbook/r/creating-arrow-objects.html#creating-arrow-objects).  

## Use R functions in dplyr verbs in Arrow

You want to use an R function inside a dplyr verb in Arrow.  

### Solution

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb109-1arrow_table(starwars) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb109-2  filter(str_detect(name, "Darth")) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb109-3  collect()
    ## # A tibble: 2 × 14
    ##   name      height  mass hair_color skin_color eye_color birth_year sex   gender
    ##   <chr>      <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
    ## 1 Darth Va…    202   136 none       white      yellow          41.9 male  mascu…
    ## 2 Darth Ma…    175    80 none       red        yellow          54   male  mascu…
    ## # ℹ 5 more variables: homeworld <chr>, species <chr>, films <list<character>>,
    ## #   vehicles <list<character>>, starships <list<character>>
### Discussion

The Arrow R package allows you to use dplyr verbs containing expressions which include base R and many tidyverse functions, but call Arrow functions under the hood. If you find any base R or tidyverse functions which you would like to see a mapping of in Arrow, please [open an issue on the project JIRA](https://issues.apache.org/jira/projects/ARROW/issues).

The following packages (amongst some from others) have had many function bindings/mappings written in arrow:

* [lubridate](https://lubridate.tidyverse.org/)
* [stringr](https://stringr.tidyverse.org/)
* [dplyr](https://dplyr.tidyverse.org/)

If you try to call a function which does not have arrow mapping, the data will be pulled back into R, and you will see a warning message.  

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb111-1library(stringr)
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb111-2
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb111-3arrow_table(starwars) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb111-4  mutate(name_split = str_split_fixed(name, " ", 2)) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb111-5  collect()
    ## Warning: Expression str_split_fixed(name, " ", 2) not supported in Arrow;
    ## pulling data into R
    ## # A tibble: 87 × 15
    ##    name     height  mass hair_color skin_color eye_color birth_year sex   gender
    ##    <chr>     <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
    ##  1 Luke Sk…    172    77 blond      fair       blue            19   male  mascu…
    ##  2 C-3PO       167    75 <NA>       gold       yellow         112   none  mascu…
    ##  3 R2-D2        96    32 <NA>       white, bl… red             33   none  mascu…
    ##  4 Darth V…    202   136 none       white      yellow          41.9 male  mascu…
    ##  5 Leia Or…    150    49 brown      light      brown           19   fema… femin…
    ##  6 Owen La…    178   120 brown, gr… light      blue            52   male  mascu…
    ##  7 Beru Wh…    165    75 brown      light      blue            47   fema… femin…
    ##  8 R5-D4        97    32 <NA>       white, red red             NA   none  mascu…
    ##  9 Biggs D…    183    84 black      light      brown           24   male  mascu…
    ## 10 Obi-Wan…    182    77 auburn, w… fair       blue-gray       57   male  mascu…
    ## # ℹ 77 more rows
    ## # ℹ 6 more variables: homeworld <chr>, species <chr>, films <list<character>>,
    ## #   vehicles <list<character>>, starships <list<character>>,
    ## #   name_split <chr[,2]>
## Use Arrow functions in dplyr verbs in Arrow

You want to use a function which is implemented in Arrow’s C++ library but either:

* it doesn’t have a mapping to a base R or tidyverse equivalent, or
* it has a mapping but nevertheless you want to call the C++ function directly

### Solution

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb114-1arrow_table(starwars) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb114-2  select(name) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb114-3  mutate(padded_name = arrow_ascii_lpad(name, options = list(width = 10, padding = "*"))) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb114-4  collect()
    ## # A tibble: 87 × 2
    ##    name               padded_name       
    ##    <chr>              <chr>             
    ##  1 Luke Skywalker     Luke Skywalker    
    ##  2 C-3PO              *****C-3PO        
    ##  3 R2-D2              *****R2-D2        
    ##  4 Darth Vader        Darth Vader       
    ##  5 Leia Organa        Leia Organa       
    ##  6 Owen Lars          *Owen Lars        
    ##  7 Beru Whitesun lars Beru Whitesun lars
    ##  8 R5-D4              *****R5-D4        
    ##  9 Biggs Darklighter  Biggs Darklighter 
    ## 10 Obi-Wan Kenobi     Obi-Wan Kenobi    
    ## # ℹ 77 more rows
### Discussion

The vast majority of Arrow C++ compute functions have been mapped to their base R or tidyverse equivalents, and we strongly recommend that you use these mappings where possible, as the original functions are well documented and the mapped versions have been tested to ensure the results returned are as expected.

However, there may be circumstances in which you might want to use a compute function from the Arrow C++ library which does not have a base R or tidyverse equivalent.

You can find documentation of Arrow C++ compute functions in [the C++ documention](https://arrow.apache.org/docs/cpp/compute.html#available-functions). This documentation lists all available compute functions, any associated options classes they need, and the valid data types that they can be used with.

You can list all available Arrow compute functions from R by calling `list_compute_functions()`.

    ##   [1] "abs"                             "abs_checked"                    
    ##   [3] "acos"                            "acos_checked"                   
    ##   [5] "add"                             "add_checked"                    
    ##   [7] "all"                             "and"                            
    ##   [9] "and_kleene"                      "and_not"                        
    ##  [11] "and_not_kleene"                  "any"                            
    ##  [13] "approximate_median"              "array_filter"                   
    ##  [15] "array_sort_indices"              "array_take"                     
    ##  [17] "ascii_capitalize"                "ascii_center"                   
    ##  [19] "ascii_is_alnum"                  "ascii_is_alpha"                 
    ##  [21] "ascii_is_decimal"                "ascii_is_lower"                 
    ##  [23] "ascii_is_printable"              "ascii_is_space"                 
    ##  [25] "ascii_is_title"                  "ascii_is_upper"                 
    ##  [27] "ascii_lower"                     "ascii_lpad"                     
    ##  [29] "ascii_ltrim"                     "ascii_ltrim_whitespace"         
    ##  [31] "ascii_reverse"                   "ascii_rpad"                     
    ##  [33] "ascii_rtrim"                     "ascii_rtrim_whitespace"         
    ##  [35] "ascii_split_whitespace"          "ascii_swapcase"                 
    ##  [37] "ascii_title"                     "ascii_trim"                     
    ##  [39] "ascii_trim_whitespace"           "ascii_upper"                    
    ##  [41] "asin"                            "asin_checked"                   
    ##  [43] "assume_timezone"                 "atan"                           
    ##  [45] "atan2"                           "binary_join"                    
    ##  [47] "binary_join_element_wise"        "binary_length"                  
    ##  [49] "binary_repeat"                   "binary_replace_slice"           
    ##  [51] "binary_reverse"                  "binary_slice"                   
    ##  [53] "bit_wise_and"                    "bit_wise_not"                   
    ##  [55] "bit_wise_or"                     "bit_wise_xor"                   
    ##  [57] "case_when"                       "cast"                           
    ##  [59] "ceil"                            "ceil_temporal"                  
    ##  [61] "choose"                          "coalesce"                       
    ##  [63] "cos"                             "cos_checked"                    
    ##  [65] "count"                           "count_all"                      
    ##  [67] "count_distinct"                  "count_substring"                
    ##  [69] "count_substring_regex"           "cumulative_max"                 
    ##  [71] "cumulative_min"                  "cumulative_prod"                
    ##  [73] "cumulative_prod_checked"         "cumulative_sum"                 
    ##  [75] "cumulative_sum_checked"          "day"                            
    ##  [77] "day_of_week"                     "day_of_year"                    
    ##  [79] "day_time_interval_between"       "days_between"                   
    ##  [81] "dictionary_encode"               "divide"                         
    ##  [83] "divide_checked"                  "drop_null"                      
    ##  [85] "ends_with"                       "equal"                          
    ##  [87] "exp"                             "extract_regex"                  
    ##  [89] "fill_null_backward"              "fill_null_forward"              
    ##  [91] "filter"                          "find_substring"                 
    ##  [93] "find_substring_regex"            "first"                          
    ##  [95] "first_last"                      "floor"                          
    ##  [97] "floor_temporal"                  "greater"                        
    ##  [99] "greater_equal"                   "hour"                           
    ## [101] "hours_between"                   "if_else"                        
    ## [103] "index"                           "index_in"                       
    ## [105] "index_in_meta_binary"            "indices_nonzero"                
    ## [107] "invert"                          "is_dst"                         
    ## [109] "is_finite"                       "is_in"                          
    ## [111] "is_in_meta_binary"               "is_inf"                         
    ## [113] "is_leap_year"                    "is_nan"                         
    ## [115] "is_null"                         "is_valid"                       
    ## [117] "iso_calendar"                    "iso_week"                       
    ## [119] "iso_year"                        "last"                           
    ## [121] "less"                            "less_equal"                     
    ## [123] "list_element"                    "list_flatten"                   
    ## [125] "list_parent_indices"             "list_slice"                     
    ## [127] "list_value_length"               "ln"                             
    ## [129] "ln_checked"                      "local_timestamp"                
    ## [131] "log10"                           "log10_checked"                  
    ## [133] "log1p"                           "log1p_checked"                  
    ## [135] "log2"                            "log2_checked"                   
    ## [137] "logb"                            "logb_checked"                   
    ## [139] "make_struct"                     "map_lookup"                     
    ## [141] "match_like"                      "match_substring"                
    ## [143] "match_substring_regex"           "max"                            
    ## [145] "max_element_wise"                "mean"                           
    ## [147] "microsecond"                     "microseconds_between"           
    ## [149] "millisecond"                     "milliseconds_between"           
    ## [151] "min"                             "min_element_wise"               
    ## [153] "min_max"                         "minute"                         
    ## [155] "minutes_between"                 "mode"                           
    ## [157] "month"                           "month_day_nano_interval_between"
    ## [159] "month_interval_between"          "multiply"                       
    ## [161] "multiply_checked"                "nanosecond"                     
    ## [163] "nanoseconds_between"             "negate"                         
    ## [165] "negate_checked"                  "not_equal"                      
    ## [167] "or"                              "or_kleene"                      
    ## [169] "pairwise_diff"                   "pairwise_diff_checked"          
    ## [171] "partition_nth_indices"           "power"                          
    ## [173] "power_checked"                   "product"                        
    ## [175] "quantile"                        "quarter"                        
    ## [177] "quarters_between"                "random"                         
    ## [179] "rank"                            "replace_substring"              
    ## [181] "replace_substring_regex"         "replace_with_mask"              
    ## [183] "round"                           "round_binary"                   
    ## [185] "round_temporal"                  "round_to_multiple"              
    ## [187] "run_end_decode"                  "run_end_encode"                 
    ## [189] "second"                          "seconds_between"                
    ## [191] "select_k_unstable"               "shift_left"                     
    ## [193] "shift_left_checked"              "shift_right"                    
    ## [195] "shift_right_checked"             "sign"                           
    ## [197] "sin"                             "sin_checked"                    
    ## [199] "sort_indices"                    "split_pattern"                  
    ## [201] "split_pattern_regex"             "sqrt"                           
    ## [203] "sqrt_checked"                    "starts_with"                    
    ## [205] "stddev"                          "strftime"                       
    ## [207] "string_is_ascii"                 "strptime"                       
    ## [209] "struct_field"                    "subsecond"                      
    ## [211] "subtract"                        "subtract_checked"               
    ## [213] "sum"                             "take"                           
    ## [215] "tan"                             "tan_checked"                    
    ## [217] "tdigest"                         "true_unless_null"               
    ## [219] "trunc"                           "unique"                         
    ## [221] "us_week"                         "us_year"                        
    ## [223] "utf8_capitalize"                 "utf8_center"                    
    ## [225] "utf8_is_alnum"                   "utf8_is_alpha"                  
    ## [227] "utf8_is_decimal"                 "utf8_is_digit"                  
    ## [229] "utf8_is_lower"                   "utf8_is_numeric"                
    ## [231] "utf8_is_printable"               "utf8_is_space"                  
    ## [233] "utf8_is_title"                   "utf8_is_upper"                  
    ## [235] "utf8_length"                     "utf8_lower"                     
    ## [237] "utf8_lpad"                       "utf8_ltrim"                     
    ## [239] "utf8_ltrim_whitespace"           "utf8_normalize"                 
    ## [241] "utf8_replace_slice"              "utf8_reverse"                   
    ## [243] "utf8_rpad"                       "utf8_rtrim"                     
    ## [245] "utf8_rtrim_whitespace"           "utf8_slice_codeunits"           
    ## [247] "utf8_split_whitespace"           "utf8_swapcase"                  
    ## [249] "utf8_title"                      "utf8_trim"                      
    ## [251] "utf8_trim_whitespace"            "utf8_upper"                     
    ## [253] "value_counts"                    "variance"                       
    ## [255] "week"                            "weeks_between"                  
    ## [257] "xor"                             "year"                           
    ## [259] "year_month_day"                  "years_between"
The majority of functions here have been mapped to their base R or tidyverse equivalent and can be called within a dplyr query as usual. For functions which don’t have a base R or tidyverse equivalent, or you want to supply custom options, you can call them by prefixing their name with “arrow_”.

For example, base R’s `is.na()` function is the equivalent of the Arrow C++ compute function `is_null()` with the option `nan_is_null` set to `TRUE`.  
A mapping between these functions (with `nan_is_null` set to `TRUE`) has been created in arrow.  

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb118-1demo_df <- data.frame(x = c(1, 2, 3, NA, NaN))
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb118-2
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb118-3arrow_table(demo_df) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb118-4  mutate(y = is.na(x)) %>% 
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb118-5  collect()
    ## # A tibble: 5 × 2
    ##       x y    
    ##   <dbl> <lgl>
    ## 1     1 FALSE
    ## 2     2 FALSE
    ## 3     3 FALSE
    ## 4    NA TRUE 
    ## 5   NaN TRUE
If you want to call Arrow’s `is_null()` function but with `nan_is_null` set to `FALSE` (so it returns `TRUE` when a value being examined is `NA` but `FALSE` when the value being examined is `NaN`), you must call `is_null()` directly and specify the option `nan_is_null = FALSE`.  

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb120-1arrow_table(demo_df) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb120-2  mutate(y = arrow_is_null(x, options  = list(nan_is_null = FALSE))) %>% 
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb120-3  collect()
    ## # A tibble: 5 × 2
    ##       x y    
    ##   <dbl> <lgl>
    ## 1     1 FALSE
    ## 2     2 FALSE
    ## 3     3 FALSE
    ## 4    NA TRUE 
    ## 5   NaN FALSE
#### Compute functions with options

Although not all Arrow C++ compute functions require options to be specified, most do. For these functions to work in R, they must be linked up with the appropriate libarrow options C++ class via the R package’s C++ code. At the time of writing, all compute functions available in the development version of the Arrow R package had been associated with their options classes. However, as the Arrow C++ library’s functionality extends, compute functions may be added which do not yet have an R binding. If you find a C++ compute function which you wish to use from the R package, please [open an issue on the Github project](https://github.com/apache/arrow/issues).  

## Compute Window Aggregates

You want to apply an aggregation (e.g. `mean()`) on a grouped table or within a rowwise operation like `filter()`:  

### Solution

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-1arrow_table(starwars) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-2  select(1:4) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-3  filter(!is.na(hair_color)) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-4  left_join(
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-5    arrow_table(starwars) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-6      group_by(hair_color) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-7      summarize(mean_height = mean(height, na.rm = TRUE))
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-8  ) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-9  filter(height < mean_height) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-10  select(!mean_height) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb122-11  collect()
    ## # A tibble: 29 × 4
    ##    name                  height  mass hair_color
    ##    <chr>                  <int> <dbl> <chr>     
    ##  1 Luke Skywalker           172    77 blond     
    ##  2 Leia Organa              150    49 brown     
    ##  3 Beru Whitesun lars       165    75 brown     
    ##  4 Wedge Antilles           170    77 brown     
    ##  5 Yoda                      66    17 white     
    ##  6 Lobot                    175    79 none      
    ##  7 Ackbar                   180    83 none      
    ##  8 Wicket Systri Warrick     88    20 brown     
    ##  9 Nien Nunb                160    68 none      
    ## 10 Finis Valorum            170    NA blond     
    ## # ℹ 19 more rows
Or using `to_duckdb()`  

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb124-1arrow_table(starwars) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb124-2  select(1:4) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb124-3  filter(!is.na(hair_color)) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb124-4  to_duckdb() %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb124-5  group_by(hair_color) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb124-6  filter(height < mean(height, na.rm = TRUE)) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb124-7  to_arrow() %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb124-8  collect()
    ## # A tibble: 29 × 4
    ##    name                  height  mass hair_color
    ##    <chr>                  <int> <dbl> <chr>     
    ##  1 Luke Skywalker           172    77 blond     
    ##  2 Finis Valorum            170    NA blond     
    ##  3 Yoda                      66    17 white     
    ##  4 Leia Organa              150    49 brown     
    ##  5 Beru Whitesun lars       165    75 brown     
    ##  6 Wedge Antilles           170    77 brown     
    ##  7 Wicket Systri Warrick     88    20 brown     
    ##  8 Cordé                    157    NA brown     
    ##  9 Dormé                    165    NA brown     
    ## 10 Padmé Amidala            165    45 brown     
    ## # ℹ 19 more rows
### Discusson

Arrow does not support window functions, and pulls the data into R. For large tables, this sacrifices performance.  

    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb126-1arrow_table(starwars) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb126-2  select(1:4) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb126-3  filter(!is.na(hair_color)) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb126-4  group_by(hair_color) %>%
    https://arrow.apache.org/cookbook/r/manipulating-data---tables.html#cb126-5  filter(height < mean(height, na.rm = TRUE))
    ## Warning: Expression height < mean(height, na.rm = TRUE) not supported in Arrow;
    ## pulling data into R
    ## # A tibble: 29 × 4
    ## # Groups:   hair_color [5]
    ##    name                  height  mass hair_color
    ##    <chr>                  <int> <dbl> <chr>     
    ##  1 Luke Skywalker           172    77 blond     
    ##  2 Leia Organa              150    49 brown     
    ##  3 Beru Whitesun lars       165    75 brown     
    ##  4 Wedge Antilles           170    77 brown     
    ##  5 Yoda                      66    17 white     
    ##  6 Lobot                    175    79 none      
    ##  7 Ackbar                   180    83 none      
    ##  8 Wicket Systri Warrick     88    20 brown     
    ##  9 Nien Nunb                160    68 none      
    ## 10 Finis Valorum            170    NA blond     
    ## # ℹ 19 more rows
You can perform these window aggregate operations on Arrow tables by:

* Computing the aggregation separately, and joining the result
* Passing the data to DuckDB, and use the DuckDB query engine to perform the operations

Arrow supports zero-copy integration with DuckDB, and DuckDB can query Arrow datasets directly and stream query results back to Arrow. This integreation uses zero-copy streaming of data between DuckDB and Arrow and vice versa so that you can compose a query using both together, all the while not paying any cost to (re)serialize the data when you pass it back and forth. This is especially useful in cases where something is supported in one of Arrow or DuckDB query engines but not the other. You can find more information about this integration on the [Arrow blog post](https://arrow.apache.org/blog/2021/12/03/arrow-duckdb/).
