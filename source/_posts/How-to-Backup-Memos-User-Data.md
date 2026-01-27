---
title: How to Backup Memos User Data
date: 2026-01-26 16:21:55
tags:
    - memos
    - database
    - backup
---
I have been using memos[https://usememos.com/] for almost two years now. I write memos almost everyday. So there are a lot of data I do not want to lose, I have been thinking about backup my personal data just in case. But too busy to do it. Recently I am not that busy anymore. So I dig a little out about how to backup my personal data on memos. Thanks for the help of chatGPT which has made this process a lot of easier.

1. SSH into my original server which is running my memos instance, in my case, it is running on my geekhh-nas.
2.

``` bash
# stop the running memos instance
docker compose down
# go into the memos_prod.db dir
cd /parth/to/memos_prod.db
# tar the memos_prod.db file and send it to the new server
tar -czvf memos-data.tar.gz /path/to/memos_prod.db
scp memos-data.tar.gz stone@backup-server:/home/stone/docker/memos/memos
# untar the database file
tar -cxvf memos-data.tar.gz . 
# start the memos instance on the new server
docker compose up -d

```
___
The thing is all the stuff you need to backup is just the ***memos_prod.db*** file which is really simple. BUT you need to stop the instance and do the backup. That's it.