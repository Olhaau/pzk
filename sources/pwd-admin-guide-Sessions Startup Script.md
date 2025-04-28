---
title: Sessions Startup Scripts – Administration Guide
keywords: source
---

# Sessions Startup Scripts – Administration Guide

url:: https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/session_startup_scripts.html
up: [[sources]]

When an RStudio Pro Session starts up, the following scripts are run:

1. [Workbench](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../#intended-audience "This feature is only available with Posit Workbench."): Any PAM session modules
2. [Workbench](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../#intended-audience "This feature is only available with Posit Workbench."): The `/etc/rstudio/rsession-profile` script
3. [Workbench](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../#intended-audience "This feature is only available with Posit Workbench."): The environment module for the R version in use, if any; see [Extended R version definitions](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../r/using_multiple_versions_of_r.html#extended-r-version-definitions)
4. [Workbench](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../#intended-audience "This feature is only available with Posit Workbench."): The prelaunch script for the R version in use, if any; see [Extended R version definitions](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../r/using_multiple_versions_of_r.html#extended-r-version-definitions)
5. [Workbench](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../#intended-audience "This feature is only available with Posit Workbench."): System and user shell profile scripts, such as `.bash_profile`
6. [Workbench](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../#intended-audience "This feature is only available with Posit Workbench."): The R process itself, inside any supervisor specified by `rsession-exec-command`
7. `.Rprofile` and other R initialization scripts; see [Initialization at the Start of a Session](https://stat.ethz.ch/R-manual/R-devel/library/base/html/Startup.html) for details
8. RStudio Pro-internal session startup scripts
9. The `rstudio.sessionInit` hook

The very last of these provides a way to run code after the R Session is fully booted and ready to use; since the RStudio Pro-internal session startup scripts only run in RStudio Pro it can be used to finalize R Sessions in RStudio Pro-specific ways, using for example methods in the `rstudioapi` package. Here is an example that prints the RStudio Pro version in new RStudio Pro Sessions:  

    #cb1-1# /opt/R/version/lib64/r/etc/Rprofile.site
    #cb1-2setHook("rstudio.sessionInit", function(newSession) {
    #cb1-3  if (newSession)
    #cb1-4    message("Welcome to RStudio ", rstudioapi::getVersion())
    #cb1-5}, action = "append")
Back to top
