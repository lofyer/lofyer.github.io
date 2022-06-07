---
title: "VDSM"
date: "2012-10-29"
categories: 
  - "cloud-infra"
---

不喜欢java，就是不喜欢。。但还是要干的。。 4000多个文件构建UML图的话有点困难，都是小类，看了些tools的代码，然后转到vdsm内容，通讯方式是xml-rpc，当初我猜对了。

vdsm是oVirt的节点代理，功能可订制，也可移植到其他管理平台，基于KVM，储存VM各种暂态数据 vdsm提供的api通过xmlrpc使用，架构如下

------------+------VDSM-------+------+-----
            |                 |      |
    sysfs---+                 |      |
      LVM---+              libvirt   |
net-tools---+              qmp|      |Virtio serial
      xxx---+             ---+      +-------------/
           ...                |      |
                      -------/        -------
                     /                        
                    |  KVM-QEMU VM             |
                     \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_/

主要包括vdsm,vdsm\_cli,vds\_bootstrap,vdsm\_reg,vdsm\_hooks 其中lifecycle hooks有针对vdsm和vm在before和after期间 bootstrap用于验证兼容性，包括网络，cpu，认证等，目前只支持RH自家产品
