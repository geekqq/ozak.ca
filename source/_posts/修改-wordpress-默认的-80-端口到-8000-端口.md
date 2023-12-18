---
title: 修改 WordPress 默认的 80 端口到 8000 端口
tags: []
id: '120'
categories:
  - - uncategorized
date: 2023-12-16 14:39:04
---

最近又开始折腾 WordPress 博客了。因为一直 host 在 dreamhost 一年 150 多刀也不算便宜，自己有没有多少流量和内容要更新，就打算换一个平台，然后就想自己 host 在家里的服务器上。这样就要折腾 dns 域名转发，怎么让外网访问我的博客。最终是用了 nginx proxy manager 做的反向代理。暂时也可以访问，但是刚开始因为 WordPress 默认的 80 端口，所以安装nginx proxy manager 失败，所以就想给 WordPress 的端口改成 8000，经过多番搜索还是问 chatGPT，最终搞成了。以下是步骤：

1.  修改 WordPress 配置文件 wordpress.conf ，将默认的 80 端口改成 8000  
    `$ sudo nano /etc/apache2/sites-available/wordpress.conf`

![](https://www.stonehoo.me/wp-content/uploads/2023/12/image.png)

2\. 修改 Apache 的端口配置文件

$ sudo nano /etc/apache2/ports.conf

![](https://www.stonehoo.me/wp-content/uploads/2023/12/image-1.png)

3\. 重启 Apache2

$ sudo systemctl restart apache2

4\. 在 wp-config.php 文件里新增下面的内容：

define('WP\_SITEURL','http://SERVER-IP:8000/');

define('WP\_HOME','http://SERVER-IP:8000/');

这里有个大坑，因为网上搜到的内容有很多是错误，语法里都把 WP\_SITEURL 中间的下划线省掉了。所以一直搞不成。这个 chatGPT 倒是给出了正确的语法。

这里的 SERVER-IP 是你的实际服务器 IP 地址。

5\. 进入 MySQL 修改数据库里的地址

sudo mysql -u root

use wordpress;

update wp\_options set option\_value='http://localhost:8000' where option\_name='siteurl';

update wp\_options set option\_value='http://localhost:8000' where option\_name='home';