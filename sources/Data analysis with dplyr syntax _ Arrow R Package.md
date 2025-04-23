The arrow package provides functionality allowing users to manipulate tabular Arrow data (`Table` and `Dataset` objects) with familiar [dplyr](https://dplyr.tidyverse.org) syntax. To enable this functionality, ensure that the arrow and dplyr packages are both loaded. In this article we will take the `starwars` data set included in dplyr, convert it to an Arrow Table, and then analyze this data. Note that, although these examples all use an in-memory `Table` object, the same functionality works for an on-disk `Dataset` object with only minor differences in behavior (documented later in the article).

To get started let’s load the packages and create the data:  

## One-table dplyr verbs

The arrow package provides support for the dplyr one-table verbs, allowing users to construct data analysis pipelines in a familiar way. The example below shows the use of [filter()](https://dplyr.tidyverse.org/reference/filter.html), [rename()](https://dplyr.tidyverse.org/reference/rename.html), [mutate()](https://dplyr.tidyverse.org/reference/mutate.html), [arrange()](https://dplyr.tidyverse.org/reference/arrange.html) and [select()](https://dplyr.tidyverse.org/reference/select.html):

It is important to note that arrow uses lazy evaluation to delay computation until the result is explicitly requested. This speeds up processing by enabling the Arrow C++ library to perform multiple computations in one operation. As a consequence of this design choice, we have not yet performed computations on the `sw` data. The `result` variable is an object with class `arrow_dplyr_query` that represents all the computations to be performed:

    ## Table (query)
    ## name: string
    ## height_in: double (divide(cast(height, {to_type=double, allow_int_overflow=false, allow_time_truncate=false, allow_time_overflow=false, allow_decimal_truncate=false, allow_float_truncate=false, allow_invalid_utf8=false}), cast(2.54, {to_type=double, allow_int_overflow=false, allow_time_truncate=false, allow_time_overflow=false, allow_decimal_truncate=false, allow_float_truncate=false, allow_invalid_utf8=false})))
    ## mass_lbs: double (multiply_checked(mass, 2.2046))
    ## 
    ## * Filter: (homeworld == "Tatooine")
    ## * Sorted by birth_year [desc]
    ## See $.data for the source Arrow object
To perform these computations and materialize the result, we call [compute()](https://dplyr.tidyverse.org/reference/compute.html) or [collect()](https://dplyr.tidyverse.org/reference/compute.html). The difference between the two determines what kind of object will be returned. Calling [compute()](https://dplyr.tidyverse.org/reference/compute.html) returns an Arrow Table, suitable for passing to other arrow or dplyr functions:

    ## Table
    ## 10 rows x 3 columns
    ## $name <string>
    ## $height_in <double>
    ## $mass_lbs <double>
In contrast, [collect()](https://dplyr.tidyverse.org/reference/compute.html) returns an R data frame, suitable for viewing or passing to other R functions for analysis or visualization:

    ## # A tibble: 10 x 3
    ##    name               height_in mass_lbs
    ##    <chr>                  <dbl>    <dbl>
    ##  1 C-3PO                   65.7    165. 
    ##  2 Cliegg Lars             72.0     NA  
    ##  3 Shmi Skywalker          64.2     NA  
    ##  4 Owen Lars               70.1    265. 
    ##  5 Beru Whitesun Lars      65.0    165. 
    ##  6 Darth Vader             79.5    300. 
    ##  7 Anakin Skywalker        74.0    185. 
    ##  8 Biggs Darklighter       72.0    185. 
    ##  9 Luke Skywalker          67.7    170. 
    ## 10 R5-D4                   38.2     70.5
The arrow package has broad support for single-table dplyr verbs, including those that compute aggregates. For example, it supports [group_by()](https://dplyr.tidyverse.org/reference/group_by.html) and [summarize()](https://dplyr.tidyverse.org/reference/summarise.html), as well as commonly-used convenience functions such as [count()](https://dplyr.tidyverse.org/reference/count.html):

    ## # A tibble: 38 x 2
    ##    species        mean_height
    ##    <chr>                <dbl>
    ##  1 Human                 178 
    ##  2 Droid                 131.
    ##  3 Wookiee               231 
    ##  4 Rodian                173 
    ##  5 Hutt                  175 
    ##  6 NA                    175 
    ##  7 Yoda's species         66 
    ##  8 Trandoshan            190 
    ##  9 Mon Calamari          180 
    ## 10 Ewok                   88 
    ## # i 28 more rows
    ## # A tibble: 3 x 2
    ##   gender        n
    ##   <chr>     <int>
    ## 1 masculine    66
    ## 2 feminine     17
    ## 3 NA            4
Note, however, that window functions such as [ntile()](https://dplyr.tidyverse.org/reference/ntile.html) are not yet supported.  

## Two-table dplyr verbs

Equality joins (e.g. [left_join()](https://dplyr.tidyverse.org/reference/mutate-joins.html), [inner_join()](https://dplyr.tidyverse.org/reference/mutate-joins.html)) are supported for joining multiple tables. This is illustrated below:

    ## # A tibble: 3 x 4
    ##   name           height  mass jedi 
    ##   <chr>           <int> <dbl> <lgl>
    ## 1 Luke Skywalker    172    77 TRUE 
    ## 2 C-3PO             167    75 FALSE
    ## 3 Obi-Wan Kenobi    182    77 TRUE
## Expressions within dplyr verbs

Inside dplyr verbs, Arrow offers support for many functions and operators, with common functions mapped to their base R and tidyverse equivalents: you can find a [list of supported functions within dplyr queries](https://arrow.apache.org/docs/r/articles/../reference/acero.html) in the function documentation. If there are additional functions you would like to see implemented, please file an issue as described in the [Getting help](https://arrow.apache.org/docs/r/#getting-help) guidelines.  

## Registering custom bindings

The arrow package makes it possible for users to supply bindings for custom functions in some situations using [register_scalar_function()](https://arrow.apache.org/docs/r/articles/../reference/register_scalar_function.html). To operate correctly, the to-be-registered function must have `context` as its first argument, as required by the query engine. For example, suppose we wanted to implement a function that converts a string to snake case (a greatly simplified version of `janitor::make_clean_names()`). The function could be written as follows:

To call this within an arrow/dplyr pipeline, it needs to be registered:

In this expression, the `name` argument specifies the name by which it will be recognized in the context of the arrow/dplyr pipeline and `fun` is the function itself. The `in_type` and `out_type` arguments are used to specify the expected data type for the input and output, and `auto_convert` specifies whether arrow should automatically convert any R inputs to their Arrow equivalents.

Once registered, the following works:

    ## # A tibble: 87 x 2
    ##    name               snake_name        
    ##    <chr>              <chr>             
    ##  1 Luke Skywalker     luke_skywalker    
    ##  2 C-3PO              c3po              
    ##  3 R2-D2              r2d2              
    ##  4 Darth Vader        darth_vader       
    ##  5 Leia Organa        leia_organa       
    ##  6 Owen Lars          owen_lars         
    ##  7 Beru Whitesun Lars beru_whitesun_lars
    ##  8 R5-D4              r5d4              
    ##  9 Biggs Darklighter  biggs_darklighter 
    ## 10 Obi-Wan Kenobi     obiwan_kenobi     
    ## # i 77 more rows
To learn more, see [help("register_scalar_function", package = "arrow")](https://arrow.apache.org/docs/r/articles/../reference/register_scalar_function.html).  

## Handling unsupported expressions

For dplyr queries on Table objects, which are held in memory and should usually be representable as data frames, if the arrow package detects an unimplemented function within a dplyr verb, it automatically calls [collect()](https://dplyr.tidyverse.org/reference/compute.html) to return the data as an R data frame before processing that dplyr verb. As an example, neither [lm()](https://rdrr.io/r/stats/lm.html) nor [residuals()](https://rdrr.io/r/stats/residuals.html) are implemented, so if we write code that computes the residuals for a linear regression model, this automatic collection takes place:

    ## Warning: In residuals(lm(mass ~ height)): 
    ## i Expression not supported in Arrow
    ## > Pulling data into R
    ## # A tibble: 59 x 4
    ##    name               height  mass   res
    ##    <chr>               <int> <dbl> <dbl>
    ##  1 Luke Skywalker        172    77 -18.8
    ##  2 C-3PO                 167    75 -17.7
    ##  3 R2-D2                  96    32 -16.4
    ##  4 Darth Vader           202   136  21.4
    ##  5 Leia Organa           150    49 -33.1
    ##  6 Owen Lars             178   120  20.4
    ##  7 Beru Whitesun Lars    165    75 -16.5
    ##  8 R5-D4                  97    32 -17.0
    ##  9 Biggs Darklighter     183    84 -18.7
    ## 10 Obi-Wan Kenobi        182    77 -25.1
    ## # i 49 more rows
For queries on `Dataset` objects – which can be larger than memory – arrow is more conservative and always raises an error if it detects an unsupported expression. To illustrate this behavior, we can write the `starwars` data to disk and then open it as a Dataset. When we use the same pipeline on the Dataset, we obtain an error:

    ## Error in `residuals()`:
    ## ! Expression not supported in Arrow
    ## > Call collect() first to pull data into R.
Calling [collect()](https://dplyr.tidyverse.org/reference/compute.html) in the middle of the pipeline fixes the issue:

    ## # A tibble: 59 x 4
    ##    name               height  mass   res
    ##    <chr>               <int> <dbl> <dbl>
    ##  1 Luke Skywalker        172    77 -18.8
    ##  2 C-3PO                 167    75 -17.7
    ##  3 R2-D2                  96    32 -16.4
    ##  4 Darth Vader           202   136  21.4
    ##  5 Leia Organa           150    49 -33.1
    ##  6 Owen Lars             178   120  20.4
    ##  7 Beru Whitesun Lars    165    75 -16.5
    ##  8 R5-D4                  97    32 -17.0
    ##  9 Biggs Darklighter     183    84 -18.7
    ## 10 Obi-Wan Kenobi        182    77 -25.1
    ## # i 49 more rows
For some operations, you can use [DuckDB](https://www.duckdb.org). It supports Arrow natively, so you can pass the `Dataset` or query object to DuckDB without paying a performance penalty using the helper function [to_duckdb()](https://arrow.apache.org/docs/r/articles/../reference/to_duckdb.html) and pass the object back to Arrow with [to_arrow()](https://arrow.apache.org/docs/r/articles/../reference/to_arrow.html):

    ## # A tibble: 28 x 4
    ##    name                    height  mass hair_color
    ##    <chr>                    <int> <dbl> <chr>     
    ##  1 "Leia Organa"              150    49 brown     
    ##  2 "Beru Whitesun Lars"       165    75 brown     
    ##  3 "Wedge Antilles"           170    77 brown     
    ##  4 "Wicket Systri Warrick"     88    20 brown     
    ##  5 "Cord\u00e9"               157    NA brown     
    ##  6 "Dorm\u00e9"               165    NA brown     
    ##  7 "R4-P17"                    96    NA none      
    ##  8 "Lobot"                    175    79 none      
    ##  9 "Ackbar"                   180    83 none      
    ## 10 "Nien Nunb"                160    68 none      
    ## # i 18 more rows
