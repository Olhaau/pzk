The arrow package provides [reticulate](https://rstudio.github.io/reticulate/) methods for passing data between R and Python within the same process. This article provides a brief overview.

Code in this article assumes arrow and reticulate are both loaded:  

## Motivation

One reason you might want to use PyArrow in R is to take advantage of functionality that is better supported in Python than in R at the current state of development. For example, at one point in time the R arrow package didn’t support [concat_arrays()](https://arrow.apache.org/docs/r/articles/../reference/concat_arrays.html) but PyArrow did, so this would have been a good use case at that time. At the time of current writing PyArrow has more comprehensive support for [Arrow Flight](https://arrow.apache.org/docs/format/Flight.html) than the R package – but see [the article on Flight support in arrow](https://arrow.apache.org/docs/r/articles/flight.html) – so that would be another instance in which PyArrow would be of benefit to R users.

A second reason that R users may want to use PyArrow is to efficiently pass data objects between R and Python. With large data sets, it can be quite costly – in terms of time and CPU cycles – to perform the copy and covert operations required to translate a native data structure in R (e.g., a data frame) to an analogous structure in Python (e.g., a Pandas DataFrame) and vice versa. Because Arrow data objects such as Tables have the same in-memory format in R and Python, it is possible to perform “zero-copy” data transfers, in which only the metadata needs to be passed between languages. As illustrated later, this drastically improves performance.  

## Installing PyArrow

To use Arrow in Python, the `pyarrow` library needs to be installed. For example, you may wish to create a Python [virtual environment](https://docs.python.org/3/library/venv.html) containing the `pyarrow` library. A virtual environment is a specific Python installation created for one project or purpose. It is a good practice to use specific environments in Python so that updating a package doesn’t impact packages in other projects.

You can perform the set up from within R. Let’s suppose you want to call your virtual environment something like `my-pyarrow-env`. Your setup code would look like this:

If you want to install a development version of `pyarrow` to the virtual environment, add `nightly = TRUE` to the [install_pyarrow()](https://arrow.apache.org/docs/r/articles/../reference/install_pyarrow.html) command:

Note that you don’t have to use virtual environments. If you prefer [conda environments](https://docs.conda.io/projects/conda/en/latest/user-guide/concepts/environments.html), you can use this setup code:

To learn more about installing and configuring Python from R, see the [reticulate documentation](https://rstudio.github.io/reticulate/articles/python_packages.html), which discusses the topic in more detail.  

## Importing PyArrow

Assuming that arrow and reticulate are both loaded in R, your first step is to make sure that the correct Python environment is being used. To do that with a virtual environment, use a command like this:

For a conda environment use the following:

Once you have done this, the next step is to import `pyarrow` into the Python session as shown below:

Executing this command in R is the equivalent of the following import in Python:

It may be a good idea to check your `pyarrow` version too, as shown below:

    ## [1] "8.0.0"

Support for passing data to and from R is included in `pyarrow` versions 0.17 and greater.  

## Using PyArrow

You can use the reticulate function [r_to_py()](https://rstudio.github.io/reticulate/reference/r-py-conversion.html) to pass objects from R to Python, and similarly you can use [py_to_r()](https://rstudio.github.io/reticulate/reference/r-py-conversion.html) to pull objects from the Python session into R. To illustrate this, let’s create two objects in R: `df_random` is an R data frame containing 100 million rows of random data, and `tb_random` is the same data stored as an Arrow Table:

Transferring the data from R to Python without Arrow is a time-consuming process because the underlying object has to be copied and converted to a Python data structure:

    ##   user  system elapsed 
    ##  0.307   5.172   5.529 
In contrast, sending the Arrow Table across happens almost instantaneously:

    ##   user  system elapsed 
    ##  0.004   0.000   0.003 
“Send”, however, isn’t really the correct word. Internally, we’re passing pointers to the data between the R and Python interpreters running together in the same process, without copying anything. Nothing is being sent: we’re sharing and accessing the same internal Arrow memory buffers.

It’s possible to send data the other direction also. For example let’s create an `Array` in pyarrow.  

    a <- pa$array(https://rdrr.io/r/base/c.html(1, 2, 3))
    a
    ## Array
    ## <double>
    ## [
    ##   1,
    ##   2,
    ##   3
    ## ]
Notice that `a` is now an `Array` object in your R session – even though you created it in Python – and you can apply R methods on it:

    ## Array
    ## <double>
    ## [
    ##   2,
    ##   3
    ## ]
Similarly, you can combine this object with Arrow objects created in R, and you can use PyArrow methods like `pa$concat_arrays()` to do so:  

    b <- Array$create(https://rdrr.io/r/base/c.html(5, 6, 7, 8, 9))
    a_and_b <- pa$concat_arrays(https://rdrr.io/r/base/list.html(a, b))
    a_and_b
    ## Array
    ## <double>
    ## [
    ##   1,
    ##   2,
    ##   3,
    ##   5,
    ##   6,
    ##   7,
    ##   8,
    ##   9
    ## ]
Now you have a single Array in R.
