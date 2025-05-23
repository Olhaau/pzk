---
title: Auditing Configuration – Administration Guide
keywords: source
---

# Auditing Configuration – Administration Guide

url:: https://docs.posit.co/ide/server-pro/auditing_and_monitoring/auditing_configuration.html
up: [[sources]]

[Workbench](https://docs.posit.co/ide/server-pro/auditing_and_monitoring/../#intended-audience "This feature is only available with Posit Workbench.") | [Enhanced](https://docs.posit.co/ide/server-pro/auditing_and_monitoring/../license_management/license_management.html#product-tiers "This feature is only available with the purchase of the Enhanced or Advanced product tier. Click the badge for additional info.") [Advanced](https://docs.posit.co/ide/server-pro/auditing_and_monitoring/../license_management/license_management.html#product-tiers "This feature is only available with the purchase of the Advanced product tier. Click the badge for additional info.")

### R console auditing

Posit Workbench can be optionally configured to audit all R console activity by writing console input and output to a central location (the `/var/lib/rstudio-server/audit/r-console` directory by default). This feature can be enabled using the `audit-r-console` setting. For example:  

    #cb1-1# /etc/rstudio/rserver.conf
    #cb1-2audit-r-console=input
This will audit all R console *input* . If you wish to record both console input and output then you can use the `all` setting. For example:  

    #cb2-1# /etc/rstudio/rserver.conf
    #cb2-2audit-r-console=all
Note that if you choose to record both input and output you’ll need considerably more storage available than if you record input only. See the [Storage Options](#storage-options) section below for additional discussion of storage requirements and configuration.

#### Data format

The R console activity for each user is written into individual files within the `r-console` data directory (by default `/var/lib/rstudio-server/audit/r-console`). The following fields are included:

|------------|----------------------------------------------------------------------------|
| session_id | Unique identifier for R session where this action occurred.                |
| project    | Path to RStudio project directory if the action occurred within a project. |
| pid        | Unix process ID where this console action occurred.                        |
| username   | Unix user which executed this console action.                              |
| timestamp  | Timestamp of action in milliseconds since the epoch.                       |
| type       | Console action type (prompt, input, output, or error).                     |
| data       | Console data associated with this action (e.g. output text).               |

The `session_id` field refers to a concurrent R session as described in the section on [Multiple R Sessions](https://docs.posit.co/ide/server-pro/auditing_and_monitoring/../rstudio_pro_sessions/multiple_r_sessions.html) (i.e. it can span multiple projects and/or pids).

The default format for the log file is CSV (Comma Separated Values). It’s also possible to write the data to [Newline Delimited JSON](http://ndjson.org/) by using the `audit-r-console-format` option. For example:  

    #cb3-1audit-r-console-format=json

Note that when using the JSON format the entire file is not a valid JSON object but rather each individual line is one. This follows the Newline Delimited JSON specification supported by several libraries including the R **jsonlite** package.

### R session auditing

Posit Workbench can be optionally configured to write an audit log of session related events (e.g., login/logout, session start/suspend/exit) to a central location (the `/var/lib/rstudio-server/audit/r-sessions` directory by default). This feature can be enabled using the `audit-r-sessions` setting. For example:  

    #cb4-1# /etc/rstudio/rserver.conf
    #cb4-2audit-r-sessions=1
Note that this is enabled by default if you are using named user licenses.  
Session auditing is only supported for RStudio IDE R Sessions and is not currently supported for Jupyter or VS Code sessions.

#### Data format

The R session event log is written by default to the file at `/var/lib/rstudio-server/audit/r-sessions/r-sessions.csv`. The following fields are included:

|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| pid       | Unix process ID the event is associated with (for auth events this will be the main `rserver` process, for session events the `rsession` process). |
| username  | Unix user that the event is associated with.                                                                                                       |
| timestamp | Timestamp of event in milliseconds since the epoch.                                                                                                |
| type      | Event type (see documentation on event types below).                                                                                               |
| data      | Event data (see documentation on event types below).                                                                                               |

The following values are valid for the event `type` field:

|-------------------------|--------------------------------------------------------------------------------------------------------------------|
| auth_login              | User logged in to Posit Workbench; `data` contains name of admin user if they are impersonating the user           |
| auth_throttled          | User temporarily blocked due to multiple login attempts (as defined by the option `auth-sign-in-throttle-seconds`) |
| auth_unlicensed         | User is locked or there is no license available                                                                    |
| auth_license_failed     | User blocked due to a failure in obtaining a license                                                               |
| auth_logout             | User logged out of Posit Workbench                                                                                 |
| auth_login_failed       | User login attempt failed because a local account may not exist                                                    |
| session_start           | R session started                                                                                                  |
| session_suicide         | R session exiting due to suicide (internal error)                                                                  |
| session_suspend         | R session exiting due to suspend                                                                                   |
| session_file_upload     | File uploaded from browser to R session; `data` field contains file name                                           |
| session_file_download   | File downloaded from R session to browser; `data` field contains file name                                         |
| session_quit            | R session exiting due to user quit                                                                                 |
| session_exit            | R session exited                                                                                                   |
| session_admin_suspend   | Administrator attempt to suspend R session; `data` field contains administrative user who initiated the event      |
| session_admin_terminate | Administrator attempt to terminate R session ; `data` field contains administrative user who initiated the event   |

The default format for the log file is CSV (Comma Separated Values). It’s also possible to write the data to [Newline Delimited JSON](http://ndjson.org/) by using the `audit-r-sessions-format` option. For example:  

    #cb5-1audit-r-sessions-format=json

Note that when using the JSON format the entire file is not a valid JSON object but rather each individual line is one. This follows the Newline Delimited JSON specification supported by several libraries including the R **jsonlite** package.

### Storage options

You can customize both the location where audit data is written as well as the maximum amount of R session event data to log (by default this is 1 GB). To specify the root directory for audit data you use the `audit-data-path` setting. For example:  

    #cb6-1# /etc/rstudio/rserver.conf
    #cb6-2audit-data-path=/audit-data
Note that this path affects the location of both R console auditing and R session auditing data. When Posit Workbench starts, it will change the ownership of this folder and its children to be owned by the `server-user` that is configured in `/etc/rstudio/rserver.conf`, as described in [Server permissions](https://docs.posit.co/ide/server-pro/auditing_and_monitoring/../access_and_security/server_permissions.html). If it is unable to change the ownership on this directory, it may be unable to write audit data under it.  
When running Posit Workbench in a Load Balanced cluster (see [Load balancing](https://docs.posit.co/ide/server-pro/auditing_and_monitoring/../load_balancing/load_balancing.html) for more details), the `audit-data-path` should be distinct for each instance of Posit Workbench to prevent the servers from overwriting each others’ data.

#### R console auditing storage options

To specify the maximum amount of data to write to an individual user’s R console log file you use the `audit-r-console-user-limit-mb` setting. For example:  

    #cb7-1# /etc/rstudio/rserver.conf
    #cb7-2audit-r-console-user-limit-mb=100
The default maximum R console log file size is 50 megabytes per-user. To configure no limit to the size of files which can be written you set the value to `0`, for example:  

    #cb8-1# /etc/rstudio/rserver.conf
    #cb8-2audit-r-console-user-limit-mb=0
If you wish for RStudio to automatically roll the log files once the maximum size is reached, set the `audit-r-console-user-limit-months` setting. For example:  

    #cb9-1# /etc/rstudio/rserver.conf
    #cb9-2audit-r-console-user-limit-months=2
This will cause log files to be rolled over once the maximum size is reached, and only two months of data will be kept. Note that this setting is not set by default.

Note that if the month limit is not set, then log files will not be rotated automatically. Depending on the number of users and their activity level this means that you should either create a scheduled (e.g. cron) job to periodically move the files off the server onto auxiliary storage and/or ensure that the volume they are stored on has sufficient capacity.

#### R session auditing storage options

To specify the maximum amount of R session event data to log you use the `audit-r-sessions-limit-mb` setting. For example:  

    #cb10-1# /etc/rstudio/rserver.conf
    #cb10-2audit-r-sessions-limit-mb=2048
The default maximum R session event log file size is 1 GB (1024 MB). To configure no limit to the size of files which can be written you set the value to `0`, for example:  

    #cb11-1# /etc/rstudio/rserver.conf
    #cb11-2audit-r-sessions-limit-mb=0
If you wish for RStudio to automatically roll the log files once the maximum size is reached, set the `audit-r-sessions-limit-months` setting. The default is set to 13 months. To set it manually, for example:  

    #cb12-1# /etc/rstudio/rserver.conf
    #cb12-2audit-r-sessions-limit-months=13
This will cause log files to be rolled over once the maximum size is reached, and only thirteen months of data will be kept. We do not recommend you change this setting if using named user licenses.

Note that if the month limit is not set, then log files will not be rotated automatically. This means that you should either create a scheduled (e.g. cron) job to periodically move the file off the server onto auxiliary storage and/or ensure that the volume that it is stored on has sufficient capacity.

In any case, the amount of data written to the R session event log file is not large (less than 1 KB per session) so a large number of session events can be stored within the default 1 GB maximum log file size.
Back to top
