---
title: Using fdisk to expand the disk space on Ubuntu
date: 2024-01-02 11:16:33
tags:
    - coding
    - ubuntu
    - disk
---

最近总是遇到系统磁盘不足的情况，都是在虚拟机上遇到的，Ubuntu 扩展磁盘空间，于是学习了一下，YouTube 上的视频，自己跟着做了一下，也还是挺简单的。
首先需要在Vmware 或者 virtualbox 上增加磁盘空间。这个是很简单的。然后就是在进入 guest OS，用 fdisk 工具进行操作。
```
sudo fdisk /dev/sda

# p to print the list of the devices
# d to delete the device you want to increase
# n to create a new partition on the device
# enter to select the start and end sector
# w to write the change
```
最后还需要用下面的命令使新分区生效：
```
sudo resize2fs /dev/sda
```
最后用 df -h 命令看一下，是否已经生效了。
![expand your disk on ubuntu](/images/expand_disk_ubuntu.png)