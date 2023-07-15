---
title: "虚拟化平台镜像去冗测试（opendedup）"
date: "2017-06-02"
categories: 
  - "cloud-infra"
  - "linux-admin"
  - "draft"
---

OpenDedup，是一款开源去重文件系统，https://github.com/opendedup/sdfs，可以分布式，支持NFS、iSCSI等，感觉非常厉害，作者是Veritas的银堡（Sam Silverberg）。

!!! note

    RedHat 7就开始带了VDO，但是还没测过。

可以去官网下载镜像，或者是笔者认为更加实用的NAS系统。

## 简单测试

目的是减少更多本地环境占用，使用opendedup测试。

1. 首先测试我新闻服务器上的数据，以索引文件为主，经常变动，有大有小。

2. 然后选择一款kvm软件平台。

安装：

```
wget http://www.opendedup.org/downloads/sdfs-latest.deb
sudo dpkg -i sdfs-latest.deb

sudo su
echo "* hardnofile 65535" >> /etc/security/limits.conf
echo "* soft nofile 65535" >> /etc/security/limits.conf
exit
sudo mkfs.sdfs --volume-name=pool0 --volume-capacity=256GB
sudo mkdir /media/pool0 
sudo mount.sdfs pool0 /media/pool0/
```

数据拷贝进去后，原9.3GB索引数据降为8.5GB，效果不如想象中好。

可能原因：

sdfs提供了丰富的命令行选项，我没有使用，使用后可能会达到预期，比如更小的block。