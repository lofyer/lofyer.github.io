---
title: "备份工具（迁云必备）"
date: 2017-10-26
categories: 
  - "cloud-infra"
tags:
  - "Backup"
  - "Cloud Computing"
---

# 备份工具（迁云必备）

- UrBackup [https://www.urbackup.org/features.html](https://www.urbackup.org/features.html) 

- Amanda [http://amanda.zmanda.com/](http://amanda.zmanda.com/) 

- Bacula [https://blog.bacula.org/](https://blog.bacula.org/) 

- BareOS [https://www.bareos.org](https://www.bareos.org) 

- Duplicati [https://www.duplicati.com](https://www.duplicati.com) 

- kvmBackup [https://github.com/bioinformatics-ptp/kvmBackup](https://github.com/bioinformatics-ptp/kvmBackup)

直接说结论吧，使用下来，UrBackup与Duplicati是较为合适的两个选择。

UrBackup采用传统的CS架构，对全平台提供支持，备份选项较为丰富，而且支持公网中继； Duplicati则是较为轻量级的选择，只要安装agent即可，支持备份目标地址较为丰富，除了传统接口外也支持AWS S3接口； Bacula与BareOS（前者变种）属于同一体系，但后者支持更好广泛些； Amanda除了硬盘文件备份，也多了一个MySQL数据库的备份产品； 最后一个KVMBackup则是一个脚本，限制比较多，适合单纯的KVM环境备份。

如果与ZStack对接，这俩都可以，备份存储地址可以选择FTP，这样就可以直接使用ZStack的ZStore镜像存储，格式使用raw，然后就直接启动了。