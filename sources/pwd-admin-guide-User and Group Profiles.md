---
title: User and Group Profiles – Administration Guide
keywords: source
---

# User and Group Profiles – Administration Guide

url:: https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/user_and_group_profiles.html
up: [[sources]]

[Workbench](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../#intended-audience "This feature is only available with Posit Workbench.")

User and Group Profiles enable you to tailor the behavior of sessions on a per-user or per-group basis. The following attributes of a session can be configured within a profile:

1. Version of R used
2. CPU affinity (i.e. which set of cores the session should be bound to)
3. Scheduling priority (i.e. nice value)
4. Resource limits (maximum memory, processes, open files, etc.)
5. RStudio Pro Session timeouts (amount of idle time which triggers session suspend)
6. RStudio Pro Session kill timeouts (amount of idle time which triggers a session to be destroyed and cleaned up)

### Creating profiles

Profiles are defined within the file `/etc/rstudio/profiles`. Note that this file is not created by default so you’ll need to create it if doesn’t already exist. Profiles are divided into sections of three different types:

1. Global (`[*]`)
2. Per-group (`[@groupname]`)
3. Per-user (`[username]`)

Here’s an example profiles file that illustrates each of these types:  

    #cb1-1[*]
    #cb1-2cpu-affinity = 1-4
    #cb1-3max-processes = 800
    #cb1-4max-memory-mb = 2048
    #cb1-5session-limit=5
    #cb1-6session-timeout-minutes=60
    #cb1-7session-timeout-kill-hours=24
    #cb1-8
    #cb1-9[@powerusers]
    #cb1-10cpu-affinity = 5-16
    #cb1-11nice = -10
    #cb1-12max-memory-mb = 4096
    #cb1-13session-limit=10
    #cb1-14
    #cb1-15[jsmith]
    #cb1-16r-version = /opt/R/4.2.2
    #cb1-17session-timeout-minutes=360
This configuration specifies that by default users will run on cores 1 to 4 with a limit of 800 processes and 2GB of virtual memory. It also specifies that members of the `powerusers` group will run on cores 5 to 16 with an elevated nice priority and a limit of 4GB of memory. Finally, the user `jsmith` is configured to use a different version of R from the system default.

Note that the `/etc/rstudio/profiles` file is processed from top to bottom (i.e. settings matching the current user that occur later in the file always override ones that appeared prior). Additionally, some settings do not apply to certain editor types (like Jupyter or VS Code). The settings available within `/etc/rstudio/profiles` are described in more depth below.

### Session Limit

To configure the maximum allowed number of sessions you can use the `session-limit` option. This is a more flexible choice than disabling multiple sessions entirely with `server-multiple-sessions`.

For example to limit user to 5 sessions:

By default there is no limit to the number of sessions other than the one specified by the license. This limit has no effect if greater than the number of sessions allowed by your license.

This setting applies to all session types (RStudio Pro, Jupyter, and VS Code).

### Session timeout

To configure the amount of idle time to wait before suspending sessions you can use the `session-timeout-minutes` option. Idle time is the amount of time since a session has run any R commands, including commands typed by the user in the console and the execution of any R scripts.

For example:  

    #cb3-1session-timeout-minutes=360

The default value if none is explicitly specified is 120 minutes.

There are some conditions where an RStudio Pro Session will not be suspended, these include:

1. When a top-level R computation is running
2. When the R prompt is not in it’s default state (e.g. during a debugging session)

You can also specify that RStudio Pro Sessions should never be suspended by setting the `session-timeout-minutes` to zero. For example:  

    #cb4-1session-timeout-minutes=0

This setting only applies to the RStudio Pro IDE, and does not apply to Jupyter or VS Code. Session timeout can be configured for Jupyter sessions via the `session-cull` setting - see [Jupyter Configuration](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../jupyter_sessions/configuration.html) for more details. There is currently no mechanism for timing out VS Code sessions.

### Session timeout kill

To configure the amount of time to wait before forcefully killing and destroying sessions you can use the `session-timeout-kill-hours` option in the `/etc/rstudio/rsession.conf` file. This allows you to specify how much time should pass before a session is automatically cleaned up. This is the length of time during which a session exists without user input (**regardless of computation status**) allowing you to automatically reclaim temporary disk space used by the session, and to stop its processes and children.

This setting should only be used to ensure that any sessions that users have forgotten about are destroyed, reclaiming valuable disk space. Note that this setting does not work if the `server-shared-storage-path` is located on a root squash mount.

**Sessions can be destroyed while important computations are executing**. The user whose session is cleaned will also lose all unsaved code and data.

This setting can also be used in conjunction with `session-timeout-minutes`, allowing already suspended sessions to be cleaned up. For example:  

    #cb5-1session-timeout-kill-hours=48

The default value if none is explicitly specified is 0 hours, meaning sessions will never be killed and destroyed automatically. The supplied value should be an integer representing the amount of hours a session can go without user interaction before being killed.

This setting only applies to the RStudio Pro IDE, and does not apply to Jupyter or VS Code. Session timeout can be configured for Jupyter sessions via the `session-cull` setting - see [Jupyter Configuration](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../jupyter_sessions/configuration.html) for more details. There is currently no mechanism for timing out VS Code sessions.

#### Interaction with session-timeout-minutes

It is recommended that the `session-timeout-kill-hours` be set to a much higher span of time than that specified by `session-timeout-minutes`. This will allow sessions to be suspended (and not destroyed) before they are forcefully killed and cleaned up, allowing for a better user experience. Note however, that if a session is running a long-running computation, it may not be suspended but it will still be killed.

If `session-timeout-kill-hours` is set to an amount of time less than `session-timeout-minutes` (which has a default of 2 hours), then sessions will *never* be suspended and they will be forcefully killed and cleaned up.

The two settings may be set to the same amount of time, but this is not recommended. In this case, it is undefined whether or not the session will be suspended, but the session will be killed and cleaned up regardless.

### CPU affinity and scheduling priority

If you have users or groups that consistently require more compute resources than others you can use profile settings to reserve CPUs (`cpu-affinity`) as well as raise overall scheduling priority (`nice`).

#### CPU affinity

The `cpu-affinity` setting specifies which cores on a multi-core system should be used to schedule work for a session. This is specified as a comma-separated list of core numbers (1-based) where both individual cores and ranges of cores can be specified. For example:  

    #cb6-1cpu-affinity = 1,2,3,4
    #cb6-2cpu-affinity = 1-4
    #cb6-3cpu-affinity = 1,2,15-16
To determine the number of addressable cores on your system you can use the `nproc` command:

This setting applies to all session types (RStudio Pro, Jupyter, and VS Code).

#### Scheduling priority

The `nice` setting specifies a relative priority for scheduling session CPU time. Negative 20 is the highest nice priority and positive 20 is the lowest priority. The system default niceness for processes is typically 0. The following are all valid nice values:  

    #cb8-1nice = -10
    #cb8-2nice = 0
    #cb8-3nice = 15
Scheduler behavior around nice priorities varies by system. For more details see [nice use and effect](http://en.wikipedia.org/wiki/Nice_(Unix)#Use_and_effect).

This setting applies to all session types (RStudio Pro, Jupyter, and VS Code).

### Resource limits

Profiles can also be used to specify limits on available memory as well as the maximum number of processes and open files.

#### Available memory

The `max-memory-mb` setting controls the maximum amount of addressable memory for RStudio Pro Sessions (by default memory is unlimited). This example specifies a limit of 2GB:

Note that this value sets the amount of virtual memory that can be used by a process. Virtual memory includes code (i.e. shared libraries) loaded by the process as well as things like memory mapped files, so can often consume several hundred megabytes even for a vanilla RStudio Pro Session. Therefore, you want to be sure not to set this threshold too low (in no case should you set it below 1024).

This setting applies to all session types (RStudio Pro, Jupyter, and VS Code).

#### Number of processes

The `max-processes` setting controls the maximum number of system processes createable by a user. This setting is useful to prevent either inadvertent or malicious [fork bombs](http://en.wikipedia.org/wiki/Fork_bomb). The following example sets a limit of 800 processes:

Note that `max-processes` refers to the total number of system processes and threads the user needs to run their RStudio Pro Sessions, and any jobs launched by that session. A given RStudio Pro Session may create many processes and threads behind the scenes so we recommend setting this value high and testing the outcome before deploying in production. Use `ps -LU username` to see the set of processes and threads running on behalf of the supplied `username`.

This setting applies to all session types (RStudio Pro, Jupyter, and VS Code).

#### Number of open files

In most Linux environments there is a maximum of 1024 open files per process. This is typically more than enough, but if you have a particular applications that requires more open files the `max-open-files` setting can be used to increase the limit. For example:

This setting applies to all session types (RStudio Pro, Jupyter, and VS Code).

### Using multiple versions of R

As illustrated above, you can bind users or groups to distinct versions of R installed on your server. This is controlled by the `r-version` option. Here are several examples of it’s use:  

    #cb12-1r-version = /usr/lib/R
    #cb12-2r-version = /usr/local/lib/R
    #cb12-3r-version = /opt/R/4.2.2
    #cb12-4r-version = /opt/R/4.3.0
Note that `r-version` specifies the full path to the directory where R is installed.

See the [R versions](https://docs.posit.co/ide/server-pro/rstudio_pro_sessions/../r/using_multiple_versions_of_r.html) chapter for additional details on running multiple versions of R on a single server.

This setting only applies to the RStudio Pro IDE, and does not apply to Jupyter or VS Code.

### Usage in distributed environments

User profiles are still applicable when RStudio Pro Sessions are not all co-located on the same server, but some considerations apply.

#### Job Launcher

If launching sessions via the Job Launcher, scheduling priority and resource limits are not ignored, but these should generally be configured via the Job Launcher itself where applicable. For example, when running Kubernetes sessions, memory limits can be specified each time a session is launched, so memory limits should be configured by the Job Launcher in that case.

#### Load balancer

When using load balancing, resource limits are applied to a session *after* it has been assigned to a server in a load-balanced cluster. So, for example, if you have `cpu-affinity` set to `1-4` for a user, then the user’s session can run on cores 1 through 4 of any server in the cluster. Likewise, a session’s `max-memory-mb` value will cap the memory it can use on the server on which it runs, but doesn’t otherwise restrict memory usage across servers.
Back to top
