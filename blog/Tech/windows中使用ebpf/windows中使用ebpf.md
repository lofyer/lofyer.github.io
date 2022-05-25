---
title: "Windows中使用eBPF"
date: "2022-02-10"
categories: 
  - "cloud-infra"
  - "draft"
tags: 
  - "ebpf"
  - "gre"
  - "network"
  - "sdn"
  - "vxlan"
  - "windows"
---

# 背景

也挺有意思的，前年念叨着[Windows内的SDN方案咋做](https://blog.lofyer.org/sdn-windows-linux-baremetal/)，去年微软终于就搞了个[eBPF for Windows](https://github.com/Microsoft/ebpf-for-windows)，如此以来结合Windows新版本自带的NVGRE/VxLAN网络，安全组、EIP、LB啥的在纯软裸金属里都好说了。

另外，迫于适配智能与半智能网卡的压力，ZStack已经提供了ovs与linux bridge两个技术栈的网络（新建集群的时候可以选，虽然不是第一个但早些时候收到的需求确实不硬开发也忙。。），所以现在看起来一切都很合理。

当然，产品的东西跟新技术并不是什么强绑定关系，技术只是锦上添花实现产品的工具，稳定优先。

# 测试

## 编译eBPF for Windows

## 测试程序

## VxLAN/NVGRE集成
