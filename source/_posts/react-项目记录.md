---
title: React 项目记录
tags: []
id: '102'
categories:
  - - uncategorized
date: 2023-05-30 10:45:04
---

iscuu.com 是第一个react的personal portfolio项目。

host在了我的linode的CentOS服务器上。用caddy这个web sever来搞定的。设置也很easy

![](https://www.stonehoo.me/wp-content/uploads/2023/05/image-1024x768.png)

以上是CaddyFile的配置文件。文件路径是/etc/caddy/CaddyFile

本地macOS的dev目录里有原始代码。每次改动之后需要push到GitHub上的仓库。然后ssh到linode服务器上进入/home/stone/iscuu 目录下，做git pull 就可以更新到最新的代码。网站会自动更新。

另外做了一个personal portfolio的网站，暂时没有搞定在Ubuntu 或者 CentOS 上用caddy来hosting，不知道为什么，设置也是一样，但是caddy的网站root目录指向project 的 build目录之后，网站只显示react的默认页面，而不是网站内容页面。最后是在vercel上做的部署，直接连了GitHub仓库，然后每次在本地改动代码之后push到GitHub上之后，Vercel就会自动pull并更新网站内容。还是挺方便的，只是目前用的域名是免费的，还需要配置自己的域名才可以。

![](https://www.stonehoo.me/wp-content/uploads/2023/05/image-1-1024x768.png)