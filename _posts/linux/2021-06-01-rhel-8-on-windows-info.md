---
layout: post
published: true
title: notes on everything im wokring
date: 2020-03-26 03:28:00
category: notes
author:  "datahawklab"
tags: [openshift,minishift,codeready containers,docker,podman]
summary: notes on everything im wokring on
---
# notes on various things im wokring on

## get ip of running windows container
```
get-vm | Where-Object {$_.State -eq 'Running'} | Select -ExpandProperty Networkadapters
```

## info on interactive bash commandlie here documents
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

## containes info
ARM binaries in a container image will not run on POWER container hosts. Containers do not offer compatibility guarantees; only virtualization can do that.

problem extends to processor architecture, and also versions of the operating system. Try running a RHEL 8 container image on a RHEL 4 container host -- that isn't going to work.''


Containers are just regular Linux processes that were started as child processes of a container runtime instead of by a user running commands in a shell. All Linux processes live side by side, whether they are daemons, batch jobs or user commands - the container engine, container runtime, and containers (child processes of the container runtime) are no different. All of these processes make requests to the Linux kernel for protected resources like memory, RAM, TCP sockets, etc.''

With a single container host, containerized applications can be managed quite similarly to traditional applications, while gaining incremental efficiencies

## podman info

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

## jekyll info

```bash
touch ./servidc.github.io/Gemfile.lock
chmod a+w ./servidc.github.io/Gemfile.lock
```

## curl wget info

```bash
curl -k -SL "https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.39.tar.gz" | tar -xzf - -C /tmp/quickstart/jars --strip-components=1 mysql-connector-java-5.1.39/mysql-connector-java-5.1.39-bin.jar
```

## tomcat stuff

go to [https://www.javaguides.net/2018/06/apache-maven-war-plugin.html](https://www.javaguides.net/2018/06/apache-maven-war-plugin.html)

Deploying applications to Tomcat
[https://www.codejava.net/servers/tomcat/how-to-deploy-a-java-web-application-on-tomcat](https://www.codejava.net/servers/tomcat/how-to-deploy-a-java-web-application-on-tomcat)
1. Copy the WAR file into $CATALINA_HOME\webapps directory.
2. Copy the application’s directory from its location into $CATALINA_HOME\webapps directory.
3. deploy the web application remotely via a web interface provided by Tomcat’s manager application.
	[http://localhost:8080/manager](http://localhost:8080/manager)


[https://docs.oracle.com/database/121/ADXDB/xdb11jav.htm#ADXDB4944](https://docs.oracle.com/database/121/ADXDB/xdb11jav.htm#ADXDB4944)