# virt-install to create VM's

## 

```bash
# create a storage pool
root@dlp:~# mkdir -p /var/kvm/images
root@dlp:~# virt-install \
--name ubuntu2004 \
--ram 4096 \
--disk path=/var/kvm/images/ubuntu2004.img,size=20 \
--vcpus 2 \
--os-type linux \
--os-variant ubuntu20.04 \
--network bridge=br0 \
--graphics none \
--console pty,target_type=serial \
--location 'http://mirrors.seas.harvard.edu/linuxmint/stable/20.1/linuxmint-20.1-cinnamon-64bit.iso' \
--extra-args 'console=ttyS0,115200n8 serial'
Starting install...     # installation starts

# after finishing installation, back to KVM host and shutdown the guest like follows
root@dlp:~# virsh shutdown ubuntu2004
Domain template is being shutdown
# mount guest's disk and enable a service like follows
root@dlp:~# guestmount -d ubuntu2004 -i /mnt
root@dlp:~# ln -s /mnt/lib/systemd/system/getty@.service /mnt/etc/systemd/system/getty.target.wants/getty@ttyS0.service
root@dlp:~# umount /mnt
# start guest again, if it's possible to connect to the guest's console, it's OK all
root@dlp:~# virsh start ubuntu2004 --console

Ubuntu 20.04 LTS ubuntu ttyS0

ubuntu login:


root@dlp:~# virsh console ubuntu2004

    irt-clone --original ubuntu2004 --name ubuntu2004_org --file /var/kvm/images/ubuntu2004_org.img
```

## 

