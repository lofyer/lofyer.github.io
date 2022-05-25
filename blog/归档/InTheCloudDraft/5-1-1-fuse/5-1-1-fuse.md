---
title: "6.  FUSE"
date: "2014-04-03"
categories: 
  - "inthecloud"
---

既然是文件系统，那就不得不了解一下FUSE。

FUSE全名Filesystem in Userspace，是在类UNIX系统下的一个机制，可以让普通用户创建修改访问文件系统。功能大体就是连接内核接口与用户控件程序的一座“桥”，目前普遍存在于多个操作系统中，比如Linux、BSD、Solaris、OSX、Android等。

[![490px-FUSE_structure.svg](/blog/post/images/490px-FUSE_structure.svg_.png)](http://blog.lofyer.org/5-1-1-fuse/490px-fuse_structure-svg/) **图5-1**

FUSE来源于AVFS，不同于传统文件系统从磁盘读写数据，FUSE在文件系统或磁盘中有“转换”的角色，本身并不会存储数据。

在Linux系统中的实现有很多，比如即将要用到的glusterfs-fuse。
