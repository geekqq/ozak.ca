---
title: wordpress 博客文章导出到 hexo
date: 2023-12-17 20:43:58
tags:
    - hexo
    - wordpress
---

原以为很复杂，因为有图片什么的，其实就这么几步就搞定了，用了一个叫做exo-migrator-wordpress 的插件。
以下是官网上的内容：

WordPress
First, install the hexo-migrator-wordpress plugin.

$ npm install hexo-migrator-wordpress --save
Export your WordPress site by going to “Tools” → “Export” → “WordPress” in the WordPress dashboard (see the WordPress support page for more details).

Now run:

$ hexo migrate wordpress <source>
Where source is the file path or URL to the WordPress export file.