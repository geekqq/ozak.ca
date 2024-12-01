---
title: Download all my photos and videos from iCloud account to my Synology NAS
date: 2024-11-06 10:42:19
tags:
    - code
    - icloudpd
---
之前太太一直想让我把她手机里的照片导出来备份到电脑里，可是折腾来折腾去也没有找到一个方法，网上用的办法基本都是不成功，因为她手机里的照片是在是太多了，大概三万多张照片，一万多个视频，我没办法最后给她换了一个1t的iPhone 15，然后花钱买了土耳其区的iCloud 2T计划，暂时把这个问题给解决了。不过最终还是把照片放在了iCloud上等于，没有放在本地电脑上，万一以后iCloud账号出个什么问题，资料还是会小时。最近看到用icloudpd的办法可以搞定，很多人都分享了经验，我也准备再折腾一把。也遇到了不少坑，但是现在总算是搞定了，而且可以每天或者每隔一段指定的时间自动去iCloud账户里fetch新照片。默认折腾是24小时自动同步一次，然后有新照片或者视频的话就会自动下载到指定目录。

我是用了docker的方法在synology里面运行的icloudpd，参考的是这篇帖子：
https://forums.spacerex.co/t/backup-your-icloud-photos-to-your-synology-nas-and-even-convert-to-jpg-automatically-with-icloudpd-via-docker/1083

基本过程是这样的：
1. 现在file station新建文件夹
``` bash
mkdir -p docker/icloudpd/config
```
然后还要在自己想要保存照片的地方创建一个photos的文件夹，用来保存所有照片，我是直接在群晖根目录volume1里面创建了一个shared folder，命名为photos

2. 现在用群晖的container manager来创建项目，直接使用上面给的docker compose文件，我做了一点点修改
``` yml
version: '3.3'
services:
    icloudpd:
        container_name: icloudpd
        restart: always
        environment:
            - user=stone
            - user_id=1026
            - group=stone
            - group_id=100
            - apple_id=xxxxx@apple.com
            - authentication_type=2FA
            - 'folder_structure={:%Y/%m}'
            - notification_days=7
            - synchronisation_interval=86400 #同步间隔为24小时默认。
            - TZ=America/Los_Angeles
            - 'download_path=/icloud' #上传照片目录，这里不需要修改，因为这个是container内部的目录
            - skip_check=false  #官方如果照片很多，有几万张的话，为了避免出错，这个地方最好设置为true，不过我设置false也没有出错。
            - convert_heic_to_jpeg=true 
            - auto_delete=true  #自动删除在iCloud上被删除的照片。
        network_mode: host
        volumes:
            - '/volume1/docker/icloudpd/config:/config'
            - '/volume1/photos:/icloud'
        image: boredazfcuk/icloudpd:latest
```
3. 然后启动容器，之后就自动拉取镜像，等正常启动之后，还需要做一个动作就是进入container内部，运行一行命令进行iCloud账户的登录授权，系统就是提示输入密码，然后再输入验证码，之后会提示验证成功，之后等待一会儿就可以正常开始同步了，照片就会被下载到photos目录里。
``` bash
 docker exec -it icloudpd sync-icloud.sh --Initialise
 ```

4. 还有一个很重要的地方就是在创建的photos文件夹下面，要touch一个.mounted 的文件，不然会导致无法挂载目录。
5. 另外一个我遇到的坑是，启动容器之后看log有错误，说是icloud文件夹not readable，不清楚是什么原因导致的，但是我搜了一下，GitHub上有人提出有同样的问题，解决办法也很简单，直接进入容器修改目录权限就可以了。
``` bash
chmod +755 icloud/
```

Updated on Nov 30 2024
补充一下，如果直接exec进入container的话，可以直接执行这个命令来授权apple ID
``` bash
/usr/local/bin/sync-icloud.sh --Initialise
```

docker镜像的GitHub页面有更加详细的关于icloudpd的各种参数的设置，各位有兴趣的话可以自行查阅。
https://github.com/boredazfcuk/docker-icloudpd/blob/master/CONFIGURATION.md