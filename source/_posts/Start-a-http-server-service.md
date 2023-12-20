---
title: Start a http-server service
date: 2023-12-20 11:54:52
tags:
    - command
    - bash
    - coding
---

### Using this command to start the service

```
nohup http-server &

```
nohop will make the service is still running when quit the ssh session, the & at the end will make it run in the background

### Usding this command to stop the service

```
pkill -f "http-server"

```
The service will run at port 8080 by default.