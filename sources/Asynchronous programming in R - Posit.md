---
title: Asynchronous programming in R - Posit
---

# Asynchronous programming in R - Posit

url:: https://posit.co/resources/videos/asynchronous-programming-in-r/
up: [[sources]]

#source

---

Writing regular R code is straightforward: you tell R to do something, it does it, and then it returns control back to you. This is called synchronous programming. However, if you use R to coordinate threads, processes, or network communication, the regular model may be unable to do what you want, or it may only be able to do it with a significant performance penalty. In this talk I'll explain how asynchronous programming with the **later** package can handle these kinds of programming problems. I'll also show how to provide a synchronous interface for asynchronous code, so that users will have a simple, familiar way to use your code.

[Materials](https://github.com/rstudio/rstudio-conf/tree/master/2020/Async-programming-later_Winston-Chang)
