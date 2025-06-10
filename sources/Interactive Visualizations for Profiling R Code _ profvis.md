---
title: Interactive Visualizations for Profiling R Code • profvis
---

# Interactive Visualizations for Profiling R Code • profvis

url:: https://profvis.r-lib.org/
up: [[sources]]

#source

profvis is a tool for visualizing code profiling data from R. It creates a web page which provides a graphical interface for exploring the data.  

## Example

To run code with profiling, wrap the expression in [profvis()](https://profvis.r-lib.org/reference/profvis.html). By default, this will result in the interactive profile visualizer opening in a web browser.

The [profvis()](https://profvis.r-lib.org/reference/profvis.html) call returns an [htmlwidget](http://www.htmlwidgets.org/), which by default when printed opens a web browser. If you wish to save the object, it won’t open the browser at first, but you can view it later by typing the variable name at the console, or calling [print()](https://rdrr.io/r/base/print.html) on it.  

    p <- https://profvis.r-lib.org/reference/profvis.html(f())

    # View it with:
    p
    # or print(p)
