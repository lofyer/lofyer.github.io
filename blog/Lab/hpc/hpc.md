---
title: "HPC相关"
date: "2018-08-13"
categories: 
  - "cloud-infra"
  - "hpc"
tags:
  - "HPC"
---

# HPC相关

## HPC入门指南(2018-08-13)

### 1. History

Wikipedia is good. [https://en.wikipedia.org/wiki/Supercomputer](https://en.wikipedia.org/wiki/Supercomputer)

### 2. Stack

#### 2.1. Hardware

Storage Network Server Cooler

#### 2.2. Software

Infrastructure deployment Parallel Filesystem Workload Management Development

Finally, we've got a PPT.

但是这个PPT很长，我只能截一些出来。

![](/blog/images/20220129131347-1024x470.jpg)

![](/blog/images/20220129131424-1024x509.jpg)

## HPC增补(2022-02)

HPC的生意目前来看还是集成，部分组件可以标准化，但是克雷、富士通、IBM、NVIDIA都有自己的盘子，库也都是为自家架构优化，这些都没有问题，理解。

想搞高性能向HPC技术方向学习，也没有问题，但是搞完过后想复制，那就铺人天了。一堆定制设备、一堆专有技术栈，加上已知最好的设备和软件都是国外垄断，生意做着做着就自己就成集成商了。集成商没啥不好，毕竟150W刀一个P的算力，这生意走量。国产化版本堆算力早些年成本差不多两倍，但现在差距在逐步缩小。

HPC的生意不好做，进不去圈子，但是汤可以喝，而且大厂们也想喝这个汤，不信你看GPFS都卖给了谁。后来做的纯软裸金属算是产品入局了，但是再往上走适配一个已经很很很完整的生态，想想都觉得累。

算了，回头把RDMA技术栈补一补，这样或许可以再搞些其他的花样，只是产品化的路没那么好走，但是集成的生意要啥产品化呢，还是要的，你看SAP模块化也厉害的一塌糊涂。
