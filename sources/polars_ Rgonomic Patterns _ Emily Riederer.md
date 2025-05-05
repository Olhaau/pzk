---
title: polars’ Rgonomic Patterns | Emily Riederer
---

# polars’ Rgonomic Patterns | Emily Riederer

url:: https://www.emilyriederer.com/post/py-rgo-polars/
up: [[sources]]

#source

A few weeks ago, I shared some [recommended modern python tools and libraries](https://www.emilyriederer.com/post/py-rgo-polars/../..\post/py-rgo/) that I believe have the most similar ergonomics for R (specifically `tidyverse`) converts. This post expands on that one with a focus on the `polars` library.

At the surface level, all data wrangling libraries have roughly the same functionality. Operations like selecting existing columns and making new ones, subsetting and ordering rows, and summarzing results is tablestakes.

However, no one falls in love with a specific library because it has the best `select()` or `filter()` function the world has ever seen. It’s the ability to easily do more complex transformations that differentiate a package expert versus novice, and the learning curve for everything that happens *after* the “Getting Started” guide ends is what can leave experts at one tool feeling so disempowered when working with another.

This deeper sense of intuition and fluency – when your technical brain knows intuitively how to translate in code what your analytical brain wants to see in the data – is what I aim to capture in the term “ergonomics”. In this post, I briefly discuss the surface-level comparison but spend most of the time exploring the deeper similarities in the functionality and workflows enabled by `polars` and `dplyr`.

## What are `dplyr`’s ergonomics?

To claim `polars` has a similar aesthetic and user experience as `dplyr`, we first have to consider what the heart of `dplyr`‘s ergonomics actually is. The explicit design philosophy is described in the developers’ writings on [tidy design principles](https://design.tidyverse.org/unifying.html), but I’ll blend those official intended principles with my personal definitions based on the lived user experience.

* Consistent:
  * Function names are highly consistent (e.g. snake case verbs) with dependable inputs and outputs (mostly dataframe-in dataframe-out) to increase intuition, reduce mistakes, and eliminate surprises
  * Metaphors extend throughout the codebase. For example `group_by()` + `summarize()` or `group_by()` + `mutate()` do what one might expect (aggregation versus a window function) instead of requiring users to remember arbitrary command-specific syntax
  * Always returns a new dataframe versus modifying in-place so code is more idempotent[^1^](#fn1) and less error prone
* Composable:
  * Functions exist at a “sweet spot” level of abstraction. We have the right primitive building blocks that users have full control to do anything they want to do with a dataframe but almost never have to write brute-force glue code. These building blocks can be layered however one choose to conduct
  * Conistency of return types leads to composability since dataframe-in dataframe-out allows for chaining
* Human-Centered:
  * Packages hit a comfortable level of abstraction somewhere between fully procedural (e.g. manually looping over array indexes without a dataframe abstraction) and fully declarative (e.g. SQL-style languages where you “request” the output but aspects like the order of operations may become unclear). Writing code is essentially articulating the steps of an analysis
  * This focus on code as recipe writing leads to the creation of useful optional functions and helpers (like my favorite – column selectors)
  * User’s rarely need to break the fourth wall of this abstraction-layer (versus thinking about things like indexes in `pandas`)

TLDR? We’ll say `dplyr`’s ergonomics allow users to express complex transformation precisely, concisely, and expressively.

So, with that, we will import `polars` and get started!

This document was made with `polars` version `0.20.4`.

## Basic Functionality

The similarities between `polars` and `dplyr`’s top-level API are already well-explored in many posts, including those by [Tidy Intelligence](https://blog.tidy-intelligence.com/posts/dplyr-vs-polars/) and [Robert Mitchell](https://robertmitchellv.com/blog/2022-07-r-python-side-by-side/r-python-side-by-side.html).

We will only do the briefest of recaps of the core data wrangling functions of each and how they can be composed in order to make the latter half of the piece make sense. We will meet these functions again in-context when discussing `dplyr` and `polar`’s more advanced workflows.

### Main Verbs

`dplyr` and `polars` offer the same foundational functionality for manipulating dataframes. Their APIs for these operations are substantially similar.

For a single dataset:

* Column selection: `select()` -> `select()` + `drop()`
* Creating or altering columns: `mutate()` -> `with_columns()`
* Subsetting rows: `filter()` -> `filter()`
* Ordering rows: `arrange()` -> `sort()`
* Computing group-level summary metrics: `group_by()` + `summarize()` -> `group_by()` + `agg()`

For multiple datasets:

* Merging on a shared key: `*_join()` -> `join(strategy = '*')`
* Stacking datasets of the same structure: `union()` -> `concat()`
* Transforming rows and columns: `pivot_{longer/wider}()`[^2^](#fn2) -> `pivot()`

### Main Verb Design

Beyond the similarity in naming, `dplyr` and `polars` top-level functions are substantially similar in their deeper design choices which impact the ergonomics of use:

* Referencing columns: Both make it easy to concisely references columns in a dataset without the repeated and redundant references to said dataset (as sometimes occurs in base R or python’s `pandas`). dplyr does this through nonstandard evaluation wherein a dataframe’s coumns can be reference directly within a data transformation function as if they were top-level variables; in `polars`, column names are wrapped in `pl.col()`
* Optional argument: Both tend to have a wide array of nice-to-have optional arguments. For example the joining capabilities in both libraries offer optional join validation[^3^](#fn3) and column renaming by appended suffix
* Consistent dataframe-in -> dataframe-out design: `dplyr` functions take a dataframe as their first argument and return a dataframe. Similarly, `polars` methods are called on a dataframe and return a dataframe which enables the chaining workflow discussed next

### Chaining (Piping)

These methods are applied to `polars` dataframes by *chaining* which should feel very familiar to R `dplyr` fans.

In `dplyr` and the broad `tidyverse`, most functions take a dataframe as their first argument and return a dataframe, enabling the piping of functions. This makes it easy to write more human-readable scripts where functions are written in the order of execution and whitespace can easily be added between lines. The following lines would all be equivalent.  

    #cb2-1transformation2(transformation1(df))
    #cb2-2
    #cb2-3df |> transformation1() |> transformation2()
    #cb2-4
    #cb2-5df |>
    #cb2-6  transformation1() |>
    #cb2-7  transformation2()
Similarly, `polars`’s main transfomration methods offer a consistent dataframe-in dataframe-out design which allows *method chaining* . Here, we similarly can write commands in order where the `.` beginning the next method call serves the same purpose as R’s pipe. And for python broadly, to achieve the same affordance for whitespace, we can wrap the entire command in parentheses.  

    #cb3-1(
    #cb3-2  df
    #cb3-3  .transformation1()
    #cb3-4  .transformation2()
    #cb3-5)
One could even say that `polars` dedication to chaining goes even deeper than `dplyr`. In `dplyr`, while core dataframe-level functions are piped, functions on specific columns are still often written in a nested fashion[^4^](#fn4)  

    #cb4-1df %>% mutate(z = g(f(a)))

In contrast, most of `polars` column-level transformation methods also make it ergonomic to keep the same literate left-to-right chaining within column-level definitions with the same benefits to readability as for dataframe-level operations.  

    #cb5-1df.with_columns(z = pl.col('a').f().g())

## Advanced Wrangling

Beyond the surface-level similarity, `polars` supports some of the more complex ergonomics that `dplyr` users may enjoy. This includes functionality like:

* expressive and explicit syntax for transformations across multiple rows
* concise helpers to identify subsets of columns and apply transformations
* consistent syntax for window functions within data transformation operations
* the ability to work with nested data structures

Below, we will examine some of this functionality with a trusty fake dataframe.[^5^](#fn5) As with `pandas`, you can make a quick dataframe in `polars` by passing a dictionary to `pl.DataFrame()`.  

    #cb6-1import polars as pl 
    #cb6-2
    #cb6-3df = pl.DataFrame({'a':[1,1,2,2], 
    #cb6-4                   'b':[3,4,5,6], 
    #cb6-5                   'c':[7,8,9,0]})
    #cb6-6df.head()
shape: (4, 3)

|  a  |  b  |  c  |
| i64 | i64 | i64 |
|-----|-----|-----|
| 1   | 3   | 7   |
| 1   | 4   | 8   |
| 2   | 5   | 9   |
| 2   | 6   | 0   |

### Explicit API for row-wise operations

While row-wise operations are relatively easy to write ad-hoc, it can still be nice semantically to have readable and stylistically consistent code for such transformations.

`dplyr`’s [`rowwise()`](https://dplyr.tidyverse.org/articles/rowwise.html) eliminates ambiguity in whether subsequent functions should be applied element-wise or collectively. Similiarly, `polars` has explicit `*_horizontal()` functions.  

    #cb7-1df.with_columns(
    #cb7-2  b_plus_c = pl.sum_horizontal(pl.col('b'), pl.col('c')) 
    #cb7-3)
shape: (4, 4)

|  a  |  b  |  c  | b_plus_c |
| i64 | i64 | i64 |   i64    |
|-----|-----|-----|----------|
| 1   | 3   | 7   | 10       |
| 1   | 4   | 8   | 12       |
| 2   | 5   | 9   | 14       |
| 2   | 6   | 0   | 6        |

### Column Selectors

`dplyr`’s [column selectors](https://dplyr.tidyverse.org/reference/select.html) dynamically determine a set of columns based on pattern-matching their names (e.g. `starts_with()`, `ends_with()`), data types, or other features. I’ve previously [written](https://www.emilyriederer.com/post/py-rgo-polars/../..\post/column-name-contracts/) and [spoken](https://www.emilyriederer.com/post/py-rgo-polars/../..\talk/col-names-contract/) at length about how transformative this functionality can be when paired with

`polars` has a similar set of [column selectors](https://docs.pola.rs/py-polars/html/reference/selectors.html). We’ll import them and see a few examples.  

    #cb8-1import polars.selectors as cs

To make things more interesting, we’ll also turn one of our columns into a different data type.  

    #cb9-1df = df.with_columns(pl.col('a').cast(pl.Utf8))

#### In `select`

We can select columns based on name or data type and use one or more conditions.  

    #cb10-1df.select(cs.starts_with('b') | cs.string())

shape: (4, 2)

|  b  |  a  |
| i64 | str |
|-----|-----|
| 3   | "1" |
| 4   | "1" |
| 5   | "2" |
| 6   | "2" |

Negative conditions also work.  
shape: (4, 2)

|  b  |  c  |
| i64 | i64 |
|-----|-----|
| 3   | 7   |
| 4   | 8   |
| 5   | 9   |
| 6   | 0   |

#### In `with_columns`

Column selectors can play multiple rows in the transformation context.

The same transformation can be applied to multiple columns. Below, we find all integer variables, call a method to add 1 to each, and use the `name.suffix()` method to dynamically generate descriptive column names.  

    #cb12-1df.with_columns(
    #cb12-2  cs.integer().add(1).name.suffix("_plus1")
    #cb12-3)
shape: (4, 5)

|  a  |  b  |  c  | b_plus1 | c_plus1 |
| str | i64 | i64 |   i64   |   i64   |
|-----|-----|-----|---------|---------|
| "1" | 3   | 7   | 4       | 8       |
| "1" | 4   | 8   | 5       | 9       |
| "2" | 5   | 9   | 6       | 10      |
| "2" | 6   | 0   | 7       | 1       |

We can also use selected variables within transformations, like the rowwise sums that we just saw earlier.  

    #cb13-1df.with_columns(
    #cb13-2  row_total = pl.sum_horizontal(cs.integer())
    #cb13-3)
shape: (4, 4)

|  a  |  b  |  c  | row_total |
| str | i64 | i64 |    i64    |
|-----|-----|-----|-----------|
| "1" | 3   | 7   | 10        |
| "1" | 4   | 8   | 12        |
| "2" | 5   | 9   | 14        |
| "2" | 6   | 0   | 6         |

#### In `group_by` and `agg`

Column selectors can also be passed as inputs anywhere else that one or more columns is accepted, as with data aggregation.  

    #cb14-1df.group_by(cs.string()).agg(cs.integer().sum())

shape: (2, 3)

|  a  |  b  |  c  |
| str | i64 | i64 |
|-----|-----|-----|
| "1" | 7   | 15  |
| "2" | 11  | 9   |

### Consistent API for Window Functions

Window functions are another incredibly important tool in any data wrangling language but seem criminally undertaught in introductory analysis classes. Window functions allows you to apply aggregation *logic* over subgroups of data while preserving the original *grain* of the data (e.g. in a table of all customers and orders and a column for the max purchase account by customer).

`dplyr` make window functions trivially easy with the `group_by()` + `mutate()` pattern, invoking users’ pre-existing understanding of how to write aggregation logic and how to invoke transformations that preserve a table’s grain.

`polars` takes a slightly different but elegant approach. Similarly, it reuses the core `with_columns()` method for window functions. However, it uses a more SQL-reminiscent specification of the “window” in the column definition versus a separate grouping statement. This has the added advantage of allowing one to use multiple window functions with different windows in the same `with_columns()` call if you should so choose.

A simple window function tranformation can be done by calling `with_columns()`, chaining an aggregation method onto a column, and following with the `over()` method to define the window of interest.  

    #cb15-1df.with_columns(
    #cb15-2  min_b = pl.col('b').min().over('a')
    #cb15-3)
shape: (4, 4)

|  a  |  b  |  c  | min_b |
| str | i64 | i64 |  i64  |
|-----|-----|-----|-------|
| "1" | 3   | 7   | 3     |
| "1" | 4   | 8   | 3     |
| "2" | 5   | 9   | 5     |
| "2" | 6   | 0   | 5     |

The chaining over and aggregate and `over()` can follow any other arbitrarily complex logic. Here, it follows a basic “case when”-type statement that creates an indicator for whether column b is null.  

    #cb16-1df.with_columns(
    #cb16-2  n_b_odd = pl.when( (pl.col('b') % 2) == 0)
    #cb16-3              .then(1)
    #cb16-4              .otherwise(0)
    #cb16-5              .sum().over('a')
    #cb16-6)
shape: (4, 4)

|  a  |  b  |  c  | n_b_odd |
| str | i64 | i64 |   i32   |
|-----|-----|-----|---------|
| "1" | 3   | 7   | 1       |
| "1" | 4   | 8   | 1       |
| "2" | 5   | 9   | 1       |
| "2" | 6   | 0   | 1       |

### List Columns and Nested Frames

While the R `tidyverse`’s raison d’etre was originally around the design of heavily normalize [tidy data](https://vita.had.co.nz/papers/tidy-data.pdf), modern data and analysis sometimes benefits from more complex and hierarchical data structures. Sometimes data comes to us in nested forms, like from an API[^6^](#fn6), and other times nesting data can help us perform analysis more effectively[^7^](#fn7) Recognizing these use cases, `tidyr` provides many capability for the creation and manipulation of [nested data](https://tidyr.tidyverse.org/articles/nest.html) in which a single cell contains values from multiple columns or sometimes even a whoel miniature dataframe.

`polars` makes these operations similarly easy with its own version of structs (list columns) and arrays (nested dataframes).

#### List Columns & Nested Frames

List columns that contain multiple key-value pairs (e.g. column-value) in a single column can be created with `pl.struct()` similar to R’s `list()`.  

    #cb17-1df.with_columns(list_col = pl.struct( cs.integer() ))

shape: (4, 4)

|  a  |  b  |  c  | list_col  |
| str | i64 | i64 | struct[2] |
|-----|-----|-----|-----------|
| "1" | 3   | 7   | {3,7}     |
| "1" | 4   | 8   | {4,8}     |
| "2" | 5   | 9   | {5,9}     |
| "2" | 6   | 0   | {6,0}     |

These structs can be further be aggregated across rows into miniature datasets.  

    #cb18-1df.group_by('a').agg(list_col = pl.struct( cs.integer() ) )

shape: (2, 2)

|  a  |    list_col     |
| str | list[struct[2]] |
|-----|-----------------|
| "2" | [{5,9}, {6,0}]  |
| "1" | [{3,7}, {4,8}]  |

In fact, this could be a good use case for our column selectors! If we have many columns we want to keep unnested and many we want to next, it could be efficient to list out only the grouping variables and create our nested dataset by examining matches.  

    #cb19-1cols = ['a']
    #cb19-2(df
    #cb19-3  .group_by(cs.by_name(cols))
    #cb19-4  .agg(list_col = pl.struct(~cs.by_name(cols)))
    #cb19-5)
shape: (2, 2)

|  a  |    list_col     |
| str | list[struct[2]] |
|-----|-----------------|
| "2" | [{5,9}, {6,0}]  |
| "1" | [{3,7}, {4,8}]  |

#### Undoing

Just as we constructed our nested data, we can denormalize it and return it to the original state in two steps. To see this, we can assign the nested structure above as `df_nested`.  

    #cb20-1df_nested = df.group_by('a').agg(list_col = pl.struct( cs.integer() ) )

First `explode()` returns the table to the original grain, leaving use with a single struct in each row.  

    #cb21-1df_nested.explode('list_col')

shape: (4, 2)

|  a  | list_col  |
| str | struct[2] |
|-----|-----------|
| "1" | {3,7}     |
| "1" | {4,8}     |
| "2" | {5,9}     |
| "2" | {6,0}     |

Then, `unnest()` unpacks each struct and turns each element back into a column.  

    #cb22-1df_nested.explode('list_col').unnest('list_col')

shape: (4, 3)

|  a  |  b  |  c  |
| str | i64 | i64 |
|-----|-----|-----|
| "1" | 3   | 7   |
| "1" | 4   | 8   |
| "2" | 5   | 9   |
| "2" | 6   | 0   |

## Footnotes

1. Meaning you can’t get the same result twice because if you rerun the same code the input has already been modified[↩︎](#fnref1)

2. Of the `tidyverse` funtions mentioned so far, this is the only one found in `tidyr` not `dplyr`[↩︎](#fnref2)

3. That is, validating an assumption that joins should have been one-to-one, one-to-many, etc.[↩︎](#fnref3)

4. However, this is more by convention. There’s not a strong reason why they would strictly need to be.[↩︎](#fnref4)

5. I recently ran a [Twitter poll](https://twitter.com/EmilyRiederer/status/1744707632886095998) on whether people prefer real, canonical, or fake datasets for learning and teaching. Fake data wasn’t the winner, but a strategy I find personally fun and useful as the unit-test analog for learning.[↩︎](#fnref5)

6. For example, an API payload for a LinkedIn user might have nested data structures representing professional experience and educational experience[↩︎](#fnref6)

7. For example, training a model on different data subsets.[↩︎](#fnref7)

