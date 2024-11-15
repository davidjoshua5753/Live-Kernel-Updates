# Live Kernel Updates
The idea of Live Kernel updates is to reduce system downtime, which can be costly and potentially take hours.  
Ideally, with this approach, the concept is simple:  

Firstly, backup old data. Then, move the session into swap space and RAM, then update the kernel files and finally restore user data.  
How this is to be achieved is up to you (the reader), and I make no promises that this bash script will work "outside the box" without a little bit of jiggery-pokery.  

## WARNING  
### This bash script may be incomplete and not final, and could damage your system!  
### DO NOT test this unless doing so in a Virtual Machine, so as not to wreck your system!

## GIVEN: 
16 GB RAM  
512 GB SSD

## PARTITIONING SCHEME
/dev/sda - /boot/efi - 256 MB  
/dev/sdb - /root - 8 GB  
/dev/sdc - /home 256 GB  
/dev/sdd - /swap - 32 GB  
/dev/sde - /recovery - 16 GB  
Free Space: ~200 GB  

## PSEUDO-SCRIPT (ASSUMING HOME IS MOUNTED IN ROOT)
### Make a temp directory to store new kernel package in swap:  
mkdir /swap/tmp  

### Retrieve new Kernel and store in /swap/tmp:  
wget ***[link-to-kernel-image]*** /swap/tmp  

### Backup User Password and Group Data:
rsync /etc/passwd /recovery/etc/passwd  
rsync /etc/shadow /recovery/etc/shadow  
rsync /etc/group /recovery/etc/group  
rsync /etc/gshadow /recovery/etc/gshadow

### Unmount home from root  
umount /dev/sdc

### Change directory to swap partition:  
cd /swap  

### Copy Bash to swap (so as to ensure that we still have an execution environment):  
cp /etc/bash /swap  

### Temporarily update PATH Variable to include new bash location:   
export PATH=$PATH: /swap/etc/bash

### Move old kernel to swap partition:  
mv /root /swap  

### Depackage the new kernel into the old root partition:  
sudo dpkg /swap/tmp/***[kernel-image-file]*** /root 

### Remount Home Directory in root  
mount /dev/sdc /home

### Restore User Password and Group Data:
rsync /recovery/etc/passwd /etc/passwd  
rsync /recovery/etc/shadow /etc/shadow  
rsync /recovery/etc/group /etc/group  
rsync /recovery/etc/gshadow /etc/gshadow  

### Free the memory in Swap:  
free /swap  

### Logout of session to confirm changes  
logout   

```
$ mkdir /swap/tmp
$ wget [link-to-kernel-image] /swap/tmp
$ rsync /etc/passwd /recovery/etc/passwd  
$ rsync /etc/shadow /recovery/etc/shadow  
$ rsync /etc/group /recovery/etc/group  
$ rsync /etc/gshadow /recovery/etc/gshadow
$ umount /dev/sdc
$ cd /swap  
$ cp /etc/bash /swap  
$ export PATH=$PATH:/swap/etc/bash  
$ mv /root /swap  
$ sudo dpkg /swap/tmp/[kernel-image-file] /root
$ mount /dev/sdc /home
$ rsync /recovery/etc/passwd /etc/passwd  
$ rsync /recovery/etc/shadow /etc/shadow  
$ rsync /recovery/etc/group /etc/group  
$ rsync /recovery/etc/gshadow /etc/gshadow 
$ free /swap
$ logout
```
