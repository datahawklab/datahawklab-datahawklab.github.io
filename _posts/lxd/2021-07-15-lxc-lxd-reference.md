---
layout: post
title: lxc lxd reference
date: 2021-07-15 02:07
category: 
author: 
tags: [lxc, lxd, infinispan]
summary: 
---

```bash
lxc alias add shell "exec @ARGS@ -- su -l servid"lx
```
## get shell on lxc container as non root user

```bash
lxc shell infinispan-test-1
lxc shell infinispan-test-2
lxc shell infinispan-test-3
```

## mount directory on host on container

```bash
lxc config device add infinispan-test-1 downloads disk source=$HOME/Downloads path=/home/servid/Downloads
lxc config device add infinispan-test-2 downloads disk source=$HOME/Downloads path=/home/servid/Downloads
lxc config device add infinispan-test-3 downloads disk source=$HOME/Downloads path=/home/servid/Downloads
```

```bash
lxc restart infinispan-test-1
lxc restart infinispan-test-2
lxc restart infinispan-test-3
```

```bash
lxc config device set infinispan-test-4 eth0 ipv4.address 10.228.91.178
lxc config device set infinispan-test-5 eth0 ipv4.address 10.228.91.178
```

```bash
.ssh/config file

Host ros1-live
    HostName 10.141.221.65
    User ubuntu
    IdentityFile ~/.ssh/id_rsa
    ForwardAgent Yes
    ForwardX11 Yes
```

### how to convert from priviliged containers to non-priviliged

```bash
user@mendota$ lxc exec awx01 -- shutdown -h now
user@mendota$ lxc config set awx security.privileged fal
```

```bash
mkdir -p src/main/webapp/WEB-INF
mkdir -p src/main/java/sample
```

The application is deployed to http://localhost:8080/{artifactId}/.
The XML content can be viewed by accessing the following URL: http://localhost:8080/{artifactId}/rest/xml
The JSON content can be viewed by accessing this URL: http://localhost:8080/{artifactId}/rest/json

```bash
mvn package
```

install dependencies
install adoptopenjdk
install maven