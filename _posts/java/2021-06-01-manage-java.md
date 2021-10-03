---
layout:     post
title:      "manage java"
subtitle:   "something"
date:       2021-05-28 00:01:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
  - java
---

# manage java

### install java, maven, gradle, node.js. ruby jekyll

```bash
useradd -d /home/servid -m -s /bin/bash -c "service id" servid && \
echo -e "tublu1224\ntublu1224" | passwd servid && \
sudo apt-get install git curl unzip zip vim -y && \
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash && \
export NVM_DIR="$HOME/.nvm" && \
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" && \
nvm install --lts && \
curl -s "https://get.sdkman.io" | bash && \
source $HOME/.sdkman/bin/sdkman-init.sh
sdk install java 16.0.1.j9-adpt && \
sdk install java 11.0.11.j9-adpt && \
sdk install java 8.0.292.j9-adpt && \ 
sdk default java 11.0.11.j9-adpt && \
sdk install maven 3.8.1 && \
sdk install gradle 7.1 && \
## https://jekyllrb.com/docs/installation/ubuntu/
sudo apt-get install ruby-full build-essential zlib1g-dev -y && \
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc && \
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc && \
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc && \
source ~/.bashrc && \
gem install jekyll bundler



su - servid -c "mkdir -p usr/local/bin"
```




vim ~/.sdkman/etc/config

#set the following
sdkman_auto_answer=true
sdkman_auto_selfupdate=true


sdk use java 11.0.11.j9-adpt
sdk default java 11.0.11.j9-adpt

```

```bash
wget https://www-us.apache.org/dist/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
sudo tar xf apache-maven-3.6.3-bin.tar.gz -C /opt
sudo ln -s /opt/apache-maven-3.6.3 /opt/maven
sudo nano /etc/profile.d/maven.sh
export JAVA_HOME=/usr/lib/jvm/jre-openjdk
export M2_HOME=/opt/maven
export MAVEN_HOME=/opt/maven
export PATH=${M2_HOME}/bin:${PATH}
sudo chmod +x /etc/profile.d/maven.sh
source /etc/profile.d/maven.sh
```

```bash
cd /usr/local/src
wget https://www-eu.apache.org/dist/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz
tar -xf apache-maven-3.6.2-bin.tar.gz
mv apache-maven-3.6.2 /usr/local/maven/
cd /etc/profile.d/
vim maven.sh
# Apache Maven Environment Variables
# MAVEN_HOME for Maven 1 - M2_HOME for Maven 2
export M2_HOME=/usr/local/maven
export PATH=${M2_HOME}/bin:${PATH}
chmod +x /etc/profile.d/maven.sh
source /etc/profile.d/maven.sh
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.272.b10-1.el8_2.x86_64
export PATH=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.272.b10-1.el8_2.x86_64/bin:$PATH
export M2_HOME=/home/sservid/
export PATH=/home/sservid/code-profiles/apache-maven-3.6.3/bin:$PATH
```

## JAVA_HOME

```bash
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.265.b01-0.el8_2.x86_64 && \
export PATH=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.265.b01-0.el8_2.x86_64/bin:$PATH && \
export PATH=/opt/apache-maven-3.6.3/bin:$PATH
```

## Graal Mandrel Quarkus

```bash
#as root:
dnf install gcc glibc-devel zlib-devel
#non root
wget https://github.com/graalvm/mandrel/releases/download/mandrel-20.2.0.0.Final/mandrel-java11-linux-amd64-20.2.0.0.Final.tar.gz .
tar -xf mandrel-java11-linux-amd64-20.2.0.0.Final.tar.gz
export JAVA_HOME="$( pwd )/mandrel-java11-20.2.0.0.Final" ;\
export GRAALVM_HOME="${JAVA_HOME}" ;\
export PATH="${JAVA_HOME}/bin:${PATH}" ;\
curl -O -J  https://code.quarkus.io/d\?e\=io.quarkus:quarkus-resteasy ;\
unzip code-with-quarkus.zip ;\
cd code-with-quarkus/ ;\
./mvnw package -Pnative ;\
./target/code-with-quarkus-1.0.0-SNAPSHOT-runner
```

### install jar in local maven

```bash
mvn install:install-file -Dfile=C:\\Users\\swapa\\Documents\\tomcat-runner\\assembly\\target\\tomcat-runner.jar -DgroupId=com.test -DartifactId=tomcat-runner -Dversion=0.01 -Dpackaging=jar -DgeneratePom=true

java -jar target/dependency/tomcat-runner.jar target/single_webapp.war
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <name>single_webapp</name>
  <groupId>test.webapp-runner</groupId>
  <artifactId>single_webapp</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.0.1</version>
      <scope>provided</scope>
    </dependency>
  </dependencies>

  <properties>
    <compiler.plugin.version>3.8.1</compiler.plugin.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>${compiler.plugin.version}</version>
        <configuration>
          <release>11</release>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-war-plugin</artifactId>
        <version>3.3.1</version>
        <configuration>
          <failOnMissingWebXml>false</failOnMissingWebXml>
          <webappDirectory>${project.build.directory}/${project.artifactId}</webappDirectory>
          <warName>${project.artifactId}</warName>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
        <version>3.1.2</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>copy</goal>
            </goals>
            <configuration>
              <artifactItems>
                <artifactItem>
                  <groupId>com.test</groupId>
                  <artifactId>tomcat-runner</artifactId>
                  <version>0.01</version>
                  <destFileName>tomcat-runner.jar</destFileName>
                </artifactItem>
              </artifactItems>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

```bash
cd /usr/local/src
wget <https://www-eu.apache.org/dist/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz>
tar -xf apache-maven-3.6.2-bin.tar.gz
mv apache-maven-3.6.2 /usr/local/maven/
cd /etc/profile.d/
vim maven.sh
```

## MAVEN_HOME for Maven 1 - M2_HOME for Maven 2



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

```bash
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
```


```bash
export M2_HOME=/usr/local/maven
export PATH=${M2_HOME}/bin:${PATH}
chmod +x /etc/profile.d/maven.sh
source /etc/profile.d/maven.sh
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.272.b10-1.el8_2.x86_64
export PATH=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.272.b10-1.el8_2.x86_64/bin:$PATH
export M2_HOME=/home/sservid/
export PATH=/home/sservid/code-profiles/apache-maven-3.6.3/bin:$PATH
```

## JAVA_HOME

```bash
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.265.b01-0.el8_2.x86_64 && \
export PATH=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.265.b01-0.el8_2.x86_64/bin:$PATH && \
export PATH=/opt/apache-maven-3.6.3/bin:$PATH
```

## Graal Mandrel Quarkus

```bash
#as root:
dnf install gcc glibc-devel zlib-devel
#non root
wget https://github.com/graalvm/mandrel/releases/download/mandrel-20.2.0.0.Final/mandrel-java11-linux-amd64-20.2.0.0.Final.tar.gz .
tar -xf mandrel-java11-linux-amd64-20.2.0.0.Final.tar.gz
export JAVA_HOME="$( pwd )/mandrel-java11-20.2.0.0.Final" ;\
export GRAALVM_HOME="${JAVA_HOME}" ;\
export PATH="${JAVA_HOME}/bin:${PATH}" ;\
curl -O -J  https://code.quarkus.io/d\?e\=io.quarkus:quarkus-resteasy ;\
unzip code-with-quarkus.zip ;\
cd code-with-quarkus/ ;\
./mvnw package -Pnative ;\
./target/code-with-quarkus-1.0.0-SNAPSHOT-runner

# Java

####

## Local Maven install

```bash
cd /usr/local/src
wget https://www-eu.apache.org/dist/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz
tar -xf apache-maven-3.6.2-bin.tar.gz
mv apache-maven-3.6.2 /usr/local/maven/
cd /etc/profile.d/
vim maven.sh
# Apache Maven Environment Variables
# MAVEN_HOME for Maven 1 - M2_HOME for Maven 2
export M2_HOME=/usr/local/maven
export PATH=${M2_HOME}/bin:${PATH}
chmod +x /etc/profile.d/maven.sh
source /etc/profile.d/maven.sh
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.272.b10-1.el8_2.x86_64
export PATH=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.272.b10-1.el8_2.x86_64/bin:$PATH
export M2_HOME=/home/sservid/
export PATH=/home/sservid/code-profiles/apache-maven-3.6.3/bin:$PATH
```

### JAVA\_HOME

```bash
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.265.b01-0.el8_2.x86_64 && \
export PATH=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.265.b01-0.el8_2.x86_64/bin:$PATH && \
export PATH=/opt/apache-maven-3.6.3/bin:$PATH
```

### Graal Mandrel Quarkus

```bash
#as root:
dnf install gcc glibc-devel zlib-devel
#non root
wget https://github.com/graalvm/mandrel/releases/download/mandrel-20.2.0.0.Final/mandrel-java11-linux-amd64-20.2.0.0.Final.tar.gz .
tar -xf mandrel-java11-linux-amd64-20.2.0.0.Final.tar.gz
export JAVA_HOME="$( pwd )/mandrel-java11-20.2.0.0.Final" ;\
export GRAALVM_HOME="${JAVA_HOME}" ;\
export PATH="${JAVA_HOME}/bin:${PATH}" ;\
curl -O -J  https://code.quarkus.io/d\?e\=io.quarkus:quarkus-resteasy ;\
unzip code-with-quarkus.zip ;\
cd code-with-quarkus/ ;\
./mvnw package -Pnative ;\
./target/code-with-quarkus-1.0.0-SNAPSHOT-runner
```

#### install jar in local maven

```bash
mvn install:install-file -Dfile=C:\\Users\\swapa\\Documents\\tomcat-runner\\assembly\\target\\tomcat-runner.jar -DgroupId=com.test -DartifactId=tomcat-runner -Dversion=0.01 -Dpackaging=jar -DgeneratePom=true

java -jar target/dependency/tomcat-runner.jar target/single_webapp.war
```

```markup
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <name>single_webapp</name>
  <groupId>test.webapp-runner</groupId>
  <artifactId>single_webapp</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.0.1</version>
      <scope>provided</scope>
    </dependency>
  </dependencies>

  <properties>
    <compiler.plugin.version>3.8.1</compiler.plugin.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>${compiler.plugin.version}</version>
        <configuration>
          <release>11</release>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-war-plugin</artifactId>
        <version>3.3.1</version>
        <configuration>
          <failOnMissingWebXml>false</failOnMissingWebXml>
          <webappDirectory>${project.build.directory}/${project.artifactId}</webappDirectory>
          <warName>${project.artifactId}</warName>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
        <version>3.1.2</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>copy</goal>
            </goals>
            <configuration>
              <artifactItems>
                <artifactItem>
                  <groupId>com.test</groupId>
                  <artifactId>tomcat-runner</artifactId>
                  <version>0.01</version>
                  <destFileName>tomcat-runner.jar</destFileName>
                </artifactItem>
              </artifactItems>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

```bash
cd /usr/local/src
wget <https://www-eu.apache.org/dist/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz>
tar -xf apache-maven-3.6.2-bin.tar.gz
mv apache-maven-3.6.2 /usr/local/maven/
cd /etc/profile.d/
vim maven.sh
```
