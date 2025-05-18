---
title: From R to excel: Part 1
---

# From R to excel: Part 1

url:: https://www.r-bloggers.com/2024/01/from-r-to-excel-part-1/
up: [[sources]]

#source

[This article was first published on
**[ZAHIER NASRUDIN](https://zahier-nasrudin.netlify.app/posts/2024-01-03-r-to-excel-part1/r-to-excel.html)**

, and kindly contributed to
[R-bloggers](https://www.r-bloggers.com/)

]. (You can report issue about the content on this page
[here](https://www.r-bloggers.com/contact-us/)

)

---

Want to share your content on R-bloggers?
[click here](https://www.r-bloggers.com/add-your-blog/)

if you have a blog, or
[here](http://r-posts.com/)

if you don't.

## Introduction

Welcome welcome.

In this blog post, we will be exploring:

1. How to export multiple data frames into a single Excel file with multiple sheets using the **`writexl`** and **`openxlsx`** packages

By the end of this post, you & I should have a solid understanding of how to export multiple data frames into 1 excel file (.xlsx)

## Purpose

> *Why do you need to export your R output to Excel?*

1. **Data Sharing**: Excel is a widely used tool for data analysis and reporting.

2. **Further Analysis**: Some users might want to perform additional analyses or calculations on the exported data using Excel’s functions, pivot tables, or other features.

3. **User Preference**: For some tasks or audiences/stakeholders, Excel might be the preferred tool due to its user-friendly interface or specific features.

## How-to

### Load library

We will first load the `dplyr` library:

### Data sets

For the data frames, we will use datasets available from the **`dplyr`** package.

Here are the datasets available in **`dplyr`**:

![](https://i1.wp.com/zahier-nasrudin.netlify.app/posts/2024-01-03-r-to-excel-part1/dplyr.png?w=578&ssl=1)

However, for this, we will focus only just four of them:

`band_instruments` (3 rows and 2 columns)

---

`band_members` (3 rows and 2 columns)

---

`star_wars` (87 rows and 14 columns)

---

`storms` (19,066 rows and 13 columns)

---

### Export (writexl)

I will demonstrate two methods for exporting to an Excel file: using the **`writexl`** and **`openxlsx`** packages

* Using [`writexl` package](https://docs.ropensci.org/writexl/)
  * First, define the data frames within a list.
  * Export using `write_xlsx`

```
library(writexl)

excel_list <- list("Instruments" = band_instruments,
                   "Members" = band_members,
                   "Star Wars" = starwars,
                   "Storms" = storms)


write_xlsx(excel_list, "file_with_sheets_version1.xlsx")
```

![](https://i1.wp.com/zahier-nasrudin.netlify.app/posts/2024-01-03-r-to-excel-part1/version1.gif?w=578&ssl=1)

If you prefer not to format the header (no bold header), you may include `format_headers = FALSE`:  

```
write_xlsx(excel_list, "file_with_sheets_version1.xlsx", format_headers = FALSE)
```

![](https://i0.wp.com/zahier-nasrudin.netlify.app/posts/2024-01-03-r-to-excel-part1/version2.gif?w=578&ssl=1)

### Export (openxlsx)

* Using [openxlsx package](https://ycphs.github.io/openxlsx/)

  * Follow the same process: define the list.
  * Export using `write.xlsx`

```
library(openxlsx)

write.xlsx(excel_list, "file_with_sheets_version2.xlsx")
```

![](https://i0.wp.com/zahier-nasrudin.netlify.app/posts/2024-01-03-r-to-excel-part1/version2.gif?w=578&ssl=1)

---

And there you have it! Now you can send the file to stakeholders who prefer Excel.  
![](https://i1.wp.com/zahier-nasrudin.netlify.app/posts/2024-01-03-r-to-excel-part1/spek.gif?w=578&ssl=1)
