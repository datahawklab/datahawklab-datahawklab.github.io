---
layout: post
published: true
title: Podman Kafka RHEL 8
date: 2020-03-20 03:28
category: containers
author:  "datahawklab"
tags: [openshift,minishift,codeready containers,docker,podman]
summary: podman kafka instead of Docker
---

### clean-up containers

```bash
bash << EOF
podman stop $(podman ps -a -q)
podman rm $(podman ps -a -q)
podman rmi $(podman images -a -q)
podman volume prune -f
podman ps -a
podman images -a
podman volume ls
EOF
```
   
### create podman zookeeper
```bash
podman run -d \
    --net=host \
    --name=zookeeper \
    -e ZOOKEEPER_CLIENT_PORT=32181 \
    -e ZOOKEEPER_TICK_TIME=2000 \
    confluentinc/cp-zookeeper:5.3.1-1
```   
   
### create kafka broker 
```bash
podman run -d \
    --net=host \
    --name=kafka \
    -e KAFKA_ZOOKEEPER_CONNECT=localhost:32181 \
    -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:29092 \
    -e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1 \
    -p 29092:29092 \
    confluentinc/cp-kafka:5.3.1-1
```   
   
### create schema registry
```bash
podman run -d \
  --net=host \
  --name=schema-registry \
  -e SCHEMA_REGISTRY_KAFKASTORE_CONNECTION_URL=localhost:32181 \
  -e SCHEMA_REGISTRY_HOST_NAME=localhost \
  -e SCHEMA_REGISTRY_LISTENERS=http://localhost:8081 \
  -p 8081:8081 \
  confluentinc/cp-schema-registry:5.3.1-1
```   
   
### create topics
```bash
podman run \
  --net=host \
  --rm \
  confluentinc/cp-kafka:5.3.1-1 \
  kafka-topics --create --topic quickstart-avro-offsets --partitions 1 --replication-factor 1 --if-not-exists --zookeeper localhost:32181
```
```bash
podman run \
  --net=host \
  --rm \
  confluentinc/cp-kafka:5.3.1-1 \
  kafka-topics --create --topic quickstart-avro-config --partitions 1 --replication-factor 1 --if-not-exists --zookeeper localhost:32181
```   
```bash
podman run \
  --net=host \
  --rm \
  confluentinc/cp-kafka:5.3.1-1 \
  kafka-topics --create --topic quickstart-avro-status --partitions 1 --replication-factor 1 --if-not-exists --zookeeper localhost:32181
```   
   
### verify topic creation
```bash
podman run \
   --net=host \
   --rm \
   confluentinc/cp-kafka:5.3.1-1 \
   kafka-topics --describe --zookeeper localhost:32181
```   
   
### start mysql db container
```bash
podman run -d \
    --name quickstart-mysql \
    --net=host \
    -e MYSQL_USER=confluent \
    -e MYSQL_PASSWORD=confluent \
    -e MYSQL_DATABASE=connect_test \
    -p 3306:3306 \
    rhscl/mysql-57-rhel7
```   
   
### build a custom Kafka-connect Docker image   
- create a Kafka-connect folder to hold the dockerfile and mysql driver
```bash
# create a Kafka-connect folder to hold the dockerfile and mysql driver
mkdir kafka-connect
cd kafka-connect
#
# create a dockerfile
cat >> Dockerfile << EOF
FROM confluentinc/cp-kafka-connect
ARG JDBC_JAR_FILE=mysql-connector-java-8.0.18.jar
ARG CON_PATH=/usr/share/java/kafka
COPY ${JDBC_JAR_FILE} ${CON_PATH}/${JDBC_JAR_FILE}
EOF
#
# get the latest mysql jdbc driver
wget "http://search.maven.org/remotecontent?filepath=mysql/mysql-connector-java/8.0.19/mysql-connector-java-8.0.19.jar" -O mysql-connector-java-8.0.19.jar
```   
   
- Build the custom kafka-connect image
```bash
cd /root/kafka-connect  ;\
podman build -t servidc/kafka-connect:latest . ;\
podman image ls
```   
   
- connect to the mysql container and configure database
```bash
podman exec -it quickstart-mysql bash
```   
```sql
mysql -u confluent -p confluent
CREATE DATABASE IF NOT EXISTS connect_test;
USE connect_test;
DROP TABLE IF EXISTS test;
CREATE TABLE IF NOT EXISTS test (
  id serial NOT NULL PRIMARY KEY,
  name varchar(100),
  email varchar(200),
  department varchar(200),
  modified timestamp default CURRENT_TIMESTAMP NOT NULL,
  INDEX `modified_index` (`modified`)
);
INSERT INTO test (name, email, department) VALUES ('alice', 'alice@abc.com', 'engineering');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
exit;
```   
   
- start custom kafka connect connector
```bash
podman run -d \
  --name=kafka-connect-avro \
  --net=host \
  -p 28083:28083 \
  -e CONNECT_BOOTSTRAP_SERVERS=localhost:29092 \
  -e CONNECT_REST_PORT=28083 \
  -e CONNECT_GROUP_ID="quickstart-avro" \
  -e CONNECT_CONFIG_STORAGE_TOPIC="quickstart-avro-config" \
  -e CONNECT_OFFSET_STORAGE_TOPIC="quickstart-avro-offsets" \
  -e CONNECT_STATUS_STORAGE_TOPIC="quickstart-avro-status" \
  -e CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_STATUS_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_KEY_CONVERTER="io.confluent.connect.avro.AvroConverter" \
  -e CONNECT_VALUE_CONVERTER="io.confluent.connect.avro.AvroConverter" \
  -e CONNECT_KEY_CONVERTER_SCHEMA_REGISTRY_URL="http://localhost:8081" \
  -e CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL="http://localhost:8081" \
  -e CONNECT_INTERNAL_KEY_CONVERTER="org.apache.kafka.connect.json.JsonConverter" \
  -e CONNECT_INTERNAL_VALUE_CONVERTER="org.apache.kafka.connect.json.JsonConverter" \
  -e CONNECT_REST_ADVERTISED_HOST_NAME="localhost" \
  -e CONNECT_LOG4J_ROOT_LOGLEVEL=DEBUG \
  -e CONNECT_PLUGIN_PATH=/usr/share/java,/etc/kafka-connect/jars \
  -p 2803:2803 \
  servidc/kafka-connect
```

## single command
```bash
podman stop $(podman ps -a -q) ;\
podman rm $(podman ps -a -q) ;\
podman rmi $(podman images -a -q) ;\
podman volume prune -f ;\
podman ps -a ;\
podman images -a ;\
podman volume ls ;\
podman run -d \
    --net=host \
    --name=zookeeper \
    -e ZOOKEEPER_CLIENT_PORT=32181 \
    -e ZOOKEEPER_TICK_TIME=2000 \
    confluentinc/cp-zookeeper:5.3.1-1 ;\
podman run -d \
    --net=host \
    --name=kafka \
    -e KAFKA_ZOOKEEPER_CONNECT=localhost:32181 \
    -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:29092 \
    -e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1 \
    -p 29092:29092 \
    confluentinc/cp-kafka:5.3.1-1 ;\
podman run -d \
  --net=host \
  --name=schema-registry \
  -e SCHEMA_REGISTRY_KAFKASTORE_CONNECTION_URL=localhost:32181 \
  -e SCHEMA_REGISTRY_HOST_NAME=localhost \
  -e SCHEMA_REGISTRY_LISTENERS=http://localhost:8081 \
  -p 8081:8081 \
  confluentinc/cp-schema-registry:5.3.1-1 ;\
podman run \
  --net=host \
  --rm \
  confluentinc/cp-kafka:5.3.1-1 \
  kafka-topics --create --topic quickstart-avro-offsets --partitions 1 --replication-factor 1 --if-not-exists --zookeeper localhost:32181 ;\
podman run \
  --net=host \
  --rm \
  confluentinc/cp-kafka:5.3.1-1 \
  kafka-topics --create --topic quickstart-avro-config --partitions 1 --replication-factor 1 --if-not-exists --zookeeper localhost:32181 ;\
podman run \
  --net=host \
  --rm \
  confluentinc/cp-kafka:5.3.1-1 \
  kafka-topics --create --topic quickstart-avro-status --partitions 1 --replication-factor 1 --if-not-exists --zookeeper localhost:32181 ;\
podman run \
   --net=host \
   --rm \
   confluentinc/cp-kafka:5.3.1-1 \
   kafka-topics --describe --zookeeper localhost:32181 ;\
podman run -d \
    --name quickstart-mysql \
    --net=host \
    -e MYSQL_USER=confluent \
    -e MYSQL_PASSWORD=confluent \
    -e MYSQL_DATABASE=connect_test \
    -p 3306:3306 \
    rhscl/mysql-57-rhel7 ;\
mkdir ~/kafka-connect ;\
cd kafka-connect ;\
wget -q "http://search.maven.org/remotecontent?filepath=mysql/mysql-connector-java/8.0.18/mysql-connector-java-8.0.18.jar" -O mysql-connector-java-8.0.18.jar ;\
cat >> Dockerfile << EOF
FROM confluentinc/cp-kafka-connect
ARG JDBC_JAR_FILE=mysql-connector-java-8.0.18.jar
ARG CON_PATH=/usr/share/java/kafka
COPY ${JDBC_JAR_FILE} ${CON_PATH}/${JDBC_JAR_FILE}
EOF
podman build -t servidc/kafka-connect:latest . ;\
podman image ls
```   
   
- connect to the mysql container and configure database
```bash
podman exec -it quickstart-mysql bash
```   
```sql
mysql -u confluent -p confluent
CREATE DATABASE IF NOT EXISTS connect_test;
USE connect_test;
DROP TABLE IF EXISTS test;
CREATE TABLE IF NOT EXISTS test (
  id serial NOT NULL PRIMARY KEY,
  name varchar(100),
  email varchar(200),
  department varchar(200),
  modified timestamp default CURRENT_TIMESTAMP NOT NULL,
  INDEX `modified_index` (`modified`)
);
INSERT INTO test (name, email, department) VALUES ('alice', 'alice@abc.com', 'engineering');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
INSERT INTO test (name, email, department) VALUES ('bob', 'bob@abc.com', 'sales');
exit;
```   
   
- start custom kafka connect connector
```bash
podman run -d \
  --name=kafka-connect-avro \
  --net=host \
  -p 28083:28083 \
  -e CONNECT_BOOTSTRAP_SERVERS=localhost:29092 \
  -e CONNECT_REST_PORT=28083 \
  -e CONNECT_GROUP_ID="quickstart-avro" \
  -e CONNECT_CONFIG_STORAGE_TOPIC="quickstart-avro-config" \
  -e CONNECT_OFFSET_STORAGE_TOPIC="quickstart-avro-offsets" \
  -e CONNECT_STATUS_STORAGE_TOPIC="quickstart-avro-status" \
  -e CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_STATUS_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_KEY_CONVERTER="io.confluent.connect.avro.AvroConverter" \
  -e CONNECT_VALUE_CONVERTER="io.confluent.connect.avro.AvroConverter" \
  -e CONNECT_KEY_CONVERTER_SCHEMA_REGISTRY_URL="http://localhost:8081" \
  -e CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL="http://localhost:8081" \
  -e CONNECT_INTERNAL_KEY_CONVERTER="org.apache.kafka.connect.json.JsonConverter" \
  -e CONNECT_INTERNAL_VALUE_CONVERTER="org.apache.kafka.connect.json.JsonConverter" \
  -e CONNECT_REST_ADVERTISED_HOST_NAME="localhost" \
  -e CONNECT_LOG4J_ROOT_LOGLEVEL=DEBUG \
  -e CONNECT_PLUGIN_PATH=/usr/share/java,/etc/kafka-connect/jars \
  -p 2803:2803 \
  servidc/kafka-connect
```   
## one command to create a Kafka connect image with mysql jdbc driver

```bash
mkdir ~/kafka-connect ;\
cd kafka-connect ;\
wget -q "http://search.maven.org/remotecontent?filepath=mysql/mysql-connector-java/8.0.18/mysql-connector-java-8.0.18.jar" -O mysql-connector-java-8.0.18.jar ;\
cat >> Dockerfile << EOF
FROM confluentinc/cp-kafka-connect
ARG JDBC_JAR_FILE=mysql-connector-java-8.0.18.jar
ARG CON_PATH=/usr/share/java/kafka
COPY ${JDBC_JAR_FILE} ${CON_PATH}/${JDBC_JAR_FILE}
EOF
podman build -t servidc/kafka-connect-custom:latest . ;\
podman image ls
```

## run custom kafka connect container image

```bash
podman run -d \
  --name=kafka-connect-avro \
  --net=host \
  -p 28083:28083 \
  -e CONNECT_BOOTSTRAP_SERVERS=localhost:29092 \
  -e CONNECT_REST_PORT=28083 \
  -e CONNECT_GROUP_ID="quickstart-avro" \
  -e CONNECT_CONFIG_STORAGE_TOPIC="quickstart-avro-config" \
  -e CONNECT_OFFSET_STORAGE_TOPIC="quickstart-avro-offsets" \
  -e CONNECT_STATUS_STORAGE_TOPIC="quickstart-avro-status" \
  -e CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_STATUS_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_KEY_CONVERTER="io.confluent.connect.avro.AvroConverter" \
  -e CONNECT_VALUE_CONVERTER="io.confluent.connect.avro.AvroConverter" \
  -e CONNECT_KEY_CONVERTER_SCHEMA_REGISTRY_URL="http://localhost:8081" \
  -e CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL="http://localhost:8081" \
  -e CONNECT_INTERNAL_KEY_CONVERTER="org.apache.kafka.connect.json.JsonConverter" \
  -e CONNECT_INTERNAL_VALUE_CONVERTER="org.apache.kafka.connect.json.JsonConverter" \
  -e CONNECT_REST_ADVERTISED_HOST_NAME="localhost" \
  -e CONNECT_LOG4J_ROOT_LOGLEVEL=DEBUG \
  -e CONNECT_PLUGIN_PATH=/usr/share/java,/etc/kafka-connect/jars \
  -p 2803:2803 \
  servidc/kafka-connect-custom:
  ```
# Podman systemd



```bash
podman run --user 200 -it -v $(pwd)/myfolder:/mnt/myfolder:Z busybox
podman unshare chown 200:200 -R /home/tom/myshares/nexus2
podman run -it --rm --name nexus2 \
    -v /home/tom/myshares/nexus2:/sonatype-work:Z \
    sonatype/nexus /bin/sh
```

```bash
 sudo loginctl enable-linger "$USER"
systemctl edit --user --full --force <service_name>
[Unit]
Description=<Service>

[Service]
Restart=always
ExecStart=<Servece_Start>
ExecStop=<Service_Stop>

[Install]
WantedBy=default.target
systemctl enable --user <service_name>
```

```bash
podman create --name nginx nginx:latest
$ podman generate systemd --restart-policy=always -t 1 nginx
```

The --new flag generates systemd unit files that create and remove containers at service start and stop commands \(see ExecStartPre and ExecStopPost service actions\). Such unit files are not tied to a single machine and can easily be shared and used on other machines.

```bash
$ sudo podman generate systemd --new --files --name bb310a0780ae
# container-busy_moser.service
# autogenerated by Podman 1.8.3
```

Once you have generated the systemd unit file, you can copy the generated systemd file to /etc/systemd/system for installing as a root user and to $HOME/.config/systemd/user for installing it as a non-root user. Enable the copied unit file or files using systemctl enable.

Note: Copying unit files to /etc/systemd/system and enabling it marks the unit file to be automatically started at boot. And similarly, copying a unit file to $HOME/.config/systemd/user and enabling it marks the unit file to be automatically started on user login.

```bash
# Generated systemd files.
$ podman pod create --name systemd-pod
$ podman create --pod systemd-pod alpine top
$ podman generate systemd --files --name systemd-pod
```

### Copy all the generated files.

```bash
$ sudo cp pod-systemd-pod.service container-great_payne.service /etc/systemd/system
$ systemctl enable pod-systemd-pod.service
Created symlink /etc/systemd/system/multi-user.target.wants/pod-systemd-pod.service → /etc/systemd/system/pod-systemd-pod.service.
Created symlink /etc/systemd/system/default.target.wants/pod-systemd-pod.service → /etc/systemd/system/pod-systemd-pod.service.
$ systemctl is-enabled pod-systemd-pod.service
enabled
```

To run the user services placed in $HOME/.config/systemd/user on first login of that user, enable the service with –user flag.

```bash
$ systemctl --user enable <.service>
```

The systemd user instance is killed after the last session for the user is closed. The systemd user instance can be kept running ever after the user logs out by enabling lingering using

```bash
$ loginctl enable-linger <username>
```

Use systemctl to perform operations on generated installed unit files. Create and enable systemd unit files for a pod using the above examples as reference and use systemctl to perform operations.

Since systemctl defaults to using the root user, all the changes using the systemctl can be seen by appending sudo to the podman cli commands. To perform systemctl actions as a non-root user use the --user flag when interacting with systemctl.

Note: If the previously created containers or pods are using shared resources, such as ports, make sure to remove them before starting the generated systemd units.

```bash
$ systemctl --user start pod-systemd-pod.service
$ podman pod ps
POD ID         NAME          STATUS    CREATED          # OF CONTAINERS   INFRA ID
0815c7b8e7f5   systemd-pod   Running   29 minutes ago   2                 6c5d116f4bbe
$ sudo podman ps # 0 Number of pods on root.
CONTAINER ID  IMAGE  COMMAND  CREATED  STATUS  PORTS  NAMES
$ systemctl stop pod-systemd-pod.service
$ podman pod ps
POD ID         NAME          STATUS   CREATED          # OF CONTAINERS   INFRA ID
272d2813c798   systemd-pod   Exited   29 minutes a
```

[https://medium.com/devops-dudes/how-to-setup-root-less-podman-containers-efd109fa4e0d](https://medium.com/devops-dudes/how-to-setup-root-less-podman-containers-efd109fa4e0d) [https://www.n0r1sk.com/post/2019-10-02-podman-with-vxlan-overlay-network-deep-dive/](https://www.n0r1sk.com/post/2019-10-02-podman-with-vxlan-overlay-network-deep-dive/)

```bash
module podman-ovs 1.0;

require {
        type openvswitch_t;
        type container_t;
        class unix_stream_socket connectto;
}

#============= container_t ==============

#!!!! The file '/run/openvswitch/db.sock' is mislabeled on your system.
#!!!! Fix with $ restorecon -R -v /run/openvswitch/db.sock
allow container_t openvswitch_t:unix_stream_socket connectto;
```

