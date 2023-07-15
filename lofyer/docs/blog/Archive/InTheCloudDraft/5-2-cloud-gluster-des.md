---
title: "7. Gluster简述"
date: "2014-04-03"
categories: 
  - "inthecloud"
---

这一节会简述下glusterfs功能和特性。

glusterfs设计符合奥卡姆剃刀原则，即“若无必要，勿增实体”。

[![cloud-5.2-1](/blog/images/cloud-5.2-1.png)](http://blog.lofyer.org/5-2-cloud-gluster-src/cloud-5-2-1/)

## 功能特性

gluster集群组成的存储可以通过NFS、CIFS、Glusterfs、HTTP、FTP协议交给用户使用；

[![GlusterFS_Architecture](/blog/images/GlusterFS_Architecture.png)](http://blog.lofyer.org/5-2-cloud-gluster-des/glusterfs_architecture/)

gluster支持四种存储逻辑组合：Striped、Replicated、Striped-Replicated、Distributed；

[![Distributed_Volume](/blog/images/Distributed_Volume.png)](http://blog.lofyer.org/5-2-cloud-gluster-des/distributed_volume/)

[![Distributed_Striped_Volume](/blog/images/Distributed_Striped_Volume.png)](http://blog.lofyer.org/5-2-cloud-gluster-des/distributed_striped_volume/)

[![Distributed_Replicated_Volume](/blog/images/Distributed_Replicated_Volume.png)](http://blog.lofyer.org/5-2-cloud-gluster-des/distributed_replicated_volume/)

[![Distributed_Striped_Replicated_Volume](/blog/images/Distributed_Striped_Replicated_Volume.png)](http://blog.lofyer.org/5-2-cloud-gluster-des/distributed_striped_replicated_volume/)

支持跨网备份（geo-replication）；

[![Geo-Rep_LAN](/blog/images/Geo-Rep_LAN.png)](http://blog.lofyer.org/5-2-cloud-gluster-des/geo-rep_lan/)

[![Geo-Rep_WAN](/blog/images/Geo-Rep_WAN.png)](http://blog.lofyer.org/5-2-cloud-gluster-des/geo-rep_wan/)

[![Geo-Rep03_Internet](/blog/images/Geo-Rep03_Internet.png)](http://blog.lofyer.org/5-2-cloud-gluster-des/geo-rep03_internet/)

[![Geo-Rep04_Cascading](/blog/images/Geo-Rep04_Cascading.png)](http://blog.lofyer.org/5-2-cloud-gluster-des/geo-rep04_cascading/)

统一对象视图，与UNIX设计哲学类似，所有皆对象；

跨平台兼容性高，可作为hadoop、openstack、ovirt、Amazon EC的后端；

## 名词解释

砖块（brick）：即服务器节点上导出的一个目录，作为glusterfs的最基本单元；

扩展属性（xattr）：用户/程序以之将数据和元数据关联起来；

GFID：glusterfs中的每一个文件或者目录都有一个独立的128位GFID，与普通文件系统中的inode类似；

glusterd：运行于所有服务器中的管理守护程序；
