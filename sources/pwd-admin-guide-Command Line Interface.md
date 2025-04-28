---
title: Command Line Interface – Administration Guide
keywords: source
---

# Command Line Interface – Administration Guide

url:: https://docs.posit.co/ide/server-pro/reference/rstudio_server_cli.html
up: [[sources]]

# NAME

rstudio-server - a command-line interface for administering an RStudio server.

# SYNOPSIS

**rstudio-server** COMMAND [ARGS…]

# DESCRIPTION

rstudio-server is a utility script containing a collection of subcommands for performing administrative actions on an Posit Workbench instance.

# COMMANDS

## SERVER MANAGEMENT


**offline**
:
    Takes the server offline. Users who attempt to access the server while it is offline will see a friendly message indicating that the server is currently offline, rather than the generic HTTP error they would see if the server is fully stopped.


**online**
:
    Brings the server back online after the
    **offline** command was used.


**reload**
:
    Reloads the server’s configuration without starting or stopping it. Only some configuration values are affected; others cannot be reloaded in place and require a full restart. Consult the administration guide for information on specific values.


**restart**
:
    Restarts the server, similar to a stop followed by a start. Useful for applying configuration changes.


**start**
:
    Start the server, if it is currently stopped.


**status**
:
    Report the status of the server according to the service manager, for instance whether it is stopped, starting, or running


**stop**
:
    Stop the server. Also stops all R sessions running locally on the server.

## USER MANAGEMENT


**add-user**
`NAME [0|1]`
:
    Adds the user NAME to the list of licensed users. This is useful for allocating license seats; note however that it does not create Linux users or other system resources. The second argument, which is optional, indicates whether to create an ordinary user (0) or administrator (1).


**list-users**
:
    Lists all the users, their license status (active or locked), and whether or not they are administrators.


**lock-user**
`NAME`
:
    Locks a user. Locked users cannot log in and don’t count against the licensing seat limit.


**set-admin**
`NAME 0|1`
:
    Sets the user NAME to either be an administrator (as in
    `set-admin johndoe 1`) or an ordinary user (0).


**unlock-user**
`NAME`
:
    Unlocks a previously locked user.

## USER SERVICE

Tokens for authenticating with Workbench’s SCIM API can be managed using the **user-service** command.


**user-service generate-token** [–duration]
`NAME`
:
    Generates a token for the user service with the given name. This token can be used to authenticate with the user service SCIM API. The
    `--duration` option can be used to specify the duration of the token in days. If not specified, the token will be valid for 365 days.


**user-service list-tokens**
:
    Lists all tokens for the user service.


**user-service revoke-token**
`[--all] TOKEN`
:
    Revokes the token with the given name. If
    `--all` is specified, revokes all tokens.

## LOAD BALANCING


**list-nodes**
:
    Lists all active nodes in the load balancing cluster, along with the status of each.


**node-status**
:
    Load balancing status (enabled/disabled), and lists all cluster nodes and corresponding sessions


**reset-cluster**
:
    Clears the internal load balancing state. This is used to reset the secure cookie key hash and transfer protocol used by the load balancing cluster.


**delete-node**
`ID`
:
    Permanently deletes the node with ID from the database and stops all nodes from messaging that node.

## SESSION MANAGEMENT


**active-sessions**
:
    Lists all currently active sessions. Only lists local R sessions; sessions running on other nodes or via the Job Launcher are not included.


**force-suspend-all**
:
    Forcefully suspends all R sessions at once; doesn’t give users an opportunity to save their work. For a gentler alternative, use
    **suspend-all**.


**force-suspend-session**
`ID|PID`
:
    Forcefully suspends one specific session. If using a session controlled by the Job Launcher, its ID should be supplied (the eight characters at the end of its URL). Otherwise, supply the ID of session’s process ID (PID), which you can obtain via
    `Sys.getpid()` inside the session or by inspecting the process ID of the
    `rsession` process with
    `ps`.


**kill-all**
`ID|PID`
:
    Kills all sessions. This uses
    `SIGKILL` or the equivalent, so the session will not shut down gracefully and work can be lost. When the user next attempts to use the session, R will report it as having crashed.


**kill-session**
`ID|PID`
:
    Kills one specific session. If using a session controlled by the Job Launcher, its ID should be supplied (the eight characters at the end of its URL). Otherwise, supply the ID of session’s process ID (PID), which you can obtain via
    `Sys.getpid()` inside the session or by inspecting the process ID of the
    `rsession` process with
    `ps`.


**suspend-all**
:
    Suspends all R sessions at once.


**suspend-session**
`ID|PID`
:
    Suspends one specific session. If using a session controlled by the Job Launcher, its ID should be supplied (the eight characters at the end of its URL). Otherwise, supply the ID of session’s process ID (PID), which you can obtain via
    `Sys.getpid()` inside the session or by inspecting the process ID of the
    `rsession` process with
    `ps`.

## LICENSE MANAGEMENT

The server’s license can be managed through the **license-manager** command, which has an extensive set of subcommands. Most of these subcommands accept the `--output` option, which can be used to set the output format to either `text` (the default when connected to a terminal) or `json`, which is useful for scripting or machine readability.


**license-manager activate**
`[--proxy=PROXY] PRODUCT-KEY`
:
    Attempts to activate the server with the given product key. If successful, the server uses one “activation” of the product key and becomes fully licensed. It is not necessary to restart the server after activating it; the license will take effect immediately. Requires an Internet connection; use the
    `--proxy` option to specify a proxy URL.


**license-manager activate-file**
`LICENSE-FILE`
:
    Attempts to activate the server with the given license file. License files take precedence over other types of licensing.


**license-manager activate-offline**
`ACTIVATION-FILE`
:
    Applies an activation file from Posit Support to the server.


**license-manager activate-offline-request**
:
    Starts the process of requesting a offline activation and generates a file you can send to Posit to complete the activation.


**license-manager acquire-lease-verbose**
:
    Attempts to acquire a lease on a floating license, printing diagnostic information while doing so. Not useful except as a troubleshooting tool; Posit automatically acquires and releases leases during the normal course of operation, and also acquires a temporary lease while executing the
    **status** command.


**license-manager begin-evaluation-offline**
`EVALUATION-FILE`
:
    Begins the evaluation period, using an evaluation file supplied by Posit Support.


**license-manager begin-evaluation-request**
:
    Starts the process of requesting an evaluation period and generates a file you can send to Posit. This typically isn’t necessary except on air-gapped machines which can’t automatically connect to the Internet to start an evaluation.


**license-manager deactivate**
`[--proxy=PROXY]`
:
    Deactivates the current license, making it available so that it can be used on another server. If successful, removes one “activation” of the product key. Requires an Internet connection; use the
    `--proxy` option to specify a proxy URL.


**license-manager clear-license-server**
:
    Clears a license server previously set with the
    **license-server** command. Used to switch from floating licensing to traditional key-based licensing.


**license-manager deactivate-offline**
:
    Deactivates the current license and prints an offline deactivation request; the license won’t be fully deactivated until you send the request to Posit for processing.


**license-manager initialize**
`[--userspace]`
:
    Initializes the licensing system. This is normally done during installation, so only needs to be performed manually when using alternative installation methods or switching to userspace licensing with the
    `--userspace` flag (see administration guide).


**license-manager license-server**
`HOSTNAME|URL`
:
    Sets the host (e.g. 
    `license.corp.com:8989`) or URL (e.g. 
    `https://license.corp.com/`) to use for floating licensing. Note that this has no effect unless Posit Workbench is configured to use floating licensing; see documentation for
    `server-license-type`.


**license-manager verify**
:
    Verifies that the current license is valid; prints basic information about the license and exits. Typically
    **status** will give you richer information.


**license-manager status**
:
    Prints detailed information about the current status of the licensing system: whether the license is currently valid, what licensing mode is in use (trial, key, floating), etc.

## DIAGNOSTICS


**run-diagnostics**
`[--output-file=PATH]`
:
    Prepares an extensive diagnostics report containing configuration files, logs, system information, and other output useful to help troubleshoot errors and problems. The report is placed in
    `/tmp/rstudio-diagnostics` by default, but you can specify any other path using the
    `--output-file` option.


**test-config**
:
    Tests the server’s configuration for validity.


**upload-minidump**
`MINIDUMP`
:
    Uploads a previously collected crash dump file (.dmp) to Workbench’s crash reporting service. Requires an active Internet connection. Crash dump files can be found in the
    `crash-db-path` path set in your
    `crash-handler.conf` file.


**verify-installation**
:
    Performs a variety of tests to ensure that the server is installed and working correctly, including starting an R session, launching a trial job (if the Job Launcher is configured), etc.
:
    Note that the server must be stopped in order for these tests to run, so use the
    **stop** command first if your server is running.


**version**
:
    Reports the current version of the server and exits.

## VS CODE


**configure-vs-code**
:
    Creates the default configuration in /etc/rstudio/vscode.conf for using VS Code sessions. Pass
    `-h` to see additional options.


**install-vs-code-ext**
:
    Installs VS Code extensions from vscode.extensions.conf to a global extensions directory.


**install-positron-ext**
:
    Installs Positron extensions from positron.extensions.conf to a global extensions directory. ## DATABASE


**encrypt-password**
:
    Encrypts a password. Doesn’t take any arguments; will prompt you for the password to encrypt and then emit the encrypted password. Use this utility to encrypt the database password in database.conf. When load balancing, ensure the same secure-cookie-key and database.conf are distributed across the cluster.


**migrate-db**
:
    Migrates the internal database from a SQLite database to a PostgreSQL database. Useful when transitioning from a single-server to a multiple-server configuration.
Back to top
