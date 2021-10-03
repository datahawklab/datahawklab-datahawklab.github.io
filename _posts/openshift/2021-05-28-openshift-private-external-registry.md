---
layout:     post
title:      "openshift private registry two"
subtitle:   " \"ansible ingo\""
date:       2021-05-28 17:00:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - deploy
    - openshift
---



# openshift private external registry

```text
[sservid@acer15 ~]$ oc registry login
info: Using registry public hostname default-route-openshift-image-registry.apps.sandbox.x8i5.p1.openshiftapps.com
Saved credentials for default-route-openshift-image-registry.apps.sandbox.x8i5.p1.openshiftapps.com
```

```text
[sservid@acer15 ~]$ oc registry info
default-route-openshift-image-registry.apps.sandbox.x8i5.p1.openshiftapps.com
```

```text
export CR_PAT=YOUR_TOKEN
```

```text
echo $CR_PAT | docker login ghcr.io -u USERNAME --password-stdin
```

```text
docker tag IMAGE_ID docker.pkg.github.com/OWNER/REPOSITORY/IMAGE_NAME:VERSION
```

```text
$ docker build -t docker.pkg.github.com/OWNER/REPOSITORY/IMAGE_NAME:VERSION PATH
```

```text
docker push docker.pkg.github.com/OWNER/REPOSITORY/IMAGE_NAME:VERSION
```

```text

```

### openshift info

```text
[sservid@acer15 ~]$ oc get secrets
NAME                            TYPE                                  DATA   AGE
builder-dockercfg-fxfkb         kubernetes.io/dockercfg               1      2d19h
builder-token-82n8m             kubernetes.io/service-account-token   4      2d19h
builder-token-k2kb9             kubernetes.io/service-account-token   4      2d19h
che-workspace-dockercfg-5xvqf   kubernetes.io/dockercfg               1      45h
che-workspace-token-2srdp       kubernetes.io/service-account-token   4      45h
che-workspace-token-7vkzj       kubernetes.io/service-account-token   4      45h
default-dockercfg-r2gpw         kubernetes.io/dockercfg               1      2d19h
default-token-bc4kg             kubernetes.io/service-account-token   4      2d19h
default-token-t68jt             kubernetes.io/service-account-token   4      2d19h
deployer-dockercfg-4bg48        kubernetes.io/dockercfg               1      2d19h
deployer-token-j52ss            kubernetes.io/service-account-token   4      2d19h
deployer-token-wxz6q            kubernetes.io/service-account-token   4      2d19h
```

### generate new private key

```bash
sh-keygen -C "openshift-source-builder/repo@bitbucket" -f repo-at-bitbucket -N ''
```

### for code ready containers source oc client

```bash
export PATH="/home/sservid/.crc/bin/oc:$PATH"
eval $(crc oc-env)
```

### add private key to openshift

```bash
oc create secret generic repo-at-bitbucket \
     --from-file=ssh-privatekey=repo-at-bitbucket \
     --type=kubernetes.io/ssh-auth
```

### add private key to bitbucket

Enable access to the secret from the builder service account

```bash
oc secrets link builder repo-at-bitbucket
```

### create a new build and deployment using oc new-app,

which uses this source secret, supply the --source-secret option to oc new-app

```bash
$ oc new-app httpd~git@bitbucket.org:osevg/private-repo.git \
    --source-secret repo-at-bitbucket --name mysite
```

### If only creating a new build, supply --source-secret to the oc new-build command instead

```bash
$ oc new-build httpd~git@bitbucket.org:osevg/private-repo.git \
    --source-secret repo-at-bitbucket --name mysite
```

```bash
IhateThePats8!
oc login -u developer -p developer <https://api.crc.testing:6443>
oc login -u kubeadmin -p Ddbvk-odz4g-NghN8-TiFC8 <https://api.crc.testing:6443>
<https://console-openshift-console.apps-crc.testing>
kubeadmin
Ddbvk-odz4g-NghN8-TiFC8
```

ref: \[[https://cookbook.openshift.org/building-and-deploying-from-source/how-can-i-build-from-a-private-repository-on-bitbucket.html](https://cookbook.openshift.org/building-and-deploying-from-source/how-can-i-build-from-a-private-repository-on-bitbucket.html)\]
