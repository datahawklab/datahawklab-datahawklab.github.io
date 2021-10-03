---
layout:     post
title:      "weblobic install devleoper non-root"
subtitle:   "something"
date:       2021-05-28 00:01:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
- weblogic
---

# Weblogic java non root developer install

## latest

export JAVA_HOME=/home/servidc/bin/java/jdk/jdk1.8.0_301 ;\
export PATH=${JAVA_HOME}/bin:${PATH} ;\
java -version && \
mkdir -p /home/servidc/bin/wls/wls14 && \
export ORACLE_HOME=/home/servidc/bin/wls/wls14 && \
cd $ORACLE_HOME && \
java -jar /home/servidc/Downloads/weblogic/fmw_14.1.1.0.0_wls_lite_generic.jar


## create the required directories
```bash
mkdir -p /home/servidc/code/weblogic-testing/bin && \
mkdir -p /home/servidc/code/weblogic-testing/app/wls1411-servid && \
```
## delete everything in one shot
```bash
export WEBLOGIC_PROJECT_HOME=/home/servidc/IdeaProjects/app/wls1411-servid ;\
([[ -e $WEBLOGIC_PROJECT_HOME ]] && (rm -rf $WEBLOGIC_PROJECT_HOME || echo "error deleting ${WEBLOGIC_PROJECT_HOME}")) || "$WEBLOGIC_PROJECT_HOME doesnt exist"
```

## install non-root oracle JDK
```bash
export WEBLOGIC_PROJECT_HOME=/home/servidc/code/weblogic-testing/app/wls1411-servid ;\
export JDK_INSTALL_HOME=${WEBLOGIC_PROJECT_HOME}/jdk ;\
export JDK_ARCHIVE=jdk-8u281-linux-x64.tar.gz ;\
export JDK=jdk1.8.0_281 ;\
export JAVA_HOME=${JDK_INSTALL_HOME}/${JDK} ;\
export PATH=${JAVA_HOME}/bin:$PATH ;\
( [[ ! -e $WEBLOGIC_PROJECT_HOME ]] && (( mkdir -p $JDK_INSTALL_HOME) || ( echo "could not create $WEBLOGIC_PROJECT_HOME" && exit 1 ))) || ( echo "$WEBLOGIC_PROJECT_HOME already exists" && exit 1 )
echo "installing java ${JAVA_HOME} in ${JDK_INSTALL_HOME}" ;\
cd $JDK_INSTALL_HOME && \
wget -O - -q https://raw.githubusercontent.com/typekpb/oradown/master/oradown.sh  | bash -s -- --cookie=accept-weblogicserver-server --username=servid.servid@com.com--password=pass http://download.oracle.com/otn/java/jdk/8u281-b09/89d678f2be164786b292527658ca1605/jdk-8u281-linux-x64.tar.gz && \
tar xzvf $JDK_ARCHIVE && \
java -version && \
echo "success installing java to ${JAVA_HOME}"
```
export WEBLOGIC_VERSION="12.2.1.4.0"
export JAVA_VERSION="1.8.0_221"
export TAR_GZ_NAME=jdk-8u281-linux-x64.tar.gz
export APP_HOME=~/code/weblogic-testing
export JAVA_BASE=${APP_HOME}/java
export ORACLE_BASE=${JAVA_BASE}/wls
export JAVA_HOME=${APP_HOME}/jdk${JAVA_VERSION}
export ORACLE_HOME=${ORACLE_BASE}/fmw-wls-slim-${WEBLOGIC_VERSION}
export BINARY_DOWNLOAD_HOME=${APP_HOME}/bin
export WEBLOGIC_APP_HOME=${APP_HOME}/app
export TAR_GZ_PATH=${BINARY_DOWNLOAD_HOME}/${TAR_GZ_NAME}
mkdir -p $APP_HOME
mkdir -p $BINARY_DOWNLOAD_HOME && \
mkdir -p $WEBLOGIC_ORACLE_HOME && \
mkdir -p $WEBLOGIC_APP_HOME && \

wget https://download.oracle.com/otn/java/jdk/8u281-b09/89d678f2be164786b292527658ca1605/jdk-8u281-linux-x64.tar.gz?AuthParam=1614597082_91aea17799380ac17d424c56ae863a27 \
-P $BINARY_DOWNLOAD_HOME
wget https://download.oracle.com/otn/nt/middleware/14c/14110/fmw_14.1.1.0.0_wls_lite_Disk1_1of1.zip?AuthParam=1614597228_ca215ebdcfeb591fd915f6c118a1fbd3 \
-P $BINARY_DOWNLOAD_HOME
tar xzvf ${TAR_GZ_PATH} -C
# Using curl

    curl -L --header "Cookie: s_nr=1359635827494; s_cc=true; gpw_e24=http%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjavase%2Fdownloads%2Fjdk6downloads-1902814.html; s_sq=%5B%5BB%5D%5D; gpv_p24=no%20value" http://download.oracle.com/otn-pub/java/jdk/8u281-b09/jdk-8u281-linux-x64.bin -o jdk-8u281-linux-x64.bin

    curl -L --header "Cookie: s_nr=1359635827494; s_cc=true; gpw_e24=http%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjavase%2Fdownloads%2Fjdk6downloads-1902814.html; s_sq=%5B%5BB%5D%5D; gpv_p24=no%20value" http://download.oracle.com/otn-pub/java/jdk/6u41-b02/jdk-6u41-linux-x64.bin -o jdk-6u41-linux-x64.bin

# Using wget

    wget --no-cookies --header "Cookie: s_nr=1359635827494; s_cc=true; gpw_e24=http%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjavase%2Fdownloads%2Fjdk6downloads-1902814.html; s_sq=%5B%5BB%5D%5D; gpv_p24=no%20value" http://download.oracle.com/otn-pub/java/jdk/6u39-b04/jdk-6u39-linux-x64.bin

    wget --no-cookies --header "Cookie: s_nr=1359635827494; s_cc=true; gpw_e24=http%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjavase%2Fdownloads%2Fjdk6downloads-1902814.html; s_sq=%5B%5BB%5D%5D; gpv_p24=no%20value" http://download.oracle.com/otn-pub/java/jdk/6u41-b02/jdk-6u41-linux-x64.bin


echo "inventory_loc=/home/servidc/code/weblogic-testing" > ${WEBLOGIC_ORACLE_BASE}/oraInst.loc

export ORACLE_HOME==${APP_HOME}/fmw-wls-slim-12.2.1.4.0
export JAVA_HOME=${JAVA_BASE}/jdk1.8.0_221
export PATH=$JAVA_HOME/bin:$PATH
java -jar fmw_12.2.1.4.0_wls_quick_slim.jar ORACLE_HOME=$ORACLE_HOME -invPtrLoc ${WEBLOGIC_ORACLE_BASE}/oraInst.loc



```bash
export INSTALL_HOME=/home/servidc/IdeaProjects/bin ;\
export JDK=jdk1.8.0_281 ;\
export JAVA_HOME=${INSTALL_HOME}/${JDK} ;\
export PATH=${JAVA_HOME}/bin:${PATH} ;\
export PROJ_HOME=/home/servidc/IdeaProjects/app ;\
export PROJECT=wls1411-servid ;\
export JAR=fmw_14.1.1.0.0_wls_lite_quick_generic.jar ;\
export OCL_HOME=${PROJ_HOME}/${PROJECT} ;\
java -version && \
java -jar ${INSTALL_HOME}/${JAR} ORACLE_HOME=${OCL_HOME} && \
echo "successfuly created Weblogic home: ${OCL_HOME}"
```

## prep non-root weblogic

```bash
export WEBLOGIC_PROJECT_HOME=/home/servidc/IdeaProjects/app/wls1411-servid
export JAR_DOWNLOAD_HOME=${WEBLOGIC_PROJECT_HOME}/weblogic_jar ;\
export WEBLOGIC_ARCHIVE=fmw_14.1.1.0.0_wls_lite_quick_Disk1_1of1.zip ;\
export jar=fmw_14.1.1.0.0_wls_lite_quick_generic.jar ;\
( ([[ -e $WEBLOGIC_PROJECT_HOME ]] && [[ -e $JAR_DOWNLOAD_HOME ]]) && ( echo "$JAR_DOWNLOAD_HOME already exists" && exit 1 ) )

wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk//8u281-b09/jdk-8u281-linux-x64.tar.gz

&& (( mkdir -p $JAR_DOWNLOAD_HOME) || echo "could not create $JAR_DOWNLOAD_HOME" && exit 1 )) || ( echo "could not create $WEBLOGIC_PROJECT_HOME" && exit 1 ))) ||
echo "unarchiving ${ARCHIVE} to ${AR_DOWNLOAD_HOME" ;\
cd $INSTALL_HOME && \
wget -O - -q https://raw.githubusercontent.com/typekpb/oradown/master/oradown.sh | bash -s -- --cookie=accept-weblogicserver-server --username=servid.servid@com.com --password=pass https://download.oracle.com/otn/nt/middleware/14c/14110/fmw_14.1.1.0.0_wls_lite_quick_Disk1_1of1.zip && \
unzip $ARCHIVE && \
echo "successfuly unzipped"
```
## create non-root weblogic oracle_home for developers

```bash
export INSTALL_HOME=/home/servidc/IdeaProjects/bin ;\
export JDK=jdk1.8.0_281 ;\
export JAVA_HOME=${INSTALL_HOME}/${JDK} ;\
export PATH=${JAVA_HOME}/bin:${PATH} ;\
export PROJ_HOME=/home/servidc/IdeaProjects/app ;\
export PROJECT=wls1411-servid ;\
export JAR=fmw_14.1.1.0.0_wls_lite_quick_generic.jar ;\
export OCL_HOME=${PROJ_HOME}/${PROJECT} ;\
java -version && \
java -jar ${INSTALL_HOME}/${JAR} ORACLE_HOME=${OCL_HOME} && \
echo "successfuly created Weblogic home: ${OCL_HOME}"
```
# create a Weblogic domain

## git clone this repo repo

## set env variables at command line

```bash
export MW_HOME=$1
export JAVA_HOME=$2
```

## CreateDomain.sh

```bash
cat >/home/servidc/scripts/wls1411-servid/createDomain.sh <<<'#!/usr/bin/bash

export MW_HOME=/home/servidc/IdeaProjects/app/wls1411-servid
export JAVA_HOME=/home/servidc/IdeaProjects/bin/jdk1.8.0_281
export SCRIPT_HOME=/home/servidc/scripts/wls1411-servid
export WLS_HOME=$MW_HOME/wlserver
export WL_HOME=$WLS_HOME
export PATH=${JAVA_HOME}/bin:$PATH
export CONFIG_JVM_ARGS=-Djava.security.egd=file:/dev/./urandom
. $WLS_HOME/server/bin/setWLSEnv.sh && \
java weblogic.WLST CreateDomain.py -p myDomain.properties
'
```
### myDomain.properties
```bash

cat >/home/servidc/scripts/wls1411-servid/myDomain.properties <<<"\
# Paths
path.middleware=/home/servidc/IdeaProjects/app/wls1411-servid
path.wls=/home/servidc/IdeaProjects/app/wls1411-servid/wlserver
path.domain.config=/home/servidc/IdeaProjects/app/servid-wlsdomains
path.app.config=/home/servidc/IdeaProjects/app/servid-wlsapplications

# Credentials
domain.name=servid-domain
domain.username=weblogic
domain.password=Password1

# Listening address
domain.admin.port=700
domain.admin.address=acer.test.com
domain.admin.port.ssl=7501# Paths
path.middleware=/home/servidc/IdeaProjects/app/wls1411-servid
path.wls=/home/servidc/IdeaProjects/app/wls1411-servid/wlserver
path.domain.config=/home/servidc/IdeaProjects/app/servid-wlsdomains
path.app.config=/home/servidc/IdeaProjects/app/servid-wlsapplications

# Credentials
domain.name=servid-domain
domain.username=weblogic
domain.password=Password1

# Listening address
domain.admin.port=7001
domain.admin.address=acer.test.com
domain.admin.port.ssl=7501
EOF
"

chmod u+x /home/servidc/scripts/wls1411-servid/createDomain.sh && \
cd /home/servidc/scripts/wls1411-servid && \
./createDomain.sh
```

## Start weblogic domain
```bash
export INSTALL_HOME=/home/servidc/IdeaProjects/bin ;\
export JDK=jdk1.8.0_281 ;\
export JAVA_HOME=${INSTALL_HOME}/${JDK} ;\
export PATH=${JAVA_HOME}/bin:${PATH} ;\
java -version &&
cd /home/servidc/IdeaProjects/app/servid-wlsdomains && \
./startWebLogic.sh
nohup ./startWebLogic.sh > /dev/null 2>&1 &
```

## recreate Domain

**make sure to set DOMAIN_PATH**

```bash
rm -rf /home/sservid/wlsdomains/domains/servid-domain && \
cd /home/sservid/Documents/work/code-work/weblogic-openshift/wlst && \
./CreateDomain.sh && \
cd /home/servidc/IdeaProjects/app/servid-wlsdomains/servid-domain &&\
./startWebLogic.sh
```

## access the weblogic admin console

[http://192.168.1.178:7001/console](http://192.168.1.178:7001/console)

```bash
/tmp/yourfilehere


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















## archive
## download binaries
```bash
mkdir -p /home/sservid/IdeaProjects/bin &&
cd /home/sservid/IdeaProjects/bin &&
wget -O - -q https://raw.githubusercontent.com/typekpb/oradown/master/oradown.sh  | bash -s -- --cookie=accept-weblogicserver-server --username=servid.servid@com.com --password=pass http://download.oracle.com/otn/nt/middleware/12c/122140/fmw_12.2.1.4.0_wls_lite_Disk1_1of1.zip &&
wget -O - -q https://raw.githubusercontent.com/typekpb/oradown/master/oradown.sh  | bash -s -- --cookie=accept-weblogicserver-server --username=servid.servid@com.com --password=pass http://download.oracle.com/otn/java/jdk/8u281-b09/89d678f2be164786b292527658ca1605/jdk-8u281-linux-x64.tar.gz
```
https://www.oracle.com/webapps/redirect/signon?nexturl=



## Install binaries for Oracle JDK and Weblogic dev installer (ONE TIME)

## Get the latest Oracle JDK and the 12.2.1.4 generic weblogic installer. Install under a non root id
[https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)
[https://www.oracle.com/middleware/technologies/weblogic-server-downloads.html](https://www.oracle.com/middleware/technologies/weblogic-server-downloads.html)

## install weblogic dev installer locally

```bash
export JAVA_HOME=/local/path/jdk-8u271-linux-x64/jdk1.8.0_271 ; \
export PATH=${JAVA_HOME}/bin:$PATH ; \
java -version && \
java -jar /localpath/fmw_12.2.1.4.0_wls_quick_Disk1_1of1/fmw_12.2.1.4.0_wls_quick.jar ORACLE_HOME=/local/path/wls1221
```

FROM registry.access.redhat.com/rhel7.1

MAINTAINER Gidi Kern <gidikern@gmail.com>

RUN curl -b oraclelicense=accept-securebackup-cookie -O -L http://download.oracle.com/otn-pub/java/jdk/8u60-b27/jre-8u60-linux-x64.rpm && rpm -ivh jre-8u60-linux-x64.rpm && rm -rf jre-8u60-linux-x64.rpm

ENV JAVA_HOME /usr/java/jre1.8.0_60

# The official Red Hat registry and the base image
FROM registry.access.redhat.com/rhel7-minimal
USER root
# Install Java runtime
RUN microdnf --enablerepo=rhel-7-server-rpms \
install java-1.8.0-openjdk --nodocs ;\
microdnf clean all
# Set the JAVA_HOME variable to make it clear where Java is located
ENV JAVA_HOME /etc/alternatives/jre
# Dir for my app
RUN mkdir -p /app
# Expose port to listen to
EXPOSE 8080
# Copy the MicroProfile starter app
COPY demo-thorntail.jar /app/
# Copy the script from the source; run-java.sh has specific parameters to run a Thorntail app from the command line in a container. More on the script can be found at https://github.com/sshaaf/rhel7-jre-image/blob/master/run-java.sh
COPY run-java.sh /app/
# Setting up permissions for the script to run
RUN chmod 755 /app/run-java.sh
# Finally, run the script
CMD [ "/app/run-java.sh" ]

FROM	centos

ENV	UPDATE_VERSION=8u73
ENV	JAVA_VERSION=1.8.0_73
ENV	BUILD=b02

RUN	yum -y update && \
	yum -y install wget && \
	wget -c --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/${UPDATE_VERSION}-${BUILD}/jdk-${UPDATE_VERSION}-linux-x64.rpm" --output-document="jdk-${UPDATE_VERSION}-linux-x64.rpm" && \
	rpm -i jdk-${UPDATE_VERSION}-linux-x64.rpm && \
	alternatives --install /usr/bin/java java /usr/java/jdk${JAVA_VERSION}/bin/java 1 && \
	alternatives --set java /usr/java/jdk${JAVA_VERSION}/bin/java && \
	export JAVA_HOME=/usr/java/jdk${JAVA_VERSION}/ && \
	echo "export JAVA_HOME=/usr/java/jdk${JAVA_VERSION}/" | tee /etc/environment && \
	source /etc/environment && \
	rm jdk-${UPDATE_VERSION}-linux-x64.rpm


cd /usr/local
wget --no-cookies --no-check-certificate \
--header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F;oraclelicense=accept-securebackup-cookie" \
http://download.oracle.com/otn/java/jdk/8u281-b09/89d678f2be164786b292527658ca1605/jdk-8u281-linux-x64.tar.gz


ENV	JAVA_HOME=/usr/java/jdk${JAVA_VERSION}

export JAVA_VERSION_MAJOR="8"
export JAVA_VERSION_MINOR="60"
export JAVA_VERSION_BUILD="27"
export JAVA_PACKAGE="server-jre"
curl -jksSLH "Cookie: oraclelicense=accept-securebackup-cookie"\
  http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-b${JAVA_VERSION_BUILD}/${JAVA_PACKAGE}-${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-linux-x64.tar.gz | gunzip -c - | tar -xf -


wget -c -O "jdk-8u141-linux-x64.tar.gz" \
--no-check-certificate \
--no-cookies \
--header \
"Cookie: oraclelicense=accept-securebackup-cookie" \
"https://download.oracle.com/otn/java/jdk/8u281-b09/89d678f2be164786b292527658ca1605/jre-8u281-linux-x64.tar.gz"


jdk-8u281-linux-x64.tar.gz
https://download.oracle.com/otn/java/jdk/8u281-b09/89d678f2be164786b292527658ca1605/jre-8u281-linux-x64.tar.gz

wget -c -O "jdk-8u141-linux-x64.tar.gz" \
--no-check-certificate \
--no-cookies \
--header \
"Cookie: oraclelicense=accept-securebackup-cookie" \
"https://download.oracle.com/otn/java/jdk/8u281-b09/89d678f2be164786b292527658ca1605/jre-8u281-linux-x64.tar.gz"

curl -L --header "Cookie: s_nr=1359635827494; s_cc=true; gpw_e24=http%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjavase%2Fdownloads%2Fjdk6downloads-1902814.html; s_sq=%5B%5BB%5D%5D; gpv_p24=no%20value" http://download.oracle.com/otn-pub/java/jdk/8u281-b09/jdk-8u281-linux-x64.bin -o jdk-8u281-linux-x64.bin
