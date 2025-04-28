---
title: License Management – Administration Guide
keywords: source
---

# License Management – Administration Guide

url:: https://docs.posit.co/ide/server-pro/license_management/license_management.html
up: [[sources]]

[Workbench](https://docs.posit.co/ide/server-pro/license_management/../#intended-audience "This feature is only available with Posit Workbench.")

## Overview

Posit Workbench uses a built-in license management system:

* A license controls the number of users that can connect to Workbench.
* Activation of the Job Launcher feature and the number of concurrent sessions that can be run.  
* Additionally, the license server supports named users as well as floating licenses.

### Product tiers

Product tiers have been introduced for Workbench, called, Basic, Enhanced, and Advanced. These tiers include different features, number of server activations, and pricing than our current product tiers (currently called Base, Standard, and Enterprise).  
Prior to a renewal in 2024, you will be unaffected by any packaging changes. Every renewal will receive a new license in 2024 that corresponds to a new product tier. This means you will continue to have full access to all of the features according to your existing license.

**Product tier badges:**

* Feature documentation without a product tier badge label is part of the Basic tier.
* Features that require elevated product tiers are labeled [Enhanced](https://docs.posit.co/ide/server-pro/license_management/../license_management/license_management.html#product-tiers "This feature is only available with the purchase of the Enhanced or Advanced product tier. Click the badge for additional info.") and or [Advanced](https://docs.posit.co/ide/server-pro/license_management/../license_management/license_management.html#product-tiers "This feature is only available with the purchase of the Advanced product tier. Click the badge for additional info."):
  * All features that are available with the Enhanced tier are also available for Advanced tier holders, so those features are labeled with both.
  * Advanced tier product features are only accessible with the purchase of the Advanced tier.

## License status

Initially, Posit Workbench operates in evaluation mode and then subsequently requires activation for continued use. If you find that an automatic evaluation is not available on your system, speak to your dedicated Posit Customer Success contact or email Posit Support ([support@posit.co](mailto:support@posit.co)) to speak about your evaluation.

**How do I check the status of my current license?**

To check the status of your current license, use the license status command:  

    #cb1-1$ sudo rstudio-server license-manager status

You can perform an offline check of your current license status using the following command:  

    #cb2-1$ sudo rstudio-server license-manager status-offline

### License details

Posit Workbench licenses include details that display when you run the `status` command. These values are part of the license and can’t be changed without purchasing a different license. They are as follows:

|-------------------|----------------------------------------------------------------------------------------|
| `enable-launcher` | Whether the Job Launcher is enabled.                                                   |
| `sessions`        | The maximum number of concurrent RStudio Pro Sessions that can be run; 0 if unlimited. |
| `users`           | The maximum number of users that can connect to the server; 0 if unlimited.            |

Typically, Posit Workbench licenses either:

* disable the Launcher but allow for unlimited local users and sessions, or
* enable the Launcher and provide limits on users and/or sessions.

If you have questions about the most appropriate license for your use case, contact [sales@posit.co](mailto:sales@posit.co) for help.

### License warnings

When the license or evaluation is within 15 days of expiration, a warning displays to users inside RStudio Pro sessions. The warning indicates the number of days remaining in the license or evaluation period.

You can change the length of the 15-day warning period with the `license-warning-days` option, which can be set to 0 to disable license warnings altogether:  

```
/etc/rstudio/rserver.conf
```

Disabling license warnings will cause your license to expire without warning to you or your users. We recommend changing this setting *only* if you have other processes in place to ensure that the license is renewed prior to expiration.

## License activation

After purchasing a license to Posit Workbench, you’ll receive a license key or license file that can be used to activate the license on a given system.

### License file

A license file is an encrypted text file with a name having the shape `<expiration-date>_<product-code>_<organization>_<license-key>.lic` that fully describes the purchased product license. Use of license files does not communicate with an external licensing provider, making it suitable for containerized, offline, and air-gapped environments.

If you are having issues with alternate licensing methods, please speak to Posit Support ([support@posit.co](mailto:support@posit.co)) about using license files.

When a license file exists in the `/var/lib/rstudio-server/` directory, other licensing methods are bypassed.  
You must ensure that the license file is secured, used only in your environment, and within the bounds of your license agreement.

#### Activation

Activate your license file with the following commands:  

    #cb4-1$ sudo chown rstudio-server <license-file>.lic
    #cb4-2$ sudo chmod 600 <license-file>.lic
    #cb4-3$ sudo cp -a <license-file>.lic /var/lib/rstudio-server/.
    #cb4-4$ sudo rstudio-server restart
#### Deactivation

You must deactivate your license before moving it to another system. If you use a license file in a container, no action is required when the container is terminated; the license is deactivated.

Deactivate your license file by removing it from the `/var/lib/rstudio-server/` directory:  

    #cb5-1$ sudo rm /var/lib/rstudio-server/<license-file>.lic
    #cb5-2$ sudo rstudio-server restart
### License key

You can activate a license key locally or through a persistent server. This section describes local activation. Suppose you are stopping and starting Workbench instances frequently, for instance, because you are running them inside virtual machines or containers. In that case, you may use a persistent server and reference [Floating Licensing](#floating-licensing).

#### Online activation

##### Connectivity requirements

Activating and deactivating a Posit Workbench license key via the licensing server requires internet connectivity.

Additionally, your server should have a synchronized system clock, using `ntp` or some other clock syncing service. If the server’s clock is sufficiently incorrect, licensing verification fails.

If you have internet access and are not using an internet proxy, activate your license key with the commands below:  

    #cb6-1$ sudo rstudio-server license-manager activate <product-key>
    #cb6-2$ sudo rstudio-server restart
Posit uses wyDay’s LimeLM TurboActivate and TurboFloat for license management as an approved part of our software delivery infrastructure. As such, for online license activation, you need to allow outbound access to wyday.com.

If needed, add the port number from where the activation key is being downloaded so it can be added to a safe sites list by your networking team:

    Address | https://www.wyday.com
    Port | 443
##### Proxy servers

If your server is behind an internet proxy, you may need to add an additional command line flag indicating the address and credentials required to communicate through the proxy. This may not be necessary if either the `http_proxy` or `all_proxy` environment variable is defined (these are read and used by the license manager when available).

If you do need to specify a proxy server explicitly, use the `--proxy` command line parameter. For example:  

    #cb8-1$ sudo rstudio-server license-manager --proxy=http://127.0.0.1/ activate <product-key>
    #cb8-2$ sudo rstudio-server restart
Proxy settings can include a host-name, port, and username/password if necessary. The following are all valid proxy configurations:  

    #cb9-1http://127.0.0.1/
    #cb9-2http://127.0.0.1:8080/
    #cb9-3http://user:pass@127.0.0.1:8080/
If the port is not specified, the license manager defaults to port `1080`.

##### Online deactivation

If you want to move your license of Posit Workbench to another system, first deactivate it on the old system with the command:  

    #cb10-1$ sudo rstudio-server license-manager deactivate

Then run the activation command on the new system.

#### Offline activation

If your system isn’t connected to the internet, then perform an offline activation.

To activate your license offline, first generate an offline activation request:  

    #cb11-1$ sudo rstudio-server license-manager activate-offline-request <product-key>

Executing this command will print an offline activation request to the terminal which you should copy and paste and enter into our [offline activation application](http://apps.rstudio.com/offline-activation) or send to Posit Support ([support@posit.co](mailto:support@posit.co)). You will receive a reply with a file attachment that can be used to activate offline as follows:  

    #cb12-1$ sudo rstudio-server license-manager activate-offline <activation-file>

##### Offline deactivation

If you are renewing your license or want to move your license of Posit Workbench to another system, you can perform license deactivation offline by executing the following:  

    #cb13-1$ sudo rstudio-server license-manager deactivate-offline

Executing this command prints an offline deactivation request to the terminal, which you should copy and paste into the [offline activation application](http://apps.rstudio.com/offline-activation). Then, send to Posit Support ([support@posit.co](mailto:support@posit.co)).

## Floating licensing

If you stop and start Posit Workbench instances frequently, for instance because you’re running them inside virtual machines or containers, you may wish to use floating licensing instead of traditional licensing.

To use floating licensing, you run a small, lightweight server, which holds a license that grants you the right to run a certain number of concurrent Posit Workbench instances.

When Posit Workbench starts, it will connect to the license server and obtain a temporary lease, releasing it when Posit Workbench is stopped. Using this method, you can have any number of Posit Workbench instances, so long as you do not run more instances at once than specified in your license.

### Floating license keys

A license key which distributes floating license leases is not the same as a traditional license key, and the two cannot be used interchangeably. If you have purchased traditional license keys and wish to exchange them for a floating license key, or vice versa, please get in touch with Posit Support ([support@posit.co](mailto:support@posit.co)).

#### The Posit Workbench License Server

The Posit Workbench License Server supplies licenses to Posit Workbench. It is available for Linux, Mac, and Windows. A license server can supply licenses to any platform; for instance, you can run your license server on Windows and distribute licenses to Posit Workbench instances running Linux.

##### Linux

The [Posit License Server site](https://www.rstudio.com/floating-license-servers/) contains Linux license server downloads for all Posit products. Download and install the license server for Posit Workbench. You then activate your license key with the command:  

    #cb14-1$ sudo dpkg -i rsp-license-server-1.1.2-x86_64.deb
    #cb14-2$ sudo rsp-license-server activate <product-key>
    #cb14-3$ sudo rsp-license-server start
The file `/etc/rsp-license-server.conf` contains configuration settings for the Posit Workbench License Server, including the network port to listen on and any proxy settings required for connecting to the Internet.

##### Mac and Windows

The [Posit License Server site](https://www.rstudio.com/floating-license-servers/) contains downloads for the Mac and Windows license servers. These require a few additional configuration steps. They can be set up as follows (order is important):

1. Download the license server (`TurboFloatServer`) appropriate to your platform, and place it in the directory where you wish it to run.
2. Copy the file `/usr/lib/rstudio-server/bin/license-manager.conf` to the same directory as `TurboFloatServer`, and change its name to `TurboActivate.dat`.
3. If activating online, run the command `TurboFloatServer.exe -a="ABCD-EFGH-IJKL-MNOP-QRST-UVWX"`.
4. If activating offline, follow the [offline activation steps](https://wyday.com/limelm/help/turbofloat-server/#activate-offline). Note that it is necessary to supply the *fully qualified path* to file arguments to `TurboFloatServer`, even if they are in the same directory as the executable.
5. After successful activation, `TurboFloatServer.exe -i` with administrator permissions. This will install the license server as a service that will start automatically and run in the background.

For additional help with Mac and Windows license server installation and configuration, refer to the official [TurboFloat Server documentation](https://wyday.com/limelm/help/turbofloat-server/).

#### License Server hardware requirements

While the license server is designed to distribute licenses to ephemeral virtual machines or containers, which may move freely between hosts, the license server *itself* must stay on the same physical host on which it was activated.

It is acceptable to run the license server inside a container or a virtual machine, as long as the container or the VM always runs on the same hardware. Stopping the license server, moving it to a different physical host, and starting it again will cause it to become deactivated. Because it’s often impractical to try to ensure that the server only runs on a particular host, we recommend running the license server outside containers and virtualization software.

Note that the system requirements for the license server are very low, so it can be run on almost any server. If your environment will not allow for ensuring that the server stays on a single physical host, please contact [support@posit.co](mailto:support@posit.co) to discuss alternate licensing options.

#### License Server offline activation

The `rsp-license-server activate` command requires an internet connection. If your license server has no connection to the Internet it’s also possible to perform an offline activation. The process for doing this on the license server is identical to the process used to activate Posit Workbench offline. Generate an offline activation request as follows:  

    #cb15-1$ sudo rsp-license-server activate-offline-request <product-key>

Executing this command will print an offline activation request to the terminal which you should copy and paste and then send to Posit Support ([support@posit.co](mailto:support@posit.co)). You will receive a reply with a file attachment that can be used to activate offline as follows:  

    #cb16-1$ sudo rsp-license-server activate-offline <activation-file>
    #cb16-2$ sudo rsp-license-server restart
#### License Server deactivation

If you are permanently decommissioning your license server, or need to transfer its license to a different license server, you should deactivate (remove) its license as follows:  

    #cb17-1sudo rsp-license-server deactivate

#### Using floating licensing

Once your license server is up and running, you need to tell Posit Workbench to use floating licensing instead of traditional licensing.  

```
/etc/rstudio/rserver.conf
```

    #cb18-1server-license-type=remote

The value `remote` indicates that Posit Workbench should connect to a remote licensing server to obtain a license; the value `local` can be used to explicitly specify traditional (local) activation.

Then, tell Posit Workbench which licensing server to connect to:  

    #cb19-1$ sudo rstudio-server license-manager license-server <server-hostname-or-ip>
    #cb19-2$ sudo rstudio-server restart
You only need to run the `license-server` command once; Posit Workbench saves the server name and will use it on each subsequent startup.

When operating Workbench within a containerization system like Docker, it may also be necessary to specify the `RSW_LICENSE_SERVER` environment variable on the container host. Assuming you use an [official Workbench docker image](https://hub.docker.com/r/rstudio/rstudio-workbench), the container’s startup script will check whether this environment variable is defined as it starts the Workbench server process, and automatically run the necessary license server commands. This prevents you from having to access the container and manually run these commands every time. As with the `RSW_LICENSE` environment variable, placing it in a `.bashrc` or any similar `rc` file is recommended, so that you only have to set it once.

Depending on your system configuration, it is possible that the Posit Workbench service will be started before the service which allows hostname resolution (this is known to be the case for example on some Amazon EC2 systems). If this is the case, you’ll want to specify the license server using a private IP address rather than a hostname, so that Posit Workbench can acquire a license immediately when starting up.  
If you are renewing your floating license offline or if you want to move your floating license for Posit Workbench to another system, then you should first perform license deactivation offline.

To renew your floating license offline or to move your floating license, run the following to perform license deactivation offline:  

    #cb20-1$ sudo rstudio-server deactivate-offline-request <product-key>

#### Specifying a license server

Workbench supports three methods for connecting to a license server. You can select the method by specifying the license server as follows:

##### Connecting over TCP

*Example*  

    #cb21-1$ sudo rstudio-server license-manager license-server mylicensehost:9403

By default, the Posit Workbench License Server listens via raw TCP on port 8989. If you specify the only the hostname or IP address of the license server, this the kind of connection you’ll get.

If you wish to use a different port, you will need to specify the port in `/etc/rsp-license-server.conf`, and specify `license-server` to Posit Workbench as `<server-hostname-or-ip:port>`.

##### Connecting over HTTPS

*Example*  

    #cb22-1$ sudo rstudio-server license-manager license-server https://mylicensehost/

You can also connect to the license server via HTTPS rather than raw TCP. This can be useful when using proxies or load balancers in front of the license server. Note that the license server does not use HTTPS by default, so you can only use this connectivity method if you have configured your license server to use HTTPS.

Configuring the license server for HTTPS support is outside the scope of this guide. You can read instructions here:

[Configuring TurboFloat Server for HTTPS Communication](https://wyday.com/limelm/help/turbofloat-server/#config-https)

##### Connecting to a hosted server

*Example*  

    #cb23-1$ sudo rstudio-server license-manager license-server 871A2BFA-87C5-11E9-BD16-4749DB7B7927

Finally, if you are connecting to a hosted license server, specify the UUID of the server rather than its hostname. Hosted license servers are run by our licensing vendor, WyDay, and do not require you to run anything in your own network. Read more about setting up a hosted license server and getting a UUID here:

[Creating a LicenseChest Server](https://wyday.com/licensechest/help/create-tfs-instance)

#### Configuring license leases

When using floating licenses, you can optionally determine how long the license leases last by setting the `lease length` value on the licensing server. This value is in seconds, so for instance to make license leases last 30 minutes you would use the following syntax:  

```
/etc/rsp-license-server.conf
```

The lease length controls how frequently the Posit Workbench instances need to contact the licensing server to renew their license leases in order for the lease to remain valid.

A **shorter** lease length will increase tolerance to failures on Posit Workbench instances by making leases available for reuse more quickly. Posit Workbench will release its lease immediately if shut down normally, but if abnormally terminated, the lease will not be released until it expires.

A **longer** lease length will increase tolerance to transient failures on the network and the Posit Workbench License Server. Any such issues that can be resolved before the lease is due for renewal won’t interrupt use of Posit Workbench.

We generally recommend using a longer lease length. Use a short lease length only if your environment routinely encounters abnormal terminations of the server or the container/instance on which it runs.

#### License server downtime tolerance

Posit Workbench does not wait until its lease has fully expired before it renews it. It renews its lease when it is *halfway to expiry*. For instance, if you use 30 minute leases, Posit Workbench will actually renew its lease every 15 minutes.

This means that it is possible to take down the license server for a short period of time without affecting any running Posit Workbench instances. Because of the aforementioned behavior, no existing lease will be more than halfway to expiry if the server goes down.You have a grace period of N/2 (where N is the length of the lease) during which the server can be offline without consequences. For instance, if you use 30 minute leases, your license server can be offline for 15 minutes.

#### Lease expiration and renewal

Under normal conditions Posit Workbench will automatically renew its license lease in a configurable interval as described above. However, there are situations in which it will be unable to do so, such as a network problem, or an issue on the host running the license server.

When Posit Workbench cannot obtain a license lease, either because there are no leases currently available or because it can’t reach the licensing server, it will begin automatically attempting to acquire a lease every 10 seconds. This interval is configurable; for instance, to retry every 30 seconds instead you would set the following value:  

```
/etc/rstudio/rserver.conf
```

If you don’t want Posit Workbench to attempt to reestablish a license lease automatically, set the value to `0` to disable retries. In this case you will need to manually restart Posit Workbench in order to reestablish the lease. This can be useful if you often run more instances than you have keys for, and wish to have more control over which Posit Workbench instances receive license leases from the limited pool on the license server.

#### Troubleshooting floating licensing

To validate that the license server has been successfully activated, run the `activation-status` command. This will report the version of the server as well as the license key and the number of available slots.  

    #cb26-1$ sudo rsp-license-server activation-status

If your server is activated but you’re still having trouble with floating licensing, you can tell the Posit Workbench License Server to emit more detailed logs. Change the log level to `notification`:  

```
/etc/rsp-license-server.conf
```

    #cb27-1<log file="/var/log/rstudio-licensing.log" level="notification"/>

Then, restart the license server, tail the licensing log, and start your Posit Workbench instances.  

    #cb28-1$ sudo rsp-license-server restart
    #cb28-2$ tail -f /var/log/rstudio-licensing.log
At the `notification` level, the licensing log will tell you the total number of licenses associated with your key, and how many are currently in use. It will also notify you when Posit Workbench instances acquire leases, and when those leases are released, renewed, or expired. No rotation is done for this log, so it’s recommended to use the `warning` level in production.

## Userspace licensing

In a standard installation of Posit Workbench, licensing configuration operations require elevated access (e.g., via `sudo`). This is appropriate given the product will normally be installed and activated by an administrator on behalf of multiple user accounts.

Alternatively, the licensing system may be switched to a “userspace” model where the licensing commands are available to a non-elevated user account. This is a system-wide setting; it is not possible to mix both models on a single system.

#### Switching to userspace licensing

Before switching, stop the server, and deactivate any existing license-key or license-server.  

    #cb29-1$ sudo rstudio-server stop
    #cb29-2$ sudo rstudio-server license-manager deactivate
    #cb29-3$ sudo rstudio-server license-manager clear-license-server
Next, disable the standard system-level licensing mode by deleting the “verify” file.  

    #cb30-1$ sudo rm /var/lib/rstudio-server/verify

Initialize the license system in userspace mode. This command must *not* be run elevated, but as the Posit Workbench account (normally `rstudio-server`) as described in [Server account](https://docs.posit.co/ide/server-pro/license_management/../access_and_security/server_permissions.html#server-account).  

    #cb31-1$ rstudio-server license-manager initialize --userspace

Confirm that the mode was successfully changed.  

    #cb32-1$ rstudio-server license-manager verify
    #cb32-2Trial-Type: Verified
    #cb32-3Status: Evaluation
    #cb32-4Days-Left: 5
    #cb32-5License-Scope: User
The line `License-Scope: User` indicates successful switch to userspace licensing (versus the default setting of `System`).

At this point, the user may perform licensing commands without using “sudo”, such as activating a license-key or configuring a license-server.
Back to top
