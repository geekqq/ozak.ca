---
title: 用JavaScript写的几个小练习项目
date: 2024-06-22 10:02:08
tags:
    - javascript
    - coding
---

按照codebro的教程用JavaScript写了几个小项目，然后配合ChatGPT写了Dockerfile，并build 了各自的镜像文件，push到docker hub仓库，这样之后在任何地方只要一行docker run 命令就可以直接使用了。有了docker 真是太方便了，不过听说中国把docker也给墙了，不知道为什么，互联网孤岛的说法真的太贴切了。

#### 1. password generator
```
docker run --name password -dp 3005:3005 osaks/passwordgenerator
```
[password.stonelab.me](https://password.stonelab.me)

#### 2. get weather
```
docker run --name getweather -dp 3013:3013 osaks/getweather
```
[weather.stonelab.me](http://weather.stonelab.me)

#### 3. temperature conversion
```
docker run --name tempcon -dp 3003:3003 osaks/tempconversionsite
```
[tempconv.stonelab.me](http://tempconv.stonelab.me)

#### 4. sliding images
```
docker run --name slidingimage -dp 3011:3011 osaks/slidingimage
```
[slidingimage.stonelab.me](http://slidingimage.stonelab.me)

#### 5. simple calculator
```
docker run --name calculator -dp 3009:3009 osaks/simplecalculator
```
[calculator.stonelab.me](http://calculator.stonelab.me)

#### 6. dice roller
```
docker run --name diceroller -dp 3004:3004 osaks/dicerollersite
```
[diceroller.stonelab.me](http://diceroller.stonelab.me)

所有的这几个服务都跑在家里的homelab 里其中一台miniPC上面。我在miniPC上安装了Ubuntu的系统，24小时开机不间断。之前是用的一台新买的Windows做服务器的，上面装了VMware来跑Ubuntu等虚拟机，但是Windows没事老自动更新然后重启，之后虚拟机就关机了，我必须要手动打开。后来就用了这个miniPC做服务器效果好多了。一直没有问题。

***
20241221更新
服务器换来换去，折腾homelab无止境。先是把这几个服务用docker文件分别单个host在一台mini PC上，后来买了一台新的mini PC搭建了proxmox的PVE环境，上面跑了一个docker lxc，专门用来host docker 和docker compose project，前几天莫名其妙这台lxc出问题无法访问了，不知道什么问题，关机也关不了，reboot也没有任何反应，一直出错，最终无奈进行物理断电重启的方式，PVE顺利启动了，但是这个lxc还是无法启动。最后全部删除了，又重新create了一个docker 的container，所有的docker 和 docker compose文件全部重新搞了一遍。索性直接把这几个小服务弄进了一个docker-compose.yml的文件中。然后一个一个用NPM进行反代，保证外网也可以正常访问。