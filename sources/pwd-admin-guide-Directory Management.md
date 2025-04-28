---
title: Directory Management – Administration Guide
keywords: source
---

# Directory Management – Administration Guide

url:: https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/directory_management.html
up: [[sources]]

### Working Directories

The default working directory for both new RStudio Pro Sessions and new R projects is the user’s home directory (`~`). You can change this behavior via the `session-default-working-dir` and `session-default-new-project-dir` configuration parameters within the `rsession.conf` config file.

For example, to set the default values to “~/working” and “~/projects” you’d use the following configuration:  

    #cb1-1# /etc/rstudio/rsession.conf
    #cb1-2session-default-working-dir=~/working
    #cb1-3session-default-new-project-dir=~/projects
You should ensure that users have the permissions required to write to the specified default directories. The specified directories will be automatically created if they don’t already exist.

Note that these settings control only the *default* working and new project directories (users can still override these settings locally if they choose to).

### Restricted Directories

This setting only applies to RStudio Pro’s IDE web interface. Users will still have access to any files on the system that their system-level permissions allow by using R itself and/or the Terminal interface.

Follow security best practices by relying on operating system-level file permissions, and consider using `chroot` or containers, *not* front end restrictions, to guard access to sensitive content and files.

Like most IDEs, RStudio Pro allows users to open and edit any file to which they have read or write permission (respectively) at the filesystem level. However, in some environments it’s important to restrict access to system files from web front ends. RStudio Pro can optionally enforce a restrictive mode for most directories, which will prevent users from using the IDE to open files in the directories. This is enabled by the `restrict-directory-view` option as follows:  

    #cb2-1# /etc/rstudio/rsession.conf
    #cb2-2restrict-directory-view=1
When enabled, RStudio Pro users will only have access to open files from the following in the IDE:

* Home directories
* R and R library directories
* RStudio Pro-specific directories
* User/session specific temporary directories

If you wish to allow users to open and view files in a directory that RStudio Pro would ordinarily forbid access to, you can change the `directory-view-allow-list` setting. This setting accepts a list of directories to enable access to, separated by `:`. For example, if you wish to allow users to open files from `/var/run` and `/usr/share/examples`:  

    #cb3-1# /etc/rstudio/rsession.conf
    #cb3-2directory-view-allow-list=/var/run:/usr/share/examples
Back to top
