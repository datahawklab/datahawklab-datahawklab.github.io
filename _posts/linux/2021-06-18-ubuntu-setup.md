---
layout: post
published: true
title: setup ubuntu 20.04 curl, node.js, java, ruby, jekyll, nvm, sdkman
date: 2021-06-18 03:28:00
category: notes
author:  "datahawklab"
tags: [ node.js, java, ruby, jekyll, mvm, sdkman ]
summary: setup ubuntu 20.04 curl, node.js, java, ruby, jekyll, mvm, sdkman
---

### install Node Npm Java Maven Ruby Jekyll   

```bash
sudo apt update -y && \
sudo snap remove curl
sudo apt-get install curl
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
curl -s "https://get.sdkman.io" | bash 
nvm install --lts && \
sdk install java 16.0.1.j9-adpt &&\ \
sdk install maven 3.6.3 && \
## https://jekyllrb.com/docs/installation/ubuntu/
sudo apt-get install ruby-full build-essential zlib1g-dev && \
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc && \
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc && \
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc && \
source ~/.bashrc && \
gem install jekyll bundler && \
```

### setup sshkeys for multiple github accounts

```bash
ssh-keygen -t rsa -b 4096 -C "servid.servid@datahawklab.com" -f $HOME/.ssh/id_rsa_datahawklab
ssh-keygen -t rsa -b 4096 -C "servid.servid@gmail.com" -f $HOME/.ssh/id_rsa_servidc
```
### in file ~/.ssh/config  

```bash
Host datahawk-lab
  HostName github.com
  User git
  IdentityFile ~/.ssh/d_rsa_datahawklab
 
Host servidc
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_servidc
```

### ruby,jekyll, gitpages site

```bash
sudo apt-get install ruby-full build-essential zlib1g-dev && \
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc && \
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc && \
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc && \
source ~/.bashrc && \
gem install jekyll bundler && \
git clone https://github.com/datahawk-lab/datahawk-lab.github.io.git && \
mv datahawk-lab.github.io $YOURPROJNAME && \
cd $YOUPROJNAME && \
bundle install && \
bundle exec jekyll serve
```


### install Node Npm Java Maven Ruby Jekyll   

```bash
sudo apt update -y && \
sudo snap remove curl
sudo apt-get install curl
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
curl -s "https://get.sdkman.io" | bash 
nvm install --lts && \
sdk install java 16.0.1.j9-adpt &&\ \
sdk install maven 3.6.3 && \
## https://jekyllrb.com/docs/installation/ubuntu/
sudo apt-get install ruby-full build-essential zlib1g-dev && \
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc && \
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc && \
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc && \
source ~/.bashrc && \
gem install jekyll bundler && \
```



### configure Git client


```bash
git config --global user.email "servid.servid@gmail.com" ;\
git config --global user.name "servid servid"
```

### create public/private putty ssh keys and save to onedrive via rclone

```bash
sudo apt-get install putty &&\
sudo apt-get install puttygen &&\
sudo apt-get install xclip &&\
```

```bash
puttygen -t ed25519 -b 256 -C "servid.servid@gmail.com" -o datacrunch.ppk && puttygen -L  datacrunch.ppk > datacrunch.pub &&\
cat datacrunch.pub | xclip &&\
xclip -o &&\
rclone copy datacrunch.ppk onedrive:secure &&\
rclone copy datacrunch.pub onedrive:secure
```

### mount onedrive via rclone ubuntu 20.04

```bash
rclone --vfs-cache-mode writes mount onedrive: ~/OneDrive &
```

### install Chromium on Mint O.S 20.1

```bash
sudo apt update
sudo apt install chromium

sudo apt-get remove chromium-browser
sudo apt autoremove
```

### install chrome

```bash
wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add - && \
echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" | sudo tee /etc/apt/sources.list.d/google-chrome.list && \
sudo apt update && \
sudo apt install -y google-chrome-stable && \
echo "install chrome-stable"
```

### install cockpit Ubuntu distributions

```bash
sudo apt update
sudo apt install cockpit -y
sudo apt install cockpit-packagekit
sudo apt install cockpit-machines -y
sudo apt install cockpit-dashboard
```

### install SSH Ubuntu distributions

```bash

```

### SSHFS filesharing Ubuntu and openssh server

* install SSHFS
* create a local folder to mount
* mount the remote folder via SSHFS

```text
sudo apt install -y openssh-server sshfs

#mkdir localhost_folder
sshfs remote_host:/remote/folder $HOME/localhost_folder
```

### Generate public/private SSH key pairs

```bash
ssh-keygen -t rsa -b 4096 -C "servid.servid@datahawklab.com" -f /home/servidc/.ssh/id_rsa

chmod 700 /home/servidc/.ssh
chmod 644 /home/servidc/.ssh/*.pub
chmod 600 /home/servidc/authorized_keys
```

**copy $HOME/.ssh/id_rsa.pub to $HOME/.ssh/authorized_keys onto each server you would like to passwordless ssh into**

example

```bash
ssh user@host
echo $pub_key >> $HOME/.ssh/authorized_keys
```

### configure Openssh server for passwordless login

**setup each remote host you would like to passwordless ssh into**

```bash
sudo apt install -y openssh-server
```

as root

```bash
# vi /etc/ssh/sshd_config
```

uncomment

```bash
PubkeyAuthentication yes
```

uncomment and set to no

```
PasswordAuthentication no
```

restart sshd

```bash
sudo systemctl restart ssh
```


### setup auto-update and auto-upgrade on Ubuntu Server 20.04

* install:

```bash
sudo apt install unattended-upgrades
```

* open: `sudo nano /etc/apt/apt.conf.d/50unattended-upgrades`
* un-comment:

```bash
"${distro_id}:${distro_codename}-updates";
Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "02:38";
```

* open: `sudo nano /etc/apt/apt.conf.d/20auto-upgrades`
* add:

```bash
APT::Periodic::Update-Package-Lists "1";
 APT::Periodic::Download-Upgradeable-Packages "1";
 APT::Periodic::AutocleanInterval "7";
 APT::Periodic::Unattended-Upgrade "1";
check if it works:
sudo unattended-upgrades --dry-run --debug
```

### install barrier from source Ubuntu
**Keyboard and mouse sharing between Linux hosts Barrier**

```bash
sudo apt update && sudo apt upgrade ;\
sudo apt install git cmake make xorg-dev g++ libcurl4-openssl-dev \
                 libavahi-compat-libdnssd-dev libssl-dev libx11-dev \
                 libqt4-dev qtbase5-dev && \
git clone https://github.com/debauchee/barrier.git && \
cd barrier && \
git submodule update --init --recursive && \
./clean_build.sh && \
cd build && \
sudo make install
```

* will install in the following locations

```bash
-- Install configuration: "Debug"
-- Installing: /usr/local/share/icons/hicolor/scalable/apps/barrier.svg
-- Installing: /usr/local/share/applications/barrier.desktop
-- Installing: /usr/local/bin/barrierc
-- Installing: /usr/local/bin/barriers
-- Installing: /usr/local/bin/barrier
```

**Barrier server**

* this lets you run th server component \(the host that has the keyboard and mouse attached\), in the background

```bash
nohup /usr/local/bin/barriers -f --no-tray --debug INFO --name servidc-Aspire-E5-576 --enable-crypto -c /home/servidc/barrier.conf --address :24800 </dev/zero >/dev/null 2>&1 &
```

* ~/barrier.conf contains info describing the screen position and other info

```bash
section: screens
    servidc-Aspire-E5-576:
        halfDuplexCapsLock = false
        halfDuplexNumLock = false
        halfDuplexScrollLock = false
        xtestIsXineramaUnaware = false
        preserveFocus = false
        switchCorners = none
        switchCornerSize = 0
    servid-hp350g1:
        halfDuplexCapsLock = false
        halfDuplexNumLock = false
        halfDuplexScrollLock = false
        xtestIsXineramaUnaware = false
        preserveFocus = false
        switchCorners = none
        switchCornerSize = 0
end

section: aliases
end

section: links
    servidc-Aspire-E5-576:
        right = servid-hp350g1
    servid-hp350g1:
        left = servidc-Aspire-E5-576
end

section: options
    relativeMouseMoves = false
    screenSaverSync = true
    win32KeepForeground = false
    clipboardSharing = true
    switchCorners = none
    switchCornerSize = 0
end
```

* Files created for SSL authentication on Barrier server
* the local.txt key on the server matches the TrustedServers.txt entry on the client

```bash
cat /home/servidc/.local/share/barrier/SSL/Fingerprints/Local.txt

43:BD:9D:B7:2C:61:E9:FC:CA:03:2B:B2:43:BC:5C:0F:A3:E2:E9:6C
```

```bash
cat /home/servidc/.local/share/barrier/SSL/Barrier.pem

-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQCvW8hEHApPEM6B
tjZR5moUiUKAX/LVJeX35Tbabt3oBSmI4MnHtRdTYXiDrNRpmtkdcfQCMEErIFBj
ny6WljKNjeCbeOldPatIDNQgwxqJ6Av0drxnwTNBr3kd/LVslnFgRVOxU3DZEA28
cx9C3woZv02gMBynrhJF4gCp91ksNxAyGvxWg8GujgXa7pWD14yMMhovzjcqIxDs
....
-----END PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
MIIDBTCCAe2gAwIBAgIUMZhE4WE5wuDim9dsoFtsIabS2aEwDQYJKoZIhvcNAQEL
BQAwEjEQMA4GA1UEAwwHQmFycmllcjAeFw0yMTAyMjcyMzQ5MDhaFw0yMjAyMjcy
MzQ5MDhaMBIxEDAOBgNVBAMMB0JhcnJpZXIwggEiMA0GCSqGSIb3DQEBAQUAA4IB
DwAwggEKAoIBAQCvW8hEHApPEM6BtjZR5moUiUKAX/LVgfF4LP8Q8c0ZHVPBIjLW4Uu+37weWcFcMzH1+JXzI4r8XgAB6RqloiJY8ETkaL4d
kRFllnz6kbsI1IrEYYPhUUCs5DT2cQ9hJbokjBQhCPc/P0oRO4ibu7KlAnFk7wYU
ggSUoC+K9EAdYg9NJBS50sfZP4XYksyfh2PXfIGySLdr8OtHWiRNLeK1WlqiSGfi
```

**Barrier client**

this is the host wiht just a monitor attached, that you want to control

```bash
nohup /usr/local/bin/barrierc -f --no-tray --debug INFO --name servid-hp350g1 --enable-crypto 192.168.1.243:24800 </dev/zero >/dev/null 2>&1 &
```

* files created on the local client:

```bash
cat /home/servidc/.local/share/barrier/SSL/Fingerprints/Local.txt
8B:01:F0:FE:1C:D7:33:A2:63:A5:38:4B:51:50:DF:6B:C3:DB:1B:7
```

```bash
cat /home/servidc/.local/share/barrier/SSL/Fingerprints/TrustedServers.txt
43:BD:9D:B7:2C:61:E9:FC:CA:03:2B:B2:43:BC:5C:0F:A3:E2:E9:6C
```

```bash
cat /home/servidc/.local/share/barrier/SSL/Barrier.pem
-----BEGIN PRIVATE KEY-----
MIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQCiXQLyYiIQNBfh
Q7yvISYKO1W/6ZfQ+vXoegDzxlc4/yg1G4zL+UgXY2/Geyuj40GmyEqTRedOTK6K
zj/gQKayplRlwawMed7H7iOhtMNi9o3WrK7ygfF4LP8Q8c0ZHVPBIjLW4Uu+37we
WcFcMzH1+JXzI4r8XgAB6RqloiJY8ETkaL4dkRFllnz6kbsI1IrEYYPhUUCs5DT2
cQ9hJbokjBQhCPc/P0oRO4ibu7KlAnFk7wYUggSUoC+K9EAdYg9NJBS50sfZP4XY
ksyfh2PXfIGySLdr8OtHWiRNLeK1WlqiSGfi84OJZ6M2DvjavGeW4I+RR8Pc2TDb
....
-----END PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
MIIDBTCCAe2gAwIBAgIUQ2TvM/Z8XZu9yjFVo4099R0t4DIwDQYJKoZIhvcNAQEL
BQAwEjEQMA4GA1UEAwwHQmFycmllcjAeFw0yMTAyMjcyMzEwMTFaFw0yMjAyMjcy
MzEwMTFaMBIxEDAOBgNVBAMMB0JhcnJpZXIwggEiMA0GCSqGSIb3DQEBAQUAA4IB
DwAwggEKAoIBAQCiXQLyYiIQNBfhQ7yvISYKO1W/6ZfQ+vXoegDzxlc4/yg1G4zL
+UgXY2/Geyuj40GmyEqTRedOTK6Kzj/gQKayplRlwawMed7H7iOhtMNi9o3WrK7y
...
-----END CERTIFICATE-----
```

**Barrier Example: server and Client configuraiton**

```bash
#run on host that has the keyboard and mouse attached
nohup /usr/local/bin/barriers -f --no-tray --debug INFO --name servidc-Aspire-E5-576 --enable-crypto -c /home/servidc/barrier.conf --address :24800 </dev/zero >/dev/null 2>&1 &

#run on client that you want to share keyboard and mouse with
nohup /usr/local/bin/barrierc -f --no-tray --debug INFO --name servid-hp350g1 --enable-crypto servidc-Aspire-E5-576:24800 </dev/zero >/dev/null 2>&1 &
```

#### install oh my zsh Powerlevel19k Colorize with syntax higlighting on Ubuntu

```bash
sudo apt-get update && \
sudo apt install wget curl git -y && \
sudo apt-get install zsh -y &&  \
sudo chsh -s /usr/bin/zsh servidc && \
echo $SHELL && \
sudo apt install ruby ruby-dev ruby-colorize && \
sudo gem install colorls && \
sh -c "$(curl -fsSLk https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended && \
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting && \
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && \
wget https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/Bold/complete/Fira%20Code%20Bold%20Nerd%20Font%20Complete%20Mono.ttf -P ~/.local/share/fonts && \
wget https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/Light/complete/Fira%20Code%20Light%20Nerd%20Font%20Complete%20Mono.ttf -P ~/.local/share/fonts && \
wget https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/Medium/complete/Fira%20Code%20Medium%20Nerd%20Font%20Complete%20Mono.ttf -P ~/.local/share/fonts && \
wget https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/Regular/complete/Fira%20Code%20Regular%20Nerd%20Font%20Complete%20Mono.ttf -P ~/.local/share/fonts && \
wget https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/Retina/complete/Fira%20Code%20Retina%20Nerd%20Font%20Complete%20Mono.ttf -P ~/.local/share/fonts && \
wget https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/SemiBold/complete/Fira%20Code%20SemiBold%20Nerd%20Font%20Complete%20Mono.ttf -P ~/.local/share/fonts && \
git --version && \
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k && \
sed -i 's/ZSH_THEME="[^"]*/ZSH_THEME="powerlevel10k\/powerlevel10k/g' .zshrc && \
cat .zshrc | grep ZSH_THEME | grep "^[^#;]" &&  \
sed -i 's/plugins=.*/plugins=(git zsh-syntax-highlighting zsh-autosuggestions ansible common-aliases docker docker-compose fabric extract web-search yum git-extras docker vagrant genpass helm kubectl lxd mvn oc nvm npm npx pip pod ubuntu ufw systemd systemadmin postgres)/g' .zshrc && \
cat .zshrc | grep plugins | grep "^[^#;]" && \
source ~/.zshrc && \
alias lc='colorls -lA --sd' ;\
echo "done"
```

#### install custom alias oh my zsh

```text
cat >/home/servidc/.oh-my-zsh/custom/alias.zsh <<<"#
alias uuid_alias=\"cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 4 | head -n 1\"
alias date_alias=\"date '+%Y%m%d%H%M%S'\"
" && \
cat /home/servidc/.oh-my-zsh/custom/alias.zsh && \
echo "added aliases"
```

```bash
sudo apt-get purge \
virt-manager \
virt-viewer \
qemu-kvm \
libvirt-daemon-system \
libvirt-clients \
bridge-utils \
qemu \
libvirt-daemon-system \
libvirt-clients \
libxslt-dev \
libxml2-dev \
libvirt-dev \
zlib1g-dev \
ruby-dev \
ruby-libvirt \
ebtables \
dnsmasq-base
sudo apt autoremove
```

