These instructions describe how to install Workbench on a Linux server.

For alternative installation instructions, see our [recommended installation paths](https://docs.posit.co/ide/server-pro/getting_started/installation/).  
This page includes instructions for downloading Posit professional products. Download and/or use of these products is governed under the terms of the [Posit End User License Agreement](https://posit.co/about/eula/). By downloading, you agree to the terms posted there. The same instructions apply if you still use a legacy RStudio Server Pro license configuration (no launcher enabled).

## Prerequisites

* Review and meet the [requirements](https://docs.posit.co/ide/server-pro/getting_started/requirements.html)

  We have several requirements, such as [Database Connectivity](https://docs.posit.co/ide/server-pro/getting_started/requirements.html#database-connectivity), that must be satisfied before installing Workbench. Please review the [Requirements](https://docs.posit.co/ide/server-pro/getting_started/requirements.html) page in its entirety before proceeding with the installation.
* Install R (required)

* Install Python (optional, unless you wish to integrate Workbench with VS Code and/or Jupyter)

## Step 1. Install R & Python

### Install R

*Installing R is required.*

* Install a version of R on the server following the steps to [Install R](https://docs.posit.co/resources/install-r/).

  Our recommended installation instructions for R allow you to make multiple versions of R available and avoid replacing existing versions of R when updating system packages.

### Install Python

*Installing Python is optional unless you wish to integrate Workbench with VS Code and/or Jupyter.*

* Install a version of Python on the server following the steps to [Install Python](https://docs.posit.co/resources/install-python/).

  RStudio Pro and VS Code do not require Python. However, we recommend installing Python to provide users with the most choice and the option to develop in Python and/or R.

## Step 2. Download and install

* Download and install Workbench version 2024.12.1:

* RHEL
* Ubuntu
* SUSE 15 SP6 / openSUSE 15.6

###### RHEL 9

    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb1-1$ curl -O https://download2.rstudio.org/server/rhel9/x86_64/rstudio-workbench-rhel-2024.12.1-x86_64.rpm
    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb1-2$ sudo yum install rstudio-workbench-rhel-2024.12.1-x86_64.rpm
###### RHEL 8

    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb2-1$ curl -O https://download2.rstudio.org/server/rhel8/x86_64/rstudio-workbench-rhel-2024.12.1-x86_64.rpm
    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb2-2$ sudo yum install rstudio-workbench-rhel-2024.12.1-x86_64.rpm
###### Ubuntu 24 / 22 | Debian 12

    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb3-1$ sudo apt-get install gdebi-core
    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb3-2$ curl -O https://download2.rstudio.org/server/jammy/amd64/rstudio-workbench-2024.12.1-amd64.deb
    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb3-3$ sudo gdebi rstudio-workbench-2024.12.1-amd64.deb
###### Ubuntu 20 / Debian 11

    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb4-1$ sudo apt-get install gdebi-core
    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb4-2$ curl -O https://download2.rstudio.org/server/focal/amd64/rstudio-workbench-2024.12.1-amd64.deb
    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb4-3$ sudo gdebi rstudio-workbench-2024.12.1-amd64.deb
    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb5-1$ curl -O https://download2.rstudio.org/server/opensuse15/x86_64/rstudio-workbench-2024.12.1-x86_64.rpm
    https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb5-2$ sudo zypper install rstudio-workbench-2024.12.1-x86_64.rpm
Upon installation, Workbench configuration file (`/etc/rstudio/rserver.conf`) contains default configuration to run Workbench using local launcher sessions, without SSL enabled, listening on port 8787.

For additional information, please see the [Workbench Administration Guide Appendix](https://docs.posit.co/ide/server-pro/reference/rserver_conf.html).

## Step 3. Activate license

* Run the following command to determine the status of your license and verify that your evaluation license is active:

      https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb6-1$ sudo rstudio-server license-manager status

  If you already have a license key and your server is in an online environment, you can activate the license using the following command:  

      https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#cb7-1$ sudo rstudio-server license-manager activate <LICENSE-KEY>

  where `<LICENSE-KEY>` is the license key that was provided to you.  
  If your server is offline, you can use [this app](http://apps.rstudio.com/offline-activation/) to activate your offline license. For more information about offline license activation, refer to the [Offline activation](https://docs.posit.co/ide/server-pro/license_management/license_management.html#offline-activation).

## Step 4. Verify installation

* Now, view Workbench in a browser at `http://<SERVER-ADDRESS>:8787`, replacing `<SERVER-ADDRESS>` with the DNS name or IP address of the server.

## Next steps

### Jupyter configuration

Posit is prioritizing JupyterLab sessions as the main IDE for native Jupyter tooling. JupyterLab is a better fit for using Jupyter Notebooks on Posit Workbench, given the more advanced and full-featured IDE experience compared to Notebook Classic.

JupyterLab is enabled by default for new installs via the `labs-enabled=1` setting that’s placed in the default version of `jupyter.conf`.  
We have deprecated JupyterLab 3 now that maintenance support for that version has ended.

Classic Jupyter Notebook IDE sessions outside of JupyterLab are disabled by default, however they can be re-enabled by setting `notebooks-enabled=1` in `jupyter.conf`.

For additional Jupyter configuration options, follow the process most appropriate for your installation type:

* [On a Single Server](https://docs.posit.co/ide/server-pro/integration/jupyter-standalone.html)
* [On a Load-balanced Cluster](https://docs.posit.co/ide/server-pro/integration/jupyter-ha.html)
* [On a Slurm cluster](https://docs.posit.co/ide/server-pro/integration/jupyter-slurm.html)
* [On a Kubernetes cluster](https://docs.posit.co/ide/server-pro/integration/jupyter-kubernetes.html)

### IDE selection

* RStudio Pro: Is installed and enabled by default.
* VS Code: Is installed and enabled by default.
* Jupyter: See [Jupyter configuration](https://docs.posit.co/ide/server-pro/getting_started/installation/installation.html#jupyter-configuration) section above.

### Quarto

[Quarto](https://quarto.org/) is an open-source scientific and technical publishing system built on Pandoc and is bundled with RStudio Pro. To use it with VS Code or JupyterLab, Quarto needs to be added to the `PATH`. See [Installing Quarto](https://docs.posit.co/resources/install-quarto/) for instructions on installing Quarto or symlinking it into a location on the `PATH` for those editors.
Back to top
