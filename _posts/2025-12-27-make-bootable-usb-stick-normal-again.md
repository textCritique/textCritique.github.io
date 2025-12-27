---
layout: post
title: "Make bootable usb stick normal again"
date: 2025-12-27 12:39 +0530
description: How to guide for how make bootable usb drive into normal storage device again on linux
image:
category: [blog, how-to]
tags: [usb drive, bootable pen drive]
author: textCritique
published: true
sitemap: true
---

## Prerequisites

Assuming you are on arch linux, here are some prerequisite packages that needs to be to installed.

```bash
# for using mkfs.vfat command
sudo pacman -S dosfstools
```
## Find the device name

First find out device name. It is usually sdb but can be different. Pay close attention to total storage. It will give you a hint.

For example
```bash
sudo fdisk -l
```
```
...
Disk /dev/sdb: 57.3 GiB, 61530439680 bytes, 120176640 sectors
Disk model:  SanDisk 3.2Gen1
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x16da8f38

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1  *         64 2491103 2491040  1.2G  0 Empty
/dev/sdb2       2491104 3005151  514048  251M ef EFI (FAT-12/16/32)
```
Or

```bash
lsblk
```
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
...
sdb      8:16   1  57.3G  0 disk 
├─sdb1   8:17   1   1.2G  0 part 
└─sdb2   8:18   1   251M  0 part
```
First one is more clear because device model is given. In my case it is **SanDisk 3.2Gen1** which is mounted as `/dev/sdb`

## Wipe everything out including partition table

```bash
sudo wipefs --all /dev/sdb
```

## Create a new partition table


```bash
sudo cfdisk /dev/sdb
```

It will open up tui interface.
- Select dos partition table format as it is widely supported and will work on all systems.
- Next select new partition and keep default partition size that is maximum available. And set it as primary.
- Finally quit the interface.

## Format the partition as a FAT32

Following will format the partition as FAT32 and name the partition 1 ( *the only partition* ) as **A** 
```bash
sudo mkfs.vfat -n "A" /dev/sdb
```
Done!