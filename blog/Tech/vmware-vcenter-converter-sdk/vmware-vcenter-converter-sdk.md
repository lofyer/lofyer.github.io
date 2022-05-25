---
title: "VMWare vCenter Converter SDK"
date: "2017-06-16"
categories: 
  - "cloud-infra"
  - "draft"
---

看到有公司专门做P2V、V2V，看了一下他们做了一些管理与适应各平台的边边角角工作，其技术原理是使用iotap或者VMWare Hypervisor层的 VAIO（感谢黄老师指导），从而达成CDP效果。

[![](/blog/post/images/微信图片_20170616172058.jpg)](https://blog.lofyer.org/wp-content/uploads/微信图片_20170616172058.jpg)

但这种技术的现在的缺点有两点：

1\. 没法支持任何的并行IO类应用，比如GPFS，Oracle RAC等；

2\. 本质上没法做consistent group。

更多具体内容可以查看Falcon与Commvault的相关专利，但这篇文章中2V的话还可以玩玩，那么我们试一下Converter SDK吧，也可直接[下载Converter](https://my.vmware.com/en/group/vmware/evalcenter?p=converter)。

首先去下载最新的[VMware vCenter Converter Standalone SDK 6.1](https://my.vmware.com/group/vmware/get-download?downloadGroup=CONV61_SDK)，
