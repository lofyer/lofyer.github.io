---
title: "10. oVirt简介"
date: 2014-04-24
categories: 
  - "inthecloud"
---

Welcome to the core!

云计算目前主流实现有SaaS（Software-as-a-service）、PaaS（Platform-as-a-service）和IaaS（Infrastructure-as-a-service）。IaaS和PaaS都算作基础件，SaaS可以与基础件自由组合或者单独使用。

图

虚拟化技术已经很受重视而且被推到了一个浪尖。如今诸多开源虚拟化平台，比如XenServer、CloudStack、OpenStack、Eucalyptus、oVirt、OpenVZ等，我们都看花了眼，些许慌乱不知哪个适合自己了。

各平台实现方式：全虚拟化，半虚拟化，TBD

我在写这篇文档的时候，只对oVirt略知一二，其他平台（XenServer、OpenStack）稍懂皮毛，再剩下的我就没怎么用过了。那么，只挑最熟悉的来讲吧。

IaaS云计算平台，综合来说具有以下特性：

**虚拟化**

虚拟化作为云计算平台的核心，是资源利用的主要形式之一。网络、存储、CPU乃至GPU等主要通过虚拟主机进行实体化。

**分布式**

分布式可利用共享的存储，通过网络将资源进行整合，是实现资源化的必备条件。

**高可用**

对于规模庞大的云平台，提供管理节点、重要服务的高度可用性是十分必要的。笔者在写这篇文章时，oVirt 3.4已经可以做到管理节点的高度可用。

**兼容性**

云计算平台众多，各家有各家的特点，同一数据中心部署不同的平台的可能性极大，因此，主要服务（比如虚拟网络、存储、虚机等）要有一定兼容性，比如oVirt可以利用OpenStack的Nouveau提供的虚拟网络、Foreman可以方便地在oVirt上部署新机器等。

另外，也有DeltaCloud、libvirt等API，用户可以利用它们自由地实现自己的云综合管理工具。

**资源池化**

网络、存储、CPU或者GPU可以综合或者单独划分资源池，通过配额进行分配，从而保证其合理利用。

**安全性**

现代企业对于安全性的要求已经十分苛刻，除去传统数据加密、访问控制，甚至对于社会工程也要有一定防护能力；用户数据具有对非企业管理员具有防护性能，即使将虚拟机磁盘文件拷贝出来也不能直接获取其内容。

**需求导向性**

在计算水平上，优质资源最先提供给重要服务；服务水平上，平台具有可定制能力。

## oVirt物理层视图

**管理独占一台物理机**

[![Normal](/blog/images/Normal.png)](http://blog.lofyer.org/cloud-6-1-ovirt-description/normal/)

高可用管理机

[![HA](/blog/images/HA.png)](http://blog.lofyer.org/cloud-6-1-ovirt-description/ha/)

## 内容预览

第11（6-2）搭建管理引擎； 第12（6-3）搭建高可用管理引擎； 第13（6-4）加入节点，构建一个完整的云平台； 第14（6-5）应用进阶；
