---
title: Install MagicMirror2 on Raspberry PI
date: 2023-12-22 22:08:24
tags:
    - Raspberry Pi
    - MagicMirror
---

MagicMirror 是一个开源的小程序，很多人把它装在 raspberry pi 上面，可以显示一些定制的信息，比如天气，比特币信息，日程等，具体介绍可以看官网。我是按照官网的教程做的。以前折腾过，但是很久没有升级了，已经落后了 130 个 commit 了，于是今天就干脆直接重新安装了吧。没想到被封了 config 和 modules 文件都还是要重新折腾一遍，今天就干这个事情了。不过总算是搞好了，这里就写个流程，记录一下以便下次忘记了。

基本都是按照官网的教程走的。

[MagicMirror 官网](https://magicmirror.builders/)

安装流程就是 manual 的方式，同时也有一个脚本自动安装的模式，但是我发现安装好之后不知道如何重启，因为是需要修改 config 文件和 modules 安装的，之后都需要重启，搞了半天不知道怎么重启，之后用手动模式重新安装了一下。还手动升级了 pi 上的 node.js 因为好久没用了，版本太老旧都不支持了。

安装好之后就是修改一些 css 和 config 里面的内容然后安装一些插件，也就是所谓的 modules。

因为我的显示器是竖着放的，所以需要先修改显示方向，这个官网上也是有说明的，链接在[这里](https://github.com/MichMich/MagicMirror/wiki/Configuring-the-Raspberry-Pi)。也还是蛮简单的，修改了显示方向之后需要重启reboot。

然后还找到了一个人分享的，在 magic mirror 正在运行的情况下用命令行工具截图 Magic Mirror 界面：
``` bash
sudo apt-get install scrot
DISPLAY=:0 scrot
```
scrot 命令后面也可以跟上保存截图的位置，不加的话就保存在当前文件夹下面。
这是我刚刚保存的截图：
[![2023-12-22-220515_1080x1920_scrot_MagicMirror2_screenshot.md.png](http://192.168.1.235:8145/images/2023/12/23/2023-12-22-220515_1080x1920_scrot_MagicMirror2_screenshot.md.png)](http://192.168.1.235:8145/image/UZV)

需要注意的几个坑是：
1. 默认的 clock 显示在左上角，但是字体很小，这个需要在/MagicMirror/modules/default/clock 目录下的 css 文件里面，添加一个字体大小，我用的是这个，可以正常工作：
``` css
.clock .time {
 font-size: 100px;
}

.clock .date {
 font-size: 30px;
}
```
2. 在修改天气 modules 的时候也费了不少时间，给出的一个city list 太大了，打开没办法搜索，找不到自己的城市，后来还是在这个直接在网上搜了一下也能用，可以用这个链接直接去输入城市名字，然后 URL 里就会有该城市的 ID

链接是这个[https://openweathermap.org/city/5219722](https://openweathermap.org/city/5219722)




