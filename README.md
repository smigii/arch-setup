# Arch Install

## Check Internet

`ping google.com`

## Set clock

`timedatectl set-ntp true`

## List Attached Devices

`lsblk`

## Partition the drive

1. `cfdisk /dev/drive_to_partition`
    * This document will assume `sda`
2. ***BE CAREFUL, YOU WILL NUKE YOUR SHIT IF YOU CHOOSE THE WRONG ONE***
3. Select label
    * Drive size > 2TB ? GPT : DOS
5. Create partitions
    * Boot: 128M, primary, press b to set boot flag
    * Swap: 2G, primary, change type to linux swap
    * Root: Remaining, primary

## Format to ext4

`mkfs.ext4 /dev/sdaX` 

where X is one of the partition numbers. Do this for each partition.

## Setup Partitions

### Mount Root

`mount /dev/sdaX /mnt`

where X is partition number of root partition. `/mnt` is conventional mount location.

### Create Boot Directory

`mkdir /mnt/boot`

### Mount Swap Partition

`mkswap /dev/sdxy`

`swapon /dev/sdxy`

Where xy corresponds to swap device and partition.

### Mount Boot Partition

`mount /dev/sdaX /mnt/boot`

where X is partition number of boot partition.

## Let Pacstrap Install Everything

`pacstrap /mnt base base-devel linux linux-firmware vim`

## Generate fstab

`genfstab -U /mnt > /mnt/etc/fstab`

-U means fstab will use UUID instead of drive ID (sda, sdb, ...)

## Chroot Into Arch

`arch-chroot /mnt /bin/bash`

This will take us from the ISO that we booted into, to the actual installation of Arch.

## Install Some Tools

`pacman -S networkmanager grub git`

## Setup Systemd Networking

`systemctl enable NetworkManager`

## Configure Grub

Register drives

`grub-install /dev/sda`

Create config

`grub-mkconfig -o /boot/grub/grub.cfg`

## Set Password

`passwd`

## Edit visudo

`visudo`

Uncomment line "allow group wheel to do whatever"

## Set Locale

Edit locale.gen and uncomment locales to be used (`en_US.UTF-8 UTF-8` & `en_US ISO-8859-1`)

`vim /etc/locale.gen`

Generate locale

`locale-gen`

Create locale.conf

`vim /etc/locale.conf`

Add following line

`LANG=en-US.UTF-8`

## Set Hostname

`vim /etc/hostname`

## Add User

`useradd -m NAME`

`usermod -aG wheel NAME`

## Set Timezone

Find timezone

`ls /usr/share/zoneinfo`

Set symlink

`ln -sf /usr/share/zoneinfo/rest_of_path_to_zone /etc/localtime`

## Finish

`exit`

`umount -R /mnt`

Then reboot into arch!
