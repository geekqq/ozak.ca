---
title: Create a shared folder between host and vm
date: 2024-01-05 21:27:15
tags:
    - vmware
    - ubuntu
    - linux
---
这两天因为删除 pihole 的时候把一堆 dependency 也误删了，导致一个Ubuntu 的虚拟机没有了 network manager，不但不能上网，那么所有的软件包就无法安装，想尽各种办法，目前还是没有到顶。但是有一件事情搞明白了，就是如何在 host 机器上建立一个共享文件夹让虚拟机也可以访问这个文件夹。本来是很简单的事情，用 vagrant 就是默认 vagrantfile 所在的文件夹就是可以共享的。但是 VMware 就比较麻烦。
首先是在Vmware 对应的虚拟机里，点击 setting - options - shared folder 然后 add 一个文件夹，并加一个名字。
![add shared folders in vmware](/images/sharedfolder_vmware.png)
然后进去虚拟机 mount 一下就好了。命令如下：
```
vmware-hgfsclient #显示共享文件夹的名字，以便 mount 的时候使用，比如我的名字是 vmshare，下一步的命令就是
mkdir -p /mnt/hgfs #创建一个挂载点。
$ sudo vmhgfs-fuse .host:/vmshare /mnt/hgfs/ -o allow_other -o uid=1000 
```
就这么简单就好了。
参考了这个帖子的讨论内容：
[mount shared folder on ubuntu](https://askubuntu.com/questions/29284/how-do-i-mount-shared-folders-in-ubuntu-using-vmware-tools)