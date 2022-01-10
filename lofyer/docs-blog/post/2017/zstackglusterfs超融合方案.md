---
title: "ZStack Glusterfs 超融合方案"
date: "2017-07-14"
categories: 
  - "cloud"
  - "draft"
---

ZStack 2.1版本中已经支持了NFS作为MNHA节点的存储，那么我们可以按照之前glusterfs组件oVirt超融合的方式直接使用，过程如下。

**实验环境** 服务器3台，双千兆网口，均已安装ZStack专家模式操作系统。

**部署三节点无条带双副本分布式glusterfs存储** 节点安装ZStack计算节点后，分别在三台上使用如下命令安装glusterfs。

**部署ZStack HA管理节点** 将glusterfs的路径分别挂载至/zstack/glusterfs/mnha/，但要注意NFS/glusterfs的IP，因为glusterfs到服务端的连接不同于
