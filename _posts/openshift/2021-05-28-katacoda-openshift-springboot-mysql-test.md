---
layout:     post
title:      "openshift katacoda"
subtitle:   "ansible ingo"
date:       2021-05-28 13:00:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - ansible
    - openshift
---


## Katacoda

go to: [https://katacoda.com/courses/openshift/playground](https://www.katacoda.com/openshift/courses/playgrounds/openshift42)

## generate sample project

```bash
mkdir apptest
cd apptest
git clone https://github.com/servidc/minishift_jdg_springboot_kafka_example
cd minishift_jdg_springboot_kafka_example
mvn clean install
```

## generate mysql docker build

```bash
cd src/main/docker-files  ;
docker build -t springboot_mysql -f ./dockerfile_springboot_mysql .
docker pull openshift/mysql-56-centos7
```

## creaet openshfit mysql pod

```bash
oc new-app -e MYSQL_USER=root -e MYSQL_PASSWORD=root -e MYSQL_DATABASE=test openshift/mysql-56-centos7
```

## this shows local openshift access but interactive access like this is not idiomatic

```bash
oc rsh mysql-56-centos7-1-nvth9
mysql -u root
CREATE USER 'root'@'%' IDENTIFIED BY 'root';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
exit
```

## create springboot mysql app in openshift

```bash
oc get pods
oc new-app -e spring_datasource_url=jdbc:mysql://172.30.188.236:3306/test mysql-56-centos7 --name=swapapp
oc get svc
oc expose svc springbootmysql
oc get route
```

## alidate rest endpoints of app via curl

```bash
curl -v http://swapapp-myproject.192.168.1.204.nip.io/demo/all
output:
[
{"name":"UBUNTU 17.10 LTS","lastaudit":1502409600000,"id":1},
{"name":"RHEL 7","lastaudit":1500595200000,"id":2},
{"name":"Solaris 11","lastaudit":1502582400000,"id":3}
]

curl http://swapapp-myproject.192.168.1.204.nip.io/demo/add?name=SpringBootMysqlTest
3Saved

curl http://swapapp-myproject.192.168.1.204.nip.io/demo/all
[
{"name":"UBUNTU 17.10 LTS","lastaudit":1502409600000,"id":1},
{"name":"RHEL 7","lastaudit":1500595200000,"id":2}
{"name":"Solaris 11","lastaudit":1502582400000,"id":3},
{"name":"SpringBootMysqlTest","lastaudit":1548892800000,"id":4}
]
```
