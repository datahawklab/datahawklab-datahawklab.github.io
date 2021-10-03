---
layout:     post
title:      "openshift deploy app"
subtitle:   "done"
date:       2021-05-28 12:00:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - deploy
    - openshift
---

# **Development leveraging customized image from Operations**
```BASH
# oc login -u admin
# oc new-project ruby-ops
# oc new-project ruby-dev
```
### create 3 groups
* group to allow operations to edit the ruby-ops project
* group to allow development to view the ruby-ops project
* group to edit the ruby-dev project

```BASH
oadm groups new ops-edit
oadm groups new dev-view
oadm groups new dev-edit
```

### Associate groups to projects
```BASH
# oadm policy add-role-to-group edit ops-edit -n ruby-ops
# oadm policy add-role-to-group view dev-view -n ruby-ops
# oadm policy add-role-to-group edit dev-edit -n ruby-dev
```

### setup pull permissions to allow ruby-dev to pull images from ruby-ops
```BASH
oadm policy add-role-to-group system:image-puller system:serviceaccounts:ruby-dev -n ruby-ops
```

### As ops user create a ruby runtime image using application test code
```BASH
# oc login -u ops
# oc project ruby-ops
# oc new-app centos/ruby-22-centos7~https://github.com/openshift/ruby-hello-world.git
```

### extract database and service name to provide app
```BASH
# oc new-app mysql-ephemeral -p DATABASE_SERVICE_NAME=database
# oc env dc database --list | oc env dc ruby-hello-world -e -
```

### As dev user pull the operations ruby runtime image and build using latest code from different Github branch or project.
```bash
# oc login -u dev
# oc project ruby-dev
# oc new-app ruby-ops/ruby-22-centos7:latest~https://github.com/ktenzer/ruby-hello-world.git
```

### Application requires database and service name called database.
```bash
# oc new-app mysql-ephemeral -p DATABASE_SERVICE_NAME=database
# oc env dc database --list | oc env dc ruby-hello-world -e -
```

# image promotion

### Create projects and setup pull permissions
```bash
# oc new-project ticket-monster-dev
# oc new-project ticket-monster-prod
# oc policy add-role-to-group system:image-puller system:serviceaccounts:ticket-monster-prod -n ticket-monster-dev
```

### Create ticket monster template for development
```yaml
kind: Template
apiVersion: v1
metadata:
 name: monster
 annotations:
 tags: instant-app,javaee
 iconClass: icon-jboss
 description: |
 Ticket Monster is a moderately complex application that demonstrates how
 to build modern applications using JBoss web technologies

parameters:
- name: GIT_URI
 value: git://github.com/kenthua/ticket-monster-ose
- name: MYSQL_DATABASE
 value: monster
- name: MYSQL_USER
 value: monster
- name: MYSQL_PASSWORD
 from: '[a-zA-Z0-9]{8}'
 generate: expression


objects:
- kind: ImageStream
 apiVersion: v1
 metadata:
 name: monster

- kind: BuildConfig
 apiVersion: v1
 metadata:
 name: monster
 spec:
 triggers:
 - type: Generic
 generic:
 secret: secret
 - type: ImageChange
 - type: ConfigChange
 strategy:
 type: Source
 sourceStrategy:
 from:
 kind: ImageStreamTag
 name: jboss-eap64-openshift:latest
 namespace: openshift
 source:
 type: Git
 git:
 uri: ${GIT_URI}
 ref: master
 output:
 to:
 kind: ImageStreamTag
 name: monster:latest

- kind: DeploymentConfig
 apiVersion: v1
 metadata:
 name: monster
 spec:
 replicas: 1
 selector:
 deploymentConfig: monster
 template:
 metadata:
 labels:
 deploymentConfig: monster
 name: monster
 spec:
 containers:
 - name: monster
 image: monster
 ports:
 - name: http
 containerPort: 8080
 - name: jolokia
 containerPort: 8778
 - name: debug
 containerPort: 8787
 readinessProbe:
 exec:
 command:
 - /bin/bash
 - -c
 - /opt/eap/bin/readinessProbe.sh
 env:
 - name: DB_SERVICE_PREFIX_MAPPING
 value: monster-mysql=DB
 - name: TX_DATABASE_PREFIX_MAPPING
 value: monster-mysql=DB
 - name: DB_JNDI
 value: java:jboss/datasources/MySQLDS
 - name: DB_DATABASE
 value: ${MYSQL_DATABASE}
 - name: DB_USERNAME
 value: ${MYSQL_USER}
 - name: DB_PASSWORD
 value: ${MYSQL_PASSWORD}
 - name: JAVA_OPTS
 value: "-Xmx512m -XX:MaxPermSize=256m -Djava.net.preferIPv4Stack=true -Djboss.modules.system.pkgs=org.jboss.logmanager -Djava.awt.headless=true -Djboss.modules.policy-permissions=true"
 - name: DEBUG
 value: "true"
 triggers:
 - type: ImageChange
 imageChangeParams:
 automatic: true
 containerNames:
 - monster
 from:
 kind: ImageStream
 name: monster

- kind: DeploymentConfig
 apiVersion: v1
 metadata:
 name: monster-mysql
 spec:
 triggers:
 - type: ImageChange
 imageChangeParams:
 automatic: true
 containerNames:
 - monster-mysql
 from:
 kind: ImageStreamTag
 name: mysql:latest
 namespace: openshift
 replicas: 1
 selector:
 deploymentConfig: monster-mysql
 template:
 metadata:
 labels:
 deploymentConfig: monster-mysql
 name: monster-mysql
 spec:
 containers:
 - name: monster-mysql
 image: mysql
 ports:
 - containerPort: 3306
 env:
 - name: MYSQL_USER
 value: ${MYSQL_USER}
 - name: MYSQL_PASSWORD
 value: ${MYSQL_PASSWORD}
 - name: MYSQL_DATABASE
 value: ${MYSQL_DATABASE}

- kind: Service
 apiVersion: v1
 metadata:
 name: monster
 spec:
 ports:
 - name: http
 port: 8080
 selector:
 deploymentConfig: monster

- kind: Service
 apiVersion: v1
 metadata:
 name: monster-mysql
 spec:
 ports:
 - port: 3306
 selector:
 deploymentConfig: monster-mysql

- kind: Route
 apiVersion: v1
 metadata:
 name: monster
 spec:
 to:
 name: monster
 ```

`	`bash
oc create -n openshift -f monster-dev.yaml
`	`

### Create template for ticket monster production environment
* Below trigger will only deploy production environment when the image stream in development is tagged with monster:prod.

```yaml
kind: Template
apiVersion: v1
metadata:
 name: monster-prod
 annotations:
 tags: instant-app,javaee
 iconClass: icon-jboss
 description: |
 Ticket Monster is a moderately complex application that demonstrates how
 to build modern applications using JBoss web technologies. This template
 is for "production deployments" of Ticket Monster.

parameters:
- name: MYSQL_DATABASE
 value: monster
- name: MYSQL_USER
 value: monster
- name: MYSQL_PASSWORD
 from: '[a-zA-Z0-9]{8}'
 generate: expression

objects:
- kind: DeploymentConfig
 apiVersion: v1
 metadata:
 name: monster
 spec:
 replicas: 3
 selector:
 deploymentConfig: monster
 te 	 	mplate:
 metadata:
 labels:
 deploymentConfig: monster
 name: monster
 spec:
 containers:
 - name: monster
 image: monster
 ports:
 - name: http
 containerPort: 8080
 - name: jolokia
 containerPort: 8778
 readinessProbe:
 exec:
 command:
 - /bin/bash
 - -c
 - /opt/eap/bin/readinessProbe.sh
 env:
 - name: DB_SERVICE_PREFIX_MAPPING
 value: monster-mysql=DB
 - name: TX_DATABASE_PREFIX_MAPPING
 value: monster-mysql=DB
 - name: DB_JNDI
 value: java:jboss/datasources/MySQLDS
 - name: DB_DATABASE
 value: ${MYSQL_DATABASE}
 - name: DB_USERNAME
 value: ${MYSQL_USER}
 - name: DB_PASSWORD
 value: ${MYSQL_PASSWORD}
 - name: JAVA_OPTS
 value: "-Xmx512m -XX:MaxPermSize=256m -Djava.net.preferIPv4Stack=true -Djboss.modules.system.pkgs=org.jboss.logmanager -Djava.awt.headless=true -Djboss.modules.policy-permissions=true"
 triggers:
 - type: ImageChange
 imageChangeParams:
 automatic: true
 containerNames:
 - monster
 from:
 kind: ImageStreamTag
 name: monster:prod
 namespace: ticket-monster-dev

- kind: DeploymentConfig
 apiVersion: v1
 metadata:
 name: monster-mysql
 spec:
 triggers:
 - type: ImageChange
 imageChangeParams:
 automatic: true
 containerNames:
 - monster-mysql
 from:
 kind: ImageStreamTag
 name: mysql:latest
 namespace: openshift
 replicas: 1
 selector:
 deploymentConfig: monster-mysql
 template:
 metadata:
 labels:
 deploymentConfig: monster-mysql
 name: monster-mysql
 spec:
 containers:
 - name: monster-mysql
 image: mysql
 ports:
 - containerPort: 3306
 env:
 - name: MYSQL_USER
 value: ${MYSQL_USER}
 - name: MYSQL_PASSWORD
 value: ${MYSQL_PASSWORD}
 - name: MYSQL_DATABASE
 value: ${MYSQL_DATABASE}

- kind: Service
 apiVersion: v1
 metadata:
 name: monster
 spec:
 ports:
 - name: http
 port: 8080
 selector:
 deploymentConfig: monster

- kind: Service
 apiVersion: v1
 metadata:
 name: monster-mysql
 spec:
 ports:
 - port: 3306
 selector:
 deploymentConfig: monster-mysql

- kind: Route
 apiVersion: v1
 metadata:
 name: monster
 spec:
 to:
 name: monster
 ```

### using ui deploy the prod template
* you'll notice that in dev the app is running and accessible
* If you look at production environment, the database is running and a service endpoint exists however ticket monster application is not running
* he production template is as mentioned setup to pull images from development automatically that have a certain name/tag
* The production environment also runs scale of 4 for application where development only has scale of 1

### Promote development application version to production
* Get the image stream pull spec.
```bash
oc get is monster -o yaml
```

```yaml
apiVersion: v1
kind: ImageStream
metadata:
 annotations:
 openshift.io/image.dockerRepositoryCheck: 2016-08-09T13:37:47Z
 creationTimestamp: 2016-08-09T13:14:53Z
 generation: 7
 name: monster
 namespace: ticket-monster-dev
 resourceVersion: "107170"
 selfLink: /oapi/v1/namespaces/ticket-monster-dev/imagestreams/monster
 uid: 42740a3d-5e33-11e6-aa8d-001a4ae42e01
spec:
 tags:
 - annotations: null
 from:
 kind: ImageStreamImage
 name: monster@sha256:3a48a056a58f50764953ba856d90eba73dd0dfdee10b8cb6837b0fd9461da7f9
 generation: 7
 importPolicy: {}
 name: prod
status:
 dockerImageRepository: 172.30.139.50:5000/ticket-monster-dev/monster
 tags:
 - items:
 - created: 2016-08-09T13:26:04Z
 dockerImageReference: 172.30.139.50:5000/ticket-monster-dev/monster@sha256:3a48a056a58f50764953ba856d90eba73dd0dfdee10b8cb6837b0fd9461da7f9
 generation: 1
 image: sha256:3a48a056a58f50764953ba856d90eba73dd0dfdee10b8cb6837b0fd9461da7f9
 tag: latest
```

* Once you have the image stream pull specification
```bASH
name: monster@sha256:3a48a056a58f50764953ba856d90eba73dd0dfdee10b8cb6837b0fd9461da7f9
```

* tag the image stream monster:prod
```BASH
oc tag monster@sha256:3a48a056a58f50764953ba856d90eba73dd0dfdee10b8cb6837b0fd9461da7f9 monster:prod
```

* VERIFY IMAGE STREAM TAGS
```BASH
oc get is
NAME DOCKER REPO TAGS UPDATED
monster 172.30.139.50:5000/ticket-monster-dev/monster prod,latest 2 minutes ago
```

* As soon as the image stream in ticket-monster-dev is tagged with monster:prod it will be deployed to production

# Manual AB Deployment using Jenkins

* three environments: development, integration and production
* two version of our application, v1 and v2 in dev environment
* Using Jenkins we will show how to promote v2 of application from development to integration to production
* inally we will show how to rollback production from v2 to v1

```BASH
oc new-project dev && \
oc new-project int && \
oc new-project prod
```
### Setup pull permissions
* Allow int environment to pull images from dev environment
```BASH
# oc policy add-role-to-group system:image-puller system:serviceaccounts:int -n dev
```

* Prod to pull images from both dev and int environments.
```bash
# oc policy add-role-to-group system:image-puller system:serviceaccounts:prod -n int
# oc policy add-role-to-group system:image-puller system:serviceaccounts:prod -n dev
```
### Setup jenkins  in development environment


### allow Jenkins to access OpenShift environment
```bash
# oc login -u admin
# oc whoami -t
DMzhKyEN87DZiDYV6i1d8L8NL2e6gFVFPpT5FnozKtU

git pull https://github.com/servidc/openshift-demo.git
# in jenkins-jobs update the auth token in promote-int.xml promote-prod.xml rel-v2.xml rollback-prod.xml
```

### Setup all three environements
```bash
# cd openshift-demo
# oc create -f template.json -n dev
# oc create -f acceptance.template.json -n int
# oc create -f production.template.json -n prod
```

### Deploy nodejs hello-world application in dev environment
* This template creates two versions of the application: v1-ab and v2-ab.
```bash
# oc new-app -f template.json -n dev
```

### Deploy v1 from development to integration
* Deploy v1 to int using tags. A trigger is set on int (acceptance) template to deploy when the image acceptance:latest is updated
```bash
oc tag v1:latest v1:1.0 -n dev
oc tag dev/v1:1.0 acceptance:v1 -n int
oc tag acceptance:v1 acceptance:latest -n int
```

### Deploy v1 from integration to production
* A trigger is set on prod template to deploy when the image production:latest is updated.
```bash
oc tag int/acceptance:v1 production:v1 -n prod
oc tag production:v1 production:latest -n prod
```

# AB Deployment using Jenkins
* promote a release from development to integration
* promote a release from integration to production
* build a new version of the v2 application
* perform a rollback in production.

```bash
# curl -k -u admin:password -XPOST -d @jenkins-jobs/rel-v2.xml 'https://jenkins-dev.apps.lab/createItem?name=rel-v2' -H "Content-Type: application/xml"
# curl -k -u admin:password -XPOST -d @jenkins-jobs/promote-int.xml 'https://jenkins-dev.apps.lab/createItem?name=promote-int' -H "Content-Type: application/xml"
# curl -k -u admin:password -XPOST -d @jenkins-jobs/promote-prod.xml 'https://jenkins-dev.apps.lab/createItem?name=promote-prod' -H "Content-Type: application/xml"
# curl -k -u admin:password -XPOST -d @jenkins-jobs/rollback-prod.xml 'https://jenkins dev.apps.lab/createItem?name=rollback-prod' -H "Content-Type: application/xml"
```
