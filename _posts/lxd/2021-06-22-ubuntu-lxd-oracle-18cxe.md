---
layout:     post
title:      "Ubuntu 20.04 LXD Linux container Oracle 18cXE database install and configure"
subtitle:   "something"
date:       2021-06-22 00:01:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - lxd
    - lxc
    - container
    - oracle database
---

### install lxd

```bash
sudp snap install lxd
```

### initialize LXD as a non root user with a simple profile

```bash
#just hit enter to accept all the defaults for all the questions
lxc init
```

### lanuch LXD oracle linux 8 container

```bash
lxc launch images:oracle/8/cloud orcl1
```

###  launch shell to container you just created

```bash
lxc exec orcl1 bash
```

## Run below on the LXD container
### in /etc/hosts set the ip addess to hostname

```bash
10.115.6.55	orcl1
```


### install and start the oracle database

```bash
dnf install glibc-devel ksh libaio-devel libstdc++-devel libxcrypt-devel lm_sensors-libs make sysstat libnsl
dnf install --nogpgcheck oracle-database-xe-18c-1.0-1.x86_64.rpm
/etc/init.d/oracle-xe-18c start
/etc/init.d/oracle-xe-18c Configure
```

### create an admin user in the pluggable db so you can do remote admin going forward

```bash
mkdir -p /home/oracle
chown oracle:oinstall /home/oracle


su - oracle 
export ORACLE_SID=XE ;\
export ORAENV_ASK=NO ;\
. /opt/oracle/product/18c/dbhomeXE/bin/oraenv

export ORACLE_HOME=/opt/oracle/product/18c/dbhomeXE ;\
export ORACLE_PDB_SID=XEPDB1 ;\
sqlplus '/as sysdba'

SQL> create user dbadmin identified by tublu1224;
SQL> grant connect, dba to dbadmin;
SQL> exit;
```

### now you can use sql developer or any other admin tool to manage the db

![image](https://user-images.githubusercontent.com/81254968/123020436-3dcceb80-d3a0-11eb-92c0-16a80249002e.png)

### Sample JDBC thin client setup

```bash
jdbc:oracle:thin:@orcl1:1539/XEPDB1
```

## run below on base LXD host 
### create a proxy on your lxd host to db lxd container to expose db outside of lxd host


```bash
lxc config device add orcl1 oralistener proxy connect=tcp:10.115.6.55:1539 listen=tcp:0.0.0.0:1539 
``` 

* orcl1 is the hostname of the container
* oraListner is the name of proxy connecton 
* connect =tcp* is the ip address of the container
* listen* is creating a proxy connection from the machine hosting the lxd container and the container
  * and opening port 1539 on the machine hosting the lxd container 
  * and making it available on all ip addresses configured on the machine hosting the lxd container. 
  
### now my thin client connection can be changes to the machine hosting the lxd container

this thin client string

```bash
jdbc:oracle:thin:@orcl1:1539/XEPDB1
```
gets changed to 

```bash
jdbc:oracle:thin:@servidc-ThinkCentre-M93p:1539/XEPDB1
```