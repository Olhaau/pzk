---
title: Core Administrative Tasks – Administration Guide
keywords: source
---

# Core Administrative Tasks – Administration Guide

url:: https://docs.posit.co/ide/server-pro/server_management/core_administrative_tasks.html
up: [[sources]]

### Configuration files

Posit Workbench uses several configuration files all located within the `/etc/rstudio` directory.

|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                                                                                                                                                  | rserver.conf                      | Core Workbench server settings                                                                                                                                                           |
|                                                                                                                                                  | rsession.conf                     | Settings related to individual RStudio or RStudio Pro sessions                                                                                                                           |
|                                                                                                                                                  | logging.conf                      | Configuration of the logging system including logger type, location, and level                                                                                                           |
|                                                                                                                                                  | crash-handler.conf                | Configuration of the crash handling system (see [Automated Crash Reporting](https://docs.posit.co/ide/server-pro/server_management/../server_management/automated_crash_reporting.html)) |
|                                                                                                                                                  | database.conf                     | Configuration of the database that Posit Workbench will store data in (see [Database](https://docs.posit.co/ide/server-pro/server_management/../database/database.html))                 |
|                                                                                                                                                  | repos.conf                        | Customize the location of user packages installed from CRAN as well as the default CRAN repo                                                                                             |
|                                                                                                                                                  | file-locks                        | Configuration for file locking                                                                                                                                                           |
|                                                                                                                                                  | env-vars                          | Additional environment variables to set during the Workbench server process startup                                                                                                      |
|                                                                                                                                                  | secure-cookie-key                 | Private key used to sign authentication cookies                                                                                                                                          |
|                                                                                                                                                  | login.html                        | Custom HTML for login page                                                                                                                                                               |
|                                                                                                                                                  | themes/                           | Custom editor color schemes                                                                                                                                                              |
|                                                                                                                                                  | fonts/                            | Fonts for RStudio Desktop’s / Pro’s R console and code editor                                                                                                                            |
|                                                                                                                                                  | keybindings/                      | Custom IDE keybindings                                                                                                                                                                   |
|                                                                                                                                                  | snippets/                         | Editor snippet files                                                                                                                                                                     |
|                                                                                                                                                  | templates/                        | Default templates for new files created in the IDE                                                                                                                                       |
|                                                                                                                                                  | dictionaries/                     | Custom spelling languages and dictionaries                                                                                                                                               |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | notifications.conf                | Notifications to be delivered to user sessions                                                                                                                                           |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | nginx.http.conf                   | Extra HTTP configuration for nginx                                                                                                                                                       |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | nginx.server.conf                 | Extra server configuration for nginx                                                                                                                                                     |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | nginx.site                        | Extra site configuration for nginx                                                                                                                                                       |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | profiles                          | User and group resource limits                                                                                                                                                           |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | r-versions                        | Manual specification of additional versions of R                                                                                                                                         |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | ip-rules                          | IP access rules (allow or deny groups of IP addresses)                                                                                                                                   |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | load-balancer                     | Load balancing configuration                                                                                                                                                             |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | health-check                      | Template for content to return for Workbench server process health checks                                                                                                                |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | google-accounts                   | Mappings from Google accounts to local accounts                                                                                                                                          |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | launcher.conf                     | Configuration of the Launcher                                                                                                                                                            |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | launcher.local.conf               | Configuration of the Local Launcher Plugin                                                                                                                                               |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | launcher.kubernetes.conf          | Configuration of the Kubernetes Launcher Plugin                                                                                                                                          |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | launcher.kubernetes.profiles.conf | Configuration of resource limits by user and group when using the Kubernetes Launcher Plugin                                                                                             |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | launcher.slurm.conf               | Configuration of the Slurm Launcher Plugin                                                                                                                                               |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | launcher.slurm.profiles.conf      | Configuration of resource limits by user and group when using the Slurm Launcher Plugin                                                                                                  |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | jupyter.conf                      | Configuration of JupyterLab and Jupyter Notebook sessions                                                                                                                                |
| [Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.") | vscode.conf                       | Configuration of VS Code sessions                                                                                                                                                        |

The `rserver.conf` and `rsession.conf` files are created by default during installation however the other config files are optional so need to be created explicitly. It should be noted that the `rsession.conf` file must be readable by each Workbench user, so it should be given appropriate permissions (e.g. 644).

The `notifications.conf` and `r-versions` files are created, but their entries are commented out as an example.

Whenever making changes to configuration files you need to restart the Workbench server process for them to take effect. You can do this using the `restart` command of the Workbench server management utility:  

    #cb1-1$ sudo rstudio-server restart

### Alternate configuration file location

Posit Workbench can be instructed to use a directory other than `/etc/rstudio` for hosting configuration files using the XDG standard environment variable `XDG_CONFIG_DIRS`. This can be useful when running Posit Workbench in a container and mounting configuration at runtime. It can also be helpful for setting up alternate configurations for testing or troubleshooting purposes without running the risk of corrupting a known-good production configuration.

For the example below, presume that you’d like Posit Workbench’s configuration to live in `/mnt/config/rstudio`.

#### Create the directory

First, create the directory that needs to host configuration (this can of course be skipped when mounting). Make sure that the rstudio-server service account can read content in this directory.  

    #cb2-1$ mkdir -p /mnt/config/rstudio
    #cb2-2$ chmod 755 /mnt/config/rstudio
#### Copy configuration

Presuming that you’d like to start with your existing configuration, copy all of the configuration files and folders from your existing configuration set to your new configuration. You can do this as follows:  

    #cb3-1$ cp -r /etc/rstudio/* /mnt/config/rstudio/

#### Configure service

Because Posit Workbench runs as a system service, you must use your system’s service manager to change its environment. If your Linux distribution uses the `systemd` init system, run `sudo systemctl edit rstudio-server`. In the editor, add the following section to the file (replacing `/mnt/config` with your choice of root). Note that the `rstudio` folder is not included in this path; this is a configuration root directory that will be respected by other applications that use the XDG standard.  

    #cb4-1[Service]
    #cb4-2Environment="XDG_CONFIG_DIRS=/mnt/config"
If you wish to set Posit Workbench’s configuration folder directly, use the `RSTUDIO_CONFIG_DIR` environment variable instead. For example, to use `/mnt/config/rstudio` as the configuration folder:  

    #cb5-1[Service]
    #cb5-2Environment="RSTUDIO_CONFIG_DIR=/mnt/config/rstudio"
`RSTUDIO_CONFIG_DIR` is also useful if you do not wish other XDG-compliant applications to be affected by the environment variable. If set, it takes precedence over `XDG_CONFIG_DIRS`.

If your Linux distribution does not use the `systemd` init system, consult the documentation for your Linux distribution to learn which init system it uses and the appropriate method for setting environment variables for Workbench’s `rstudio-server` service.

#### Change and restart

Finally, make any configuration changes you’d like in your new configuration folder, and then restart the Workbench server process to use the new configuration files.  

    #cb6-1$ sudo rstudio-server restart

To return to the configuration in `/etc/rstudio`, just remove the `Environment` directive added above and restart the service.

### Configuring the run-time data directory

Posit Workbench needs to write several temporary files while running to function properly. The directory at which these files is written can be set by the `server-data-dir` configuration option by modifying `/etc/rstudio/rserver.conf` like so:  

    #cb7-1server-data-dir=/var/run/rstudio-server

The data directory defaults to `/var/run/rstudio-server` but you can change it to any directory. The specified location must be readable by any users of Posit Workbench.

### Setting environment variables

You can set environment variables for Posit Workbench’s server process using the `env-vars` configuration file. This is an alternative to setting the environment variables using your system’s service manager. For example, to set the `HTTP_PROXY` and `XDG_DATA_HOME` environment variables for the Workbench server process:  

    #cb8-1# Set proxy for outbound HTTP requests
    #cb8-2HTTP_PROXY=http://192.168.1.1
    #cb8-3
    #cb8-4# Store user data on mounted external storage
    #cb8-5XDG_DATA_HOME=/mnt/storage/$USER
The `env-vars` file is reloaded, and the environment variables set again, when you restart Posit Workbench or reload the configuration. See [Reloading configuration values](#reloading-configuration-values) for more information.  
This technique cannot be used to set the specific environment variables `XDG_CONFIG_DIRS` or `RSTUDIO_CONFIG_DIR`, because those variables control where configuration files are loaded from, and `env-vars` is *itself* a configuration file. Use your system’s service manager to set those variables as described in [Alternate Configuration File Location](#alternate-configuration-file-location).  
With the exception of XDG variables, environment variables set for the Workbench server process are not generally forwarded to individual RStudio Pro Sessions. To set environment variables such as `HTTP_PROXY` for all RStudio Pro Sessions running in Workbench, use [Renviron.site](https://stat.ethz.ch/R-manual/R-devel/library/base/html/Startup.html) or set them in one of the scripts executed when RStudio Pro Sessions are initialized (see [Profile script execution](https://docs.posit.co/ide/server-pro/server_management/../rstudio_pro_sessions/r_executable_and_libraries.html#profile-script-execution)).

### Stopping and starting

During installation, Posit Workbench registers as a daemon with the operating system’s init system. Refer to the documentation for your Linux distribution to learn which init system it uses.

To manually stop, start, and restart the Workbench server service, use the following commands:  

    #cb9-1$ sudo rstudio-server stop
    #cb9-2$ sudo rstudio-server start
    #cb9-3$ sudo rstudio-server restart
To check the current stopped/started status of the Workbench server service:  

    #cb10-1$ sudo rstudio-server status

### Reloading configuration values

To reload the Workbench server’s configuration without restarting it, use the `reload` command:  

    #cb11-1$ sudo rstudio-server reload

Alternately, you can send a `SIGHUP` to the Workbench `rserver` process, using a command like `kill -s SIGHUP $PID`, where `$PID` is the process ID of the `rserver` process.

Note that most configuration values cannot be applied without a full restart. The following are the values and settings that will be reloaded when you send `SIGHUP` or execute the `reload` command:

1. **Logging configuration** (`logging.conf`), as described in [Logging](https://docs.posit.co/ide/server-pro/server_management/../server_management/logging.html).
2. **Environment variables** (`env-vars`), as described in [Setting Environment Variables](#setting-environment-variables).
3. **Load balancing settings** (`load-balancer`), as described in [Load Balancing](https://docs.posit.co/ide/server-pro/server_management/../job_launcher/load_balancing.html).
4. **nginx configuration** (`nginx.*.conf`), as described in [Customizing default proxy](https://docs.posit.co/ide/server-pro/server_management/../access_and_security/running_with_a_proxy.html#customizing-default-proxy).
5. **Custom R version settings** (`r-versions`), as described in [Extended R version definitions](https://docs.posit.co/ide/server-pro/server_management/../r/using_multiple_versions_of_r.html#extended-r-version-definitions).
6. **Product license data** , as described in [License management](https://docs.posit.co/ide/server-pro/server_management/../license_management/license_management.html).

#### Session Lifecycle

When using the Job Launcher, Workbench sessions are launched as child processes of the respective Job Launcher Plugin. This means that restarting Posit Workbench will not necessarily cause sessions to suspend or be terminated.

When not using the Job Launcher, Workbench sessions are started as children of the Workbench server process. As a result, in this configuration, restarting Workbench will cause the termination of sessions.

If you want to more actively manage sessions and trigger suspension or termination then you can make use of the [Managing active sessions](#managing-active-sessions) section below.

### Managing active sessions

There are a number of administrative commands which allow you to see what sessions are active and request suspension of running sessions.

To list all currently active sessions:  

    #cb12-1$ sudo rstudio-server active-sessions

#### Suspending sessions

When RStudio Pro Sessions have been idle (no processing or user interaction) for a specified period of time (2 hours by default) Posit Workbench suspends them to disk to free up system resources. When the user next interacts with their session it is restored from disk and the user resumes right back where they left off. This is all done seamlessly such that users aren’t typically aware that a suspend and resume has occurred.

To manually suspend an individual session:  

    #cb13-1$ sudo rstudio-server suspend-session <pid>

To manually suspend all running sessions:  

    #cb14-1$ sudo rstudio-server suspend-all

The suspend commands also have a “force” variation which will send an interrupt to the session to request the termination of any running R command:  

    #cb15-1$ sudo rstudio-server force-suspend-session <pid>
    #cb15-2$ sudo rstudio-server force-suspend-all
The `force-suspend-all` command should be issued immediately prior to any reboot so as to preserve the data and state of active RStudio Pro Sessions across the restart.

#### Killing sessions

If you are for any reason unable to cooperatively suspend an RStudio Pro Session using the commands described above you may need to force kill the session. Force killing a session results in SIGKILL being sent to the process, causing an immediate termination.

To force kill an individual session:  

    #cb16-1$ sudo rstudio-server kill-session <pid>

To force kill all running sessions:  

    #cb17-1$ sudo rstudio-server kill-all

Note that these commands should be exclusively reserved for situations where suspending doesn’t work as force killing a session can cause user data loss (e.g. unsaved source files or R workspace content).

### Listing Users

[Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.")

You can list all of the users that have signed in to Posit Workbench by running the following command:  

    #cb18-1$ sudo rstudio-server list-users

### Adding users to the user database

[Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.")

Normally, when users sign in to Posit Workbench for the first time, they are automatically added to the Posit Workbench user database. However, you can manually add them via script if you need to. This is useful for designating specific users as administrators to allow them access to the administration panel without requiring them to belong to the administration group. This can be done by running the following command:  

    #cb19-1$ sudo rstudio-server add-user <username> <0 or 1>

Specifying a 0 in the above command will add the user without admin privilege; specify a 1 to grant admin privilege.

### Changing the admin status of a user

[Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.")

You can also change the admin status of a particular existing user:  

    #cb20-1$ sudo rstudio-server set-admin <username> <0 or 1>

### Locking and unlocking users

[Workbench](https://docs.posit.co/ide/server-pro/server_management/../#intended-audience "This feature is only available with Posit Workbench.")

If you are using Posit Workbench under a named user license, you can lock and unlock specific users to disallow or re-allow access to Workbench. Locking a user will prevent them from signing in to Workbench, but will preserve their files. Locked users do not count against the named user limit on your license.

This feature may be used when a user leaves the organization or otherwise no longer needs access to Posit Workbench on a permanent basis.

To lock a user, click on the user on the *Users* admin page, and then click the *Lock* button in the upper right-hand corner of the screen.

You can also lock users via the Workbench server management utility:  

    #cb21-1$ sudo rstudio-server lock-user <username>
    #cb21-2$ sudo rstudio-server unlock-user <username>
Note that Posit Workbench’s [Software License Descriptions](https://posit.co/about/software-license-descriptions/) only allow this feature to be used to permanently terminate a named user’s access.  
To permanently terminate a user’s access to allow re-assignment of their seat per the Posit Workbench [Software License Descriptions](https://posit.co/about/software-license-descriptions/), simply lock that specific user’s account.

### Taking the Workbench server offline

If you need to perform system maintenance and want users to receive a friendly message indicating the Workbench server is offline you can issue the following command:  

    #cb22-1$ sudo rstudio-server offline

When the Workbench server is once again available you should issue this command:  

    #cb23-1$ sudo rstudio-server online

When the Workbench server is taken offline all RStudio Pro Sessions will be suspended and no new sessions can be started. Session data will be saved but any running computations will be terminated.

### Upgrading to a new version

If you upgrade Workbench and an existing version of the server is currently running, then the upgrade process also ensures that active sessions are immediately migrated to the new version.

This includes the following behavior:

* Running R sessions are suspended so that future interactions with the server automatically launch the updated R session binary.
* Connected browser clients are notified that a new version is available and automatically refreshes themselves.
* The core server binary restarts.

When load balancing is configured upgrading multiple nodes may cause brief glitches if you upgrade each Workbench node one at a time. This is due to the possibility of two Workbench nodes with different versions trying to coordinate. If some downtime is acceptable, we recommend taking all nodes offline before upgrading.

To upgrade to a new version of Posit Workbench, please use our [Upgrade](https://docs.posit.co/ide/server-pro/server_management/../upgrade/upgrade.html) documentation.
Back to top
