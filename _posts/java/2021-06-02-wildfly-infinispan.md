---
layout:     post
title:      "Wildfly infinispan info"
subtitle:   "something"
date:       2021-08-04 00:01:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - jdg
    - infinispan
    - jboss eap
    - wildfly
---

### Slave/domain/configuration/host.xml

```bash
<host name="slave1" xmlns="urn:jboss:domain:1.3">
     :
     :
    <domain-controller>
        <remote protocol="remote" host="YourMasterHostName" port="9999" security-realm="ManagementRealm" username="jadmin"/>
    </domain-controller>
     :
     <servers>
        <server name="TestServer" group="main-server-group" auto-start="true"/>
    </servers>
     :
</host>
```

## master

```bash
$ ./add-user.sh 

What type of user do you wish to add? 
 a) Management User (mgmt-users.properties) 
 b) Application User (application-users.properties)
(a): a

Enter the details of the new user to add.
Realm (ManagementRealm) : ManagementRealm
Username : jadmin
Password : passwordOne
Re-enter Password : passwordOne
About to add user 'jadmin' for realm 'ManagementRealm'
Is this correct yes/no? yes
Added user 'jadmin' to file '/opt/jboss-eap-6-Master/standalone/configuration/mgmt-users.properties'
Added user 'jadmin' to file '/opt/jboss-eap-6-Master/domain/configuration/mgmt-users.properties'
Is this new user going to be used for one AS process to connect to another AS process e.g. slave domain controller?

yes/no? yes

To represent the user add the following to the `server-identities` definition `<secret value="cGFzc3dvcmRPbmU=" />`
```


## Slave/domain/configuration/host.xml

```bash
<management>
  <security-realms>
    <security-realm name="ManagementRealm">
      <server-identities>
          <secret value="cGFzc3dvcmRPbmU=" />
      </server-identities>
      <authentication>
        <local default-user="$local" />
        <properties path="mgmt-users.properties" relative-to="jboss.domain.config.dir"/>
      </authentication>
    </security-realm>
    .
    .
    .
</management>
```










## master remove from domain.xml

```xml
<server-groups>
    <server-group name="main-server-group" profile="full">
        <jvm name="default">
            <heap size="1000m" max-size="1000m"/>
        </jvm>
        <socket-binding-group ref="full-sockets"/>
    </server-group>
    <server-group name="other-server-group" profile="full-ha">
        <jvm name="default">
            <heap size="1000m" max-size="1000m"/>
        </jvm>
        <socket-binding-group ref="full-ha-sockets"/>
    </server-group>
</server-groups>

```


## remove from host-slave.xml

```xml
    <servers>
        <server name="server-one" group="main-server-group"/>
        <server name="server-two" group="other-server-group"> <socket-bindings port-offset="150"/> </server>
    </servers>
```

## replace on host-slave.xml

```xml
    <domain-controller>
        <remote security-realm="ManagementRealm">
            <discovery-options>
                <static-discovery name="primary" protocol="${jboss.domain.master.protocol:remote+http}" host="${jboss.domain.master.address}" port="${jboss.domain.master.port:9990}"/>
            </discovery-options>
        </remote>
    </domain-controller>
```

with

```xml
    <domain-controller>
        <remote host="10.228.91.7" port="9999" security-realm="ManagementRealm" username="hostOne"/>
    </domain-controller>

    <domain-controller>
         <remote protocol="remote" host="YourMasterHostName" port="9999" security-realm="ManagementRealm" username="jadmin"/> 
    </domain-controller>
```

```bash
export JBOSS_HOME="${HOME}/Downloads/jboss-eap/EXP-1/jboss-eap-7.3" ;\
export PASS="passwordOne" ; $JBOSS_HOME/bin/add-user.sh -u admin1 -p $PASS && echo $PASS | base64
```


```bash
#!/bin/bash
PASSWORD=secret
KEYFILE=ourKeyFileName
FQHN=host.domain.com

$JBOSS_HOME/bin/jboss-cli.sh --connect <<EOF
batch
/subsystem=web/connector=https:add(secure=true,name=https,socket-binding=https,scheme=https,protocol="HTTP/1.1")
/subsystem=web/connector=https/ssl=configuration:add(name=ssl,password="$PASSWORD",certificate-key-file="\${jboss.server.config.dir}/$KEYFILE.jks",key-alias="$FQHN")
run-batch
exit
EOF
```

```bash
#!/bin/bash
PASSWORD=secret
KEYFILE=ourKeyFileName
FQHN=host.domain.com

#!/bin/bash

export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\
$JBOSS_HOME/bin/jboss-cli.sh --connect <<EOF
batch
:stop-servers(blocking=true)
/host=master/server-config=server-one:remove
/host=master/server-config=server-two:remove
/host=master/server-config=server-three:remove
/server-group=main-server-group:remove
/server-group=other-server-group:remove
/server-group=backend-servers:add(profile=full-ha, socket-binding-group=full-ha-sockets)
/host=slave-1/server-config=backend1:add(group=backend-servers, socket-binding-port-offset=100)
/host=slave-2/server-config=backend2:add(group=backend-servers, socket-binding-port-offset=200)
/server-group=backend-servers:start-servers(blocking=true)
/host=slave-1/server-config=backend1/system-property=server.name:add(boot-time=false, value=backend1)
/host=slave-2/server-config=backend2/system-property=server.name:add(boot-time=false, value=backend2)
/server-group=load-balancer:add(profile=load-balancer, socket-binding-group=load-balancer-sockets)
/host=master/server-config=load-balancer:add(group=load-balancer)
/socket-binding-group=load-balancer-sockets/socket-binding=modcluster:write-attribute(name=interface, value=public)
/server-group=load-balancer:start-servers
run-batch
exit
EOF
```

```bash
$JBOSS_HOME/bin/domain.sh -Djboss.bind.address.parameter=192.168.1.77 -Djboss.bind.address.management=192.168.1.77
```

```bash
export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\ 
$JBOSS_HOME/bin/domain.sh ;\
--host-config=host-slave-1.xml ;\
-Djboss.bind.address.parameter=192.168.1.156 ;\
-Djboss.domain.master.address=192.168.1.77 ;\
-Djboss.bind.address.management=192.168.1.156
```

```bash
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77
export CLUSTER_NODE1="192.168.1.156"
export DOMAIN_MASTER_HOST_ADDR="192.168.1.158"
run-jboss-cluster.sh

you can access it at:
http://$DOMAIN_MASTER_HOST:9990


## on Server 1 acting as the Domain controller for the cluster
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77"
$HOME/bin/run-domain-controller.sh

## on Server 2 acting as cluster node 1
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77"
export DOMAIN_SLAVE_ADDR="192.168.1.156"
$HOME/bin/run-cluster-node.sh

## on Server 3 acting as cluster node 2
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77"
export DOMAIN_SLAVE_ADDR="192.168.1.156"
$HOME/bin/run-cluster-node.sh


## on Server 1 acting as the Domain controller for the cluster

export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\
$JBOSS_HOME/bin/domain.sh ;\
-Djboss.bind.address.parameter=$DOMAIN_MASTER_HOST_ADDR ;\
-Djboss.bind.address.management=192.168.1.77

## on Server 2 acting as cluster node 1

export DOMAIN_SLAVE_ADDR-1="192.168.1.156"
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77"
export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\ 
$JBOSS_HOME/bin/domain.sh ;\
--host-config=host-slave-1.xml ;\
-Djboss.bind.address.parameter=$DOMAIN_SLAVE_ADDR-1 ;\
-Djboss.domain.master.address=$DOMAIN_MASTER_HOST ;\
-Djboss.bind.address.management=$DOMAIN_SLAVE_ADDR-1

# on Server 3 acting as cluster node 2

export DOMAIN_SLAVE_ADDR-2="192.168.1.157"
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77"
export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\ 
$JBOSS_HOME/bin/domain.sh ;\
--host-config=host-slave-1.xml ;\
-Djboss.bind.address.parameter=$DOMAIN_SLAVE_ADDR-2 ;\
-Djboss.domain.master.address=$DOMAIN_MASTER_HOST ;\
```

## for the domain controller

[http://www.mastertheboss.com/jboss-server/jboss-as-7/jboss-as-7-domain-configuration](http://www.mastertheboss.com/jboss-server/jboss-as-7/jboss-as-7-domain-configuration)
* verify in $JBOSS_HOME/configuration/domain.xml

```xml
    <server-groups>
        <server-group name="main-server-group" profile="full">
            <jvm name="default">
                <heap size="1000m" max-size="1000m"/>
            </jvm>
            <socket-binding-group ref="full-sockets"/>
        </server-group>
        <server-group name="other-server-group" profile="full-ha">
            <jvm name="default">
                <heap size="1000m" max-size="1000m"/>
            </jvm>
            <socket-binding-group ref="full-ha-sockets"/>
        </server-group>
    </server-groups>
```
* update in JBOSS_HOME/domain/configuration/host.xml

```xml
    <domain-controller>
        <local/>
    </domain-controller>
```

in since we won't add any server on this host, we need to state it, using an empty servers element:

* update in JBOSS_HOME/domain/configuration/host.xml

```xml
<servers />
```

### for each slave host update the following in $JBOSS_HOME/domain/configuration/host.xml

```xml
<host xmlns="urn:jboss:domain:11.0" name="slave-1">
...
...
<domain-controller>
<remote host="${jboss.domain.master.address}" port="${jboss.domain.master.port:9999}" username="admin1234" security-realm="ManagementRealm"/>
</domain-controller>
...
...
<security-realm name="ManagementRealm">
<server-identities>
<secret value="ZnJhbmsxMjMh" />
</server-identities>
```

### So we will configure on the first host (server1):

```xml
    <servers>
        <server name="server-one" group="main-server-group"/>
        <server name="server-two" group="main-server-group" auto-start="false">
            <socket-bindings port-offset="150"/>
        </server>
    </servers>
```


* And on the second host (server2)

```xml
    <servers>
        <server name="server-three" group="other-server-group"/>
        <server name="server-four" group="other-server-group" auto-start="false">
            <socket-bindings port-offset="150"/>
        </server>
    </servers>
```

Please notice the auto-start flag indicates that the server instances will not be started automatically if the host controller is started.
For the second server a port-offset of 150 is configured to avoid port conflicts. With the port offset we can reuse the socket-binding group of the domain configuration for multiple server instances on one host.

### add an admin user

```bash
$JBOSS_HOME/bin/add-user.sh -u admin1234 -p Password1!
```

### run the domain controller like below 

```bash
export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\
nohup $JBOSS_HOME/bin/domain.sh \
-Djboss.bind.address.parameter=192.168.1.77 \
-Djboss.bind.address.management=192.168.1.77 1> /dev/null 2>&1 &
```

### run each slave like below

```bash
export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\
nohup $JBOSS_HOME/bin/domain.sh ;\
--host-config=host-slave-1.xml ;\
-Djboss.bind.address.parameter=192.168.1.156 ;\
-Djboss.domain.master.address=192.168.1.77 ;\
-Djboss.bind.address.management=192.168.1.156 1> /dev/null 2>&1 &
```

```bash
export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\
nohup $JBOSS_HOME/bin/domain.sh ;\
--host-config=host-slave-2.xml ;\
-Djboss.bind.address.parameter=192.168.1.77 ;\
-Djboss.domain.master.address=192.168.1.77 ;\
-Djboss.bind.address.management=192.168.1.77 1> /dev/null 2>&1 &
```

### access the admin console

```bash
http://~~192.168.1.77~~:9990 
```
```bash

```bash
## on Server 1 acting as the Domain controller for the cluster
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77"
$HOME/bin/run-domain-controller.sh

## on Server 2 acting as cluster node 1
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77"
export DOMAIN_SLAVE_ADDR="192.168.1.156"
$HOME/bin/run-cluster-node.sh

## on Server 3 acting as cluster node 2
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77"
export DOMAIN_SLAVE_ADDR="192.168.1.156"
$HOME/bin/run-cluster-node.sh


## on Server 1 acting as the Domain controller for the cluster

export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\
$JBOSS_HOME/bin/domain.sh ;\
-Djboss.bind.address.parameter=$DOMAIN_MASTER_HOST_ADDR ;\
-Djboss.bind.address.management=192.168.1.77

## on Server 2 acting as cluster node 1

export DOMAIN_SLAVE_ADDR-1="192.168.1.156"
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77"
export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\
$JBOSS_HOME/bin/domain.sh ;\
--host-config=host-slave-1.xml ;\
-Djboss.bind.address.parameter=$DOMAIN_SLAVE_ADDR-1 ;\
-Djboss.domain.master.address=$DOMAIN_MASTER_HOST ;\
-Djboss.bind.address.management=$DOMAIN_SLAVE_ADDR-1

# on Server 3 acting as cluster node 2

export DOMAIN_SLAVE_ADDR-2="192.168.1.157"
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77"
export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-eap-7.3" ;\
$JBOSS_HOME/bin/domain.sh ;\
--host-config=host-slave-1.xml ;\
-Djboss.bind.address.parameter=$DOMAIN_SLAVE_ADDR-2 ;\
-Djboss.domain.master.address=$DOMAIN_MASTER_HOST ;\
-Djboss.bind.address.management=$DOMAIN_SLAVE_ADDR-1



$JBOSS_HOME/bin/jboss-cli.sh --connect controller=$JBOSS_IP:9990 --user=$USER --password=$property_value --command="/subsystem=logging:read-resource"


$JBOSS_HOME/bin/jboss-cli.sh -c
$JBOSS_HOME/bin/jboss-cli.sh -c --controller=192.168.2.101:9990 --user=jota --password=mypassword
$JBOSS_HOME/bin/jboss-cli.sh -c --controller=192.168.2.101:9990 --user=jota --password=mypassword --command=":shutdown"
$JBOSS_HOME/bin/jboss-cli.sh -c --controller=192.168.2.101:9990 --user=jota --password=mypassword --file=mybatch.cli


vexport JBOSS_CLUSTER_MASTER_IP="192.168.1.77
export CLUSTER_NODE1="192.168.1.156"
export CLUSTER_NODE2="192.168.1.158"
run-cluster.sh JBOSS

export JBOSS_CLUSTER_MASTER_IP="192.168.1.77
export CLUSTER_NODE1="192.168.1.156"
export CLUSTER_NODE2="192.168.1.158"
run-cluster.sh JDG

export JBOSS_CLUSTER_MASTER_IP="192.168.1.77
export CLUSTER_NODE1="192.168.1.156"
export CLUSTER_NODE2="192.168.1.158"
run-cluster.sh WEBLOGIC

Manage JBOSS cluster:
http://$JBOSS_DOMAIN_MASTER_HOST_IP:9990

Manage JDG cluster
http://$JDG_DOMAIN_MASTER_HOST_IP:9990

Manage Weblogic cluster
http://$DOMAIN_MASTER_HOST:9990


## run JDG cluster
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77
export CLUSTER_NODE1="192.168.1.156"
export CLUSTER_NODE2="192.168.1.158"
nohup run-JDG-cluster.sh > /dev/null 2>&1 &

## run weblogic cluster
export DOMAIN_MASTER_HOST_ADDR="192.168.1.77
export CLUSTER_NODE1="192.168.1.156"
export DOMAIN_MASTER_HOST_ADDR="192.168.1.158"
nohup run-weblogic-cluster.sh > /dev/null 2>&1 &


Manage JBOSS cluster:
http://$JBOSS_DOMAIN_MASTER_HOST_IP:9990

Manage JDG cluster
http://$JDG_DOMAIN_MASTER_HOST_IP:9990

Manage Weblogic cluster
http://$DOMAIN_MASTER_HOST:9990
```
```java

package org.infinispan;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.Base64;

/**
 * Rest example accessing a cache.
 *
 * @author Samuel Tauil (samuel@redhat.com)
 */
public class RestExample {

    /**
     * Method that puts a String value in cache.
     *
     * @param urlServerAddress URL containing the cache and the key to insert
     * @param value            Text to insert
     * @param user             Used for basic auth
     * @param password         Used for basic auth
     */
    public void putMethod(String urlServerAddress, String value, String user, String password) throws IOException {
        System.out.println("----------------------------------------");
        System.out.println("Executing PUT");
        System.out.println("----------------------------------------");
        URL address = new URL(urlServerAddress);
        System.out.println("executing request " + urlServerAddress);
        HttpURLConnection connection = (HttpURLConnection) address.openConnection();
        System.out.println("Executing put method of value: " + value);
        connection.setRequestMethod("PUT");
        connection.setRequestProperty("Content-Type", "text/plain");
        addAuthorization(connection, user, password);
        connection.setDoOutput(true);

        OutputStreamWriter outputStreamWriter = new OutputStreamWriter(connection.getOutputStream());
        outputStreamWriter.write(value);

        connection.connect();
        outputStreamWriter.flush();
        System.out.println("----------------------------------------");
        System.out.println(connection.getResponseCode() + " " + connection.getResponseMessage());
        System.out.println("----------------------------------------");
        connection.disconnect();
    }

    /**
     * Method that gets a value by a key in url as param value.
     *
     * @param urlServerAddress URL containing the cache and the key to read
     * @param user             Used for basic auth
     * @param password         Used for basic auth
     * @return String value
     */
    public String getMethod(String urlServerAddress, String user, String password) throws IOException {
        String line;
        StringBuilder stringBuilder = new StringBuilder();

        System.out.println("----------------------------------------");
        System.out.println("Executing GET");
        System.out.println("----------------------------------------");

        URL address = new URL(urlServerAddress);
        System.out.println("executing request " + urlServerAddress);

        HttpURLConnection connection = (HttpURLConnection) address.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Content-Type", "text/plain");
        addAuthorization(connection, user, password);
        connection.setDoOutput(true);

        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(connection.getInputStream()));

        connection.connect();

        while ((line = bufferedReader.readLine()) != null) {
            stringBuilder.append(line).append('\n');
        }

        System.out.println("Executing get method of value: " + stringBuilder.toString());

        System.out.println("----------------------------------------");
        System.out.println(connection.getResponseCode() + " " + connection.getResponseMessage());
        System.out.println("----------------------------------------");

        connection.disconnect();

        return stringBuilder.toString();
    }

    private void addAuthorization(HttpURLConnection connection, String user, String pass) {
        String credentials = user + ":" + pass;
        String basic = Base64.getEncoder().encodeToString(credentials.getBytes());
        connection.setRequestProperty("Authorization", "Basic " + basic);
    }

    /**
     * Main method example.
     */
    public static void main(String[] args) throws IOException {
        RestExample restExample = new RestExample();
        String user = "user";
        String pass = "pass";
        restExample.putMethod("http://localhost:11222/rest/v2/caches/default/1", "Infinispan REST Test", user, pass);
        restExample.getMethod("http://localhost:11222/rest/v2/caches/default/1", user, pass);
    }
}
```

```java
3.4. HttpClient API REST Example
package org.infinispan;

import java.io.IOException;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Base64;

/**
 * RestExample class shows you how to access your cache via HttpClient API with Java 11 or later.
 *
 * @author Gustavo Lira (glira@redhat.com)
 */
public class RestExample {
   private static final String SERVER_ADDRESS = "http://localhost:11222";
   private static final String CACHE_URI = "/rest/v2/caches/default";

   /**
    * postMethod create a named cache.
    * @param httpClient HTTP client that sends requests and receives responses
    * @param builder    Encapsulates HTTP requests
    * @throws IOException
    * @throws InterruptedException
    */
   public void postMethod(HttpClient httpClient, HttpRequest.Builder builder) throws IOException, InterruptedException {
      System.out.println("----------------------------------------");
      System.out.println("Executing POST");
      System.out.println("----------------------------------------");

      HttpRequest request = builder.POST(HttpRequest.BodyPublishers.noBody()).build();
      HttpResponse<Void> response = httpClient.send(request, HttpResponse.BodyHandlers.discarding());

      System.out.println("----------------------------------------");
      System.out.println(response.statusCode());
      System.out.println("----------------------------------------");
   }

   /**
    * putMethod stores a String value in your cache.
    * @param httpClient HTTP client that sends requests and receives responses
    * @param builder    Encapsulates HTTP requests
    * @throws IOException
    * @throws InterruptedException
    */
   public void putMethod(HttpClient httpClient, HttpRequest.Builder builder) throws IOException, InterruptedException {
      System.out.println("----------------------------------------");
      System.out.println("Executing PUT");
      System.out.println("----------------------------------------");

      String cacheValue = "Infinispan REST Test";
      HttpRequest request = builder.PUT(HttpRequest.BodyPublishers.ofString(cacheValue)).build();
      HttpResponse<Void> response = httpClient.send(request, HttpResponse.BodyHandlers.discarding());

      System.out.println("----------------------------------------");
      System.out.println(response.statusCode());
      System.out.println("----------------------------------------");
   }

   /**
    * getMethod get a String value from your cache.
    * @param httpClient HTTP client that sends requests and receives responses
    * @param builder    Encapsulates HTTP requests
    * @return           String value
    * @throws IOException
    */
   public String getMethod(HttpClient httpClient, HttpRequest.Builder builder) throws IOException, InterruptedException {
      System.out.println("----------------------------------------");
      System.out.println("Executing GET");
      System.out.println("----------------------------------------");

      HttpRequest request = builder.GET().build();
      HttpResponse<String> response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());

      System.out.println("Executing get method of value: " + response.body());

      System.out.println("----------------------------------------");
      System.out.println(response.statusCode());
      System.out.println("----------------------------------------");

      return response.body();
   }

   public static void main(String[] args) throws IOException, InterruptedException {
      RestExample restExample = new RestExample();
      HttpClient httpClient = HttpClient.newBuilder().version(HttpClient.Version.HTTP_1_1).build();

      restExample.postMethod(httpClient, getHttpReqestBuilder(String.format("%s%s", SERVER_ADDRESS, CACHE_URI)));
      restExample.putMethod(httpClient, getHttpReqestBuilder(String.format("%s%s/1", SERVER_ADDRESS, CACHE_URI)));
      restExample.getMethod(httpClient, getHttpReqestBuilder(String.format("%s%s/1", SERVER_ADDRESS, CACHE_URI)));
   }

   private static String basicAuth(String username, String password) {
      return "Basic " + Base64.getEncoder().encodeToString((username + ":" + password).getBytes());
   }

   private static final HttpRequest.Builder getHttpReqestBuilder(String url) {
      return HttpRequest.newBuilder()
            .uri(URI.create(url))
            .header("Content-Type", "text/plain")
            .header("Authorization", basicAuth("user", "pass"));
   }
}
```

### user-loggin-info

```bash
export JBOSS_HOME="${HOME}/Downloads/jboss-eap/EXP-1/jboss-eap-7.3" ;\
export PASS="Password1@" ; $JBOSS_HOME/bin/add-user.sh -u admin1 -p $PASS && echo $PASS | base64

UGFzc3dvcmQxQAo=

```

### Domain controller

```bash
export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-jdg/jboss-eap-7.3" && \
cd $JBOSS_HOME && \
mv ./domain/configuration/host.xml ./domain/configuration/host.xml.removed && \
cp ./domain/configuration/host-master.xml ./domain/configuration/host.xml && \
./bin/add-user.sh -u admin -p admin! && \
./bin/domain.sh -Djboss.bind.address=192.168.1.77 -Djboss.bind.address.management=192.168.1.77

export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-jdg/jboss-eap-7.3" && \
./bin/domain.sh -Djboss.bind.address=192.168.1.77 -Djboss.bind.address.management=192.168.1.77
```



5. Go to the slave node (with app server) and change default host settings

export JBOSS_HOME="/home/servidc/usr/local/bin/jboss-jdg/jboss-eap-7.3" && \
cd $JBOSS_HOME && \
mv ./domain/configuration/host.xml ./domain/configuration/host.xml.removed && \
cp ./domain/configuration/host-slave.xml ./domain/configuration/host.xml

6. Edit ./domain/configuration/host.xml like

<host xmlns="urn:jboss:domain:4.0" name="&lt;HOST_NAME&gt;">
<security-realm name="ManagementRealm">
<server-identities>

<secret value="MANAGEMENT_USER_PASSWORD&gt"/>
</server-identities>

......

<domain-controller>
    <remote security-realm="ManagementRealm" username="MANAGEMENT_USER">
        <discovery-options>
            <static-discovery name="primary" protocol="${jboss.domain.master.protocol:remote}" host="${jboss.domain.master.address}" port="${jboss.domain.master.port:9999}"/>
        </discovery-options>
    </remote>
</domain-controller>

7. Start slave node
   cd $JBOSS_HOME\bin &&\
   ./domain.sh &&
   $JBOSS_HOME/bin/domain.sh -Djboss.domain.master.address=192.168.1.77 -Djboss.bind.address.management=192.168.1.156 -Djboss.bind.address=0.0.0.0
......



### install steps

