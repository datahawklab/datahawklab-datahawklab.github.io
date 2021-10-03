---
layout:     post
title:      "openshift mysql deployment config"
subtitle:   " \"ansible ingo\""
date:       2021-05-28 16:00:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - deploy
    - openshift
---



```bash
piVersion: v1
kind: DeploymentConfig
metadata:
  annotations:
    openshift.io/generated-by: OpenShiftNewApp
  creationTimestamp: 2017-03-17T02:58:18Z
  generation: 3
  labels:
    app: mysql
  name: mysql
  namespace: dbinit
  resourceVersion: "3752083"
  selfLink: /oapi/v1/namespaces/dbinit/deploymentconfigs/mysql
  uid: 92f95a70-0abd-11e7-aa23-000d3af7a1bb
spec:
  replicas: 1
  selector:
    app: mysql
    deploymentconfig: mysql
  strategy:
    resources: {}
    rollingParams:
      intervalSeconds: 1
      maxSurge: 25%
      maxUnavailable: 25%
      post:
        execNewPod:
          command:
          - /bin/sh
          - -c
          - hostname && sleep 10 && /opt/rh/rh-mysql56/root/usr/bin/mysql -h $MYSQL_SERVICE_HOST
            -u $MYSQL_USER -D $MYSQL_DATABASE -p$MYSQL_PASSWORD -P 3306 -e 'CREATE
            TABLE IF NOT EXISTS emails (from_add varchar(40), to_add varchar(40),
            subject varchar(40), body varchar(200), created_at date);' && sleep 60
          containerName: mysql
        failurePolicy: ignore
      timeoutSeconds: 600
      updatePeriodSeconds: 1
    type: Rolling
  template:
    metadata:
      annotations:
        openshift.io/generated-by: OpenShiftNewApp
      creationTimestamp: null
      labels:
        app: mysql
        deploymentconfig: mysql
    spec:
      containers:
      - env:
        - name: MYSQL_DATABASE
          value: mydatabase
        - name: MYSQL_PASSWORD
          value: password
        - name: MYSQL_USER
          value: user
        image: registry.access.redhat.com/rhscl/mysql-56-rhel7@sha256:1c767450a7b1ef7151bf54f18d96ae6fef0e71a52ee4a58b9b28e65614fe5962
        imagePullPolicy: Always
        name: mysql
        ports:
        - containerPort: 3306
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        volumeMounts:
        - mountPath: /var/lib/mysql/data
          name: mysql-volume-1
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      securityContext: {}
      terminationGracePeriodSeconds: 30
      volumes:
      - emptyDir: {}
        name: mysql-volume-1
  test: false
  triggers:
  - type: ConfigChange
  - imageChangeParams:
      automatic: true
      containerNames:
      - mysql
      from:
        kind: ImageStreamTag
        name: mysql:latest
        namespace: dbinit
      lastTriggeredImage: registry.access.redhat.com/rhscl/mysql-56-rhel7@sha256:1c767450a7b1ef7151bf54f18d96ae6fef0e71a52ee4a58b9b28e65614fe5962
    type: ImageChange
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: 2017-03-17T02:58:25Z
    message: Deployment config has minimum availability.
    status: "True"
    type: Available
  - lastTransitionTime: 2017-03-17T02:58:22Z
    message: Replication controller "mysql-1" has completed progressing
    reason: NewReplicationControllerAvailable
    status: "True"
    type: Progressing
  details:
    causes:
    - imageTrigger:
        from:
          kind: ImageStreamTag
          name: mysql:latest
          namespace: dbinit
      type: ImageChange
    message: image change
  latestVersion: 1
  observedGeneration: 3
  replicas: 1
  updatedReplicas: 1
  ```
