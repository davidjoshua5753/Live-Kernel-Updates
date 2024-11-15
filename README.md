# Live Kernel Updates
The idea of Live Kernel updates is to reduce system downtime, which can be costly and potentially take hours.  

## WARNING  
### This bash script is incomplete and not final, and could damage your system!  
### Be mindful to not test this unless doing so in a Virtual Machine, so as not to wreck your system!

## GIVEN: 
16 GB RAM  
512 GB SSD

## PARTITIONING SCHEME
sda - /boot/efi - 256 MB  
sdb - /root - 256 GB  
sdc - /swap - 32 GB  
sde - /recovery - 16 GB  
Free Space: 208 GB  

## PSEUDO-SCRIPT
### Make a temp directory to store new kernel package in swap:  
mkdir /swap/tmp  

### Retrieve new Kernel and store in /swap/tmp:  
wget swift.kernel.org /swap/tmp  

### Backup User Profiles to recovery partition:  
rsync /root/users /recovery  

### Change directory to swap partition:  
cd /swap  

### Copy Bash to swap:  
cp /etc/bash /swap  

### Update Path Variable to include new bash location:   
export PATH=$PATH: /swap/etc/bash

### Move old kernel to swap partition:  
mv /root /swap  

### Depackage the new kernel into the old root partition:  
sudo dpkg /swap/tmp/swift.kernel.org /root  

### Restore user profiles to new kernel in place of old kernel:  
restore /recovery /root/users  

### Free the memory in Swap:  
free /swap    

```
$ mkdir /swap/tmp  
$ wget swift.kernel.org.latest /swap/tmp  
$ rsync /root/users /recovery  
$ cd /swap
$ cp /etc/bash /swap
$ export PATH=$PATH:/swap/etc/bash
$ mv /root /swap  
$ sudo dpkg /swap/tmp/swift.kernel.org.latest /root  
$ restore /recovery /root/users  
$ free /swap  
```
