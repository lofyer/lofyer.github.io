---
title: "8. 搭建glusterfs作为基础存储"
date: "2014-04-11"
categories: 
  - "inthecloud"
---

既然要搭建一个稳健的基础，那么glusterfs在此使用distributed striped replicated方式，这里使用4台预装CentOS 6(SELINUX设置为permissive)的机器。

[![Distributed_Striped_Replicated_Volume](/blog/images/Distributed_Striped_Replicated_Volume1.png)](http://blog.lofyer.org/5-3-glusterfs-howto/distributed_striped_replicated_volume-2/)

## 添加DNS或者修改hosts文件

鉴于笔者所在环境中暂时没有配置独立的DNS，此处先修改hosts文件以完成配置（每台机器上都如此设置）：

# echo -e "192.168.10.101\\tgs1.example.com\\n192.168.10.102\\tgs2.example.com\\n192.168.10.103\\tgs3.example.com\\n192.168.10.104\\tgs4.example.com" >> /etc/hosts

## 添加repo

\[epel\]
name=Extra Packages for Enterprise Linux 6 - $basearch
#baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=$basearch
failovermethod=priority
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6

\[glusterfs-epel\]
name=GlusterFS is a clustered file-system capable of scaling to several petabytes.
baseurl=http://download.gluster.org/pub/gluster/glusterfs/LATEST/EPEL.repo/epel-$releasever/$basearch/
enabled=1
skip\_if\_unavailable=1
gpgcheck=1
gpgkey=http://download.gluster.org/pub/gluster/glusterfs/LATEST/EPEL.repo/pub.key

\[glusterfs-noarch-epel\]
name=GlusterFS is a clustered file-system capable of scaling to several petabytes.
baseurl=http://download.gluster.org/pub/gluster/glusterfs/LATEST/EPEL.repo/epel-$releasever/noarch
enabled=1
skip\_if\_unavailable=1
gpgcheck=1
gpgkey=http://download.gluster.org/pub/gluster/glusterfs/LATEST/EPEL.repo/pub.key

\[glusterfs-source-epel\]
name=GlusterFS is a clustered file-system capable of scaling to several petabytes. - Source
baseurl=http://download.gluster.org/pub/gluster/glusterfs/LATEST/EPEL.repo/epel-$releasever/SRPMS
enabled=0
skip\_if\_unavailable=1
gpgcheck=1
gpgkey=http://download.gluster.org/pub/gluster/glusterfs/LATEST/EPEL.repo/pub.key

## 准备磁盘

每一个节点都可以看做gluster server，安装xfs用户空间工具：

# yum install -y glusterfs glusterfs-fuse glusterfs-server xfsprogs
# /etc/init.d/glusterd start
# /etc/init.d/glusterfsd start
# chkconfig glusterfsd on
# chkconfig glusterd on

假如每台机器除系统盘之外都有2块1T SATA硬盘。 对其进行分区，创建逻辑卷，格式化并挂载：

# fdisk /dev/sdX << EOF
n
p
1

w
EOF

直接使用物理盘：

# mkfs.xfs -i size 512 /dev/sdb1
# mkfs.xfs -i size 512 /dev/sdc1
# mkdir /gluster\_brick0
# mkdir /gluster\_brick1
# echo -e "/dev/sdb1\\t/gluster\_brick0\\txfs\\tdefaults\\t0 0\\n/dev/sdc1\\t/gluster\_brick1\\txfs\\tdefaults\\t0 0" >> /etc/fstab
# mount -a

或者使用逻辑卷：

# pvcreate /dev/sdb1 /dev/sdc1
# vgcreate vg\_gluster /dev/sdb1 /dev/sdc1
# lvcreate --name lv\_gluster --size 2500G vg\_gluster
# mkfs.xfs -i size 512 /dev/vg\_gluster-lv\_gluster
# mkdir /gluster\_brick
# echo -e "/dev/mapper/vg\_gluster-lv\_gluster\\t/gluster\_brick\\txfs\\tdefaults\\t0 0" >> /etc/fstab
# mount -a

_为什么要用XFS？_ XFS具有元数据日志功能，可以快速恢复数据；同时，可以在线扩容及碎片整理。其他文件系统比如EXT3，EXT4未做充分测试。

## 配置节点，添加volume

在其中任何一台机器上，比如gs2.example.com，执行：

# gluster
  > peer probe gs1.example.com
  > peer probe gs2.example.com

添加brick至volume，**合理调整砖块顺序**：

# gluster
  > volume create gluster-vol1 stripe 2 replica 2 gs1.example.com:/gluster\_brick0 gs1.example.com:/gluster\_brick1 gs2.example.com:/gluster\_brick0 gs2.example.com:/gluster\_brick1 gs3.example.com:/gluster\_brick0 gs3.example.com:/gluster\_brick1 gs4.example.com:/gluster\_brick0 gs4.example.com:/gluster\_brick1 force
  > volume start gluster-vol1
  > volume status
Status of volume: gluster-vol1
Gluster process                                         Port    Online  Pid
------------------------------------------------------------------------------
Brick gs1.example.com:/gluster\_brick0                   49152   Y       1984
Brick gs1.example.com:/gluster\_brick1                   49153   Y       1995
Brick gs2.example.com:/gluster\_brick0                   49152   Y       1972
Brick gs2.example.com:/gluster\_brick1                   49153   Y       1983
Brick gs3.example.com:/gluster\_brick0                   49152   Y       1961
Brick gs3.example.com:/gluster\_brick1                   49153   Y       1972
Brick gs4.example.com:/gluster\_brick0                   49152   Y       1975
Brick gs4.example.com:/gluster\_brick1                   49153   Y       1986
NFS Server on localhost                                 2049    Y       1999
Self-heal Daemon on localhost                           N/A     Y       2006
NFS Server on gs2.example.com                           2049    Y       2007
Self-heal Daemon on gs2.example.com                     N/A     Y       2014
NFS Server on gs2.example.com                           2049    Y       1995
Self-heal Daemon on gs2.example.com                     N/A     Y       2002
NFS Server on gs3.example.com                           2049    Y       1986
Self-heal Daemon on gs3.example.com                     N/A     Y       1993
 
Task Status of Volume gluster-vol1
------------------------------------------------------------------------------
There are no active volume tasks
  > volume info all
gluster volume info all
 
Volume Name: gluster-vol1
Type: Distributed-Striped-Replicate
Volume ID: bc8e102c-2b35-4748-ab71-7cf96ce083f3
Status: Started
Number of Bricks: 2 x 2 x 2 = 8
Transport-type: tcp
Bricks:
Brick1: gs1.example.com:/gluster\_brick0
Brick2: gs1.example.com:/gluster\_brick1
Brick3: gs2.example.com:/gluster\_brick0
Brick4: gs2.example.com:/gluster\_brick1
Brick5: gs3.example.com:/gluster\_brick0
Brick6: gs3.example.com:/gluster\_brick1
Brick7: gs4.example.com:/gluster\_brick0
Brick8: gs4.example.com:/gluster\_brick1

## 客户端挂载glusterfs

当用glusterfs-fuse挂载时，客户端的hosts文件里需要有gluster server中的任一节点做解析：

127.0.0.1       localhost.localdomain localhost
::1             localhost6.localdomain6 localhost6

192.168.1.81    gs1.example.com

安装glusterfuse，将gluster卷作为glusterfs挂载，并写入1M文件查看其在各砖块分配：

# yum install glusterfs glusterfs-fuse
# mount.glusterfs 192.168.1.81:/gluster-vol1 /mnt
# cd /mnt
# dd if=/dev/zero of=a.img bs=1k count=1k
# cp a.img b.img; cp a.img c.img; cp a.img d.img

在四台服务端分别查看：

\[root@gs1 ~\]# ls -lh /gluster\_brick\*
/gluster\_brick0:
total 1.0M
-rw-r--r--. 2 root root 512K Apr 22 17:13 a.img
-rw-r--r--. 2 root root 512K Apr 22 17:13 d.img
/gluster\_brick1:
total 1.0M
-rw-r--r--. 2 root root 512K Apr 22 17:13 a.img
-rw-r--r--. 2 root root 512K Apr 22 17:13 d.img

\[root@gs2 ~\]# ls -lh /gluster\_brick\*
/gluster\_brick0:
total 1.0M
-rw-r--r--. 2 root root 512K Apr 22 17:13 a.img
-rw-r--r--. 2 root root 512K Apr 22 17:13 d.img
/gluster\_brick1:
total 1.0M
-rw-r--r--. 2 root root 512K Apr 22 17:13 a.img
-rw-r--r--. 2 root root 512K Apr 22 17:13 d.img

\[root@gs3 ~\]# ls -lh /gluster\_brick\*
/gluster\_brick0:
total 1.0M
-rw-r--r--. 2 root root 512K Apr 22 17:13 b.img
-rw-r--r--. 2 root root 512K Apr 22 17:13 c.img
/gluster\_brick1:
total 1.0M
-rw-r--r--. 2 root root 512K Apr 22 17:13 b.img
-rw-r--r--. 2 root root 512K Apr 22 17:13 c.img

\[root@gs4 ~\]# ls -lh /gluster\_brick\*
/gluster\_brick0:
total 1.0M
-rw-r--r--. 2 root root 512K Apr 22 17:13 b.img
-rw-r--r--. 2 root root 512K Apr 22 17:13 c.img
/gluster\_brick1:
total 1.0M
-rw-r--r--. 2 root root 512K Apr 22 17:13 b.img
-rw-r--r--. 2 root root 512K Apr 22 17:13 c.img

至此，所有配置结束，下一篇说一下使用以及部分trick。
