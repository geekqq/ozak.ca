---
title: Temp Conversion Site Development
date: 2024-03-16 17:00:41
tags:
---
最近在学习javascript，跟着codebro的视频做了一个华氏度和摄氏度温度转换的简单页面，包含了index.html，index.js和style.css，是一个静态网站，输入一个数字之后选择摄氏度华氏度的转换，点击按钮既可以转换，很简单的网站。
![](/images/tempsite.jpeg)

做好之后就像能不能自己把它host在自己的服务器上，家里一堆闲置的电脑，都安装了Ubuntu的系统，也跑了一些自己的服务在上面。安装了tailscale之后就可以在外网也可以访问了。于是就问了一下chatGPT该如何操作。给出的结果也很令人满意。按照指导就搞定了。


首先要在服务器上安装node，这个早已就已经装好了。

然后开始创建项目
```
npm init -y
```
这样就生成了项目的node 目录，里面有package.json文件，在新建一个public文件，一个server.js文件。因为3000是常用端口，所以我把端口从3000改成了3003

安装express
```
npm i express
```

``` javascript
const express = require('express');
const app = express();

// Serve static files from the 'public' directory
app.use(express.static('public'));

const port = process.env.PORT || 3003; // Use the port provided by environment variable or default to 3003

app.listen(port, () => {
    console.log(`Server is running on port ${port}`);
});
```

把要host的静态文件放进public目录就可以了，文件目录如下：
```
project_directory/
├── public/
│   ├── index.html
│   ├── index.js
│   └── style.css
├── server.js
└── package.json

```
对了还有最重要的一步，在package.json文件里添加脚本：
```
"start": "node server.js"
```

最后用npm start 就可以启动服务了。

不过这么一番操作之后发现在同一个网络里用局域网ip无法打开页面，但是ssh进去服务器用localhost:3003可以顺利加载页面。也就是说网络配置应该是有问题的。这时候chatGPT给出了一个命令，一次性的解决了问题：
``` bash
sudo iptables -A INPUT -p tcp --dport 3005 -j ACCEPT
```

一不做二不休，这样虽然成功了，但是用起来还是比较麻烦的，我就想呢能不能干脆给打包成一个docker image镜像文件，然后这样在用的时候只要一行命令就可以搞定了。继续深耕chatGPT，主要就是要写一个Dockerfile。好久没有用docker了，具体怎么写也给忘记了。不过GPT给出了一个模板，测试之后基本就是这么写的。
```
FROM --platform=linux/amd64 node:alpine3.10
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3003
CMD ["npm", "start"]
```

有了Dockerfile之后就可以build 自己的image了。

```
docker build -t osaks/tempconversionsite .

docker push osaks/tempconversionsite
```

之后要用的时候直接就docker run 就可以了：
```
docker run --name tempsite -dp 3003:3003 osaks/tempconversionsite
```
