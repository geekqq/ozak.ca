---
title: Set up static ip address for Ubuntu server
date: 2024-01-12 21:18:33
tags:
    - Ubuntu
    - config
    - linux
---

Just installed Ubuntu server edition on my VMware, so I figured I need to access this VM using ssh, then I better set up the static ip address for this server. So I tried to ask ChatGPT, did not work out very well. Then googled this post and it helped me a lot:

[**Set Ubuntu Server Static Ip**](https://www.linuxtechi.com/static-ip-address-on-ubuntu-server/)


``` Bash
# Using ip addr show to see the NIC, mine is eth33
ip addr show

# cd to /etc/netplan, you should see a file 00-installer-config.yaml
cd /etc/netplan
ls
edit the file:
sudo vim 00-installer-config.yaml
network:
  renderer: networkd
  ethernets:
    ens33:
      addresses:
        - 192.168.1.127/24
      nameservers:
        addresses: [8.8.4.4,8.8.8.8]
      routes:
        - to: default
          via: 192.168.1.254
  version: 2
```

After editing the file, apply the changes, using this command
``` Bash
sudo netplan apply
```
In my case, there were some warnings popped out, but it did not matter.

Then verify the ip address:
``` Bash
ip addr show ens33
ip route show
```
![Now You have successfully set up the static ip](/images/ip-addr-route-command-output-ubuntu-server.webp)