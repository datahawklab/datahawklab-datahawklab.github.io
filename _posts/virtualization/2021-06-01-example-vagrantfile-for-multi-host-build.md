---
layout:     post
title:      "vagrant info"
subtitle:   " \"ansible ingo\""
date:       2021-05-29 16:00:00
author:  "datahawklab"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - deploy
    - openshift
---




# Example vagrantfile for multi host build



```bash
cat Vagrantfile
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
# Define VMs with static private IP addresses, vcpu, memory and vagrant-box.
  boxes = [
    {
      :name => "web1.demo.com", ⇒ Host1 this is one of the target nodes
      :box => "centos/8",             ⇒ OS version
      :ram => 1024,                   ⇒ Allocated memory
      :vcpu => 1,               ⇒  Allocated CPU
      :ip => "192.168.29.2"     ⇒ Allocated IP address of the node
    },
    {
      :name => "web2.demo.com", ⇒ Host2 this is one of the target nodes
      :box => "centos/8",
      :ram => 1024,
      :vcpu => 1,
      :ip => "192.168.29.3"
    },
    {
      :name => "ansible-host", ⇒ Ansible Host with Ansible Engine
      :box => "centos/8",
      :ram => 2048,
      :vcpu => 1,
      :ip => "192.168.29.4"
    }
  ]

  # Provision each of the VMs.
  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
#   Only Enable this if you are connecting to Proxy server
#      config.proxy.http    = "http://usernam:password@x.y:80"⇒ Needed if you have a proxy
#      config.proxy.https   = "http://usernam:password@x.y:80"
#      config.proxy.no_proxy = "localhost,127.0.0.1"
      config.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true
      config.ssh.insert_key = false
      config.vm.box = opts[:box]
      config.vm.hostname = opts[:name]
      config.vm.provider :virtualbox do |v| ⇒  Defines the vagrant provider
        v.memory = opts[:ram]
        v.cpus = opts[:vcpu]
      end
      config.vm.network :private_network, ip: opts[:ip]
      config.vm.provision :file do |file|
         file.source     = './keys/vagrant' ⇒ vagrant keys to allow access to the nodes
         file.destination    = '/tmp/vagrant' ⇒ the location to copy the vagrant key
      end
      config.vm.provision :shell, path: "bootstrap-node.sh" ⇒ script that copy hosts entry
      config.vm.provision :ansible do |ansible| ⇒ declaration to run ansible playbook
        ansible.verbose = "v"
        ansible.playbook = "playbook.yml" ⇒ the playbook used to configure the hosts
      end
        end
  en
```
