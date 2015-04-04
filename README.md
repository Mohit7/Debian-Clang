# Bootable Clang-Built Debian
Adding this repository to host tasks to be performed before coding period:

####Task 1: The list of packages which must be fixed to reach the objective:

- I have studied dependency related flags in Debian packages, and I feel
  that we should consider all the packages which are marked with priority
  field : "required"

- If we go in more deep, out of all 62 required packages, 25 packages are
  "Essential" packages, and remaining required packages are dependencies
  (direct or transitive) of these "Essential" packages, which are known
  as pseudo-essential.

- And then to satisfy dependency of these "required" packages, we require:
  - insserv (dependency of sysvinit)
  - libsemanage1 (dependency of shadow)
  - libsemanage-common (dependency of libsemanage1)
  - ustr (dependency of  libsemanage-common)

- This makes the list to be considered for our minimal bootstrapable Debian.
  Which I have uploaded as [required.list](https://github.com/Mohit7/Debian-Clang/blob/master/required.list "required list").

- Now we need find which packages are failing with Clang. For now I have used data from [clang.debian.net](http://clang.debian.net "clang.debian.net"), and listed some failing packages 
  in [failing.list](https://github.com/Mohit7/Debian-Clang/blob/master/failing.list "required list").

####Task 2: A patch to fix a bug:

- I have tried to fix [Bug #777914](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=777914 "insserv bug") for insserv with patch [undeclare_identifier.patch](https://github.com/Mohit7/Debian-Clang/blob/master/161_bts777914_fix_undeclared_identifier.patch "insserv patch").

####Task 3: Steps to make a bootable Debian:
######Create root file system (rootfs) :
 - Using some partition tool, create a free partition.
 - create file system on this partition:
```sh
# mke2fs -j /dev/partitn_no
```
 - Now mount this partition:
```sh
# mkdir /mnt/newdeb
# mount /dev/partitn_no /mnt/newdeb
```

######Install base system:
- We can use debootstrap to create a base system.
```sh
# debootstrap wheezy /mnt/newdeb http://ftp.us.debian.org/debian
```
- We can select different target architecture then host using **--arch** flag, for example **--arch mipsel** will select mips little endian as target.
- If we use **--variant=minbase** option, it will provide base system with packages, which are almost close to those listed in [required.list](https://github.com/Mohit7/Debian-Clang/blob/master/required.list "required list"),, with just some extra packages(apt and its dependencies).

######Create /dev files:
- For futher installation we need to create some device files, as right now /dev contains very basic device files.
- For this 2 options are:
 - using --bind if host and target are same.
 - using makedev

######Insert mount information in /etc/fstab:
- In this step, we provide mount information for various partition we want to mount at booting like mount point, options etc.
- Basically this gives location where each partition should be mounted.
- For example:
```sh
# file system    mount point   type    options                  dump pass
/dev/XXX1         /             ext3    defaults                 0    1
/dev/XXX2         /boot         ext3    ro,nosuid,nodev          0    2
/dev/cdrom      /media/cdrom   iso9660 noauto,ro,user,exec       0    0
```
Shows that device /dev/cdrom having iso9660 type will mount at default mount point /media/cdrom with options no automatic mount, read-only, mountable by normal user, and allows executing binaries in it.

######Configure Network, Time zone, hostname, locales:
- chroot into sysem:
```sh
# chroot /mnt/newdeb /bin/bash
```
- Configure network by adding values for differrent interface cards in /etc/network/interfaces
- set timezone using tzdata:
```sh
# dpkg-reconfigure tzdata
```
- add hostname in /etc/hostname and /etc/hosts
- Install locales package and configure it:
```sh
# aptitude install locales
# dpkg-reconfigure locales
```

######Install kernel:
- To find available kernels:
```sh
# apt-cache search linux-image
```
- Now install appropriate one
```sh
# aptitude install linux-image-arch-etc
```

######Set up the Boot Loader:
- install grub2:
```sh
# aptitude install grub-pc
# grub-install /dev/hda
# update-grub
```
