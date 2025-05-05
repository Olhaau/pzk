---
title: async: Create an async function in r-lib/async: Asynchronous Computation and 'I/O'
---

# async: Create an async function in r-lib/async: Asynchronous Computation and 'I/O'

url:: https://rdrr.io/github/r-lib/async/man/async.html
up: [[sources]]

#source

## Create an async function

### Description

Create an async function, that returns a deferred value, from a regular function. If `fun` is already an async function, then it does nothing, just returns it.

### Usage

    async(fun)

### Arguments

### Details

The result function will have the same arguments, with the same default values, and the same environment as the original input function.

### Value

Async version of the original function.

### Examples

    f <- function(x) 42
    af <- async(f)
    is_async(f)
    is_async(af)
    f()
    synchronise(dx <- af())
    dx
