---
title: Metadata • Arrow R Package
keywords: source
---

# Metadata • Arrow R Package

url:: https://arrow.apache.org/docs/r/articles/metadata.html
up: [[sources]]

This article describes the various data and metadata object types supplied by arrow, and documents how these objects are structured.  
The arrow package defines the following classes for representing metadata:

* A `Schema` is a list of `Field` objects used to describe the structure of a tabular data object; where
* A `Field` specifies a character string name and a `DataType`; and
* A `DataType` is an attribute controlling how values are represented

Consider this:

    ## Schema
    ## x: int32
    ## y: string
    ## 
    ## See $metadata for additional Schema metadata
The schema that has been automatically inferred could also be manually created:

    ## Schema
    ## x: int32
    ## y: string
The [schema()](https://arrow.apache.org/docs/r/articles/../reference/schema.html) function allows the following shorthand to define fields:

    ## Schema
    ## x: int32
    ## y: string
Sometimes it is important to specify the schema manually, particularly if you want fine-grained control over the Arrow data types:

    ## Table
    ## 3 rows x 2 columns
    ## $x <int64>
    ## $y <string>
    ## 
    ## See $metadata for additional Schema metadata
    ## Table
    ## 3 rows x 2 columns
    ## $x <double>
    ## $y <string>
    ## 
    ## See $metadata for additional Schema metadata
## R object attributes

Arrow supports custom key-value metadata attached to Schemas. When we convert a `data.frame` to an Arrow Table or RecordBatch, the package stores any [attributes()](https://rdrr.io/r/base/attributes.html) attached to the columns of the `data.frame` in the Arrow object Schema. Attributes added to objects in this fashion are stored under the `r` key, as shown below:  

    # data frame with custom metadata
    df <- https://rdrr.io/r/base/data.frame.html(x = 1:3, y = https://rdrr.io/r/base/c.html("a", "b", "c"))
    https://rdrr.io/r/base/attr.html(df, "df_meta") <- "custom data frame metadata"
    https://rdrr.io/r/base/attr.html(df$y, "col_meta") <- "custom column metadata"

    # when converted to a Table, the metadata is preserved
    tb <- https://arrow.apache.org/docs/r/articles/../reference/table.html(df)
    tb$metadata
    ## $r
    ## $r$attributes
    ## $r$attributes$df_meta
    ## [1] "custom data frame metadata"
    ## 
    ## 
    ## $r$columns
    ## $r$columns$x
    ## NULL
    ## 
    ## $r$columns$y
    ## $r$columns$y$attributes
    ## $r$columns$y$attributes$col_meta
    ## [1] "custom column metadata"
    ## 
    ## 
    ## $r$columns$y$columns
    ## NULL
It is also possible to assign additional string metadata under any other key you wish, using a command like this:  

    tb$metadata$new_key <- "new value"

Metadata attached to a Schema is preserved when writing the Table to Arrow/Feather or Parquet formats. When reading those files into R, or when calling [as.data.frame()](https://rdrr.io/r/base/as.data.frame.html) on a Table or RecordBatch, the column attributes are restored to the columns of the resulting `data.frame`. This means that custom data types, including `haven::labelled`, `vctrs` annotations, and others, are preserved when doing a round-trip through Arrow.

Note that the attributes stored in `$metadata$r` are only understood by R. If you write a `data.frame` with `haven` columns to a Feather file and read that in Pandas, the `haven` metadata won’t be recognized there. Similarly, Pandas writes its own custom metadata, which the R package does not consume. You are free, however, to define custom metadata conventions for your application and assign any (string) values you want to other metadata keys.  

## Further reading

* To learn more about arrow metadata, see the documentation for [schema()](https://arrow.apache.org/docs/r/articles/../reference/schema.html).
* To learn more about data types, see the [data types article](https://arrow.apache.org/docs/r/articles/data_types.html).
