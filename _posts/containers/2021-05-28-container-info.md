---
layout:     post
title:      "lxd container info"
subtitle:   "something"
date:       2021-05-28 12:01:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - deploy
    - openshift
---



# Container info



## container info

### systemd on wsl windows 10 pro

```bash
sudo dnf install python2 -y && \
sudo ln -s /usr/bin/python2 /usr/bin/python && \
sudo mv /usr/bin/systemctl /usr/bin/systemctl.old && \
curl https://raw.githubusercontent.com/gdraheim/docker-systemctl-replacement/master/files/docker/systemctl.py >temp && \
sudo mv temp /usr/bin/systemctl && \
sudo chmod +x /usr/bin/systemctl
```

#### oracle container

```bash
docker login container-registry.oracle.com && \
mkdir -p /home/servidc/oracle/orcl2_2_volume/oradata && \
docker volume create --name orcl2_2_oradata && \
docker pull container-registry.oracle.com/database/enterprise:12.2.0.1-slim && \
docker tag container-registry.oracle.com/database/enterprise:12.2.0.1-slim oracledb:12.2.0.1 && \
docker run -d -it --name oracle122 \
-p 1521:1521 \
-p 5500:5500 \
-e ORACLE_PWD=SomePass \
-e ORACLE_CHARACTERSET=AL32UTF8 \
-v /home/servidc/oracle/orcl2_2_volume/oradata:/opt/oracle/oradata \
oracledb:12.2.0.1

docker logs $container_id -f && \
docker ps -as -f "name=oracle122"

docker container ls
docker exec -it $containerid /bin/bash
export ORACLE_PDB_SID=ORCLPDB1
sqlplus '/as sysdba'
SQL>connect / as sysdba
SQL> alter user system identified by $pass
SQL> alter user sys identified by $pass
sqlplus system/$pass@//localhost:1521/orclpdb1.localdomain
jdbc:oracle:thin:system/$pass4@//localhost:1521/orclpdb1.localdomain

sqlplus system/$pass@localhost:1521/orclpdb1.localdomain
@mksample $pass $pass $pass SomePass SomePass SomePass
SomePass SomePass users temp /oracle/logs localhost:1521/orclpdb1.localdomain
```

#### get ip of running windows hypervisor vm

```bash
get-vm | ?{$_.State -eq "Running"} | select -ExpandProperty networkadapters | select vmname, macaddress, switchname, ipaddresses
```

#### info on interactive bash command line here documents

```bash
rm -rf /tmp/kafka-connect-*; ls -rtl /tmp ;\
export tmpdir="/tmp/kafka-connect-${RANDOM}" ;\
echo "tmpdir: $tmpdir" ;\
export version_num="8.0.19" ;\
echo "version_num: $version_num" ;\
export prefix="mysql-connector-java-${version_num}" ;\
echo "prefix: $prefix" ;\
export url_prefix="https://dev.mysql.com/get/Downloads/Connector-J" ;\
echo "url prefix $url_prefix" ;\
export targz="${prefix}.tar.gz" ;\
export download_url="${url_prefix}/${targz}" ;\
echo $download_url ;\
mkdir $tmpdir; wget -P $tmpdir $download_url ; ls -rtl $tmpdir; cd $tmpdir ;\
export jar="${prefix}.jar" ;\
export extract_file_path="${prefix}/${jar}" ;\
tar -xf $targz --strip-components=1 $extract_file_path -C $tmpdir



export tmpdir="/tmp/kafka-connect-${RANDOM}" ;\
export version="8.0.19" ;\
export url_prefix='https://dev.mysql.com/get/Downloads/Connector-J' ;\
bash << EOF
echo $tmpdir
export driver="mysql-connector-java-${version}.tar.gz"
export download_url="${url_prefix}/${driver}"
if [[ ! -d $tmpdir ]]; then if ! mkdir $tmpdir; then echo "error creating $tmpdir"; exit 1; fi ;fi
if ! cd $tmpdir; then echo "could not cd to $tmpdir"; fi
if ! wget -P "${tmpdir}/" -q $download_url ; then "echo could not download $driver to $tmpdir "; exit 1; fi
if tar -zxvf $tmpdir/$driver -C $tmpdir && tar -xf
cd $tmpdir
ls -rtl $tmpdir
EOF
```

#### containers info

ARM binaries in a container image will not run on POWER container hosts. Containers do not offer compatibility guarantees; only virtualization can do that.

problem extends to processor architecture, and also versions of the operating system. Try running a RHEL 8 container image on a RHEL 4 container host -- that isn't going to work.''

Containers are just regular Linux processes that were started as child processes of a container runtime instead of by a user running commands in a shell. All Linux processes live side by side, whether they are daemons, batch jobs or user commands - the container engine, container runtime, and containers \(child processes of the container runtime\) are no different. All of these processes make requests to the Linux kernel for protected resources like memory, RAM, TCP sockets, etc.''

With a single container host, containerized applications can be managed quite similarly to traditional applications, while gaining incremental efficiencies

#### podman info

```bash
podman run -it --rm --name jekyll \
  -v ./servidc.github.io:/srv/jekyll:rw,slave,Z \
  -v ./bundle:/usr/local/bundle:rw,slave,Z \
  --publish 4000:4000 \
  docker.io/jekyll/jekyll:3.8.5 \
  jekyll serve --drafts

podman run --rm \
 --volume="$PWD:/srv/jekyll" \
-p 127.0.0.1:4000:4000 \
-it jekyll/jekyll:pages \
jekyll serve
```

#### jekyll info

```bash
touch ./servidc.github.io/Gemfile.lock
chmod a+w ./servidc.github.io/Gemfile.lock
```

#### curl wget info

```bash
curl -k -SL "https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.39.tar.gz" | tar -xzf - -C /tmp/quickstart/jars --strip-components=1 mysql-connector-java-5.1.39/mysql-connector-java-5.1.39-bin.jar
```

## Script to evaluate different container sizes

```bash
strings=(
adoptopenjdk:11.0.6_10-jdk-hotspot-bionic
azul/zulu-openjdk-alpine:11.0.6
azul/zulu-openjdk:11.0.6
openjdk:11.0.6-slim
openjdk:11.0.6-jdk-slim-buster
adoptopenjdk/openjdk11:x86_64-ubi-minimal-jdk-11.0.6_10
adoptopenjdk/openjdk11:jdk-11.0.6_10-ubuntu-slim
adoptopenjdk/openjdk11:jdk-11.0.6_10-slim
adoptopenjdk/openjdk11:jdk-11.0.6_10-ubuntu
adoptopenjdk/openjdk11:jdk-11.0.6_10
adoptopenjdk/openjdk11:jdk-11.0.6_10-ubi-minimal
adoptopenjdk/openjdk11:jdk-11.0.6_10-ubi-slim
adoptopenjdk/openjdk11:jdk-11.0.6_10-ubi
adoptopenjdk/openjdk11:jdk-11.0.6_10-debianslim-slim
adoptopenjdk/openjdk11:jdk-11.0.6_10-debianslim
adoptopenjdk/openjdk11:jdk-11.0.6_10-debian-slim
adoptopenjdk/openjdk11:jdk-11.0.6_10-debian
adoptopenjdk/openjdk11:jdk-11.0.6_10-centos-slim
adoptopenjdk/openjdk11:jdk-11.0.6_10-centos
adoptopenjdk/openjdk11:jdk-11.0.6_10-alpine-slim
adoptopenjdk/openjdk11:jdk-11.0.6_10-alpine
mcr.microsoft.com/java/jdk:11u6-zulu-alpine
mcr.microsoft.com/java/jdk:11u6-zulu-centos
mcr.microsoft.com/java/jdk:11u6-zulu-debian10
mcr.microsoft.com/java/jdk:11u6-zulu-debian8
mcr.microsoft.com/java/jdk:11u6-zulu-debian9
mcr.microsoft.com/java/jdk:11u6-zulu-ubuntu
)

for i in "${strings[@]}"; do
echo "$i" >> output.txt
docker run --name jdk -it "$i" cat /etc/os-release | grep PRETTY_NAME | tail -n1 >> output.txt
docker images | awk '{print $NF}' | tail -n1 >> output.txt
docker stop `docker ps -qa`
docker rm `docker ps -qa`
docker rmi -f `docker images -qa `
docker volume rm $(docker volume ls -qf)
docker network rm `docker network ls -q`
done

dos2unix output.txt
cat output.txt | paste -d, - - -;
```
# container info

## systemd on wsl windows 10 pro

```bash
sudo dnf install python2 -y && \
sudo ln -s /usr/bin/python2 /usr/bin/python && \
sudo mv /usr/bin/systemctl /usr/bin/systemctl.old && \
curl https://raw.githubusercontent.com/gdraheim/docker-systemctl-replacement/master/files/docker/systemctl.py >temp && \
sudo mv temp /usr/bin/systemctl && \
sudo chmod +x /usr/bin/systemctl
```

### oracle container

```bash
docker login container-registry.oracle.com && \
mkdir -p /home/servidc/oracle/orcl2_2_volume/oradata && \
docker volume create --name orcl2_2_oradata && \
docker pull container-registry.oracle.com/database/enterprise:12.2.0.1-slim && \
docker tag container-registry.oracle.com/database/enterprise:12.2.0.1-slim oracledb:12.2.0.1 && \
docker run -d -it --name oracle122 \
-p 1521:1521 \
-p 5500:5500 \
-e ORACLE_PWD=SomePass \
-e ORACLE_CHARACTERSET=AL32UTF8 \
-v /home/servidc/oracle/orcl2_2_volume/oradata:/opt/oracle/oradata \
oracledb:12.2.0.1

docker logs $container_id -f && \
docker ps -as -f "name=oracle122"

docker container ls
docker exec -it $containerid /bin/bash
export ORACLE_PDB_SID=ORCLPDB1
sqlplus '/as sysdba'
SQL>connect / as sysdba
SQL> alter user system identified by $pass
SQL> alter user sys identified by $pass
sqlplus system/$pass@//localhost:1521/orclpdb1.localdomain
jdbc:oracle:thin:system/$pass4@//localhost:1521/orclpdb1.localdomain

sqlplus system/$pass@localhost:1521/orclpdb1.localdomain
@mksample $pass $pass $pass SomePass SomePass SomePass
SomePass SomePass users temp /oracle/logs localhost:1521/orclpdb1.localdomain
```

### get ip of running windows hypervisor vm

```bash
get-vm | ?{$_.State -eq "Running"} | select -ExpandProperty networkadapters | select vmname, macaddress, switchname, ipaddresses
```

### info on interactive bash command line here documents

```bash
rm -rf /tmp/kafka-connect-*; ls -rtl /tmp ;\
export tmpdir="/tmp/kafka-connect-${RANDOM}" ;\
echo "tmpdir: $tmpdir" ;\
export version_num="8.0.19" ;\
echo "version_num: $version_num" ;\
export prefix="mysql-connector-java-${version_num}" ;\
echo "prefix: $prefix" ;\
export url_prefix="https://dev.mysql.com/get/Downloads/Connector-J" ;\
echo "url prefix $url_prefix" ;\
export targz="${prefix}.tar.gz" ;\
export download_url="${url_prefix}/${targz}" ;\
echo $download_url ;\
mkdir $tmpdir; wget -P $tmpdir $download_url ; ls -rtl $tmpdir; cd $tmpdir ;\
export jar="${prefix}.jar" ;\
export extract_file_path="${prefix}/${jar}" ;\
tar -xf $targz --strip-components=1 $extract_file_path -C $tmpdir



export tmpdir="/tmp/kafka-connect-${RANDOM}" ;\
export version="8.0.19" ;\
export url_prefix='https://dev.mysql.com/get/Downloads/Connector-J' ;\
bash << EOF
echo $tmpdir
export driver="mysql-connector-java-${version}.tar.gz"
export download_url="${url_prefix}/${driver}"
if [[ ! -d $tmpdir ]]; then if ! mkdir $tmpdir; then echo "error creating $tmpdir"; exit 1; fi ;fi
if ! cd $tmpdir; then echo "could not cd to $tmpdir"; fi
if ! wget -P "${tmpdir}/" -q $download_url ; then "echo could not download $driver to $tmpdir "; exit 1; fi
if tar -zxvf $tmpdir/$driver -C $tmpdir && tar -xf
cd $tmpdir
ls -rtl $tmpdir
EOF
```

### containers info

ARM binaries in a container image will not run on POWER container hosts. Containers do not offer compatibility guarantees; only virtualization can do that.

problem extends to processor architecture, and also versions of the operating system. Try running a RHEL 8 container image on a RHEL 4 container host -- that isn't going to work.''

Containers are just regular Linux processes that were started as child processes of a container runtime instead of by a user running commands in a shell. All Linux processes live side by side, whether they are daemons, batch jobs or user commands - the container engine, container runtime, and containers (child processes of the container runtime) are no different. All of these processes make requests to the Linux kernel for protected resources like memory, RAM, TCP sockets, etc.''

With a single container host, containerized applications can be managed quite similarly to traditional applications, while gaining incremental efficiencies

### podman info

```bash
podman run -it --rm --name jekyll \
  -v ./servidc.github.io:/srv/jekyll:rw,slave,Z \
  -v ./bundle:/usr/local/bundle:rw,slave,Z \
  --publish 4000:4000 \
  docker.io/jekyll/jekyll:3.8.5 \
  jekyll serve --drafts

podman run --rm \
 --volume="$PWD:/srv/jekyll" \
-p 127.0.0.1:4000:4000 \
-it jekyll/jekyll:pages \
jekyll serve
```

### jekyll info

```bash
touch ./servidc.github.io/Gemfile.lock
chmod a+w ./servidc.github.io/Gemfile.lock
```

### curl wget info

```bash
curl -k -SL "https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.39.tar.gz" | tar -xzf - -C /tmp/quickstart/jars --strip-components=1 mysql-connector-java-5.1.39/mysql-connector-java-5.1.39-bin.jar
```

```bash
dnf remove -y docker-ce   && \
yum module install -y container-tools
```

## install Docker-ce instead of podman RH/Centos

```bash
yum module remove -y container-tools && \
dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo && \
dnf install -y docker-ce  && \
systemctl enable --now docker
```

# install Docker on Ubuntu 20.04

```text
sudo apt update && \
sudo apt install apt-transport-https ca-certificates curl software-properties-comm && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - &&\
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable" && \
sudo apt update && \
apt-cache policy docker-ce && \
sudo apt install docker-ce && \
sudo systemctl status docker && \
sudo usermod -aG docker ${USER}
```

### Clean-up all images, conatiners, networks and volumes

```text
docker system prune -a --volumes
```

# libvirt, portainer, docker cockpit Ubuntu 20.04

```text
# I'm bad, I do it all as root
sudo su - 
apt update
apt upgrade
```

### libVirt install:

```text
# libVirt install
# should output "active"
apt install cpu-checker && \
apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst virt-manager && \ 
systemctl is-active libvirtd && \
usermod -aG libvirt $USER && \
usermod -aG kvm $USER && \
exit
# Do this as your user also
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER
sudo brctl show
```

### Cockpit:

```text
sudo su - 
apt install cockpit -y
systemctl start cockpit
ss -tunlp | grep 9090
ufw allow 9090/tcp
apt install cockpit-machines cockpit-storaged cockpit-packagekit cockpit-networkmanager cockpit-dashboard cockpit-bridge
# Cockpit should now be available on https://ip:9090
```

### Docker:

```text
sudo apt-get update && \
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent  && \
sudo apt-get install software-properties-common && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
sudo add-apt-repository  "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" && \
sudo apt-get update && \
sudo apt-get install docker-ce docker-ce-cli containerd.io && \
sudo apt -y install mc iperf3 iptraf-ng && \
sudo docker run hello-world
```

### Portainer

```text
docker run -d --name portainer --restart unless-stopped -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

### NetData

```text
apt install curl -y
bash <(curl -Ss https://my-netdata.io/kickstart.sh)
```

### Links

```text
# Cockpit is on:
https://SERVER_IP:9090/

# Portainer is on:
http://SERVER_IP:9000/

# Netdata is on
http://SERVER_IP:19999
```

### This was needed in CentOS 8, not sure about Ubuntu 20.04 yet:

```text
Set up network bridge:
Cockpit - Networking - Add Bridge
Select your ethernet card (em1) *(ymmv)
Name - bridge0
Reboot. Really. This may save some headaches later.
```


## multi stage

```bash
##############################################################
# This file is intended to be used with ./docker-compose.yml #
##############################################################

FROM node:10.14.1-alpine as build
# working directory
WORKDIR /usr/src/app

# global environment setup : yarn + dependencies needed to support node-gyp
RUN apk --no-cache --virtual add \
    python \
    make \
    g++ \
    yarn 

# copy just the dependency files and configs needed for install
COPY packages/peregrine/package.json ./packages/peregrine/package.json
COPY packages/pwa-buildpack/package.json ./packages/pwa-buildpack/package.json
COPY packages/upward-js/package.json ./packages/upward-js/package.json
COPY packages/upward-spec/package.json ./packages/upward-spec/package.json
COPY packages/venia-concept/package.json ./packages/venia-concept/package.json
COPY package.json yarn.lock babel.config.js browserslist.js ./

# install dependencies with yarn
RUN yarn install

# copy over the rest of the package files
COPY packages ./packages

# copy .env.docker file to .env
COPY ./docker/.env.docker ./packages/venia-concept/.env

# build the app
RUN yarn run build
```

```bash
# MULTI-STAGE BUILD
FROM node:10.14.1-alpine
# working directory
WORKDIR /usr/src/app
# copy build from previous stage
COPY --from=build /usr/src/app .
# create and set non-root USER
RUN addgroup -g 1001 appuser && \
    adduser -S -u 1001 -G appuser appuser
RUN chown -R appuser:appuser /usr/src/app && \
    chmod 755 /usr/src/app
USER appuser

# command to run application
CMD [ "yarn", "workspace", "@magento/venia-concept", "run", "watch:docker"]
```

```bash
# Sample from @citizen428 https://dev.to/citizen428/comment/6cmh
FROM golang:alpine as build
RUN apk add --no-cache ca-certificates
WORKDIR /build
ADD . .
RUN CGO_ENABLED=0 GOOS=linux \
    go build -ldflags '-extldflags "-static"' -o app

FROM scratch
COPY --from=build /etc/ssl/certs/ca-certificates.crt \
     /etc/ssl/certs/ca-certificates.crt
COPY --from=build /build/app /app
ENTRYPOINT ["/app"]
```

```bash
# https://www.reddit.com/r/golang/comments/9u7qnl/deploying_go_apps_on_docker_scratch_images/
# This is the first stage, for building things that will be required by the
# final stage (notably the binary)
FROM golang

# Copy in just the go.mod and go.sum files, and download the dependencies. By
# doing this before copying in the other dependencies, the Docker build cache
# can skip these steps so long as neither of these two files change.
COPY go.mod go.sum ./
RUN go mod download

# Assuming the source code is collocated to this Dockerfile
COPY . .

# Build the Go app with CGO_ENABLED=0 so we use the pure-Go implementations for
# things like DNS resolution (so we don't build a binary that depends on system
# libraries)
RUN CGO_ENABLED=0 go build -o /myapp

# Create a "nobody" non-root user for the next image by crafting an /etc/passwd
# file that the next image can copy in. This is necessary since the next image
# is based on scratch, which doesn't have adduser, cat, echo, or even sh.
RUN echo "nobody:x:65534:65534:Nobody:/:" > /etc_passwd

# The second and final stage
FROM scratch

# Copy the binary from the builder stage
COPY --from=0 /myapp /myapp

# Copy the certs from the builder stage
COPY --from=0 /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

# Copy the /etc/passwd file we created in the builder stage. This creates a new
# non-root user as a security best practice.
COPY --from=0 /etc/passwd /etc/passwd

# Run as the new non-root by default
USER nobody
```

```bash
# https://medium.com/@pierreprinetti/the-go-1-11-dockerfile-a3218319d191

# Accept the Go version for the image to be set as a build argument.
# Default to Go 1.11
ARG GO_VERSION=1.11

# First stage: build the executable.
FROM golang:${GO_VERSION}-alpine AS builder

# Create the user and group files that will be used in the running container to
# run the process an unprivileged user.
RUN mkdir /user && \
    echo 'nobody:x:65534:65534:nobody:/:' > /user/passwd && \
    echo 'nobody:x:65534:' > /user/group

# Install the Certificate-Authority certificates for the app to be able to make
# calls to HTTPS endpoints.
RUN apk add --no-cache ca-certificates

# Set the environment variables for the go command:
# * CGO_ENABLED=0 to build a statically-linked executable
# * GOFLAGS=-mod=vendor to force `go build` to look into the `/vendor` forlder.
ENV CGO_ENABLED=0 GOFLAGS=-mod=vendor

# Set the working directory outside $GOPATH to enable the support for modules.
WORKDIR /src

# Import the code from the context.
COPY ./ ./

# Build the executable to `/app`. Mark the build as statically linked.
RUN go build \
    -installsuffix 'static' \
    -o /app .

# Final stage: the running container.
FROM scratch AS final

# Import the user and group files from the first stage.
COPY --from=builder /user/group /user/passwd /etc/

# Import the Certificate-Authority certificates for enabling HTTPS.
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

# Import the compiled executable from the second stage.
COPY --from=builder /app /app

# Declare the port on which the webserver will be exposed.
# As we're going to run the executable as an unprivileged user, we can't bind
# to ports below 1024.
EXPOSE 8080

# Perform any further action as an unprivileged user.
USER nobody:nobody

# Run the compiled binary.
ENTRYPOINT ["/app"]
```

```bash
# https://raw.githubusercontent.com/syntaqx/sandbox/
# Use Alphine instead of Scratch as it's be hardened
# Accept image version tags to be set as a build arguments
ARG GO_VERSION=1.11
ARG ALPINE_VERSION=3.8

# Throw-away builder container
FROM golang:${GO_VERSION}-alpine${ALPINE_VERSION} AS builder

# Install build and runtime dependencies
# - Git is required for fetching Go dependencies
# - Certificate-Authority certificates are required to call HTTPs endpoints
RUN apk add --no-cache git ca-certificates

# Normalize the base environment
# - CGO_ENABLED: to build a statically linked executable
# - GO111MODULE: force go module behavior and ignore any vendor directories
ENV CGO_ENABLED=0 GO111MODULE=on

# Set the builder working directory
WORKDIR /go/src/github.com/syntaqx/sandbox

# Fetch modules first. Module dependencies are less likely to change per build,
# so we benefit from layer caching
ADD ./go.mod ./go.sum* ./
RUN go mod download

# Import the remaining source from the context
COPY . /go/src/github.com/syntaqx/sandbox

# Build a statically linked executable
RUN go build -installsuffix cgo -ldflags '-s -w' -o ./bin/app ./cmd/app

# Runtime container
FROM alpine:${ALPINE_VERSION}

# Copy the binary and sources from the builder stage
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=builder /go/src/github.com/syntaqx/sandbox/bin/app ./app

# Create a non-root runtime user
RUN addgroup -S sandbox && adduser -S -G sandbox sandbox && chown -R sandbox:sandbox ./app
USER sandbox

# Document the service listening port(s)
EXPOSE 8080

# Define the containers executable entrypoint
ENTRYPOINT ["./app"]
```

```bash
# https://github.com/usefathom/fathom/blob/master/Dockerfile
# Building a Go backend + Javascript UI
FROM node:alpine AS assetbuilder
WORKDIR /app
COPY package*.json ./
COPY gulpfile.js ./
COPY assets/ ./assets/
RUN npm install && NODE_ENV=production ./node_modules/gulp/bin/gulp.js

FROM golang:latest AS binarybuilder
WORKDIR /go/src/github.com/usefathom/fathom
COPY . /go/src/github.com/usefathom/fathom
COPY --from=assetbuilder /app/assets/build ./assets/build
RUN make docker

FROM alpine:latest
EXPOSE 8080
HEALTHCHECK --retries=10 CMD ["wget", "-qO-", "http://localhost:8080/health"]
RUN apk add --update --no-cache bash ca-certificates
WORKDIR /app
COPY --from=binarybuilder /go/src/github.com/usefathom/fathom/fathom .
CMD ["./fathom", "server"]
```

### install Podman on Ubuntu to support rootless containers

**update the kernel**

```bash
echo 'kernel.unprivileged_userns_clone=1' > /etc/sysctl.d/userns.conf
```

**enable cgroups v2**
* add "systemd.unified_cgroup_hierarchy=1" to /etc/default/grub

```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash systemd.unified_cgroup_hierarchy=1 swapaccount=1"
```

**if you see v1 files then you're on v1, below indicates v2**

```bash
s ls /sys/fs/cgroup
cgroup.controllers  cgroup.max.descendants  cgroup.stat             cgroup.threads  cpuset.cpus.effective  init.scope     io.cost.qos  machine.slice    system.slice
cgroup.max.depth    cgroup.procs            cgroup.subtree_control  cpu.pressure    cpuset.mems.effective  io.cost.model  io.pressure  memory.pressure  user.slice
```

```bash
sudo apt update -y
source /etc/os-release
sudo sh -c "echo 'deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/ /' > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list"
wget -nv https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable/xUbuntu_${VERSION_ID}/Release.key -O- | sudo apt-key add -
sudo apt update -qq
sudo apt-get -qq --yes install podman
sudo apt-get -y install podman uidmap
sudo podman --version
```

```bash
sudo add-apt-repository ppa:projectatomic/ppa
sudo apt-get update
sudo apt-get install podman uidmap
echo "$(whoami):10000:65536" | sudo tee /etc/subuid
echo "$(whoami):10000:65536" | sudo tee /etc/subgid
```

**add following to /etc/containers/registries.conf**

```bash
# This is a system-wide configuration file used to
# keep track of registries for various container backends.
# It adheres to TOML format and does not support recursive
# lists of registries.

# The default location for this configuration file is
# /etc/containers/registries.conf.

# The only valid categories are: 'registries.search', 'registries.insecure',
# and 'registries.block'.

[registries.search]
registries = ['docker.io', 'quay.io', 'registry.access.redhat.com']

# If you need to access insecure registries, add the registry's fully-qualified name.
# An insecure registry is one that does not have a valid SSL certificate or only does HTTP.
[registries.insecure]
registries = []

# If you need to block pull access from a registry, uncomment the section below
# and add the registries fully-qualified name.
#
# Docker only
[registries.block]
registries = []
```

**install slirp4netns for optimized user space networking**

```bash
git clone https://github.com/rootless-containers/slirp4netns
cd slirp4netns/
./autogen.sh
./configure
make
sudo make install
podman run -it --rm debian bash
```

### test the different root and rootless configurations possible with podman

**the following are detailed below**

* rootoutside-rootinside
* useroutside-rootinsid
* rootoutside-userinside
* useroutside-userinside

**non-root outside and root inside the container**

```bash
whoami
podman run -d --name rootoutside-rootinside registry.redhat.redhat.com ubi8:latest tail -f /dev/null
podman exec -it rootoutside-rootinside /bin/bash
whoami
sleep 1000 &
exit
ps -ef | grep "sleep 1000" | grep -v grep
```

**non-root outside and root inside the container**

```bash
whoami
podman run -d --name useroutside-rootinside registry.redhat.redhat.com ubi8:latest tail -f /dev/null
podman exec -it useroutside-rootinside /bin/bash
whoami
sleep 2000 &
exit
ps -ef | grep "sleep 2000" | grep -v grep
```

**root outside and non-root inside the container**
*  note that podman maps UID inside the UID outside

```bash
whoami
podman run -d --name rootoutside-userinside registry.redhat.redhat.com ubi8:latest tail -f /dev/null
podman exec -it rootoutside-userinside -u servidc /bin/bash
whoami
sleep 3000 &
exit
ps -ef | grep "sleep 3000" | grep -v grep
id
```

**non-root outside and non-root inside the container**

```bash
whoami
podman run -d --name useroutside-userinside registry.redhat.redhat.com ubi8:latest tail -f /dev/null
podman exec -it useroutside-userinside -u sync /bin/bash
whoami
sleep 4000 &
exit
ps -ef | grep "sleep 4000" | grep -v grep
id
```
### non-root user systemd

Note: As we know podman is daemonless, so this container is stopped on next boot.
To make it persistent

**Enable linger to allow non-root users to make use of systemd**

```bash
$ sudo loginctl enable-linger {USER}
$ sudo loginctl user-status {USER}
```
**Create user systemd service for a container as a non-root user**

```bash
$ cd .config
$ mkdir -p systemd/user
$ cd systemd/user
$ podman generate systemd --name linkding --files
$ systemctl --user daemon-reload
$ systemctl --user enable --now container-linkding.service
$ systemctl --user status container-linkding.service
```

### how to deal with volume mounts with rootless containers

* im running as user 200 but the files are owned by root which are then inaccessible to uid 200 inside the container

```bash
podman run -it --rm --name nexus2 --user 200 -v /home/tom/myshares/nexus2:/sonatype-work:Z sonatype/nexus /bin/sh

# below is running inside the container
$ ls -al / | grep sonatype-work
drwxr-xr-x.  15 root root 4096 Sep 27 13:29 sonatype-work
```

* use the unshare command so that volumes own by a non root user can be reassigned to some othe not root id.
* note how the ownexusner of the file is now a non-root user

```bash
podman unshare chown 200:200 -R /home/tom/myshares/nexus2
podman run -it --rm --name nexus2 --user 200 -v /home/tom/myshares/nexus2:/sonatype-work:Z sonatype/nexus /bin/s
$ ls -al / | grep sonatype-work
drwxr-xr-x.  15 nexus nexus 4096 Sep 27 13:29 sonatype-work
```