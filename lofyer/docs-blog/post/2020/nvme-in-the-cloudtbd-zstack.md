---
title: "NVMe in the Cloud(ZStack)"
date: "2020-07-24"
categories: 
  - "cloud"
---

NVMe作为新的存储协议，更能将新的存储介质的性能发挥出来，与此同时，与SCSI存储对等的NVMeOverFabric和NVMeOverTCP也已经开始在数据中心中崭露头角。 传统云平台中的对这些存储设备的使用是没有任何问题的，但是在不改变存储对接架构的情况下发挥出其原生性能则是一个挑战。 关于瓶颈的问题，我们可以从不同的存储对接路径去分析。

当然，不同于公有云的机器完全自定义，硬件自己随便攒，私有云为了要达成产品化需要考虑的因素比公有云更为繁杂，并不是随便买了一款硬件就能包装一下提供服务的。

NVMeOverTCP的好处除了其性能之外，还有就是万兆以上线速，有多少来多少。

硬件测试设备：LightBits

软件测试方法：https://blogs.oracle.com/linux/nvme-over-tcp

测试客户端：100Gb x 2、25Gb x 4

测试云平台：ZStack 3.8

# Direct to Guest

NVMeOverTCP的特点就是，用户以极低的改造成本即可获得高性能、低延迟的高性能存储，以现在的单盘IOPS

## PassThrough

## DirectConnect

# Via Host

## Cluster Filesystem

## Share Block
