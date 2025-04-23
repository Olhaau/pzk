[Arrow Flight](https://arrow.apache.org/blog/2019/10/13/introducing-arrow-flight/) is a general-purpose client-server framework for high performance transport of large datasets over network interfaces, built as part of the Apache Arrow project. It allows for highly efficient data transfer by several means:

* Flight removes the need for deserialization during data transfer.
* Flight allows for parallel data streaming.
* Flight employs optimizations designed to take advantage of Arrow’s columnar format.

The arrow package provides methods for connecting to Flight servers to send and receive data.  

## Prerequisites

At present the arrow package in R does not supply an independent implementation of Arrow Flight: it works by calling [Flight methods supplied by PyArrow](https://arrow.apache.org/docs/python/api/flight.html) Python, and requires both the [reticulate](https://rstudio.github.io/reticulate/) package and the Python PyArrow library to be installed. If you are using them for the first time you can install them like this:

See the [python integrations article](https://arrow.apache.org/docs/r/articles/python.html) for more details on setting up pyarrow.  

## Example

The package includes methods for starting a Python-based Flight server, as well as methods for connecting to a Flight server running elsewhere. To illustrate both sides, in one R process we’ll start a demo server:

We’ll leave that one running.

In a different R process, let’s connect to it and put some data in it.

Now, in yet another R process, we can connect to the server and pull the data we put there:

Because [flight_get()](https://arrow.apache.org/docs/r/articles/../reference/flight_get.html) returns an Arrow data structure, you can directly pipe its result into a [dplyr](https://dplyr.tidyverse.org/) workflow. See the article on [data wrangling](https://arrow.apache.org/docs/r/articles/data_wrangling.html) for more information on working with Arrow objects via a dplyr interface.
