---
layout: post
title: Ubuntu 20.04 LXD/LXC Java, Gradle, Maven, Node.js, Ruby, Jekyll developer container with Ubuntu cloud image
date: 2021-08-24 00:00:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
  - java
  - maven
  - gradle
  - lxd
  - lxc
  - container
  - jekyll
  - ruby
  - nvm
  - sdkman
  - npm
---

### create ubuntu cloud server lxd container

```bash
lxc launch ubuntu-minimal:focal devserver && \
lxc launch ubuntu-minimal:focal ubuntu-minimal-focal-sdkman

lxc exec devserver bash
```

### as root on the lxd cotnainer install dependencies and a service user
```bash
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys E1DD270288B4E6030699E45FA1715D88E1DF1F24 && \
echo 'deb http://ppa.launchpad.net/git-core/ppa/ubuntu trusty main' > /etc/apt/sources.list.d/git.list && \
exi && \
su servid 
```

### as servid install sdkman, nvm, node, adopt openjdk 8,11,16, maven, gradle on the lxd cotnainer install dependencies and a service user

```bash
cd ~ && \
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash && \
export NVM_DIR="$HOME/.nvm" && \
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" && \
nvm install --lts && \
curl -s "https://get.sdkman.io" | bash && \
sed -i 's/sdkman_auto_answer=false/sdkman_auto_answer=true/g' ~/.sdkman/etc/config && \
sed -i 's/sdkman_auto_selfupdate=false/sdkman_auto_selfupdate=true/g' ~/.sdkman/etc/config && \
source $HOME/.sdkman/bin/sdkman-init.sh && \
sdk install java 16.0.1.j9-adpt && \
sdk install java 11.0.11.j9-adpt && \
sdk install java 8.0.292.j9-adpt && \ 
sdk install maven 3.8.1 && \
sdk install gradle 7.1 && \
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc && \
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc && \
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc && \
source ~/.bashrc && \
gem install jekyll bundler && \
npm -v && sdk current && \
echo "done with all installs"
```

### back on the lxd host create an alias to log into the lxd container as a non root service id

```bash
lxc alias add shell "exec @ARGS@ -- su -l servid"
```

### log into lxd dev container as a non root id

```bash
lxc shell devserver
```

### lxc script

```bash
#!/bin/bash

# variables
CONTAINER=mycontainer
IMAGE=ubuntu-daily:xenial
PORT=8080
PROFILES=default
FOLDER=app
REPO=https://github.com/CalebEverett/hello-lxd.git
RUN_USER=app
RUN_USER_UID=1444
CONTAINER_ROOT_UID=$(cat /etc/subgid | grep lxd | cut -d : -f 2)

function wait_bar () {
  for i in {1..10}
  do
    printf '= %.0s' {1..$i}
    sleep $1s
  done
}

# create the container if it doesn't exist
if [ ! -e /var/lib/lxd/containers/$CONTAINER ]
  then
    lxc launch --verbose $IMAGE $CONTAINER
    wait_bar 0.5
    echo container $CONTAINER started
  else
    echo container $CONTAINER already created
fi

# apply profiles
lxc profile apply $CONTAINER $PROFILES

# delete ubuntu user
if [ ! -z $(lxc exec $CONTAINER -- getent passwd | grep ubuntu) ]
then
  lxc exec $CONTAINER -- userdel -r ubuntu
fi

# create running user
if [ -z $(lxc exec $CONTAINER -- getent passwd | grep $RUN_USER) ]
then
  lxc exec $CONTAINER -- useradd -u $RUN_USER_UID -s /usr/sbin/nologin $RUN_USER
fi

#install node
if [ -z $(lxc exec $CONTAINER -- which node) ]
then
  printf "\n\n*** Installing node ***"
  lxc exec $CONTAINER -- /bin/bash -c 'curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -'
  lxc exec $CONTAINER -- apt-get install -y nodejs
  echo Node $(lxc exec $CONTAINER -- node -v) installed
else
  echo Node $(lxc exec $CONTAINER -- node -v) already installed
fi

#install git
if [ -z $(lxc exec $CONTAINER -- which git) ]
then
  printf "\n\n*** Installing git ***"
  lxc exec $CONTAINER -- apt-get install -y git
  echo $(lxc exec $CONTAINER -- git --version) installed
else
  echo $(lxc exec $CONTAINER -- git --version) already installed
fi

# redirect 80 to $PORT
if [[ -z $(lxc exec $CONTAINER -- cat /etc/ufw/before.rules | grep PREROUTING) ]]
then
  lxc exec $CONTAINER -- /bin/bash -c "sed -i '/#   ufw-before-forward/ a\
#\n\
# redirect 80 to $PORT\n\
*nat\n\
:PREROUTING ACCEPT [0:0]\n\
-A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port $PORT\n\
COMMIT' /etc/ufw/before.rules"
  lxc exec $CONTAINER -- ufw enable
  lxc exec $CONTAINER -- ufw allow $PORT/tcp
fi

#mount $FOLDER directory if developing
if [[ $FOLDER &&  $PROFILES == *"default"* ]]
then
  printf "\n\n*** Mounting shared folder ***\n"
  if [ ! -d ./$FOLDER ]; then mkdir ./$FOLDER; fi
  if [[ -z $(lxc config device list $CONTAINER | grep $FOLDER) ]]
  then
    lxc config device add $CONTAINER $FOLDER disk path=/usr/src/$FOLDER source=$(pwd)/$FOLDER
    sudo chown -R $((CONTAINER_ROOT_UID + RUN_USER_UID)):$((CONTAINER_ROOT_UID + $RUN_USER_UID)) ./$FOLDER
    sudo setfacl -R -m d:u:$USER:xwr,u:$USER:xwr,d:g:$USER:xwr,g:$USER:xwr ./$FOLDER
    sudo chown -R $((CONTAINER_ROOT_UID + RUN_USER_UID)):$((CONTAINER_ROOT_UID + $RUN_USER_UID)) ./$FOLDER
    echo $(pwd)/$FOLDER mounted at /usr/src/$FOLDER
  else
    echo Directory $(pwd)/$FOLDER already mounted
  fi
fi

#clone repo and install modules
if [ $REPO ]
  then 
  if [[ -z $(lxc exec $CONTAINER -- cat /usr/src/$FOLDER/package.json) ]]
    then
      lxc exec $CONTAINER -- git clone -q $REPO /usr/src/$FOLDER
      lxc exec $CONTAINER --env HOME=/usr/src/$FOLDER -- npm install
      lxc exec $CONTAINER -- chown -R $RUN_USER:$RUN_USER /usr/src/$FOLDER/node_modules
    fi
fi

# build and run as a service if production
if [[ $PROFILES == *"pro"* ]]
then  
  if [[ $(lxc exec $CONTAINER -- /bin/bash -c 'if [ ! -f /etc/systemd/system/$CONTAINER.service ]; then echo 0; fi') ]]
  then
    printf "\n\n*** Creating service file ***"
    lxc exec $CONTAINER -- /bin/bash -c "cat <<-EOF > /etc/systemd/system/$CONTAINER.service
    [Unit]
    Description=$CONTAINER
    [Service]
    WorkingDirectory=/usr/src/$FOLDER
    ExecStart=/usr/bin/node /usr/src/$FOLDER/index.js
    Restart=always
    RestartSec=10
    StandardOutput=syslog
    StandardError=syslog
    SyslogIdentifier=$CONTAINER
    User=$RUN_USER
    Environment=HOME=/usr/src/$FOLDER
    Environment=NODE_ENV=production
    Environment=PORT=$PORT
    
    [Install]
    WantedBy=multi-user.target
EOF"
    lxc exec $CONTAINER -- systemctl enable $CONTAINER.service
    sleep 3.0s
    lxc exec $CONTAINER -- systemctl start $CONTAINER.service
  fi
fi

printf "\n" && lxc list $CONTAINER

# start app for dev
if [[ $PROFILES == *"default"* && -z $(lxc exec $CONTAINER -- ps aux | grep /usr/src/$FOLDER/index.js) ]]
then
  google-chrome $(lxc exec $CONTAINER -- bash -c "ifconfig | grep -o '[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}' | head -n 1")
  lxc exec $CONTAINER --env HOME=/usr/src/$FOLDER --env PORT=$PORT -- node index.js
fi
````











####script

```bash
#!/usr/bin/sh
#######################################################################################################################################
########################################################################################################################################
#                                                              install_nvm
########################################################################################################################################
########################################################################################################################################
install_nvm(){
  cd ~ 
  [ $? -ne 0 ] && echo "error cd to ~": return 1

  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
  [ $? -ne 0 ] && echo "error with install nvm": return 1

  export NVM_DIR="$HOME/.nvm"

  [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
  [ $? -ne 0 ] && echo "error sourcing $NVM_DIR/nvm.sh"; return 1

  [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
  [ $? -ne 0 ] && echo "error sourcing $NVM_DIR/bash_completion"; return 1

  return 0
}

#######################################################################################################################################
########################################################################################################################################
#                                                              install_node_lts 
########################################################################################################################################
########################################################################################################################################
install_node_lts(){

    nvm --version >/dev/null 2>&1
    if [ $? -ne 0 ];then
            install_nvm
            if [ $? -ne 0 ];then
                echo "error install nvm"
                return 1
            fi
    fi

    nvm install --lts
    [ $? -ne 0 ] && echo "error installing node.js"; return 1
    return 0
}

#######################################################################################################################################
########################################################################################################################################
#                                                              install_sdkman 
########################################################################################################################################
########################################################################################################################################
install_sdkman() {
  curl -s "https://get.sdkman.io" | bash
  [ $? -ne 0 ] && echo "error installing sdkman via curl"; return 1

  sed -i 's/sdkman_auto_answer=false/sdkman_auto_answer=true/g' ~/.sdkman/etc/config
  [ $? -ne 0 ] && echo "error updating sdkman"; return 1

  sed -i 's/sdkman_auto_selfupdate=false/sdkman_auto_selfupdate=true/g' ~/.sdkman/etc/config
  [ $? -ne 0 ] && "error updating sdkman"; return 1

  source $HOME/.sdkman/bin/sdkman-init.sh
  [ $? -ne 0 ] && "error initializing sdkman" return 1
  return 0
}

#######################################################################################################################################
########################################################################################################################################
#                                                              install_with_sdkman 
########################################################################################################################################
########################################################################################################################################
install_with_sdkman(){
    [ -z "$1" ] && echo "tool type not specified"; return 1
    [ -z "$2" ] && echo "tool version not specified"; return 1

    sdk --version >/dev/null 2>&1
    if [ $? -ne 0 ];then
        install_sdkman
        if [ $? -ne 0 ];then
            echo "error install $1"
            return 1
        fi
    fi

    sdk install $1 $2 && echo "unable to install $1 $2"; return 1
    return 0
}

#######################################################################################################################################
########################################################################################################################################
#                                                              install_tool 
########################################################################################################################################
########################################################################################################################################
install_tool(){
    [ -z "$1" ] && echo "tool type not specified"; return 1
    
    if [ $1 == "node" ]
    then
        install_node_lts
        [[ $? -ne 0 ] && "error initializing sdkman" return 1  
    elif [ $1 == "java" || $1 == "maven"  || $1 == "gradle" ]]
    then
        [ -z "$2" ] && echo "tool version not specified"; return 1
        install_with_sdkman $1 $2
        [ $? -ne 0 ] && "error initializing sdkman"; return 1
    else
       echo "error supplying parameters"
       return 1
    fi

    return 0
}

#######################################################################################################################################
########################################################################################################################################
#                                                              MAIN 
########################################################################################################################################
########################################################################################################################################
while getopts "i:" opt; do
    case $opt in
        i ) set -f # disable glob
            IFS=' ' # split on space characters
            array=$OPTARG
            install_tool array[0] array[1]
            if [ $? -ne 0 ];then
                exit 0
            fi ;; # use the split+glob operator
        * ) exit 1
    esac
done

install_tool array[0] array[1]
if [ $? -eq 0 ];then
    exit 0
else {
    exit 1
}

#install_tools.sh -i node -i java 16.0.1.j9-adpt -i java 11.0.11.j9-adpt -i java 8.0.292.j9-adpt -i  maven 3.8.1 -i gradle
```