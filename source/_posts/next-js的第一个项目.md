---
title: next.js的第一个项目
tags: []
id: '93'
categories:
  - - uncategorized
date: 2023-04-24 12:33:27
---

最近学习使用next.js创建了第一个项目，我的个人页面，很简单的页面，还在不断优化中。

地址是 [geekkk.com](http://geekkk.com)

在本地创建开发模式的项目之后，编辑page文件夹下面的index.tsx文件，弄成自己想要的样子，然后cd

到项目目录里，用以下代码运行：

> npm run dev
> 
> 就可以在本地用locahost:3000端口来访问。

每次修改任何code都直接用command + S 来save文件，浏览器就实时更新了，都不用刷新页面。还是挺酷的。

接下来代码部署部分，在github创建远程仓库，然后在本地用命令行讲代码push到远程仓库。

然后ssh到服务器的网站目录下面，Git clone远程仓库，就自动将代码下载到了服务器上。

第一次在server上运行之前，需要先安装各种modules

npm install

然后再build一下

npm run build

成功之后start就可以了

npm run start

这时候项目就在3000端口启动了。

我用一个自己购买的域名，将A记录的IP地址修改为家里的公网IP，然后在server上安装了caddy做了reverse proxy，将域名的443和80端口请求，代理到了localhost的3000端口。这样就可以在外网用域名直接访问这个网站了。

caddy配置文件内容如下：

![](https://www.stonehoo.me/wp-content/uploads/2023/04/image.png)

看起来似乎十分简单，但是刚开始也是各种出错，自己折腾了大半天才搞定。最重要的一部是要在路由器上设置80和443的端口转发，不然加密证书就无法获取，导致用域名无法访问网站。

之后对网站做的任何修改和更新，我都是在本地电脑上的dev模式下，修改然后localhost自动更新看效果，没有问题的话再推送到远程仓库

git add .

git commit -m "some text for notes"

git push

然后在登录server做以下操作

git pull

npm run build

npm run start

这样就重新启动服务，就可以看到更新后的网站了。

如果旧的任务没有结束直接就run start新的任务，会报错，提示3000端口已经占用。这时候用下面的命令查找出node的PID，然后kill掉再启动就可以了

sudo lsof -i :3000

kill -9 {PID}