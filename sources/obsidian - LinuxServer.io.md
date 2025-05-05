---
title: obsidian - LinuxServer.io
---

# obsidian - LinuxServer.io

url:: https://docs.linuxserver.io/images/docker-obsidian/#application-setup
up: [[sources]]

#source

# [linuxserver/obsidian](https://github.com/linuxserver/docker-obsidian)[¶](#linuxserverobsidian "Permanent link")

[![Scarf.io pulls](https://scarf.sh/installs-badge/linuxserver-ci/linuxserver%2Fobsidian?color=94398d&label-color=555555&logo-color=ffffff&style=for-the-badge&package-type=docker)](https://scarf.sh) [![GitHub Stars](https://img.shields.io/github/stars/linuxserver/docker-obsidian.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://github.com/linuxserver/docker-obsidian) [![GitHub Release](https://img.shields.io/github/release/linuxserver/docker-obsidian.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://github.com/linuxserver/docker-obsidian/releases) [![GitHub Package Repository](https://img.shields.io/static/v1.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=linuxserver.io&message=GitHub%20Package&logo=github)](https://github.com/linuxserver/docker-obsidian/packages) [![GitLab Container Registry](https://img.shields.io/static/v1.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=linuxserver.io&message=GitLab%20Registry&logo=gitlab)](https://gitlab.com/linuxserver.io/docker-obsidian/container_registry) [![Quay.io](https://img.shields.io/static/v1.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=linuxserver.io&message=Quay.io)](https://quay.io/repository/linuxserver.io/obsidian) [![Docker Pulls](https://img.shields.io/docker/pulls/linuxserver/obsidian.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=pulls&logo=docker)](https://hub.docker.com/r/linuxserver/obsidian) [![Docker Stars](https://img.shields.io/docker/stars/linuxserver/obsidian.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=stars&logo=docker)](https://hub.docker.com/r/linuxserver/obsidian) [![Jenkins Build](https://img.shields.io/jenkins/build?labelColor=555555&logoColor=ffffff&style=for-the-badge&jobUrl=https%3A%2F%2Fci.linuxserver.io%2Fjob%2FDocker-Pipeline-Builders%2Fjob%2Fdocker-obsidian%2Fjob%2Fmain%2F&logo=jenkins)](https://ci.linuxserver.io/job/Docker-Pipeline-Builders/job/docker-obsidian/job/main/) [![LSIO CI](https://img.shields.io/badge/dynamic/yaml?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=CI&query=CI&url=https%3A%2F%2Fci-tests.linuxserver.io%2Flinuxserver%2Fobsidian%2Flatest%2Fci-status.yml)](https://ci-tests.linuxserver.io/linuxserver/obsidian/latest/index.html)

[Obsidian](https://obsidian.md) is a note-taking app that lets you create, link, and organize your notes on your device, with hundreds of plugins and themes to customize your workflow. You can also publish your notes online, access them offline, and sync them securely with end-to-end encryption.

[![obsidian](https://raw.githubusercontent.com/linuxserver/docker-templates/master/linuxserver.io/img/obsidian-logo.png)](https://obsidian.md)

## Supported Architectures[¶](#supported-architectures "Permanent link")

We utilise the docker manifest for multi-platform awareness. More information is available from docker [here](https://distribution.github.io/distribution/spec/manifest-v2-2/#manifest-list) and our announcement [here](https://blog.linuxserver.io/2019/02/21/the-lsio-pipeline-project/).

Simply pulling `lscr.io/linuxserver/obsidian:latest` should retrieve the correct image for your arch, but you can also pull specific arch images via tags.

The architectures supported by this image are:

| Architecture | Available |          Tag          |
|--------------|-----------|-----------------------|
| x86-64       | ✅         | amd64-<version tag>   |
| arm64        | ✅         | arm64v8-<version tag> |
| armhf        | ❌         |                       |

## Application Setup[¶](#application-setup "Permanent link")

The application can be accessed at:

* http://yourhost:3000/
* https://yourhost:3001/

**Modern GUI desktop apps have issues with the latest Docker and syscall compatibility, you can use Docker with the `--security-opt seccomp=unconfined` setting to allow these syscalls on hosts with older Kernels or libseccomp**

### Security[¶](#security "Permanent link")

Warning

Do not put this on the Internet if you do not know what you are doing.

By default this container has no authentication and the optional environment variables `CUSTOM_USER` and `PASSWORD` to enable basic http auth via the embedded NGINX server should only be used to locally secure the container from unwanted access on a local network. If exposing this to the Internet we recommend putting it behind a reverse proxy, such as [SWAG](https://github.com/linuxserver/docker-swag), and ensuring a secure authentication solution is in place. From the web interface a terminal can be launched and it is configured for passwordless sudo, so anyone with access to it can install and run whatever they want along with probing your local network.

### Options in all KasmVNC based GUI containers[¶](#options-in-all-kasmvnc-based-gui-containers "Permanent link")

This container is based on [Docker Baseimage KasmVNC](https://github.com/linuxserver/docker-baseimage-kasmvnc) which means there are additional environment variables and run configurations to enable or disable specific functionality.

#### Optional environment variables[¶](#optional-environment-variables "Permanent link")

|     Variable      |                                                                                       Description                                                                                       |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUSTOM_PORT       | Internal port the container listens on for http if it needs to be swapped from the default 3000.                                                                                        |
| CUSTOM_HTTPS_PORT | Internal port the container listens on for https if it needs to be swapped from the default 3001.                                                                                       |
| CUSTOM_USER       | HTTP Basic auth username, abc is default.                                                                                                                                               |
| PASSWORD          | HTTP Basic auth password, abc is default. If unset there will be no auth                                                                                                                |
| SUBFOLDER         | Subfolder for the application if running a subfolder reverse proxy, need both slashes IE `/subfolder/`                                                                                  |
| TITLE             | The page title displayed on the web browser, default "KasmVNC Client".                                                                                                                  |
| FM_HOME           | This is the home directory (landing) for the file manager, default "/config".                                                                                                           |
| START_DOCKER      | If set to false a container with privilege will not automatically start the DinD Docker setup.                                                                                          |
| DRINODE           | If mounting in /dev/dri for [DRI3 GPU Acceleration](https://www.kasmweb.com/kasmvnc/docs/master/gpu_acceleration.html) allows you to specify the device to use IE `/dev/dri/renderD128` |
| DISABLE_IPV6      | If set to true or any value this will disable IPv6                                                                                                                                      |
| LC_ALL            | Set the Language for the container to run as IE `fr_FR.UTF-8` `ar_AE.UTF-8`                                                                                                             |
| NO_DECOR          | If set the application will run without window borders in openbox for use as a PWA.                                                                                                     |
| NO_FULL           | Do not autmatically fullscreen applications when using openbox.                                                                                                                         |

#### Optional run configurations[¶](#optional-run-configurations "Permanent link")

|                    Variable                    |                                                                                                                       Description                                                                                                                       |
|------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--privileged`                                 | Will start a Docker in Docker (DinD) setup inside the container to use docker in an isolated environment. For increased performance mount the Docker directory inside the container to the host IE `-v /home/user/docker-data:/var/lib/docker`.         |
| `-v /var/run/docker.sock:/var/run/docker.sock` | Mount in the host level Docker socket to either interact with it via CLI or use Docker enabled applications.                                                                                                                                            |
| `--device /dev/dri:/dev/dri`                   | Mount a GPU into the container, this can be used in conjunction with the `DRINODE` environment variable to leverage a host video card for GPU accelerated applications. Only **Open Source** drivers are supported IE (Intel,AMDGPU,Radeon,ATI,Nouveau) |

### Language Support - Internationalization[¶](#language-support-internationalization "Permanent link")

The environment variable `LC_ALL` can be used to start this container in a different language than English simply pass for example to launch the Desktop session in French `LC_ALL=fr_FR.UTF-8`. Some languages like Chinese, Japanese, or Korean will be missing fonts needed to render properly known as cjk fonts, but others may exist and not be installed inside the container depending on what underlying distribution you are running. We only ensure fonts for Latin characters are present. Fonts can be installed with a mod on startup.

To install cjk fonts on startup as an example pass the environment variables (Alpine base):  

    #__codelineno-0-1-e DOCKER_MODS=linuxserver/mods:universal-package-install 
    #__codelineno-0-2-e INSTALL_PACKAGES=fonts-noto-cjk
    #__codelineno-0-3-e LC_ALL=zh_CN.UTF-8
The web interface has the option for "IME Input Mode" in Settings which will allow non english characters to be used from a non en_US keyboard on the client. Once enabled it will perform the same as a local Linux installation set to your locale.

### DRI3 GPU Acceleration (KasmVNC interface)[¶](#dri3-gpu-acceleration-kasmvnc-interface "Permanent link")

For accelerated apps or games, render devices can be mounted into the container and leveraged by applications using:

`--device /dev/dri:/dev/dri`

This feature only supports **Open Source** GPU drivers:

| Driver |                              Description                              |
|--------|-----------------------------------------------------------------------|
| Intel  | i965 and i915 drivers for Intel iGPU chipsets                         |
| AMD    | AMDGPU, Radeon, and ATI drivers for AMD dedicated or APU chipsets     |
| NVIDIA | nouveau2 drivers only, closed source NVIDIA drivers lack DRI3 support |

The `DRINODE` environment variable can be used to point to a specific GPU. Up to date information can be found [here](https://www.kasmweb.com/kasmvnc/docs/master/gpu_acceleration.html)

### Nvidia GPU Support (KasmVNC interface)[¶](#nvidia-gpu-support-kasmvnc-interface "Permanent link")

**Nvidia support is not compatible with Alpine based images as Alpine lacks Nvidia drivers**

Nvidia support is available by leveraging Zink for OpenGL support. This can be enabled with the following run flags:

|     Variable     |                                          Description                                          |
|------------------|-----------------------------------------------------------------------------------------------|
| --gpus all       | This can be filtered down but for most setups this will pass the one Nvidia GPU on the system |
| --runtime nvidia | Specify the Nvidia runtime which mounts drivers and tools in from the host                    |

The compose syntax is slightly different for this as you will need to set nvidia as the default runtime:  

    #__codelineno-1-1sudo nvidia-ctk runtime configure --runtime=docker --set-as-default
    #__codelineno-1-2sudo service docker restart
And to assign the GPU in compose:  

    #__codelineno-2-1services:
    #__codelineno-2-2  obsidian:
    #__codelineno-2-3    image: lscr.io/linuxserver/obsidian:latest
    #__codelineno-2-4    deploy:
    #__codelineno-2-5      resources:
    #__codelineno-2-6        reservations:
    #__codelineno-2-7          devices:
    #__codelineno-2-8            - driver: nvidia
    #__codelineno-2-9              count: 1
    #__codelineno-2-10              capabilities: [compute,video,graphics,utility]
### Application management[¶](#application-management "Permanent link")

#### PRoot Apps[¶](#proot-apps "Permanent link")

If you run system native installations of software IE `sudo apt-get install filezilla` and then upgrade or destroy/re-create the container that software will be removed and the container will be at a clean state. For some users that will be acceptable and they can update their system packages as well using system native commands like `apt-get upgrade`. If you want Docker to handle upgrading the container and retain your applications and settings we have created [proot-apps](https://github.com/linuxserver/proot-apps) which allow portable applications to be installed to persistent storage in the user's `$HOME` directory and they will work in a confined Docker environment out of the box. These applications and their settings will persist upgrades of the base container and can be mounted into different flavors of KasmVNC based containers on the fly. This can be achieved from the command line with:  

    #__codelineno-3-1proot-apps install filezilla

PRoot Apps is included in all KasmVNC based containers, a list of linuxserver.io supported applications is located [HERE](https://github.com/linuxserver/proot-apps?tab=readme-ov-file#supported-apps).

#### Native Apps[¶](#native-apps "Permanent link")

It is possible to install extra packages during container start using [universal-package-install](https://github.com/linuxserver/docker-mods/tree/universal-package-install). It might increase starting time significantly. PRoot is preferred.  

    #__codelineno-4-1  environment:
    #__codelineno-4-2    - DOCKER_MODS=linuxserver/mods:universal-package-install
    #__codelineno-4-3    - INSTALL_PACKAGES=libfuse2|git|gdb
## Usage[¶](#usage "Permanent link")

To help you get started creating a container from this image you can either use docker-compose or the docker cli.  
Info

Unless a parameter is flaged as 'optional', it is *mandatory* and a value must be provided.

### docker-compose (recommended, [click here for more info](https://docs.linuxserver.io/general/docker-compose))[¶](#docker-compose-recommended-click-here-for-more-info "Permanent link")

    #__codelineno-5-1---
    #__codelineno-5-2services:
    #__codelineno-5-3  obsidian:
    #__codelineno-5-4    image: lscr.io/linuxserver/obsidian:latest
    #__codelineno-5-5    container_name: obsidian
    #__codelineno-5-6    security_opt:
    #__codelineno-5-7      - seccomp:unconfined #optional
    #__codelineno-5-8    environment:
    #__codelineno-5-9      - PUID=1000
    #__codelineno-5-10      - PGID=1000
    #__codelineno-5-11      - TZ=Etc/UTC
    #__codelineno-5-12    volumes:
    #__codelineno-5-13      - /path/to/config:/config
    #__codelineno-5-14    ports:
    #__codelineno-5-15      - 3000:3000
    #__codelineno-5-16      - 3001:3001
    #__codelineno-5-17    devices:
    #__codelineno-5-18      - /dev/dri:/dev/dri #optional
    #__codelineno-5-19    shm_size: "1gb"
    #__codelineno-5-20    restart: unless-stopped
### docker cli ([click here for more info](https://docs.docker.com/engine/reference/commandline/cli/))[¶](#docker-cli-click-here-for-more-info "Permanent link")

    #__codelineno-6-1docker run -d \
    #__codelineno-6-2  --name=obsidian \
    #__codelineno-6-3  --security-opt seccomp=unconfined `#optional` \
    #__codelineno-6-4  -e PUID=1000 \
    #__codelineno-6-5  -e PGID=1000 \
    #__codelineno-6-6  -e TZ=Etc/UTC \
    #__codelineno-6-7  -p 3000:3000 \
    #__codelineno-6-8  -p 3001:3001 \
    #__codelineno-6-9  -v /path/to/config:/config \
    #__codelineno-6-10  --device /dev/dri:/dev/dri `#optional` \
    #__codelineno-6-11  --shm-size="1gb" \
    #__codelineno-6-12  --restart unless-stopped \
    #__codelineno-6-13  lscr.io/linuxserver/obsidian:latest
## Parameters[¶](#parameters "Permanent link")

Containers are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate `<external>:<internal>` respectively. For example, `-p 8080:80` would expose port `80` from inside the container to be accessible from the host's IP on port `8080` outside the container.

### Ports (`-p`)[¶](#ports-p "Permanent link")

|  Parameter  |          Function           |
|-------------|-----------------------------|
| `3000:3000` | Obsidian desktop gui.       |
| `3001:3001` | Obsidian desktop gui HTTPS. |

### Environment Variables (`-e`)[¶](#environment-variables-e "Permanent link")

|     Env      |                                                    Function                                                    |
|--------------|----------------------------------------------------------------------------------------------------------------|
| `PUID=1000`  | for UserID - see below for explanation                                                                         |
| `PGID=1000`  | for GroupID - see below for explanation                                                                        |
| `TZ=Etc/UTC` | specify a timezone to use, see this [list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List). |

### Volume Mappings (`-v`)[¶](#volume-mappings-v "Permanent link")

|  Volume   |                                 Function                                  |
|-----------|---------------------------------------------------------------------------|
| `/config` | Users home directory in the container, stores program settings and files. |

### Device Mappings (`--device`)[¶](#device-mappings-device "Permanent link")

| Parameter  |                  Function                  |
|------------|--------------------------------------------|
| `/dev/dri` | Add this for GL support (Linux hosts only) |

#### Miscellaneous Options[¶](#miscellaneous-options "Permanent link")

|              Parameter              |                                                       Function                                                       |
|-------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| `--shm-size=`                       | This is needed for electron applications to function properly.                                                       |
| `--security-opt seccomp=unconfined` | For Docker Engine only, many modern gui apps need this to function on older hosts as syscalls are unknown to Docker. |

## Environment variables from files (Docker secrets)[¶](#environment-variables-from-files-docker-secrets "Permanent link")

You can set any environment variable from a file by using a special prepend `FILE__`.

As an example:  

    #__codelineno-7-1-e FILE__MYVAR=/run/secrets/mysecretvariable

Will set the environment variable `MYVAR` based on the contents of the `/run/secrets/mysecretvariable` file.

## Umask for running applications[¶](#umask-for-running-applications "Permanent link")

For all of our images we provide the ability to override the default umask settings for services started within the containers using the optional `-e UMASK=022` setting. Keep in mind umask is not chmod it subtracts from permissions based on it's value it does not add. Please read up [here](https://en.wikipedia.org/wiki/Umask) before asking for support.

## User / Group Identifiers[¶](#user-group-identifiers "Permanent link")

When using volumes (`-v` flags), permissions issues can arise between the host OS and the container, we avoid this issue by allowing you to specify the user `PUID` and group `PGID`.

Ensure any volume directories on the host are owned by the same user you specify and any permissions issues will vanish like magic.

In this instance `PUID=1000` and `PGID=1000`, to find yours use `id your_user` as below:

Example output:  

    #__codelineno-9-1uid=1000(your_user) gid=1000(your_user) groups=1000(your_user)

## Docker Mods[¶](#docker-mods "Permanent link")

[![Docker Mods](https://img.shields.io/badge/dynamic/yaml?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=obsidian&query=%24.mods%5B%27obsidian%27%5D.mod_count&url=https%3A%2F%2Fraw.githubusercontent.com%2Flinuxserver%2Fdocker-mods%2Fmaster%2Fmod-list.yml)](https://mods.linuxserver.io/?mod=obsidian "view available mods for this container.") [![Docker Universal Mods](https://img.shields.io/badge/dynamic/yaml?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=universal&query=%24.mods%5B%27universal%27%5D.mod_count&url=https%3A%2F%2Fraw.githubusercontent.com%2Flinuxserver%2Fdocker-mods%2Fmaster%2Fmod-list.yml)](https://mods.linuxserver.io/?mod=universal "view available universal mods.")

We publish various [Docker Mods](https://github.com/linuxserver/docker-mods) to enable additional functionality within the containers. The list of Mods available for this image (if any) as well as universal mods that can be applied to any one of our images can be accessed via the dynamic badges above.

## Support Info[¶](#support-info "Permanent link")

* Shell access whilst the container is running:

      #__codelineno-10-1docker exec -it obsidian /bin/bash

* To monitor the logs of the container in realtime:

* Container version number:

      #__codelineno-12-1docker inspect -f '{{ index .Config.Labels "build_version" }}' obsidian

* Image version number:

      #__codelineno-13-1docker inspect -f '{{ index .Config.Labels "build_version" }}' lscr.io/linuxserver/obsidian:latest

## Updating Info[¶](#updating-info "Permanent link")

Most of our images are static, versioned, and require an image update and container recreation to update the app inside. With some exceptions (noted in the relevant readme.md), we do not recommend or support updating apps inside the container. Please consult the [Application Setup](#application-setup) section above to see if it is recommended for the image.

Below are the instructions for updating containers:

### Via Docker Compose[¶](#via-docker-compose "Permanent link")

* Update images:

  * All images:

  * Single image:

        #__codelineno-15-1docker-compose pull obsidian

* Update containers:

  * All containers:

  * Single container:

        #__codelineno-17-1docker-compose up -d obsidian

* You can also remove the old dangling images:

### Via Docker Run[¶](#via-docker-run "Permanent link")

* Update the image:

      #__codelineno-19-1docker pull lscr.io/linuxserver/obsidian:latest

* Stop the running container:

* Delete the container:

* Recreate a new container with the same docker run parameters as instructed above (if mapped correctly to a host folder, your `/config` folder and settings will be preserved)

* You can also remove the old dangling images:

### Image Update Notifications - Diun (Docker Image Update Notifier)[¶](#image-update-notifications-diun-docker-image-update-notifier "Permanent link")

Tip

We recommend [Diun](https://crazymax.dev/diun/) for update notifications. Other tools that automatically update containers unattended are not recommended or supported.

## Building locally[¶](#building-locally "Permanent link")

If you want to make local modifications to these images for development purposes or just to customize the logic:  

    #__codelineno-23-1git clone https://github.com/linuxserver/docker-obsidian.git
    #__codelineno-23-2cd docker-obsidian
    #__codelineno-23-3docker build \
    #__codelineno-23-4  --no-cache \
    #__codelineno-23-5  --pull \
    #__codelineno-23-6  -t lscr.io/linuxserver/obsidian:latest .
The ARM variants can be built on x86_64 hardware and vice versa using `lscr.io/linuxserver/qemu-static`  

    #__codelineno-24-1docker run --rm --privileged lscr.io/linuxserver/qemu-static --reset

Once registered you can define the dockerfile to use with `-f Dockerfile.aarch64`.

To help with development, we generate this dependency graph.
Init dependency graph  

obsidian:latest Base Images docker-mods base legacy-services custom services init-services ci-service-check init-migrations init-adduser init-kasmvnc-end init-config init-os-end init-config-end init-crontab-config init-obsidian-config init-mods-end init-custom-files init-envfile init-kasmvnc init-nginx init-kasmvnc-config init-video init-mods init-mods-package-install svc-cron svc-de svc-nginx svc-docker svc-kasmvnc svc-pulseaudio svc-kclient baseimage-kasmvnc:debianbookworm baseimage-debian:bookworm fix-attr + legacy cont-init


obsidian:latest Base Images docker-mods base legacy-services custom services init-services ci-service-check init-migrations init-adduser init-kasmvnc-end init-config init-os-end init-config-end init-crontab-config init-obsidian-config init-mods-end init-custom-files init-envfile init-kasmvnc init-nginx init-kasmvnc-config init-video init-mods init-mods-package-install svc-cron svc-de svc-nginx svc-docker svc-kasmvnc svc-pulseaudio svc-kclient baseimage-kasmvnc:debianbookworm baseimage-debian:bookworm fix-attr + legacy cont-init

## Versions[¶](#versions "Permanent link")

* **03.04.25:** - Update chromium launch options to improve performance.
* **18.06.24:** - Fix application init for Kasm.
* **06.04.24:** - Initial release.

| April 4, 2025 February 5, 2019
