---
layout:     post
title:      "openshift oc build using templates"
subtitle:   "dpme"
date:       2021-05-28 12:42:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - deploy
    - openshift
---


using templates for platforms on openshift
====================

***The templates are in the manifests folder***
[manifests](../manifests/)

**There are 3 templets:**
1. mule-base-template.yaml
    * This template has the docker build config to build the mule base image
2. hello-app-build-template.yaml
	* This template has the docker build config that uses the base mule image and layers the mule application on top of it
3. hello-app-deployment-template.yaml
    * This template has the deployment configuration for the application pointing to mule application image
	* It also has the service and route configured with tls enabled to be able to connect to mule from outside of openshift
	* Readiness and liveness probes are configured to ensure application is healthy

Here are the steps to create this application in one namespace/project in openshift.
In a real process there would be many namespaces.
The Mule base template would be applied in one project.
The hello app build template would be applied in a different project.
And the hello app deployment template would be deployed in every project from dev through production.

***The process command fills in the parameters of the template and creates valid openshift/kubernetes object***
It is then piped to apply which applies the objects against the cluster
This creates the build config for the mule base image
```bash
oc process -f mule-base-template.yaml | oc apply -f -
```
***starts a build to create the mule base image***
* Make sure to switch to the mule base image folder.
```
oc start-build mule-base-image --from-dir=.
```
***creates the build config for the application image***
```
oc process -f hello-app-build-template.yaml | oc apply -f -
```
***commands starts a build to create the hello mule app image***
```
oc start-build hello-mule --from-dir=.
```
***creates the secret mule needs to run***
* could be part of the template, but given the plan is the template will be versioned with the code, this should be kept out so developers do not have access
```
oc create secret generic mule-args --from-literal=MULE_ARGUMENTS='-M-Dmule.env=dev -M-Dmule.key=1234567890123456'
```
***creates the deployment config, the service and the route for the application***
```
oc process -f hello-app-deployment-template.yaml | oc apply -f -
```
validating running app
====================

* One of the pods should be of the form "hello-mule-#-ddddd"
* There is a ready column that a few moments after deployment will say 1/1
* This means the pod is up and running and the 1/1 means the readiness check has passed
* A pod will not be accessible until the readiness check passes.  The service layer will not route to a pod that is not ready.

```
oc get pods
```
***Get the url for the route and copy it***
---
layout:     post
title:      "opensift routing"
subtitle:   " \"ansible ingo\""
date:       2021-05-28 12:00:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - ansible
    - openshift
---

```bash
oc get routes
```
***test rest end point***
* insecure part just means that it is using tls and there is a cert, but we are using a self-signed cert, and not one signed by the company

```
curl https://{route}/hello  --insecure
```

Updating the app and Rolling deployments
====================

Now we will show the process of updating the application.  The deployment is configured to do a rolling deployment on a update.
This means for example if there are 5 replicas of the app running then during an update one old one will go down and then another new one will go up.
This process will continue until all replicas of the application are the new image.  The old ones will not go down until the new applications readiness check passes.
(note: It may not be exactly 1 down and then 1 up, the rolling parameters are configurable, but there will always be an old version up until the new version is ready)
```
oc start-build hello-mule --from-dir=.
```
* make sure no builds are running
```
oc get builds
```
***deploy latest build***
* This command kicks off the deployment of the latest image.  It will be a rolling deployment
* If you go to the openshift UI you can see how it is slowly bringing down the old version and bringing up the new one as pods get ready
```
oc rollout latest hello-mule
```
