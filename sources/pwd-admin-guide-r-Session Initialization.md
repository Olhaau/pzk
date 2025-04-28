---
title: Session Initialization – Administration Guide
keywords: source
---

# Session Initialization – Administration Guide

url:: https://docs.posit.co/ide/server-pro/r/session_initialization.html
up: [[sources]]

[Workbench](https://docs.posit.co/ide/server-pro/r/../#intended-audience "This feature is only available with Posit Workbench.")

In the preceding sections, we described how to allow the server to discover multiple versions of R to be used by the user. The R environment is loaded before the RStudio Pro Sessions begins execution, and several files are executed along the way that can cause problems with your R Environment if any variables are incorrectly set.

When an RStudio Pro Session is launched, the following steps occur:

1. If the file `/etc/rstudio/rsession-profile` exists, it is sourced.
2. If the R Version being loaded specifies a module to load, the user’s `~/.bashrc` file is sourced and the module is loaded using the `module load` command.
3. If the R version being loaded specifies a prelauch script to run, it is sourced.
4. A bash login shell is created, which sources the `~/.bash_profile`, `~/.bash_login` or the `~/.profile` script, whichever is found first to exist (in that order).
5. The RStudio Pro Session is launched with the environment constructed in steps 1-4.

As you can see, there are many different scripts which can affect the R environment which gets initialized when starting a new RStudio Pro Session, so play close attention to how these scripts could be affecting your particular environment.
Back to top
