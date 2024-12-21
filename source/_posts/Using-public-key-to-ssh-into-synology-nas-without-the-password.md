---
title: Using public key to ssh into synology nas without the password
date: 2024-12-20 20:12:26
tags:
    - code
    - synology
    - ssh
---
因为synology的登录密码设置的过于复杂，而我又经常需要ssh进入我的synology系统进行更新docker compose的projects，所以就想着要搜索一下如何免除输入密码ssh进去。果然还是chatGPT给力。一步一步详细给出了步骤，我这里就来记录一下。以后以免忘记了。
- 先是在本地电脑上生成一组public和private key
``` bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

```
需要将邮箱改成自己的邮箱。然后会提示保存路径，一般不需要更改，但是需要改一下文件名，因为我的ssh文件夹里面有很多不同的key。
参考设置为 ~/.ssh/id_rsa_synology 然后继续按照提示就可以生成密钥了。

- 下一步是将public key上传到synology服务器上面去。可以用下面的命令直接搞定
``` bash
ssh-copy-id -i ~/.ssh/id_rsa_synology.pub your_user@your_nas_ip

```
这里需要替换用户名和nas的ip地址。这一步也可以手动操作。

- 接下来要ssh进去synology，修改一下.ssh的权限。
``` bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```
- 这时候应该可以不用密码直接登录了，但是因为我的本地电脑.ssh目录中很多key，所以在ssh的时候还需要指定用哪个key登录，而我不想这么麻烦，因为在后面需要用脚本自动化，每次输入密码和指定key会导致无法自动化脚本，于是又问了chatGPT，这次也给出了完美的答案，直接修改.ssh/config文件就可以了。

``` bash
Host synology
    HostName 192.168.1.235
    User stone
    IdentityFile ~/.ssh/id_rsa_synology #这里就是指定用哪个key来登录的
```
这个保存之后，还需要ssh进入synology来重启一下sshd服务
``` bash
sudo systemctl restart sshd
```
然后退出再尝试ssh应该就可以搞定了:
``` bash
ssh synology
```
现在就简单多了。

- 上面的操作都没问题的时候，就可以进入synology里面，修改sshd_config文件，来关闭密码登录，其实要先登录进去打开公钥登录，但是不能直接把密码登录关闭，以免出现无法ssh进入的情况。

``` bash
sudo vim /etc/ssh/sshd_config
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no
```

最后再次重启一下sshd服务就可以了。
我的本地电脑是mac而且也设置了强密码，这台电脑只有我一个人用，所以为了方便才设置不用密码ssh的，用公钥登录更加安全。千万不要在一台不安全的电脑上这样设置，小心你的服务器被人私自登录搞破坏啊。😄