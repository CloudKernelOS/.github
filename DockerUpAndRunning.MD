This page forked from [Sean P. Kane](https://gist.github.com/spkane)'s (Gist)[https://gist.github.com/spkane/cf874d0cdc4af09309779a91f2bcaaea] for a ***highly-recommended*** hands-on [Docker:Up and Running course](https://learning.oreilly.com/live-events/docker-up-and-running/0636920062035/0636920076489/).

---
marp: true
theme: spkane
---
<!-- markdownlint-disable MD001 MD003 MD014 MD022 MD024 MD032 MD033 MD034 MD041 -->
<!-- theme: spkane -->
<!-- _class: lead -->

<!-- size: 16:9 -->

# Docker: Up and Running
## Docker Images

<br></br>
<br></br>

###### Release %BUILD_RELEASE%

---

<!-- _class: lead -->

# Instructor
# [Sean P. Kane](https://www.linkedin.com/in/spkane/)

### [@spkane](https://twitter.com/spkane)
##### [@superorbital_io](https://twitter.com/superorbital_io)
![height:1.3cm](images/superorbital-horizontal-darkthemes.png)

![bg right](images/skane-2018-side-black-cropped.jpg)

---

<!-- footer: Copyright © 2015-2022, Sean P. Kane (@spkane) -->
<!-- paginate: true -->
<!-- _class: lead -->

# [Follow Along Guide](https://gist.github.com/spkane/a15467f50545483282496624629bc62e)
## Textual Slides

---

<!-- footer: Copyright © 2015-2022, Sean P. Kane (@spkane) -->
<!-- paginate: true -->
<!-- _class: lead -->

# [Katacoda Online Sandbox](https://learning.oreilly.com/scenarios/devops-tools-sandbox/9781098126469/)

##### https://learning.oreilly.com/scenarios/devops-tools-sandbox/9781098126469/

---

# Prerequisites (1 of 2)
* A recent computer and OS
  * Recent/Stable Linux, macOS, or Windows 10+
  * root/admin rights
  * Sufficient resources to run one 2 CPU virtual machine (VM)
  * CPU Virtualization extensions MUST be enabled in your BIOS/EFI
  * Reliable and fast internet connectivity
* Docker Community/Desktop Edition (on Linux also install Docker Compose V2)

---

# Prerequisites (2 of 2)
* A graphical web browser
* A text editor
* A software package manager
* Git client
* General comfort with the command line will be helpful.
* [optional] tar, wget, curl, jq, SSH client

---

# A Note for Powershell Users
 Terminal commands reflect the Unix bash shell. PowerShell users will need to adjust the commands.
* Unix Variables
  * `export MY_VAR=test`
  * `echo ${MY_VAR}`
* Windows 10+ Variables (powershell)
  * `$env:my_var = "test"`
  * `Get-ChildItem Env:my_var`

---

# Translation Key

**\\** - Unix Shell Line Continuation
**`** - Powershell Line Continuation (sort of)

**${MY_VAR}** - Is generally a place holder in the slides.

---

# Linux Container Mode

* On the Windows platform make sure that you are running in [Linux Container mode](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

---

# A Note About Proxies & VPNs

Proxies can interfere with some Docker activities if they are not configured correctly and VPNs can increase audio and video streaming issues in class.

If required, you can configure a proxy in Docker Desktop via the preferences.

* [Docker](https://dev.to/mcastellin/use-docker-with-proxy-servers-tutorial-10gg)
* [Docker-Compose](https://stackoverflow.com/questions/34990458/docker-compose-build-and-http-proxy)

---

# Instructor Environment

* **Operating System**: macOS (v12.5.X+)
* **Terminal**: iTerm2 (Build 3.X.X+) - https://www.iterm2.com/
* **Shell Prompt Theme**: Starship - https://starship.rs/
* **Shell Prompt Font**: Fira Code - https://github.com/tonsky/FiraCode
* **Text Editor**: Visual Studio Code (v1.X.X+) - https://code.visualstudio.com/

---

# Docker in Translation

* **Docker client**
  * The docker command used to control most of the Docker workflow and talk to remote Docker servers.

* **Docker server**
  * The dockerd command used to launch the Docker daemon. This turns a Linux system into a Docker server that can have containers deployed, launched, and torn down via a remote client.

---

# Docker in Translation

* **Virtual Machine**
  * In general, the docker server can be only directly run on Linux. Because of this, it is common to utilize a Linux virtual machine to run Docker on other development platforms. Docker Community/Desktop Edition makes this very easy.

---

# Docker in Translation

* **Docker/OCI images**
  * Docker images consist of one or more filesystem layers and some important metadata that represent all the files required to run a Dockerized application. A single Docker image can be copied to numerous hosts. A container will typically have both a name and a tag. The tag is generally used to identify a particular release of an image.
  * OCI - Open Container Initiative

---

# Docker in Translation

* **Linux Containers**
  * A Linux Container is a single instantiation of a Docker or OCI-standard image. A specific container can only exist once; however, you can easily create multiple containers from the same image.

---

# Testing the Docker Setup

```shell
$ docker image ls
$ docker container run -d --rm --name quantum \
    --publish mode=ingress,published=18080,target=8080 \
    docker.io/spkane/quantum-game:latest
$ docker container ls
```

* In a web browser, navigate to port 18080 on your Docker server.

```shell
$ docker container stop quantum
$ docker container ls
$ docker container ls -a
```

---

# Exploring the Dockerfile

```shell
$ cd ${HOME}
$ mkdir class
$ cd ${HOME}/class
$ git clone https://github.com/spkane/balance_game.git \
    --config core.autocrlf=input
$ cd balance_game
```

* Open & explore Dockerfile in your text editor

**Full Documentation**:
https://docs.docker.com/engine/reference/builder/

---

# Registering with Docker Hub

Create an account at: https://hub.docker.com/

---

# Create Your Image Repository

* **Login**:` https://hub.docker.com/`
* **Click**:` Create Repository+`
* **Enter name**: `balance_game`
* **Set visibility**: `public`
* **Click**: `Create`

---

# Docker Login

```shell
$ docker login
$ cat ~/.docker/config.json
```

---

# Registry Authentication

```json
{
    "auths": {
        "https://index.docker.io/v1/": {
            "auth": "q378t348q7tb78bfs387b==",
            "email": "me@example.com"
        }
    }
}
```

---

# Building Your First Image

```shell
$ export HUB_USER=${USER}
$ docker image build -t docker.io/${HUB_USER}/balance_game:latest .
$ docker image push docker.io/${HUB_USER}/balance_game:latest
$ docker search ${HUB_USER}
$ docker container run -d --rm --name balance_game \
    --publish mode=ingress,published=18081,target=80 \
    docker.io/${HUB_USER}/balance_game:latest
$ docker container stop balance_game
```

---

# Docker Hub API Examples

```shell
$ curl -s -S \
  "https://registry.hub.docker.com/v2/repositories/library/alpine/tags/" \
  | jq '."results"[]["name"]' | sort
```

---

# A Typical Build Process

```sh
$ DOCKER_BUILDKIT=0 docker image build -t docker.io/${HUB_USER}/balance_game:latest .
```

1. Client uploads build context to the server
1. Base image pulled, if required
1. New intermediate container created from base image
    * or empty container created, if using `FROM scratch`
1. Dockerfile command executed inside intermediate container
1. New image/layer created from intermediate container
1. If there is another step, a new intermediate container is created from the last step, and then the build goes back to step 3.

---

<!-- _class: lead -->

# Advanced Dockerfile Techniques

---

# Keep it Small

* Each and every layer’s size matters
* Don’t install unnecessary files

```shell
$ cd ${HOME}/class
$ docker container run --rm -d --name outyet-small \
    --publish mode=ingress,published=8090,target=8080 \
    docker.io/spkane/outyet:1.9.4-small
$ docker container export outyet-small -o export.tar
$ tar -tvf export.tar
$ docker container stop outyet-small
$ rm export.tar
```

---

# Debugging an Image

* If your image has a shell installed you can access it using a command like this:

```shell
$ docker image ls
$ docker container run --rm -ti docker.io/spkane/outyet:1.9.4-small /bin/sh
```

But without a shell in the image this will fail.

---

# Debugging an Image

So, let's fix this:

```shell
$ git clone https://github.com/spkane/outyet.git \
    --config core.autocrlf=input
$ cd outyet
```

---

# Multi-Stage Images

```dockerfile
FROM golang:1.9.4 as builder

COPY . /go/src/outyet
WORKDIR /go/src/outyet

ENV CGO_ENABLED=0
ENV GOOS=linux

RUN go get -v -d && \
    go install -v && \
    go test -v && \
    go build -ldflags "-s" -a -installsuffix cgo -o outyet .
```

---

# Multi-Stage Images

```dockerfile
# To support debugging, let's use alpine instead of scratch
FROM alpine:latest as deploy

# Since we are using alpine we can simply install these
RUN apk --no-cache add ca-certificates

WORKDIR /
COPY --from=builder /go/src/outyet/outyet .
# (or) COPY --from=0 /go/src/outyet/outyet .

EXPOSE 8080

CMD ["/outyet", "-version", "1.9.4", "-poll", "600s", "-http", ":8080"]
```

---

# Building the Improved Image

```shell
$ docker image build -f Dockerfile -t outyet:1.9.4-local .
```

---

# Debugging an Image

* Now that we have a shell, let's try this again:

```shell
$ docker image ls
$ docker container run --rm -ti outyet:1.9.4-local /bin/sh
```

* Once inside the new container:

```shell
$ ls -lFa /outyet
$ exit
```

---

# Debugging a Broken Build (1 of 2)

* Break the `Dockerfile` and then try building it again.

```dockerfile
RUN apc --no-cache add ca-certificates
```

```shell
$ docker image build -f Dockerfile .
```

---

# Debugging a Broken Build (2 of 2)

* Let's debug the last successful image in that build

```shell
$ docker container run --rm -ti ${IMAGE_ID} /bin/sh
```

* Once inside the new container:

```shell
$ apc --no-cache add ca-certificates
$ apk --no-cache add ca-certificates
$ exit
```

---

# Smart Layering

* Each and every layer’s size matters
* Clean up, inside of each step.

```shell
$ cd ${HOME}/class
$ cd balance_game
$ docker image build -f Dockerfile.fedora .
$ docker image tag ${IMAGE_ID} size${#}
$ docker image history size${#}
```

---

# Smart Layering

* edit `Dockerfile.fedora`, build, and re-examine size

```shell
RUN dnf install -y httpd
RUN dnf clean all
```

```shell
RUN dnf install -y httpd && \
    dnf clean all
```

---

# Timing commands in Windows

* In the next exercise we will be timing commands using a Unix utility. If you are on Windows and want to try to time these commands locally, you can try something like this in Powershell.

```powershell
PS C:\> $t = Measure-Command { docker image build --no-cache . }
PS C:\> Write-Host That command took $t.TotalSeconds to complete.
```

---

# Order Matters

* Keep commands that change towards the end of your Dockerfile.

```shell
$ cd ${HOME}/class
$ cd balance_game
$ time docker image build --no-cache .
$ time docker image build .
```

* edit `start.sh`

```shell
$ time docker image build .
```

---

# Order Matters

* Add to the top of Dockerfile:

```dockerfile
ADD start.sh /
```

* And then remove the same line from the bottom of the Dockerfile.

```shell
$ time docker image build --no-cache .
$ time docker image build .
$ vi start.sh
$ time docker image build .
```

---

# What We Have Learned

* What a Dockerfile is
* Building a Docker image
* Using Docker Hub
* Keeping Images Small
* Keeping Builds Fast
* Multi-Stage Dockerfiles
* Debugging Images

---

# Additional Reading

* The 12-Factor App
  * http://12factor.net/

* Official Docker Documentation
  * https://docs.docker.com/

* Docker: Up and Running
  * http://shop.oreilly.com/product/0636920153566.do

---

<!-- _class: lead -->

# Additional Learning Resources


## https://learning.oreilly.com/

---

<!-- _class: lead -->

# Student Survey

**Please take a moment to fill out the class survey linked to from the bottom of the ON24 audience screen.**

O’Reilly and I value your comments about the class.

Thank you!

---

<!-- _class: lead -->

# Any Questions?
<br></br>
### [Sean P. Kane](https://www.linkedin.com/in/spkane/)
![height:2cm](images/superorbital-horizontal-darkthemes.png)
##### Providing stellar Kubernetes engineering and workshops.
##### https://superorbital.io/contact/

---

---
marp: true
theme: spkane
---
<!-- markdownlint-disable MD001 MD003 MD014 MD022 MD024 MD032 MD033 MD034 MD041 -->
<!-- theme: spkane -->
<!-- _class: lead -->

<!-- size: 16:9 -->

<!-- $notes: ... -->

# Docker: Up and Running
## Linux Containers

<br></br>
<br></br>

###### Release %BUILD_RELEASE%

---

<!-- _class: lead -->

# Instructor
# [Sean P. Kane](https://www.linkedin.com/in/spkane/)

### [@spkane](https://twitter.com/spkane)
##### [@superorbital_io](https://twitter.com/superorbital_io)
![height:1.3cm](images/superorbital-horizontal-darkthemes.png)

![bg right](images/skane-2018-side-black-cropped.jpg)

---

<!-- footer: Copyright © 2015-2022, Sean P. Kane (@spkane) -->
<!-- paginate: true -->
<!-- _class: lead -->

# [Follow Along Guide](https://gist.github.com/spkane/17aee9422d8235c02417d53d11f42dfa)
## Textual Slides

---

<!-- footer: Copyright © 2015-2022, Sean P. Kane (@spkane) -->
<!-- paginate: true -->
<!-- _class: lead -->

# [Katacoda Online Sandbox](https://learning.oreilly.com/scenarios/devops-tools-sandbox/9781098126469/)

##### https://learning.oreilly.com/scenarios/devops-tools-sandbox/9781098126469/

---

# Prerequisites (1 of 2)
* A recent computer and OS
  * Recent/Stable Linux, macOS, or Windows 10+
  * root/admin rights
  * Sufficient resources to run one 2 CPU virtual machine (VM)
  * CPU Virtualization extensions MUST be enabled in your BIOS/EFI
  * Reliable and fast internet connectivity
* Docker Community/Desktop Edition (on Linux also install Docker Compose V2)

---

# Prerequisites (2 of 2)
* A graphical web browser
* A text editor
* A software package manager
* Git client
* General comfort with the command line will be helpful.
* [optional] tar, wget, curl, jq, SSH client

---

# A Note for Powershell Users
 Terminal commands reflect the Unix bash shell. PowerShell users will need to adjust the commands.
* Unix Variables
  * `export MY_VAR=test`
  * `echo ${MY_VAR}`
* Windows 10+ Variables (powershell)
  * `$env:my_var = "test"`
  * `Get-ChildItem Env:my_var`

---

# Translation Key

**\\** - Unix Shell Line Continuation
**`** - Powershell Line Continuation (sort of)

**${MY_VAR}** - Is generally a place holder in the slides.

---

# Linux Container Mode

* On the Windows platform make sure that you are running in [Linux Container mode](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

---

# A Note About Proxies & VPNs

Proxies can interfere with some Docker activities if they are not configured correctly and VPNs can increase audio and video streaming issues in class.

If required, you can configure a proxy in Docker Desktop via the preferences.

* [Docker](https://dev.to/mcastellin/use-docker-with-proxy-servers-tutorial-10gg)
* [Docker-Compose](https://stackoverflow.com/questions/34990458/docker-compose-build-and-http-proxy)

---

# Instructor Environment

* **Operating System**: macOS (v12.5.X+)
* **Terminal**: iTerm2 (Build 3.X.X+) - https://www.iterm2.com/
* **Shell Prompt Theme**: Starship - https://starship.rs/
* **Shell Prompt Font**: Fira Code - https://github.com/tonsky/FiraCode
* **Text Editor**: Visual Studio Code (v1.X.X+) - https://code.visualstudio.com/

---

# Docker in Translation

* **Docker client**
  * The docker command used to control most of the Docker workflow and talk to remote Docker servers.

* **Docker server**
  * The dockerd command used to launch the Docker daemon. This turns a Linux system into a Docker server that can have containers deployed, launched, and torn down via a remote client.

---

# Docker in Translation

* **Virtual Machine**
  * In general, the docker server can be only directly run on Linux. Because of this, it is common to utilize a Linux virtual machine to run Docker on other development platforms. Docker Community/Desktop Edition makes this very easy.

---

# Docker in Translation

* **Docker/OCI images**
  * Docker images consist of one or more filesystem layers and some important metadata that represent all the files required to run a Dockerized application. A single Docker image can be copied to numerous hosts. A container will typically have both a name and a tag. The tag is generally used to identify a particular release of an image.
  * OCI - Open Container Initiative

---

# Docker in Translation

* **Linux Containers**
  * A Linux Container is a single instantiation of a Docker or OCI-standard image. A specific container can only exist once; however, you can easily create multiple containers from the same image.

---

# The Slow Rise Of Linux Containers

* **chroot system call** (*1979*)
  * Version 7 Unix
* **jail** (*2000*)
  * FreeBSD 4.0
* **Solaris Zones** (*2004*)
  * Solaris 10
* **Linux Containers - LXC** (*2008*)
  * version 2.6.24 of the Linux kernel

   ---

# The Quick Rise of Docker

* Docker Engine - Announced March 15, 2013

* **Provided**:
  * Application packaging
    * App & dependencies packed together
    * Single deployment artifact
  * Abstract software from hardware without sacrificing resources

---

# Docker Engine isn’t a…

* virtualization platform (VMware, KVM, etc.)
* cloud platform (AWS, Azure, etc.)
* configuration management tool (Chef, Puppet, etc.)
* deployment framework (Capistrano, etc.)
* development environment (Vagrant, etc.)
* workload management tool (Mesos, Kubernetes, etc.)

---

# Testing the Docker Setup

```shell
$ docker image ls
$ docker container run -d --rm --name quantum \
    --publish mode=ingress,published=18080,target=8080 \
    docker.io/spkane/quantum-game:latest
$ docker container ls
```

* In a web browser, navigate to port 18080 on your Docker server.

```shell
$ docker container stop quantum
$ docker container ls
$ docker container ls -a
```

---

# Network Ports

```shell
$ docker container run -d --rm --name quantum \
    --publish mode=ingress,published=18090,target=8080 \
    docker.io/spkane/quantum-game:latest
```

* In a web browser, navigate to port 18090 on your Docker server.
  * (e.g.) http://127.0.0.1:18090/

```shell
$ docker container stop quantum
```

---

# Re-Launching Your Container

```shell
$ docker container run -d --rm --name quantum \
    --publish mode=ingress,published=18080,target=8080 \
    docker.io/spkane/quantum-game:latest
```

---

# Accessing STDOUT/STDERR

* Log everything to STDOUT / STDERR
  * Avoid writing logs into file

```shell
$ docker container logs --help
$ docker container logs -f quantum
```

* Reload the game in your web browser.
  * (e.g.) http://127.0.0.1:18080/

```shell
$ docker container logs -t quantum
```

---

# Statistics

```shell
$ docker container stats --help
$ docker container run --rm -d --name stress docker.io/spkane/train-os:latest \
    stress -v --cpu 2 --io 1 --vm 2 \
    --vm-bytes 128M --timeout 360s
$ docker container stats stress
$ docker container stats --no-stream stress
$ docker container top stress
```

---

# Statistics API

* Try the API:

```shell
$ curl --no-buffer -XGET --unix-socket /var/run/docker.sock \
    http://docker/containers/stress/stats
```

---

# Events

```shell
$ docker container stop stress
$ docker system events --help
$ docker system events --since $(date +%Y%m%d)
$ docker container run debian sleep 5
```

* Try the API:

```shell
$ curl --no-buffer -XGET --unix-socket /var/run/docker.sock \
    http://docker/events
```

---

# Debugging a Live Container

* If your container has a shell installed you can access it using a command like this:

```shell
$ docker container ls
$ docker container exec -ti quantum /bin/sh
# ps auxwww
# exit
```

---

# Stopping Quantum

```shell
$ docker container stop quantum
```

---

# Local Host Storage 1/3

```shell
$ git clone https://github.com/spkane/docker-volumes-example.git \
    --config core.autocrlf=input
$ cd docker-volumes-example
$ docker image build -t volumes .
$ docker container run --rm \
    --publish mode=ingress,published=18080,target=80 volumes
```

---

# Local Host Storage 2/3

```sh
$ docker container run --rm \
    --publish mode=ingress,published=18080,target=80 \
    --mount type=bind,\
    source="${PWD}/volumes/single-file/1.jpg",\
    target=/usr/local/apache2/htdocs/images/1.jpg \
    volumes
```

---

# Local Host Storage 3/3

```sh
$ docker container run --rm \
    --publish mode=ingress,published=18080,target=80 \
    --mount type=bind,\
    source="${PWD}/volumes/directory",\
    target=/usr/local/apache2/htdocs/images \
    volumes
```

---

# Linux Namespaces

* Mount (filesystem resources)
* UTS (host & domain name)
* IPC (shared memory, semaphores)
* PID (process tree)
* Network (network layer)
* User (user and group IDs)
* Time (clock offsets)
  * Linux 5.6+ (2020)

---

# Control Groups (cgroups)

* Resource limiting
* Prioritization
* Accounting
* Control

---

# Stressing the System

```shell
$ docker container run --rm -ti docker.io/spkane/train-os:latest stress \
    -v --cpu 2 --io 1 --vm 2 --vm-bytes 128M --timeout 360s
$ docker container run -it --pid=host docker.io/spkane/alpine-base:latest sh
# htop -p $(pgrep stress | tr '\n' ',')
# exit
```

---

# CPU Quotas

```shell
$ docker container run -d --cpus="2" docker.io/spkane/train-os:latest stress \
    -v --cpu 2 --io 1 --vm 2 --vm-bytes 128M --timeout 60s
$ docker container run -d --cpus=".25" docker.io/spkane/train-os:latest stress \
    -v --cpu 2 --io 1 --vm 2 --vm-bytes 128M --timeout 60s
$ docker container run -d --cpus="10" docker.io/spkane/train-os:latest stress \
    -v --cpu 2 --io 1 --vm 2 --vm-bytes 128M --timeout 60s
```

---

# Memory Quotas

* On [Katacoda](https://katacoda.com/), you may need to run `swapoff -a` before running these commands, and `swapon -a` when you are done.

```shell
$ docker container run --rm -ti --memory="512m" docker.io/spkane/train-os:latest \
    stress -v --cpu 2 --io 1 --vm 2 \
    --vm-bytes 128M --timeout 10s
$ docker container run --rm -ti --memory="100m" docker.io/spkane/train-os:latest \
    stress -v --cpu 2 --io 1 --vm 2 \
    --vm-bytes 128M --timeout 10s
```

---

# Kernel Debug logs

```shell
$ docker container run -it  --privileged --pid=host debian \
    nsenter -t 1 -m -u -n -i sh
# dmesg
# exit
```

---

# Timing commands in Windows

* In the next exercise we will be timing commands using a Unix utility. If you are on Windows and want to try to time these commands locally, you can try something like this in Powershell.

```powershell
PS C:\> $t = Measure-Command `
    { docker container run -ti --rm docker.io/spkane/train-os:latest `
    bonnie++ -u 500:500 -d /tmp -r 1024 -s 2048 -x 1 }
PS C:\> Write-Host That command took $t.TotalSeconds to complete.
```

---

# I/O Quotas

```shell
$ docker image pull docker.io/spkane/train-os:latest
$ time docker container run -ti --rm docker.io/spkane/train-os:latest bonnie++ \
    -u 500:500 -d /tmp -r 1024 -s 2048 -x 1
$ time docker container run -ti --rm --device-write-iops /dev/vda:256 \
    docker.io/spkane/train-os:latest bonnie++ \
    -u 500:500 -d /tmp -r 1024 -s 2048 -x 1
$ time docker container run -ti --rm --device-write-bps /dev/vda:5mb \
    docker.io/spkane/train-os:latest bonnie++ \
    -u 500:500 -d /tmp -r 1024 -s 2048 -x 1
```

---

# Container UID

```shell
$ docker container run docker.io/spkane/train-os:latest whoami
$ docker container run -u 500 docker.io/spkane/train-os:latest whoami
$ docker container run -u 99 docker.io/spkane/train-os:latest whoami
$ docker container run docker.io/spkane/train-os:latest cat /etc/shadow
$ docker container run -u 99 docker.io/spkane/train-os:latest cat /etc/shadow
```

* --userns-remap
  * https://docs.docker.com/engine/security/userns-remap/
* Rootless mode
  * https://docs.docker.com/engine/security/rootless/

---

# Dockerfile UID

Set a default non-root UID in your Dockerfiles.

* `USER <UID>[:<GID>]`
or
* `USER <user>[:<group>]`

e.g. `USER myuser:mygroup`

---

# The Dangers of Privileged Containers

* **Note**: NTP might be blocked on [Katacoda](https://katacoda.com/).

```shell
$ docker container run -ti --rm docker.io/spkane/train-os:latest \
    ntpdate pool.ntp.org
$ docker container run -ti --rm --privileged=true docker.io/spkane/train-os:latest \
    ntpdate pool.ntp.org
$ docker container run -ti --rm --privileged=true docker.io/spkane/train-os:latest \
    bash -c \
    "mount /dev/vda1 /mnt && ls -F /mnt/docker/volumes | head -n 10"
```

---

# Capabilities & SECCOMP Mode

* Linux Kernel Capabilities:
  * http://man7.org/linux/man-pages/man7/capabilities.7.html
* Secure Computing Mode (Docker)
  * https://docs.docker.com/engine/security/seccomp/

---

# Controlling Capabilities

```shell
$ docker container run -ti --rm --cap-add=SYS_TIME docker.io/spkane/train-os:latest \
    ntpdate pool.ntp.org
$ docker container run -ti --rm --cap-add=SYS_TIME docker.io/spkane/train-os:latest \
    bash -c \
    "mount /dev/vda1 /mnt && ls -F /mnt/docker/volumes | head -n 10"
$ docker container run -ti --rm docker.io/spkane/train-os:latest tcpdump -i eth0
$ docker container run -ti --rm --cap-drop=NET_RAW docker.io/spkane/train-os:latest \
    tcpdump -i eth0
```

---

# Secure Computing Mode

```shell
$ docker container run -ti --rm --cap-drop=NET_RAW \
    docker.io/spkane/train-os:latest strace tcpdump -i eth0 \
    | grep "Operation not permitted"
$ wget \
    https://raw.githubusercontent.com/spkane/train-os/master/default.json
$ wget \
    https://raw.githubusercontent.com/spkane/train-os/master/tcpdump.json
$ diff -u default.json tcpdump.json
$ docker container run -ti --rm --security-opt=seccomp:tcpdump.json \
    docker.io/spkane/train-os:latest tcpdump -i eth0
```

---

# What We Have Learned

* History of Containers
* Container Logging
* Container Statistics
* Container Events
* Debugging Containers
* Kernel Functionality
* Controlling Resource Usage
* Improving Container Security

---

# Additional Reading

* The 12-Factor App
  * http://12factor.net/

* Official Docker Documentation
  * https://docs.docker.com/

* Docker: Up and Running
  * http://shop.oreilly.com/product/0636920153566.do


### [Sean P. Kane](https://www.linkedin.com/in/spkane/)
![height:2cm](images/superorbital-horizontal-darkthemes.png)
##### Providing stellar Kubernetes engineering and workshops
##### https://superorbital.io/contact/

---

