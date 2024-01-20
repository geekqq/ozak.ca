---
title: Reinstall MagicMirror on Raspberry pi
date: 2024-01-20 13:34:46
tags:
    - MagicMirror
    - Raspberry Pi
---
前几天把MagicMirror不小心怎么给弄坏了，彻底打不开了，无法启动。刚好raspberry pi的系统似乎也有问题，干脆整体重新安装算了。于是把pi的系统整个格式化掉了。上面接了键盘和鼠标，启动界面会有一个按shift进去recover模式的提醒，那时候进去选择install系统就可以了，整个过程大概十五分钟左右就完成了。
pi的系统恢复完成之后就是进入pi的系统先设置好ssh自动启动，固定ip，这样方便用ssh远程进入。
然后就是用最方便的安装方式来安装MagicMirror了，是用一个脚本搞定的。脚本链接在这里[链接到安装脚本](https://github.com/sdetweil/MagicMirror_scripts)
``` Bash
bash -c  "$(curl -sL https://raw.githubusercontent.com/sdetweil/MagicMirror_scripts/master/raspberry.sh)"
```
安装最后阶段会提示是否安装pm2开机自动启动MagicMirror和禁用系统屏保，我都选择yes 安装。之后就会重启进入MagicMirror的默认界面。

接下来就是修改系统，让屏幕旋转，因为我的显示器是竖放的。步骤是：

``` Bash

# 进入下面的目录，修改autostart文件
# atustart 位置是/etc/xdg/lxsession/LXDE-pi/autostart 
sudo vim /etc/xdg/lxsession/LXDE-pi/autostart

# 最下面加一行：
@xrandr --output HDMI-1 --rotate left
```

然后还要修改/boot/config.txt文件
``` bash
sudo vim /boot/config.txt

# 加入下面的两行命令：
display_rotate=1
avoid_warnings=1
sudo reboot
```
重启之后就可以看到屏幕左转了90度。这正是我想要的。

然后就是pm2的命令是这样的：
``` bash
pm2 start MagicMirror
pm2 stop MagicMirror
pm2 restart MagicMirror
```

之后就是安装第三方模块和修改自带的模块。
clock是系统自带的模块，默认显示的钟表字体太小了，第一部就是改大字体。这个我是Google之后找到的，官网似乎没有说明。其实就是找到模块的css文件，然后添加css字体大小就可以了
``` Bash
cd ~/MagicMirror/modules/default/clock
sudo vim clock_style.css

# 添加以下内容
.clock .time {
    font-size: 100px;
}

.clock .date {
    font-size: 50px;
}
```
之后是修改天气模块。天气是有两个模块的，一个是当前天气，type: "current" 和未来天气预报type: "forcast"，这两个都要讲location的代码改为Hayward的城市代码是5355933，然后是需要添加openweather的apiKey，我的是

"1f14e8a6046e4e74465fd6d337f84354"

之后还要修改单位为华氏度，在config括号里添加一行tempUnits: "imperial" 就可以了。

之后是添加第三方模块，我加了MMM-bitcoin-portfolio和MMM-wallpaper两个模块。第一个是用来显示比特币的实时价格的。刚开始有个问题，安装完之后没有办法启动MagicMirror，查看了log是发现，这个模块安装之后少安装一个request的依赖，导致模块无法启动，解决办法很简单，cd 进入到MMM-bitcoin-portfolio模块的目录下面，使用npm install request 就可以了。之后就顺利启动MagicMirror了，然后是修改字体大小，默认的也是太小了。
``` Bash
sudo vim ~/MagicMirror/modules/MMM-bitcoin-portfolio/MMM-bitcoin-portfolio.css
```
然后在css文件里添加字体大小就可以了，我是改成了150px，超级大啊。
``` css
.ticker {
    color: white;
    font-size: 150px
}
```
最后添加了一个wallpaper，上面有好几个wallpaper的module，我用的是[kolbyjack/MMM-Wallpaper](https://github.com/kolbyjack/MMM-Wallpaper) 的这个。他这个可以选择我的iCloud的共享相册里的内容，作为背景墙，轮换展示我里面的照片。安装好之后只需要把iCloud里的shared album的id填写在source里面就可以了。非常简单。无需其它设置。

最后的样子就是这样的：
![magcimirror screenshot](/images/mmsc3.png)


