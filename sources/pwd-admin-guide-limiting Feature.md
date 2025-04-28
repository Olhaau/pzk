---
title: Feature Limits – Administration Guide
keywords: source
---

# Feature Limits – Administration Guide

url:: https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/feature_limits.html
up: [[sources]]

[Workbench](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../#intended-audience "This feature is only available with Posit Workbench.")

Posit Workbench has a number of other limits that can be configured. This section describes these limits.  
The settings discussed in this section are specified in the `/etc/rstudio/rsession.conf` file (rather than the `rserver.conf` file).

Note that these settings are specified in the `/etc/rstudio/rsession.conf` file (rather than the `rserver.conf` file previously referenced).

### Disabling access to features

In addition to the limits on package installation and CRAN repository editing described in [Package Installation](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../rstudio_pro_sessions/package_installation.html), there are a number of other limits that can be specified in `/etc/rstudio/rsession.conf`. The following describes all of the options that can be used in `rsession.conf` to limit features.


`allow-vcs`

:   Allow access to Git and SVN version control features.


`allow-vcs-executable-edit`

:   Allow editing of the underlying Git or SVN executable.


`allow-package-installation`

:   Allow installation of packages using the Packages Pane (note that even if this is set to 0 it’s still possible to install packages using `utils::install.packages` from the command line).


`allow-r-cran-repos-edit`

:   Allow editing of the CRAN repository used for package downloads (note that it’s still possible to specify an alternate repository using the `repos` parameter of `utils::install.packages`).


`allow-shell`

:   Enable integrated terminal feature (note that it’s still possible to execute shell commands using the `system` function).


`allow-terminal-websockets`

:   Allow integrated terminal feature to use WebSockets for better responsiveness.


`allow-file-downloads`

:   Allow downloading files using the Export command in the Files Pane.


`allow-file-uploads`

:   Allow uploading files using the Upload command in the Files pane.


`allow-external-publish`

:   Allow content to be published to external (cloud) services. This includes publishing HTML documents created with R Markdown or R Presentations to RPubs (http://rpubs.com), and publishing Shiny applications and documents to ShinyApps.io (http://shinyapps.io). Note that this just removes the relevant user interface elements in the IDE, and that it may still be possible for users to publish content using the R console.


`allow-publish`

:   Allow content to be published. If specified, this option removes all user interface elements related to publishing content from the IDE, and overrides `allow-external-publish`.


`allow-launcher-jobs`

:   Allow running standalone ad hoc Job Launcher jobs from the Jobs pane.

All of these features are enabled by default. Specify `0` to disable access to the feature.

Note that these options should be specified in the `/etc/rstudio/rsession.conf` configuration file (rather than the main rserver.conf configuration file).

### Maximum file upload size

You can limit the maximum size of a file upload by using the `limit-file-upload-size-mb` setting. For example, the following limits file uploads to 100MB:  

```
/etc/rstudio/rsession.conf
```

    #cb1-1limit-file-upload-size-mb=100

The default behavior is no limit on the size of file uploads.

### CPU time per computation

If you want to prevent runaway computations that consume 100% of the CPU you can set the maximum number of minutes to allow top-level R computations to run for using the `limit-cpu-time-minutes` setting. For example:  

```
/etc/rstudio/rsession.conf
```

    #cb2-1limit-cpu-time-minutes=30

This specifies that no top level computation entered at the R console should run for more than 30 minutes. This constraint is implemented by calling the R `setTimeLimit` function immediately prior to handing off console commands to R. As a result it is possible for a particular script to override this behavior if it knows that it may exceed the threshold. This would be done as follows:  

    #cb3-1setTimeLimit(cpu = Inf)
    #cb3-2# Long running R code here...
### XFS disk quotas

If your system uses the XFS file system (<http://en.wikipedia.org/wiki/XFS>) then Posit Workbench can be configured to notify users when they come close to or exceed their disk quota. You can enable this using the `limit-xfs-disk-quota` setting. For example:  

```
/etc/rstudio/rsession.conf
```

The user’s XFS disk quota will be checked when the RStudio Pro IDE loads and a warning message will be displayed if they are near to or over their quota.
Back to top
