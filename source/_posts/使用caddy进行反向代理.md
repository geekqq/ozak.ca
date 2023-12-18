---
title: 使用caddy进行反向代理
tags: []
id: '90'
categories:
  - - uncategorized
date: 2023-04-20 15:38:50
---

轻量级又开源的caddy真的还是挺好用的。用了caddy在服务器上做反向代理之后，很多web app用IP加端口访问的方式就可以改成了用subdomain 加上 domain 来访问了。

记录一下过程。

安装caddy，问了chatGPT，给我的答案不正确，这个好办，去官网一看就有了，好解决。

然后修改CaddyFile 这个也好办，按照老师演示的去改就可以了。然后systemctl reload caddy就完成了。但是一直不生效。cha t GPT也没有给出很好的答案。

最后发现在端口转发那一步出的问题，我在路由器上设置了3000端口的转发，这个是不行的，而是要设置80/443这两个端口的转发。设置完成之后就生效了。