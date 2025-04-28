---
title: Generic Architectures for Posit Workbench – Administration Guide
keywords: source
---

# Generic Architectures for Posit Workbench – Administration Guide

url:: https://docs.posit.co/ide/server-pro/reference-architectures/generic-architectures.html
up: [[sources]]

The architectures below represent general architectures for Posit Workbench, providing a mental model for different deployment options and the requirements to support each variation.

The following section provides specific reference architectures organized by target environment.

## Target environments

Posit Workbench can be deployed on-premises or within a cloud environment.

## Workbench on a single server

In this configuration, Workbench is installed on a single Linux server and enables:

* Access to RStudio, Jupyter Notebook, JupyterLab, and VS Code development IDEs
* Multiple concurrent sessions per user
* Use of multiple versions of Python and R

### Generic architecture

```
graph LR
u1(User)
u2(User)
u3(User)
b1(Browser)
b2(Browser)
b3(Browser)
workbench(Workbench)
rsession(RStudio Session)
jupyter(Jupyter Session)
vscode(VS Code Session)
job(Workbench Job)

u1---b1
u2---b2
u3---b3
b1---workbench
b2---workbench
b3---workbench

subgraph server[Linux Server]
    workbench---rsession
    workbench---jupyter
    workbench---vscode
    workbench---job
end

classDef server fill:#FAEEE9,stroke:#ab4d26
classDef product fill:#447099,stroke:#213D4F,color:#F2F2F2
classDef rsession fill:#7494B1,color:#F2F2F2,stroke:#213D4F
classDef element fill:#C2C2C4,stroke:#213D4F

class server server
class workbench product
class rsession,jupyter,vscode,job session
class u1,u2,u3,b1,b2,b3 element

```

## Workbench as a Load-Balanced Cluster

[Workbench](https://docs.posit.co/ide/server-pro/reference-architectures/../#intended-audience "This feature is only available with Posit Workbench.") | [Enhanced](https://docs.posit.co/ide/server-pro/reference-architectures/../license_management/license_management.html#product-tiers "This feature is only available with the purchase of the Enhanced or Advanced product tier. Click the badge for additional info.") [Advanced](https://docs.posit.co/ide/server-pro/reference-architectures/../license_management/license_management.html#product-tiers "This feature is only available with the purchase of the Advanced product tier. Click the badge for additional info.")

In this configuration, Workbench is installed on two or more Linux servers and enables:

* Load balancing to provide additional computational resources for end users
* High availability to provide redundancy
* Access to RStudio, Jupyter Notebook, JupyterLab, and VS Code development IDEs
* Multiple concurrent sessions per user
* Use of multiple versions of Python and R

### Requirements

* Users’ home directories must be stored on an external shared file server (typically an NFS server)
* Workbench metadata must be stored on an external PostgreSQL database server

### Generic architecture

The example diagrams below show cluster architectures with and without an external load balancer.

#### External load balancer

```
flowchart LR
u1(User)
u2(User)
u3(User)
b1(Browser)
b2(Browser)
b3(Browser)
workbench1(Workbench)
workbench2(Workbench)
session(RStudio Session)
jupyter(Jupyter Session)
vscode(VS Code Session)
job(Workbench Job)
lb(Load Balancer)
nfs(Shared Storage)
pg(Postgres)

u1---b1
u2---b2
u3---b3
b1---lb
b2---lb
b3---lb
lb---workbench1
lb---workbench2
server1-.-nfs
server2-.-nfs
server1-.-pg
server2-.-pg

subgraph server1 [Linux Server]
    workbench1---jupyter
    workbench1---job
end

subgraph server2 [Linux Server]
    workbench2---session
    workbench2---vscode
end

workbench1-.-workbench2

classDef server fill:#FAEEE9,stroke:#ab4d26
classDef product fill:#447099,stroke:#213D4F,color:#F2F2F2
classDef session fill:#7494B1,color:#F2F2F2,stroke:#213D4F
classDef element fill:#C2C2C4,stroke:#213D4F
classDef req fill:#72994E,stroke:#1F4F4F,color:#F2F2F2

class server1,server2 server
class workbench1,workbench2 product
class session,jupyter,vscode,job session
class u1,u2,u3,b1,b2,b3,lb element
class pg,nfs req

```

#### Single node routing

```
flowchart LR
u1(User)
u2(User)
u3(User)
b1(Browser)
b2(Browser)
b3(Browser)
workbench1(Workbench)
workbench2(Workbench)
session(RStudio Session)
jupyter(Jupyter Session)
vscode(VS Code Session)
job(Workbench Job)
nfs(Shared Storage)
pg(Postgres)

subgraph server1 [Linux Server]
    workbench1---session
    workbench1---job
end

subgraph server2 [Linux Server]
    workbench2---jupyter
    workbench2---vscode
end

u1---b1
u2---b2
u3---b3
b1---workbench1
b2---workbench1
b3---workbench1
server1-.-nfs
server2-.-nfs
server1-.-pg
server2-.-pg
workbench1-.-workbench2

classDef server fill:#FAEEE9,stroke:#ab4d26
classDef product fill:#447099,stroke:#213D4F,color:#F2F2F2
classDef session fill:#7494B1,color:#F2F2F2,stroke:#213D4F
classDef element fill:#C2C2C4,stroke:#213D4F
classDef req fill:#72994E,stroke:#1F4F4F,color:#F2F2F2

class server1,server2 server
class workbench1,workbench2 product
class session,jupyter,vscode,job session
class u1,u2,u3,b1,b2,b3,lb element
class pg,nfs req
```

## Workbench with an External Resource Manager

[Workbench](https://docs.posit.co/ide/server-pro/reference-architectures/../#intended-audience "This feature is only available with Posit Workbench.") | [Advanced](https://docs.posit.co/ide/server-pro/reference-architectures/../license_management/license_management.html#product-tiers "This feature is only available with the purchase of the Advanced product tier. Click the badge for additional info.")

In this configuration, Workbench is installed on one or more Linux servers and is configured with Launcher and a external backend where interactive sessions and non-interactive Workbench jobs are run.

Launcher is a plugin that allows you to run sessions and background jobs on external cluster resource managers. In addition to Kubernetes and Slurm, Launcher can be extended to work with other cluster resource managers using the [Launcher SDK](https://posit.co/products/open-source/launcher-plugin-sdk/). [AWS Sagemaker](https://docs.aws.amazon.com/sagemaker/latest/dg/rstudio.html) and [Altair Grid Engine](https://altair.com/grid-engine) are two example uses of this SDK where a partner developed a launcher plugin for their respective cluster resource manager.

This enables:

* Users to run sessions and jobs on external compute cluster(s)
* Optional replicas for high availability
* Access to RStudio, Jupyter Notebook, JupyterLab, and VS Code development IDEs
* Multiple concurrent sessions per user
* Use of multiple versions of Python and R

### Requirements

* Users’ home directories must be stored on an external shared file server (typically an NFS server)
* It is strongly recommended that Workbench metadata be stored on an external PostgreSQL database server

### Generic architecture

The architecture below provides a mental model for how Launcher interacts with an external resource manager. For deployments using Kubernetes or Slurm, refer to the architectural overview diagrams below for these back ends:

* [Workbench with Kubernetes](#workbench-with-kubernetes)
* [Workbench with Slurm](#workbench-with-slurm)

```
flowchart LR
u1(User)
u2(User)
u3(User)
b1(Browser)
b2(Browser)
b3(Browser)
workbench(Workbench)
rsession(RStudio Session)
jupyter(Jupyter Session)
vscode(VS Code Session)
job(Workbench Job)
launcher(Launcher)
nfs(Shared Storage)
pg(Postgres)

u1---b1
u2---b2
u3---b3
b1---workbench
b2---workbench
b3---workbench

subgraph server
    workbench---launcher
end

subgraph cluster [Resource Manager]
    launcher---rsession
    launcher---jupyter
    launcher---vscode
    launcher---job
end

server-.-pg
server-.-nfs
cluster-.-nfs

classDef server fill:#FAEEE9,stroke:#ab4d26
classDef product fill:#447099,stroke:#213D4F,color:#F2F2F2
classDef session fill:#7494B1,color:#F2F2F2,stroke:#213D4F
classDef element fill:#C2C2C4,stroke:#213D4F
classDef req fill:#72994E,stroke:#1F4F4F,color:#F2F2F2

class server,cluster server
class workbench,launcher product
class rsession,jupyter,vscode,job session
class u1,u2,u3,b1,b2,b3,lb element
class pg,nfs req
```

### Workbench with Kubernetes

In this configuration, Workbench is installed entirely inside a Kubernetes cluster and enables:

* User sessions and jobs to run in isolated pods, potentially from different base images
* The entire installation to be managed in Kubernetes with tools like [Helm](https://github.com/rstudio/helm)
* Optional replicas for high availability
* Access to RStudio, Jupyter Notebook, JupyterLab, and VS Code development IDEs
* Multiple concurrent sessions per user
* Use of multiple versions of Python and R

#### Requirements

* Users’ home directories must be stored on an external shared file server (typically an NFS server)
* Workbench metadata must be stored on an external PostgreSQL database server

#### Generic architecture

```
flowchart LR
u1(User)
u2(User)
u3(User)
b1(Browser)
b2(Browser)
b3(Browser)
ingress(Ingress Controller <br/> nginx/nginx-ingress)
workbench(Workbench <br/> Launcher <br/> ghcr.io/rstudio/rstudio-workbench)
rsession(RStudio Session <br/> ghcr.io/example/custom-image-1)
jupyter(Jupyter Session <br/> ghcr.io/example/custom-image-2)
vscode(VS Code Session <br/> ghcr.io/examle/custom-image-1)
job(Workbench Job <br/> docker.io/example/custom-image-2)
nfs(Shared Storage)
pg(Postgres)


u1---b1
u2---b2
u3---b3
b1---ingress
b2---ingress
b3---ingress

subgraph k8s [Kubernetes cluster]
    ingress---workbench
    workbench---rsession
    workbench---jupyter
    workbench---vscode
    workbench---job
end

k8s-..-pg
k8s-..-nfs

classDef server fill:#FAEEE9,stroke:#ab4d26
classDef product fill:#447099,stroke:#213D4F,color:#F2F2F2
classDef session fill:#7494B1,color:#F2F2F2,stroke:#213D4F
classDef element fill:#C2C2C4,stroke:#213D4F
classDef req fill:#72994E,stroke:#1F4F4F,color:#F2F2F2

class k8s server
class workbench product
class ingress,rsession,jupyter,vscode,job session
class u1,u2,u3,b1,b2,b3,lb element
class pg,nfs req
```

#### Additional Kubernetes Resources

* [FAQ for Workbench with Launcher and Kubernetes](https://support.posit.co/hc/en-us/articles/360021328733-FAQ-for-RStudio-Server-Pro-with-Launcher-and-Kubernetes)
* [Using Docker images with Workbench, Launcher, and Kubernetes](https://support.posit.co/hc/en-us/articles/360019253393-Using-Docker-images-with-RStudio-Server-Pro-Launcher-and-Kubernetes)
* [Integrating Workbench with Kubernetes](https://docs.posit.co/ide/server-pro/reference-architectures/../integration/launcher-kubernetes.html)

### Workbench with Slurm

In this configuration, Workbench is installed on one or more Linux servers, is configured with Launcher and a Slurm cluster backend, and enables:

* Users to run sessions and submit jobs via the Slurm Launcher against a Slurm cluster with arbitrary number of compute nodes of a given type.
* Optional replicas for high availability.
* Access to RStudio, Jupyter Notebook, JupyterLab, and VS Code development IDEs.
* Multiple concurrent sessions per user.
* Use of multiple versions of Python and R.

#### Requirements

* Users’ home directories must be stored on a shared file system (typically an NFS server). Shared storage typically includes `/home`, `/scratch`, data folders, and session containers.
* Session components need to be accessible from the Slurm compute nodes (installed or mounted), or [Singularity](https://solutions.posit.co/architecting/launcher/singularity/) can be used as session containers.
* Users must exist on both Workbench servers and the Slurm cluster node, for example by pointing to the same authentication provider.
* The use of an external PostgreSQL database server is necessary when using multiple Workbench servers.

#### Generic architecture

```
flowchart LR
u1(User)
u2(User)
u3(User)
b1(Browser)
b2(Browser)
b3(Browser)
smn(Slurm Head Node)
workbench(Workbench)
rsession(RStudio Session)
jupyter(Jupyter Session)
vscode(VS Code Session)
job(Workbench Job)
launcher(Launcher)
nfs(Shared Storage)
pg(Postgres)
sj1(submits job)
sj2(schedules job)

u1---b1
u2---b2
u3---b3
b1---workbench
b2---workbench
b3---workbench

subgraph hpc [HPC]

    subgraph box [ ]

        subgraph server [Submit / Login Node]
            workbench---launcher
        end 

    %%launcher -- submits job --- smn

    launcher --- sj1 
    sj1 --- smn

    subgraph box2 [ ]

    subgraph main [Head Node]
        smn
    end

    smn--- sj2

    sj2---rsession
    sj2---jupyter
    sj2---vscode
    sj2---job
    

    subgraph cluster [Compute Node]
        rsession
        jupyter
        vscode
        job
    
    end
    end
    end

    server-.-pg
    
    server-.-nfs
    main-.-nfs
    cluster-.-nfs

end

classDef hpc fill:#eeeeee,stroke:#666666
classDef server fill:#FAEEE9,stroke:#ab4d26
classDef product fill:#7494B1,color:#F2F2F2,stroke:#213D4F
classDef session fill:#7494B1,color:#F2F2F2,stroke:#213D4F
classDef element fill:#C2C2C4,stroke:#213D4F
classDef req fill:#72994E,stroke:#1F4F4F,color:#F2F2F2
classDef note fill:#eeeeee,stroke:#333,stroke-width:0px,color:#213D4F,stroke-dasharray: 5 5
classDef box fill:#eeeeee,stroke:#eeeeee

class hpc hpc
class server,cluster,main server
class workbench,launcher,smn product
class rsession,jupyter,vscode,job session
class u1,u2,u3,b1,b2,b3,lb element
class pg,nfs req
class note,sj1,sj2 note
class box,box2 box
```

#### Additional Slurm Resources

* [FAQ for Workbench with Launcher and Slurm](https://support.posit.co/hc/en-us/articles/360035543713-FAQ-for-RStudio-Server-Pro-with-Launcher-and-Slurm)
* [Integrating Workbench with Slurm](https://docs.posit.co/rsw/integration/launcher-slurm/)
* [Workbench with Slurm and Singularity](https://solutions.posit.co/architecting/launcher/singularity/)
Back to top
