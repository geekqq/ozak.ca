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
[password.ski.sh](http://password.ski.sh)

#### 2. get weather
```
docker run --name getweather -dp 3013:3013 osaks/getweather
```
[weather.ski.sh](http://weather.ski.sh)

#### 3. temperature conversion
```
docker run --name tempcon -dp 3003:3003 osaks/tempconversionsite
```
[tempconversion.ski.sh](http://temp.ski.sh)

#### 4. sliding images
```
docker run --name slidingimage -dp 3011:3011 osaks/slidingimage
```
[image.ski.sh](http://image.ski.sh)

#### 5. simple calculator
```
docker run --name calculator -dp 3009:3009 osaks/simplecalculator
```
[calculator.ski.sh](http://calculator.ski.sh)

#### 6. dice roller
```
docker run --name diceroller -dp 3004:3004 osaks/dicerollersite
```
[dice.ski.sh](http://dice.ski.sh)

所有的这几个服务都跑在家里的homelab 里其中一台miniPC上面。我在miniPC上安装了Ubuntu的系统，24小时开机不间断。之前是用的一台新买的Windows做服务器的，上面装了VMware来跑Ubuntu等虚拟机，但是Windows没事老自动更新然后重启，之后虚拟机就关机了，我必须要手动打开。后来就用了这个miniPC做服务器效果好多了。一直没有问题。