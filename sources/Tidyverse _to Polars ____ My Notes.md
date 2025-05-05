---
title: Tidyverse 🪐to Polars 🐻❄️: My Notes
---

# Tidyverse 🪐to Polars 🐻❄️: My Notes

url:: https://www.r-bloggers.com/2024/12/tidyverse-%F0%9F%AA%90to-polars-%F0%9F%90%BB%E2%80%8D%E2%9D%84%EF%B8%8F-my-notes/
up: [[sources]]

#source

> I found `Polars` syntax is quite similar to `dplyr`. And the way that we can chain the functions makes it even more familiar! It was fun learning the nuances, now it’s time to put them into practice! Wish me luck! 🍀

## Motivation

In preparation for using more Python in 2025 and also to speak more of the same language with our datathon team, I’ve decided to practice `Polars` in Python thinking in R first. Below is my notes to myself, hopefully I’ll be able to refer back and improve this more as I use more of this for the next month. Wish me luck!

![](https://i2.wp.com/www.kenkoonwong.com/blog/polars/polars.png?w=578&ssl=1)

## Objectives

* [Create A Dataframe](https://www.kenkoonwong.com/blog/polars/#data)
* [Filter, Select, Summarize, Across](https://www.kenkoonwong.com/blog/polars/#basic)
* [Mutate, Paste](https://www.kenkoonwong.com/blog/polars/#basic2)
* [Extract](https://www.kenkoonwong.com/blog/polars/#basic3)
* [Case_when](https://www.kenkoonwong.com/blog/polars/#basic4)
* [Join / Merge](https://www.kenkoonwong.com/blog/polars/#join)
* [To Dummies/Pivot/Unpivot](https://www.kenkoonwong.com/blog/polars/#pivot)
* [Helpful Resources](https://www.kenkoonwong.com/blog/polars/#resource)
* [Lessons Learnt](https://www.kenkoonwong.com/blog/polars/#lessons)

## Create A Dataframe

#### Tidyverse

```
library(tidyverse)
library(reticulate)
use_virtualenv('path/to/your/environment')

df <- tibble(
  name = c("Alice", "Bob", "Charlie", "Ken", "Steven", "Carlos"),
  age = c(30, 25, 35, 50, 60, 58),
  city = c("New York", "San Francisco", "Tokyo", "Toronto", "Lima", "Cleveland"),
  address = c("123 Main St, Ontario, OH", "123 Main St, Calgary, AB", "456-7890, Tokyo, NY",
              "49494 Exchange St, Toronto, ON", "1010 Gb st, Lima, OH", "666 Heaven dr, Cleveland, OH"),
  phone_number = c("123-456-7890", "987-654-3210", "098-765-4332", "111-232-4141", 
                  "505-402-6060", "909-435-1000"),
  email = c("[email protected]", "[email protected]", "[email protected]", 
            "[email protected]", "[email protected]", "[email protected]"),
  salary = c(50000, 45000, 60000, 20000, 40000, 30000),
  department = c("Engineering", "Marketing", "Finance", "Marketing", "Marketing", "Finance"),
  hire_date = c("2010-01-01", "2012-05-15", "2015-10-01", "2010-04-01", 
                "2009-10-30", "2005-11-12"),
  status = c("Active", "Inactive", "Active", "Inactive", "Active", "Active"),
  salary_increase_percentage = c(10, 5, 15, 10, 10, 5),
  years_of_service = c(5, 3, 7, 10, 10, 12),
  bonus_amount = c(2000, 1500, 3000, 5000, 3000, 2000),
  performance_rating = c(4, 3, 5, 5, 4, 4),
  performance_reviews_count = c(2, 1, 3, 3, 4, 5),
  performance_reviews_last_updated = c("2022-05-01", "2021-07-15", "2022-08-31",
                                     "2024-10-30", "2023-01-02", "2024-12-12")
)
```

#### Polars

```
import polars as pl

df = pl.DataFrame({
    "name": ["Alice", "Bob", "Charlie","Ken","Steven","Carlos"],
    "age": [30, 25, 35, 50, 60, 58],
    "city": ["New York", "San Francisco", "Tokyo","Toronto","Lima","Cleveland"],
    "address" : ["123 Main St, Ontario, OH","123 Main St, Calgary, AB", "456-7890, Tokyo, NY","49494 Exchange St, Toronto, ON","1010 Gb st, Lima, OH","666 Heaven dr, Cleveland, OH"],
    "phone_number" : ["123-456-7890", "987-654-3210", "098-765-4332","111-232-4141","505-402-6060","909-435-1000"],
    "email" : ["[email protected]", "[email protected]", "[email protected]","[email protected]","[email protected]","[email protected]"],
    "salary" : [50000, 45000, 60000,20000,40000,30000],
    "department" : ["Engineering", "Marketing", "Finance","Marketing","Marketing","Finance"],
    "hire_date" : ["2010-01-01", "2012-05-15", "2015-10-01", "2010-04-01","2009-10-30","2005-11-12"],
    "status" : ["Active", "Inactive", "Active","Inactive","Active","Active"],
    "salary_increase_percentage" : [10, 5, 15,10,10,5],
    "years_of_service" : [5, 3, 7,10,10,12],
    "bonus_amount" : [2000, 1500, 3000,5000,3000,2000],
    "performance_rating" : [4, 3, 5, 5, 4, 4],
    "performance_reviews_count" : [2, 1, 3, 3, 4, 5],
    "performance_reviews_last_updated" : ["2022-05-01", "2021-07-15", "2022-08-31", "2024-10-30","2023-01-02","2024-12-12"]
})
```

## Filter, Select, Summarize, Across

* Filter records where age is greater and equal to 30
* return columns with name:address, and columns that starts with performance* and salary*
* return mean of values across all numeric data

#### Tidyverse

```
df |>
  filter(age >= 30) |>
  select(1:3, starts_with("performance"), starts_with("salary")) |> 
  summarize(across(.cols = where(is.numeric), .fns = mean, .names = "mean_{.col}"))

## # A tibble: 1 × 5
##   mean_age mean_performance_rating mean_performance_reviews_count mean_salary
##      <dbl>                   <dbl>                          <dbl>       <dbl>
## 1     46.6                     4.4                            3.4       40000
## # ℹ 1 more variable: mean_salary_increase_percentage <dbl>
```

#### Polars

```
df \
    .filter(pl.col('age') >= 30) \
    .select(df.columns[0:4]+['^performance.*$','^salary.*$']) \
    .select(pl.col(pl.Int64).mean().name.prefix('mean_'))
```

shape: (1, 5)

| mean_age | mean_performance_rating | mean_performance_reviews_count | mean_salary | mean_salary_increase_percentage |
|   f64    |           f64           |              f64               |     f64     |               f64               |
|----------|-------------------------|--------------------------------|-------------|---------------------------------|
| 46.6     | 4.4                     | 3.4                            | 40000.0     | 10.0                            |

For some reason, for the regex above, I have to use `^` and `$` sandwiched to return those column nams that I want to include. bizzare.

## Mutate, Paste

Test 1

* make a new column called `combination_of_character`
* paste all columns with character datatype separated by ``, a space
* select the created column

#### Tidyverse

```
df |>
  rowwise() |>
  transmute(combination_of_character = paste(
      across(where(is.character)), 
      collapse = " "
    )) |>
  select(combination_of_character)

## # A tibble: 6 × 1
## # Rowwise: 
##   combination_of_character                                                      
##   <chr>                                                                         
## 1 Alice New York 123 Main St, Ontario, OH 123-456-7890 [email protected] Engine…
## 2 Bob San Francisco 123 Main St, Calgary, AB 987-654-3210 [email protected] Marke…
## 3 Charlie Tokyo 456-7890, Tokyo, NY 098-765-4332 [email protected] Finance 20…
## 4 Ken Toronto 49494 Exchange St, Toronto, ON 111-232-4141 [email protected] Marketi…
## 5 Steven Lima 1010 Gb st, Lima, OH 505-402-6060 [email protected] Marketing …
## 6 Carlos Cleveland 666 Heaven dr, Cleveland, OH 909-435-1000 [email protected] …
```

#### Polars

```
df \
    .with_columns(
        pl.concat_str(
            pl.col(pl.String), separator=" "
        ).alias('combination_of_character')
    ) \
    .select(pl.col('combination_of_character'))
```

shape: (6, 1)

|     combination_of_character     |
|               str                |
|----------------------------------|
| "Alice New York 123 Main St, On… |
| "Bob San Francisco 123 Main St,… |
| "Charlie Tokyo 456-7890, Tokyo,… |
| "Ken Toronto 49494 Exchange St,… |
| "Steven Lima 1010 Gb st, Lima, … |
| "Carlos Cleveland 666 Heaven dr… |

#### Tidyverse

Test 2

* make a new column called `age_salary`
* glue column `age` and `salary` together with `-` between
* select columns `name` and `age_salary`

```
df |> 
  mutate(age_salary = paste0(age, "-", salary)) |>
  select(name, age_salary)

## # A tibble: 6 × 2
##   name    age_salary
##   <chr>   <chr>     
## 1 Alice   30-50000  
## 2 Bob     25-45000  
## 3 Charlie 35-60000  
## 4 Ken     50-20000  
## 5 Steven  60-40000  
## 6 Carlos  58-30000
```

#### Polars

```
df \
    .with_columns(
        age_salary=pl.format('{}-{}',pl.col('age'),pl.col('salary'))
    ) \
    .select(pl.col('name','age_salary'))
```

shape: (6, 2)

|   name    | age_salary |
|    str    |    str     |
|-----------|------------|
| "Alice"   | "30-50000" |
| "Bob"     | "25-45000" |
| "Charlie" | "35-60000" |
| "Ken"     | "50-20000" |
| "Steven"  | "60-40000" |
| "Carlos"  | "58-30000" |

If it’s just 1 column, you can use this format `age_salary=` to name the column, otherwise you’d have to use `alias` to name it if there are multple columns

* create a new column `area_code_and_salary`
* paste street number (extract it from `address`) with a space and the the column `salary`
* select `area_code_and_salary`

#### Tidyverse

```
df |>
  mutate(area_code_and_salary = paste0(str_extract(address, "\\d{0,5}"), " ", salary)) |>
  select(area_code_and_salary)

## # A tibble: 6 × 1
##   area_code_and_salary
##   <chr>               
## 1 123 50000           
## 2 123 45000           
## 3 456 60000           
## 4 49494 20000         
## 5 1010 40000          
## 6 666 30000
```

#### Polars

```
df \
    .select(
        pl.concat_str(
            pl.col('address').str.extract(r'^(\d{0,5})'),
            pl.lit(" "),
            pl.col('salary')
        ).alias('area_code_and_salary')
    )
```

shape: (6, 1)

| area_code_and_salary |
|         str          |
|----------------------|
| "123 50000"          |
| "123 45000"          |
| "456 60000"          |
| "49494 20000"        |
| "1010 40000"         |
| "666 30000"          |

Have to use `pl.lit(' ')` for any constant string

## Case_when

Test 1

* create a new column called `familiarity`
* if `address` contains `OH`, then return `local`
* if `address` contains `NY`, then return `foodie`
* otherwise return `elsewhere`

#### Tidyverse

```
df |>
  mutate(familiarity = case_when(
    str_detect(address, "OH") ~ "local",
    str_detect(address, "NY") ~ "foodie",
    TRUE ~ "elsewhere"
  )) 

## # A tibble: 6 × 17
##   name      age city      address phone_number email salary department hire_date
##   <chr>   <dbl> <chr>     <chr>   <chr>        <chr>  <dbl> <chr>      <chr>    
## 1 Alice      30 New York  123 Ma… 123-456-7890 alic…  50000 Engineeri… 2010-01-…
## 2 Bob        25 San Fran… 123 Ma… 987-654-3210 bob@…  45000 Marketing  2012-05-…
## 3 Charlie    35 Tokyo     456-78… 098-765-4332 char…  60000 Finance    2015-10-…
## 4 Ken        50 Toronto   49494 … 111-232-4141 ken@…  20000 Marketing  2010-04-…
## 5 Steven     60 Lima      1010 G… 505-402-6060 step…  40000 Marketing  2009-10-…
## 6 Carlos     58 Cleveland 666 He… 909-435-1000 carl…  30000 Finance    2005-11-…
## # ℹ 8 more variables: status <chr>, salary_increase_percentage <dbl>,
## #   years_of_service <dbl>, bonus_amount <dbl>, performance_rating <dbl>,
## #   performance_reviews_count <dbl>, performance_reviews_last_updated <chr>,
## #   familiarity <chr>
```

#### Polars

```
df \
    .with_columns([  
        pl.when(pl.col('address').str.contains('OH'))
        .then(pl.lit('local'))
        .when(pl.col('address').str.contains('NY'))
        .then(pl.lit('foodie'))
        .otherwise(pl.lit('elsewhere'))
        .alias('familiarity')
    ])
```

shape: (6, 17)

|   name    | age |      city       |             address              |  phone_number  |                                    email                                     | salary |  department   |  hire_date   |   status   | salary_increase_percentage | years_of_service | bonus_amount | performance_rating | performance_reviews_count | performance_reviews_last_updated | familiarity |
|    str    | i64 |       str       |               str                |      str       |                                     str                                      |  i64   |      str      |     str      |    str     |            i64             |       i64        |     i64      |        i64         |            i64            |               str                |     str     |
|-----------|-----|-----------------|----------------------------------|----------------|------------------------------------------------------------------------------|--------|---------------|--------------|------------|----------------------------|------------------|--------------|--------------------|---------------------------|----------------------------------|-------------|
| "Alice"   | 30  | "New York"      | "123 Main St, Ontario, OH"       | "123-456-7890" | "[[email protected]](https://www.r-bloggers.com/cdn-cgi/l/email-protection)" | 50000  | "Engineering" | "2010-01-01" | "Active"   | 10                         | 5                | 2000         | 4                  | 2                         | "2022-05-01"                     | "local"     |
| "Bob"     | 25  | "San Francisco" | "123 Main St, Calgary, AB"       | "987-654-3210" | "[[email protected]](https://www.r-bloggers.com/cdn-cgi/l/email-protection)" | 45000  | "Marketing"   | "2012-05-15" | "Inactive" | 5                          | 3                | 1500         | 3                  | 1                         | "2021-07-15"                     | "elsewhere" |
| "Charlie" | 35  | "Tokyo"         | "456-7890, Tokyo, NY"            | "098-765-4332" | "[[email protected]](https://www.r-bloggers.com/cdn-cgi/l/email-protection)" | 60000  | "Finance"     | "2015-10-01" | "Active"   | 15                         | 7                | 3000         | 5                  | 3                         | "2022-08-31"                     | "foodie"    |
| "Ken"     | 50  | "Toronto"       | "49494 Exchange St, Toronto, ON" | "111-232-4141" | "[[email protected]](https://www.r-bloggers.com/cdn-cgi/l/email-protection)" | 20000  | "Marketing"   | "2010-04-01" | "Inactive" | 10                         | 10               | 5000         | 5                  | 3                         | "2024-10-30"                     | "elsewhere" |
| "Steven"  | 60  | "Lima"          | "1010 Gb st, Lima, OH"           | "505-402-6060" | "[[email protected]](https://www.r-bloggers.com/cdn-cgi/l/email-protection)" | 40000  | "Marketing"   | "2009-10-30" | "Active"   | 10                         | 10               | 3000         | 4                  | 4                         | "2023-01-02"                     | "local"     |
| "Carlos"  | 58  | "Cleveland"     | "666 Heaven dr, Cleveland, OH"   | "909-435-1000" | "[[email protected]](https://www.r-bloggers.com/cdn-cgi/l/email-protection)" | 30000  | "Finance"     | "2005-11-12" | "Active"   | 5                          | 12               | 2000         | 4                  | 5                         | "2024-12-12"                     | "local"     |

Test 2

* convert `name` data to lowercase
* create new column called `email_name` and extract `email` before the `@`
* select columns that starts with `name` or end with `name`
* create a new column called `same?`
* if `name` and `email_name` is the same, then return `yes`
* otherwise return `no`

#### Tidyverse

```
df |>
  mutate(
    name = tolower(name),
    email_name = str_extract(email, "^([\\d\\w]+)@", group = 1)
  ) |>
  select(starts_with("name") | ends_with("name")) |>
  mutate(`same?` = case_when(
    name == email_name ~ "yes",
    TRUE ~ "no"))

## # A tibble: 6 × 3
##   name    email_name   `same?`
##   <chr>   <chr>        <chr>  
## 1 alice   alice        yes    
## 2 bob     bob          yes    
## 3 charlie charlie      yes    
## 4 ken     ken          yes    
## 5 steven  stephencurry no     
## 6 carlos  carlos       yes
```

#### Polars

```
df \
    .with_columns(
        [
        pl.col('name').str.to_lowercase(),    
        pl.col('email').str.extract(r'^([\d\w]+)@', group_index = 1)
        .alias('email_name')
        ]
    ) \
    .select([
        pl.col('^name|.*name$'),
        pl.when(
            pl.col('name') == pl.col('email_name')).then(pl.lit('yes'))
            .otherwise(pl.lit('no'))
            .alias('same?')
    ]
        )
```

shape: (6, 3)

|   name    |   email_name   | same? |
|    str    |      str       |  str  |
|-----------|----------------|-------|
| "alice"   | "alice"        | "yes" |
| "bob"     | "bob"          | "yes" |
| "charlie" | "charlie"      | "yes" |
| "ken"     | "ken"          | "yes" |
| "steven"  | "stephencurry" | "no"  |
| "carlos"  | "carlos"       | "yes" |

Learnt that apparently we cannot use `look forward or backward` in polars. Such as `.*(?=@)` to capture the `email_name`

## Group_by, Shift, Forward_Fill

* group by `department` column
* summarize by selecting `name`, new column `salary_shift` with conditions:
  * if the department only has 1 row of `salary` data, do not `shift` salary
  * if the department has more than 1 row of `salary` data, `shift by -1` of salary column
  * reason: there was a mistake in entering data for those with more than 1 row of data, apparently the actualy salary data is 1 row more
* then forward fill the `salary_shift` with the number prior in the same group

#### Tidyverse

```
df |>
  group_by(department) |>
  summarize(
    name = name,
    salary_shift = case_when(
      n() == 1 ~ salary,
      TRUE ~ lead(salary)
    )
  ) |>
 fill(salary_shift, .direction = "down")

## Warning: Returning more (or less) than 1 row per `summarise()` group was deprecated in
## dplyr 1.1.0.
## ℹ Please use `reframe()` instead.
## ℹ When switching from `summarise()` to `reframe()`, remember that `reframe()`
##   always returns an ungrouped data frame and adjust accordingly.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.

## `summarise()` has grouped output by 'department'. You can override using the
## `.groups` argument.

## # A tibble: 6 × 3
## # Groups:   department [3]
##   department  name    salary_shift
##   <chr>       <chr>          <dbl>
## 1 Engineering Alice          50000
## 2 Finance     Charlie        30000
## 3 Finance     Carlos         30000
## 4 Marketing   Bob            20000
## 5 Marketing   Ken            40000
## 6 Marketing   Steven         40000
```

#### Polars

```
df \
.group_by('department') \
.agg(
    pl.col('name'),
    pl.when(pl.col('salary').len()==1).then(pl.col('salary'))
    .otherwise(pl.col('salary').shift(-1))
    .alias('salary_shift')) \
.explode('name','salary_shift') \
.with_columns(
    pl.col('salary_shift').forward_fill())
```

shape: (6, 3)

|  department   |   name    | salary_shift |
|      str      |    str    |     i64      |
|---------------|-----------|--------------|
| "Engineering" | "Alice"   | 50000        |
| "Finance"     | "Charlie" | 30000        |
| "Finance"     | "Carlos"  | 30000        |
| "Marketing"   | "Bob"     | 20000        |
| "Marketing"   | "Ken"     | 40000        |
| "Marketing"   | "Steven"  | 40000        |

Apparently polars would turn the column into a nested dataframe (list) when grouped and can’t do `fill` when it’s in list? will have to `unnest` by `explode` before `fill` can be used. Unless of coure if you merge the `fill` in the same line when shifting, such as

```
df \
.group_by('department') \
.agg(
    pl.col('name'),
    pl.when(pl.col('salary').len()==1).then(pl.col('salary'))
    .otherwise(pl.col('salary').shift(-1))
    .forward_fill() 
    .alias('salary_shift'))
```

#### Is There An Easier Way to `Unnest` without Typing ALL of the columns in Polars?

Yes! I believe `pl.col, pl.select, pl.filter` all take a list of conditions. First create a list of columns you want to unnest, then use `pl.col` to select them.

```
dt = [pl.List(pl.Int64),pl.List(pl.String)]

df \
    .group_by('department', maintain_order=True) \
    .agg(  
        pl.col('name'),
        pl.col('salary_increase_percentage'),
        salary_shift = pl.when(pl.col('salary').count() == 1)  
            .then(pl.col('salary'))
            .otherwise(pl.col('salary').shift(-1))
    ) \
    .explode(pl.col(dt)) \
    .with_columns(
        pl.col('salary_shift').forward_fill()
    ) \
    .with_columns(
        new_raise = pl.col('salary_shift') * (1+pl.col('salary_increase_percentage')/100)
    )
```

shape: (6, 5)

|  department   |   name    | salary_increase_percentage | salary_shift | new_raise |
|      str      |    str    |            i64             |     i64      |    f64    |
|---------------|-----------|----------------------------|--------------|-----------|
| "Engineering" | "Alice"   | 10                         | 50000        | 55000.0   |
| "Marketing"   | "Bob"     | 5                          | 20000        | 21000.0   |
| "Marketing"   | "Ken"     | 10                         | 40000        | 44000.0   |
| "Marketing"   | "Steven"  | 10                         | 40000        | 44000.0   |
| "Finance"     | "Charlie" | 15                         | 30000        | 34500.0   |
| "Finance"     | "Carlos"  | 5                          | 30000        | 31500.0   |

## Merge / Join

* Create another mock data with dataframe called `df_dept` with columns `department` and `dept_id`

#### Tidyverse

```
df_dept <- tibble(
  department = c("Engineering", "Marketing", "Finance"),
  dept_id = c(30, 25, 20)
)
```

#### Polars

```
df_dept = pl.DataFrame({
    "department": ["Engineering", "Marketing", "Finance"],
    "dept_id": [30, 25, 20]
})
```

* left_join `df` with `df_dept`
* create new column `employee_id` by pasting:
  * {dept_id}-{random 7 digit numbers}

#### Tidyverse

```
df |>
  left_join(df_dept, by = "department") |>
  select(name, dept_id) |>
  mutate(employee_id = map_chr(dept_id, ~paste0(.x, "-", sample(1000000:9999999, 1))))

## # A tibble: 6 × 3
##   name    dept_id employee_id
##   <chr>     <dbl> <chr>      
## 1 Alice        30 30-1694470 
## 2 Bob          25 25-1696036 
## 3 Charlie      20 20-4463080 
## 4 Ken          25 25-6942432 
## 5 Steven       25 25-3012223 
## 6 Carlos       20 20-8705991
```

#### Polars

```
import random

df \
    .join(df_dept, on="department") \
    .select(['name','dept_id']) \
    .with_columns(
        employee_id = pl.format(
          '{}-{}',
          'dept_id',
          pl.Series([
            random.randint(100000, 999999) for _ in range(len(df))
            ])
            )
    )
```

shape: (6, 3)

|   name    | dept_id | employee_id |
|    str    |   i64   |     str     |
|-----------|---------|-------------|
| "Alice"   | 30      | "30-832410" |
| "Bob"     | 25      | "25-883365" |
| "Charlie" | 20      | "20-484404" |
| "Ken"     | 25      | "25-421175" |
| "Steven"  | 25      | "25-670538" |
| "Carlos"  | 20      | "20-638378" |

there is a function called `map_elements` in polars but the documentation stated that it’s inefficient, essentially using a for loop. I’m not entirely certain if list comprehension above is any more efficient. Another probably more efficent way of doing this is 2 separate process. The random number generation on another dataframe, then merge it.

## To Dummies, Pivot_longer / Pivot_wider

* create dummy variables for `department` using `name` as index or id

#### Tidyverse

```
df |>
  select(name, department) |>
  pivot_wider(id_cols = "name", names_from = "department", values_from = "department", values_fill = 0, values_fn = length, names_prefix = "department_")

## # A tibble: 6 × 4
##   name    department_Engineering department_Marketing department_Finance
##   <chr>                    <int>                <int>              <int>
## 1 Alice                        1                    0                  0
## 2 Bob                          0                    1                  0
## 3 Charlie                      0                    0                  1
## 4 Ken                          0                    1                  0
## 5 Steven                       0                    1                  0
## 6 Carlos                       0                    0                  1
```

#### Polars - `to_dummies`

```
df \
    .select(['name','department']) \
    .to_dummies(columns = 'department') 
```

shape: (6, 4)

|   name    | department_Engineering | department_Finance | department_Marketing |
|    str    |           u8           |         u8         |          u8          |
|-----------|------------------------|--------------------|----------------------|
| "Alice"   | 1                      | 0                  | 0                    |
| "Bob"     | 0                      | 0                  | 1                    |
| "Charlie" | 0                      | 1                  | 0                    |
| "Ken"     | 0                      | 0                  | 1                    |
| "Steven"  | 0                      | 0                  | 1                    |
| "Carlos"  | 0                      | 1                  | 0                    |

#### Polars - `pivot`

```
df \
    .select(['name','address']) \
    .with_columns(
        state = pl.col('address').str.extract(r'([A-Z]{2})$')
    ) \
    .select('name','state') \
    .pivot(on = 'state', index = 'name', values='state', aggregate_function='len') \
    .with_columns(
        pl.col(pl.UInt32).fill_null(0)
    ) 
```

shape: (6, 5)

|   name    | OH  | AB  | NY  | ON  |
|    str    | u32 | u32 | u32 | u32 |
|-----------|-----|-----|-----|-----|
| "Alice"   | 1   | 0   | 0   | 0   |
| "Bob"     | 0   | 1   | 0   | 0   |
| "Charlie" | 0   | 0   | 1   | 0   |
| "Ken"     | 0   | 0   | 0   | 1   |
| "Steven"  | 1   | 0   | 0   | 0   |
| "Carlos"  | 1   | 0   | 0   | 0   |

Essentially, `pivot_wider` is Polars’ `pivot`. Whereas `pivot_longer` is Polars’ `unpivot`

## Helpful Resources

* [Polars Python API documentation](https://docs.pola.rs/api/python/stable/reference/index.html)
* [Cheat sheet](https://franzdiebold.github.io/polars-cheat-sheet/Polars_cheat_sheet.pdf)
* [cookbook Polars for R](https://ddotta.github.io/cookbook-rpolars/)

## Lessons Learnt:

* `stringr::str_extract` has the parameter `group`
* We cannot use `look forward or backward` in polars
* `polars` also has `selector` for looking at `column names` more efficiently
* lots of trial and error
* going to try doing pure `Polars` for a month! Wish me luck!

If you like this article:

* please feel free to send me a [comment or visit my other blogs](https://www.kenkoonwong.com/blog/)
* please feel free to follow me on [BlueSky](https://bsky.app/profile/kenkoonwong.bsky.social), [twitter](https://twitter.com/kenkoonwong/), [GitHub](https://github.com/kenkoonwong/) or [Mastodon](https://med-mastodon.com/@kenkoonwong)
* if you would like collaborate please feel free to [contact me](https://www.kenkoonwong.com/contact/)

