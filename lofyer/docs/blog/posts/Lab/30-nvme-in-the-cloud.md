---
title: "NVMe in the Cloud(ZStack)，（适合）云原生的存储"
date: 2020-07-24
categories: 
  - "cloud-infra"
---

# NVMe in the Cloud(ZStack)，（适合）云原生的存储

NVMe作为新的存储协议，更能将新的存储介质的性能发挥出来，与此同时，与SCSI存储对等的NVMeOverRDMA和NVMeOverTCP也已经开始在数据中心中崭露头角（忽略FC，发展太慢了）。 传统云平台中的对这些存储设备的使用是没有任何问题的，但是在不改变存储对接架构的情况下发挥出其原生性能则是一个挑战。 关于瓶颈的问题，我们可以从不同的存储对接路径去分析。

当然，不同于公有云的机器完全自定义，硬件自己随便攒，私有云为了要达成产品化需要考虑的因素比公有云更为繁杂（当然在私有云大规模这个方向上就弱很多了）。

NVMeOverTCP的好处除了其性能之外，还有就是万兆以上线速，有多少来多少。

作为对比，我们使用Linux新版本自带的NVMe over TCP/RoCEv2 target。

硬件测试设备与软件：LightBits, Linux NVMe over TCP target, Linux NVMe over ROCEv2 target

软件测试方法：https://blogs.oracle.com/linux/nvme-over-tcp

测试客户端：100Gb x 2、25Gb x 4

测试云平台：ZStack 3.8，io_uring，spdk

对接方法：

# Direct to Guest

## PassThrough

## DirectConnect

# Via Host

## Cluster Filesystem

## Shared Block