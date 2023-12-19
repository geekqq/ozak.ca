---
title: hexo blog update
date: 2023-12-19 12:05:31
tags:
    - hexo
    - memo
    - commands
    - blog
---

## Update my hexo blog

Using the following command to create a new post

```
hexo new "blog title"

```
There will be a new markdown file created in the directory /source/_posts
Then edit this file.
After finishing edit, push it to github repo using the following command:

```
git add .
git commit -m "new post"
git push

```
You need to go to hexo root directory to execute the above commands, otherwise there will be errors.

Using the following commands to start the server on the local machine to check if everything is right:

```
hexo clean && hexo s

```
The server will be started at http://localhost:4000

If your port 4000 is running something else, check it and kill it using commands:
```
lsof -i :4000   #find the PID that is running on port 4000
kill -9 PID     #the PID you find to kill the process

```
Then the server shall be started without any problems.

Running this command to update the deployment on vercel:

```
npm install hexo-theme-redefine@latest

```


