---
layout:     post
title:      "python from source"
subtitle:   "done"
date:       2021-05-28 12:30:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - ansible
    - openshift
---


## start fresh install of Python

```bash
rm -rf ${HOME}/usr/local/bin/python/Python-3.9.1 ;\
rm -rf ${HOME}/usr/local/source/python/Python-3.9.1 ;\
rm -rf $HOME/.local/lib/python* ;\
rm -rf $HOME/.ansible ;\
rm -rf $HOME/.cache/pip
 ```

## install python ansible non root

```bash
export PYTHON_VERSION=3.9.1 ;\
export PYTHON_VER=$(echo $PYTHON_VERSION | cut -c -3) ;\
echo "PYTHON_VER: ${PYTHON_VER}";\
export LOCAL_HOME=/home/servidc/usr/local ;\
export LOCAL_SOURCE_PATH="${LOCAL_HOME}/source" ;\
export PYTHON_SOURCE_PATH="${LOCAL_SOURCE_PATH}/python" ;\
export PYTHON_SOURCE_VER_PATH=${PYTHON_SOURCE_PATH}/Python-${PYTHON_VERSION} ;\
export PYTHON_HOME="${LOCAL_HOME}/bin/python/Python-${PYTHON_VERSION}"  ;\
export PATH="${PYTHON_HOME}/bin:${PATH}" ;\
export PYTHON_URL="https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tgz" ;\
mkdir -p $PYTHON_SOURCE_PATH && \
mkdir -p $PYTHON_HOME && \
cd $PYTHON_SOURCE_PATH && \
wget -c $PYTHON_URL -O - | tar -xz && \
cd $PYTHON_SOURCE_VER_PATH && \
./configure --enable-optimizations --prefix=${PYTHON_HOME} && \
make -j $(nproc) && \
make altinstall && \
ln -s ${PYTHON_HOME}/bin/pip${PYTHON_VER} ${PYTHON_HOME}/bin/pip && \
ln -s ${PYTHON_HOME}/bin/python${PYTHON_VER} ${PYTHON_HOME}/bin/python  && \
python -m pip install --upgrade pip && \
pip install wheel && \
pip install ansible && \
pip install paramiko && \
export REQUIREMENTS_PATH="${PYTHON_HOME}/requirements.txt" ;\
pip freeze > $REQUIREMENTS_PATH && \
echo "wrote requirements.text to ${VIRT_ENV_REQUIREMENTS_TXT_PATH}" ;\
echo "completed building and configuring python, pip and ansible"
```

## start fresh install of node.js

```bash
rm -rf ${HOME}/usr/local/source/node/node-v14.16.0 ;\
rm -rf ${HOMEc}/usr/local/bin/node/node-v14.16.0 ;\
rm -rf ${HOME}/.npm ; \
rm -rf ${HOME}/.npm_packages
```

## install python ansible non roo

```bash
export NODE_VERSION="14.16.0" ;\
export PYTHON_VERSION=3.9.1 ;\
export LOCAL_HOME=${HOME}/usr/local ;\
export PYTHON_HOME="${LOCAL_HOME}/bin/python/Python-${PYTHON_VERSION}"  ;\
export PATH="${PYTHON_HOME}/bin:${PATH}" ;\
export LOCAL_SOURCE_PATH="${LOCAL_HOME}/source" ;\
export LOCAL_HOME_PATH="${LOCAL_HOME}/bin" ;\
export NODE_SOURCE_PATH="${LOCAL_SOURCE_PATH}/node" ;\
export NODE_SOURCE_VER_PATH="${NODE_SOURCE_PATH}/node-v${NODE_VERSION}" ;\
export NODE_HOME_PATH=${LOCAL_HOME_PATH}/node
export NODE_HOME="${NODE_HOME_PATH}/node-v${NODE_VERSION}" ;\
export NODE_URL="https://nodejs.org/download/release/v${NODE_VERSION}/node-v${NODE_VERSION}.tar.gz" ;\
mkdir -p ${NODE_SOURCE_PATH} &&\
mkdir -p ${NODE_HOME} &&\
cd $NODE_SOURCE_PATH &&\
wget -c $NODE_URL -O - | tar -xz &&\
cd $NODE_SOURCE_VER_PATH &&\
./configure --prefix=${NODE_HOME} &&\
make -j $(nproc) && \
make install
export PATH="${NODE_HOME}/bin:${PATH}" && \
which python && which node && which npm && \
npm install -g npm@latest && \
which node && which npm && node -v && npm -v && \
echo "completed building and configuring node.js"
```

## setenv.source script

```bash
export PYTHON_VERSION="3.9.1"
export NODE_VERSION="14.16.0"
export LOCAL_HOME="${HOME}/usr/local"
export PYTHON_HOME="${LOCAL_HOME}/bin/python/Python-${PYTHON_VERSION}"
export NODE_HOME="${LOCAL_HOME}/bin/node/node-v${NODE_VERSION}"
export PATH="${PYTHON_HOME}/bin:${PATH}"
export PATH="${NODE_HOME}/bin:${PATH}"
```

### run setenv.source to set non root local python env variables

```bash
source ./setenv.source  && \
which node && which npm && node -v && npm -v
```