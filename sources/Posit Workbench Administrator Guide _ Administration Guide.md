Posit Workbench allows users to:

* Launch jobs into remote or local clusters using Workbench Jobs.
* Run multiple-concurrent sessions.
* Use multiple different versions of R and Python.
* Collaborate on projects.
* Easily connect to shared resources such as databases or mounted file systems.
* Use a centralized installation and configuration of R/Python, R/Python packages, TeX, Quarto, and other supporting libraries.
* Access more powerful computing resources (memory, processors, etc.) available on a well-equipped server.

Posit Workbench is a secure and scalable developer platform for data science in R and Python. Users can choose between five different IDEs:

1. [RStudio Pro](https://docs.posit.co/ide/server-pro/user/rstudio-pro/getting-started/)
2. [Positron Pro](https://docs.posit.co/ide/server-pro/user/positron/getting-started/)
3. [VS Code](https://code.visualstudio.com/)
4. [JupyterLab](https://jupyterlab.readthedocs.io/en/stable/)
5. [Jupyter Notebook](https://jupyter-notebook.readthedocs.io/en/latest/)

### About this guide

This guide describes *Posit Workbench*, which adds many enhancements to the open-source version of RStudio Server, including:

* The ability to run multiple concurrent sessions per user.
* Support for RStudio Pro, Positron Pro, VS Code, JupyterLab, and Jupyter Notebook editor sessions.
* Flexible use of multiple versions of R and Python on the same server.
* The ability to run sessions on external cluster nodes, such as Kubernetes or Slurm.
* Project sharing for easy collaboration within workgroups.
* Load balancing for increased capacity and higher availability.
* An administrative dashboard that provides insight into active sessions, server health, and monitoring of system-wide and per-user performance and resource metrics;
* Authentication using system accounts, Active Directory, LDAP, SAML, OpenID, or Google Accounts;
* Full support for PAM (including PAM sessions for dynamically provisioning user resources);
* Ability to establish per-user or per-group CPU priorities and memory limits;
* HTTP enhancements including support for SSL and keep-alive for improved performance;
* Ability to restrict access to the server by IP;
* Customizable server health checks; and
* Suspend, terminate, or assume control of user sessions; Impersonate users for assistance and troubleshooting.

#### Intended audience

The documentation applies to both RStudio Server (Open Source) and Posit Workbench (Pro) unless the section is specifically marked [Workbench](https://docs.posit.co/ide/server-pro/#intended-audience "This feature is only available with Posit Workbench."), in which case it only applies to Posit Workbench.
Back to top
