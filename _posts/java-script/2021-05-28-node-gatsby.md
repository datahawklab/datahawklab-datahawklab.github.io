---
layout:     post
title:      "ansible node gatsby"
subtitle:   "done"
date:       2021-05-28 12:20:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - node
    - gatsby
---

# Node.js

### start fresh

```bash
rm -rf $HOME/usr/local/bin/node && \
rm -rf $HOME/usr/local/source/node && \
rm -rf $HOME/.npm && \
rm -rf $HOME/.npm_packages && \
export APP="node" ;\
export APP_VERSION="14.16.0" ;\
export APP_PREFIX="${APP}-v" ;\
export APP_URL="https://nodejs.org/download/release/v${APP_VERSION}/node-v${APP_VERSION}.tar.gz" ;\
export SOURCE_PATH="${HOME}/usr/local/source" ;\
export BIN_PATH="${HOME}/usr/local/bin" ;\
export APP_DIR="${APP}/${APP_PREFIX}${APP_VERSION}" ;\
export APP_SOURCE_PATH="${SOURCE_PATH}/${APP_DIR}" ;\
export APP_BIN_PATH="${BIN_PATH}/${APP_DIR}" ;\
export PATH="${APP_BIN_PATH}:${PATH}" ;\
export NPM_PACKAGES="${HOME}/.npm-packages" ;\
export prefix="${HOME}/.npm-packages" ;\
export NODE_PATH="$NPM_PACKAGES/lib/node_modules:$NODE_PATH" ;\
export PATH="$NPM_PACKAGES/bin:$PATH" ;\
export unset MANPATH  ;\
export MANPATH="$NPM_PACKAGES/share/man:$(manpath)"
mkdir -p ${SOURCE_PATH}/${APP} && \
mkdir -p ${APP_BIN_PATH} && \
mkdir "${HOME}/.npm-packages" && \
cd "${SOURCE_PATH}/${APP}" && \
echo "${APP_URL}" && \
wget -c $APP_URL -O - | tar -xz && \
cd $APP_SOURCE_PATH && \
./configure --prefix=${APP_BIN_PATH} && \
make -j ${NPROC} && \
make install && \
npm install -g npm@latest
which python && which node && which npm
```

### Source Node and Python home

```bash
export PYTHON_VERSION=3.9.1 ;\
export APP_VERSION="14.16.0" ;\
export LOCAL_HOME=${HOME}/usr/local ;\
export PYTHON_HOME="${LOCAL_HOME}/bin/python/Python-${PYTHON_VERSION}" ;\
export PATH="${PYTHON_HOME}/bin:${PATH}" ;\
export APP="node" ;\
export APP_PREFIX="${APP}-v"
export BIN_PATH="${HOME}/usr/local/bin" ;\
export APP_DIR="${APP}/${APP_PREFIX}${APP_VERSION}" ;\
export APP_BIN_PATH="${BIN_PATH}/${APP_DIR}" ;\
export PATH="${APP_BIN_PATH}/bin:${PATH}" ;\
export NPM_PACKAGES="${HOME}/.npm-packages" ;\
export prefix="${HOME}/.npm-packages" ;\
export NODE_PATH="$NPM_PACKAGES/lib/node_modules:$NODE_PATH" ;\
export PATH="$NPM_PACKAGES/bin:$PATH" ;\
export unset MANPATH  ;\
export MANPATH="$NPM_PACKAGES/share/man:$(manpath)"

```

### remove prevoius versions

```bash
rm -rf $HOME/usr/local/bin/node && \
rm -rf $HOME/usr/local/source/node && \
rm -rf $HOME/.npm && \
rm -rf $HOME/.npm_packages
```

### Source Python home and set Node paths

```bash
export PYTHON_VERSION=3.9.1 ;\
export APP_VERSION="14.16.0" ;\
export LOCAL_HOME=${HOME}/usr/local ;\
export PYTHON_HOME="${LOCAL_HOME}/bin/python/Python-${PYTHON_VERSION}" ;\
export PATH="${PYTHON_HOME}/bin:${PATH}" ;\
export APP="node" ;\
export APP_PREFIX="${APP}-v"
export BIN_PATH="${HOME}/usr/local/bin" ;\
export APP_DIR="${APP}/${APP_PREFIX}${APP_VERSION}" ;\
export APP_BIN_PATH="${BIN_PATH}/${APP_DIR}" ;\
export PATH="${APP_BIN_PATH}/bin:${PATH}" ;\
export NPM_PACKAGES="${HOME}/.npm-packages" ;\
export prefix="${HOME}/.npm-packages" ;\
export NODE_PATH="$NPM_PACKAGES/lib/node_modules:$NODE_PATH" ;\
export PATH="$NPM_PACKAGES/bin:$PATH" ;\
export unset MANPATH  ;\
export MANPATH="$NPM_PACKAGES/share/man:$(manpath)" ;
```

### Create source and target directories

```bash
mkdir -p ${SOURCE_PATH}/${APP} && \
mkdir -p ${APP_BIN_PATH} && \
mkdir "${HOME}/.npm-packages"
```

### Build node from source non root

```bash
cd "${SOURCE_PATH}/${APP}" && \
echo "${APP_URL}" && \
wget -c $APP_URL -O - | tar -xz && \
cd $APP_SOURCE_PATH && \
./configure --prefix=${APP_BIN_PATH} && \
make -j ${NPROC} && \
make install && \
which python && which node && which npm
```

### Upgrade NPM to latest version

```bash
npm install -g npm@latest
```

### Nuxt

```bash
create-nuxt-app v3.5.2
✨  Generating Nuxt.js project in client
? Project name: client
? Programming language: JavaScript
? Package manager: Npm
? UI framework: Bootstrap Vue
? Nuxt.js modules: Axios - Promise based HTTP client
? Linting tools: ESLint
? Testing framework: Jest
? Rendering mode: Universal (SSR / SSG)
? Deployment target: Server (Node.js hosting)
? Development tools: jsconfig.json (Recommended for VS Code if you're not using typescript)
? Continuous integration: None
? Version control system: Git

```
