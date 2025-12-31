---
layout: post
title: "Make Bootable USB Stick Normal Again"
date: 2025-12-27 12:39 +0530
description: How-to guide for making a bootable USB drive a normal storage device on Linux
image:
category: [blog, how-to]
tags: [usb drive, bootable pen drive]
author: textCritique
published: true
sitemap: true
---

## Prerequisites

Assuming you're on Arch Linux, here are the packages you need to install (adjust for your distro: e.g., `sudo apt install dosfstools` or `sudo dnf install dosfstools`).

```bash
# for using mkfs.vfat command
sudo pacman -S dosfstools
```
## Find the device name

First find the device name. It is often `/dev/sdb` but can be different—pay close attention to the total size and the model to avoid wiping the wrong disk. Replace `/dev/sdb` below with your actual device.

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
Or, for a quick overview:

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


> This is destructive. Double-check the device (`/dev/sdX`) before running.
{: .prompt-danger }
```bash
sudo wipefs --all /dev/sdb
```

## Create a new partition table


```bash
sudo cfdisk /dev/sdb
```

It opens a TUI interface:
- Select the DOS partition table format (widely supported and works on most systems).
- Create a new primary partition using the default (maximum) size.
- Write the changes and quit.

## Format the partition as a FAT32

This formats the new partition as FAT32 and labels partition 1 (the only partition) as **A** (feel free to pick another label).
```bash
sudo mkfs.vfat -n "A" /dev/sdb
```

## Verify

Check that the partition is present and formatted:

```bash
lsblk --output NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS /dev/sdb
# or
file -s /dev/sdb1
```

Done!