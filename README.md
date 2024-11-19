# Live Kernel Updates
The idea of Live Kernel updates is to reduce system downtime by allowing use of the system even while the kernel is updating and without having to restart the system  
which can be costly and potentially take hours.  
Ideally, with this approach, the concept is simple:  

Firstly, backup old data. Then, move the session into swap space and RAM, then update the kernel files and finally restore user data.  
How this is to be achieved is up to you (the reader), and I make no promises that this bash script will work "outside the box" without a little bit of jiggery-pokery.  

## WARNING  
### This bash script may be incomplete and not final, and could damage your system!  
### DO NOT test this unless doing so in a Virtual Machine, so as not to wreck your system!

### IT IS RECOMMENDED TO RUN THE SCRIPT AS ROOT.

## GIVEN   
16 GB RAM  
512 GB SSD

## PARTITIONING SCHEME  
### Boot Partition
/dev/sda - /boot/efi - 256 MB
### Root Partition
/dev/sdb - / - 8 GB
### Home Partition
/dev/sdc - /home 256 GB
### Swap Space
/dev/sdd - /swap - 32 GB
### Recovery Partition
/dev/sde - /recovery - 16 GB
### Free Space
Free Space: ~200 GB  

## PSEUDO-SCRIPT (ASSUMING HOME IS MOUNTED IN ROOT)  

### Login as root user  
su root  

### Make a temp directory to store new kernel package in swap:  
mkdir /swap/tmp  

### Retrieve new Kernel and store in /swap/tmp:  
wget ***[link-to-kernel-image]*** /swap/tmp  

### Backup User Password and Group Data:
rsync /etc/passwd /recovery/etc/passwd  
rsync /etc/shadow /recovery/etc/shadow  
rsync /etc/group /recovery/etc/group  
rsync /etc/gshadow /recovery/etc/gshadow

### Change directory to swap partition:  
cd /swap  

### Unmount home from root  
umount /home  

### Copy Bash to swap (so as to ensure that we still have an execution environment):  
cp /etc/bash /swap  

### Temporarily update PATH Variable to include new bash location:   
export PATH=$PATH: /swap/etc/bash

### Move old kernel to swap partition:  
mv / /swap  

### Depackage the new kernel into the old root partition:  
sudo dpkg /swap/tmp/***[kernel-image-file]*** / 

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
su root
mkdir /swap/tmp
wget [link-to-kernel-image] /swap/tmp
rsync /etc/passwd /recovery/etc/passwd  
rsync /etc/shadow /recovery/etc/shadow  
rsync /etc/group /recovery/etc/group  
rsync /etc/gshadow /recovery/etc/gshadow
cd /swap
umount /home 
cp /etc/bash /swap  
export PATH=$PATH:/swap/etc/bash  
mv / /swap  
sudo dpkg /swap/tmp/[kernel-image-file] /
mount /dev/sdc /home
rsync /recovery/etc/passwd /etc/passwd  
rsync /recovery/etc/shadow /etc/shadow  
rsync /recovery/etc/group /etc/group  
rsync /recovery/etc/gshadow /etc/gshadow 
free /swap
logout
```
