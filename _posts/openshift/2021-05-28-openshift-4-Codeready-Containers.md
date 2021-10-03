---
layout: post
published: true
title: Openshift 4 Codeready containers Windows laptop
date: 2019-09-24 03:28
category: openshift
author:  "datahawklab"
tags: [openshift,minishift,codeready containers]
summary: Openshift 4.* on your Windows 10 pro machine
---
## Openshift on your Windows 10 pro machine

Make sure your machine is multi core and has at last 16gb of ram.
Windows 10 PRO is a requirement.

### Prepare your machine
Prepare your laptop/desktop for Openshift.   

* Connect to an Ethernet connection and disable your wireless network card   
see ***Disable wireless network adapter*** below

* Turn on Hypervisor
	* control panel -> Programs -> Turn Windows features on or off   
	* Enable all Hyper-v options.   
	![image](https://user-images.githubusercontent.com/10190444/65516620-50ef1680-deaf-11e9-8922-9eba64bf4923.png)
   
* Add your id to the HyperVisor Admnistrators Group via PowerShell
     * run powershell as admninistrator   
     ![image](https://user-images.githubusercontent.com/10190444/65521387-2739ed80-deb7-11e9-8a51-e5756e708d96.png)        
     * Via Powershell add yourself to the Hyperv administrators group:   
     ```powershell
     ([adsi]"WinNT://./Hyper-V Administrators,group").Add("WinNT://$env:UserDomain/$env:Username,user")
     ```   
    
### Get Codeready containers and pull secret
*** This requies a free Redhat account, signup for one if you dont already have one.  
*** Make sure you copy the pull secret as well.   
[Codeready Containers](https://cloud.redhat.com/openshift/install/crc/installer-provisioned?intcmp=7013a000002CtetAAC)    
   
### Set a local path to the location of crc.exe
![set local path](https://user-images.githubusercontent.com/10190444/65509159-d23ead00-de9f-11e9-924e-0387be562ac3.png)
   
### Start Openshift
open a cmd prompt and setup your environment 
```bash
crc setup
```   
Start Openshift
```bash
crc start
#paste the pull secret when requested
```   
Note your login info:
```bash
INFO To access the cluster, first set up your environment by following 'crc oc-env' instructions
INFO Then you can access it by running 'oc login -u developer -p developer https://api.crc.testing:6443'
INFO To login as an admin, username is 'kubeadmin' and password is BMLkR-NjA28-v7exC-8bwAk
INFO
INFO These credentials can also be used to access the OpenShift web console at https://console-openshift-console.apps-cr
c.testing
```   

### Go to the Openshift console
[https://console-openshift-console.apps-crc.testing](https://console-openshift-console.apps-crc.testing)
   
### Use the Openshift OC command line interface
* In a cmd prompt set your environment   
```powershell
crc oc-env
```   
   
* Run the command on the last line   
```
@FOR /f "tokens=*" %i IN ('crc oc-env') DO @call %i
```   
   
* login to Openshift with the default developer id and password
```powershell
oc login -u developer -p developer https://api.crc.testing:6443
```   
 
### Login as Kubeadmin
* find your openshift kubeadmin password
In a cmd prompt run:
```bat
type %userprofile%\.crc\cache\crc_hyperv_4.1.14\kubeadmin-password
```   
   
* Login as kubeadmin to Openshfit
Set your env as noted above if you havent done so already.
use the password as noted above
```powershell
oc login -u kubeadmin -p BMLkR-NjA28-v7exC-8bwAk https://api.crc.testing:6443
```   
   
In Windows explorer navigate to %
# See below if you get any errors

### Disable wireless network adapter
I have had issues where the Hyper-v virtual switch doesnt work if it uses the wirless adapter for the virtual ethernet card.   
I have had to disable the wireless adapter so that Hyper-v chooses the Ethernet adapter for Default Switch.      
* Go to Control Panel --> Network and Internet --> Network Connections   
![image](https://user-images.githubusercontent.com/10190444/65515072-94945100-deac-11e9-80d3-efdb4a4a73d2.png)   

* Open the Hyper-v manager and verify that the Default switch is using the Ethernet adapter   
![image](https://user-images.githubusercontent.com/10190444/65515299-0a98b800-dead-11e9-857f-6e95eabfc7c3.png)   

### Fix windows 10 Pro bug if you get an error on crc start   
This is related to a bug with how Hyper-v on windows handles DHCP for virtual machines.   
```bash
ERRO Error occurred: Error approving the node csr Not able to get csr names (exit status 1 : Unable to connect to the se
rver: dial tcp 172.18.7.77:6443: connectex: A connection attempt failed because the connected party did not properly res
pond after a period of time, or established connection failed because connected host has failed to respond.
```  
   
* Open a Powershell cmd prompt with admin priviliges
![image](https://user-images.githubusercontent.com/10190444/65512344-05386f00-dea7-11e9-9e92-6b69f02376d6.png)   

* Get the IP address of your Openshift Hyper-v VM   
```get-vm -Name crc | Select -ExpandProperty Networkadapters```

* Open Notepad with admin priviliges
![image](https://user-images.githubusercontent.com/10190444/65511982-60b62d00-dea6-11e9-8770-d569f8c7c30f.png)   

* Open the hosts file in C:\Windows\System32\drivers\etc with ip address entries for your Openshift VM
```bash
# replace you VM ip address with the address below and save the file
172.18.7.77  api.crc.testing
172.18.7.77  oauth-openshift.apps-crc.testing
172.18.7.77  console-openshift-console.apps-crc.testing
```   

* Start Codeready containers again and navigate to the Openshift web console
```crc start```   
[https://console-openshift-console.apps-crc.testing](https://console-openshift-console.apps-crc.testing)

## Openshift 3.* on your Windows 10 pro machine

Windows 10 pro laptopPre-requisites:
1. Windows 10 Pro
2. 16GB of RAM
3. SSD hard drive
4. multi-core processor (8 preferrable but not necessdary)

High Level oveview:
1. Verify your running Windows 10 PRO or Upgrade windows 10 Home to windows 10 PRO:
    * search for `System` and in the search window.            
      ![System Information](../img/system-information-search.png)

   ![Windows 10 Pro Verification](../img/windows_10_pro_verification.png)

2. Install and configure HyperV  on Windows 10 Pro
    * Turn on HyperV on Windows 10 Pro
      `control panel --> Programs --> Turn Windows Features on or off`
      ![turn on hyperV](../img/hyperv_features_turn_on.png)

    * add your id to the HyperVisor Admnistrators Group via PowerShell:
        * run powershell as admninistrator   
          ![Run Powershell as administrator](../img/powershell-run-as-administrator.png)
        * Via Powershell add yourself to the Hyperv administrators group:
      ```
      ([adsi]”WinNT://./Hyper-V Administrators,group”).Add(“WinNT://$env:UserDomain/$env:Username,user”)
      ```   

    * create external virtual switch via HypeVadmin on your Ethernet adapter NOT wireless adapter.
      ***You may have to apply the following fix if the Virtual Switch creation errors out: [Virtual Switch Creation Error fix](https://support.microsoft.com/en-us/help/3101106/you-cannot-create-a-hyper-v-virtual-switch-on-64-bit-versions-of-windo) ***

      ![HyperV Admin](../img/hyperv-manager-add-to-start-and-taskbar.png)

      ![HyperV Admin select virtual switch](../img/hypervmanager-select-virtual-switch-manager.png)

      ![HyperV Admin create external switch](../img/hypervmanager-create-external-switch.png)

      ***make sure to add the virtual switch against your Eternet network adapter and not your wifi adapter***    
      ![HyperV Admin create external switch ethernet](../hyperv-manager-external-switch-wired-nic.png)

3. Download and start minishift with the following switches
    * from where you downloaded and extract minishift
    * start a powershell command window as administrator
      ![Run Powershell as administrator](../img/powershell-run-as-administrator.png)
    * cd to where you downloaded and extracted minishift
   ```powershell
   c:\usr\minishift\minishift.exe start --hyperv-virtual-switch "minishiftvswitch" --memory 8GB
   ```      

output:
```powershell
-- Starting profile 'minishift'
,,,,,,,
Login to server ...
Creating initial project "myproject" ...
Server Information ...
OpenShift server started.

The server is accessible via web console at:
    https://192.168.1.215:8443/console

You are logged in as:
    User:     developer
    Password: <any value>

To login as administrator:
    oc login -u system:admin


-- Exporting of OpenShift images is occuring in background process with pid 10376.
PS C:\Windows\system32>
```
Base image creation
====================
**Create a build config for  binary docker build**
Creates build config objects. This sets up a docker build in openshift that pushes to artifactory using the openjdk image as a base.
instructions on sourcing the oc binary [oc-binary-source.md](oc-binary-source.md)
```bash
oc new-build --name=base-mule-image-build --binary=true --to=openshiftdockerregistry.local.com/base-mule-image:latest --to-docker=true --strategy=docker --image-stream=bac/openjdk18:latest
```
**clone the base build repo**
```bash
git clone ssh://****.git
```
**cd to the cloned project folder and do a docker build and push base image to repo**
```bash
cd $proj_folder
oc start-build base-mule-image-build --from-dir=.
```

App base image layered on binary base image
====================
This sets up a docker build in openshift, which uses the previously created mule base image as the base layer.
It pushes the final image to artifactory
**create App image build config**
```
oc new-build --name=app-mule-image-build --binary=true --to=openshiftdockerregistry.local.com/app-mule-image:latest --to-docker=true --strategy=docker --docker-image=openshiftdockerregistry.local.com/base-mule-image:latest
```
**Starts the build for the app image.  Run this in the directory with the application docker file and the application jar**
```
oc start-build app-mule-image-build --from-dir=.
```

**Create a build config for  binary docker build**
Creates build config objects. This sets up a docker build in openshift that pushes to artifactory using the openjdk image as a base.
instructions on sourcing the oc binary [oc-binary-source.md](oc-binary-source.md)

```bash
oc new-build --name=base-mule-image-build --binary=true --to=openshiftdockerregistry.local.com/base-mule-image:latest --to-docker=true --strategy=docker --image-stream=bac/openjdk18:latest
```

## clone the base build repo**

```bash
git clone ssh://****.git
```

## cd to the cloned project folder and do a docker build and push base image to repo

```bash
cd $proj_folder
oc start-build base-mule-image-build --from-dir=.
```

## App base image layered on binary base image

====================
This sets up a docker build in openshift, which uses the previously created mule base image as the base layer.
It pushes the final image to artifactory
**create App image build config**

```bash
oc new-build --name=app-mule-image-build --binary=true --to=openshiftdockerregistry.local.com/app-mule-image:latest --to-docker=true --strategy=docker --docker-image=openshiftdockerregistry.local.com/base-mule-image:latest
```