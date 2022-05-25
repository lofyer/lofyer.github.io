---
title: "ABM-based Automatic System for OpenStack(VMWare/oVirt/maybe container...)"
date: "2017-05-03"
categories: 
  - "abm"
  - "ai"
  - "cloud-infra"
  - "modeling"
tags:
  - "Agent-based Modeling"
  - "Artificial Intelligence"
  - "Cloud Computing"
---

这篇文章可以写一篇论文。

注意，这里的controller不是指OpenStack的controller，而是整个平台过程控制的controller，也就是Automatic System。

好吧，这个问题不大，但也不小。好在我会建模会模拟，嘿哈。（这里不用EBM建模的原因是这个系统有些复杂，公式不足以直观发现问题，但为了提升一下本文的友好度，所以我会加几个公式^\_^）

如果你不熟悉ABM建模可以参考我之前的文档，[DataNote](http://datanote.readthedocs.io/zh/latest/posts/ch03.html)。

那么首先我们要确认模型的目的，即是控制整个虚拟化（容器）在一定条件下的行为，这些条件有存储、网络、计算等资源，直观的条件简单来说就是用户需求（杂项比较多）。然后确定代理人（agent, turtles）、关系连接（link）、控制规则（rules）、代理人行为（actions）等。

在公交车上想出了一个模型，factory-server-client模型，可以的，然后我再收集点主流平台的规则就可以模拟了，非常容易唬人。

**最近改为了网格-细菌-食物模型。**

先来个粗犷的图吧。

[![](/blog/post/images/微信截图_20170802002232.png)](https://blog.lofyer.org/wp-content/uploads/微信截图_20170802002232.png)

图中，细菌代表虚拟机，食物代表负载，网格代表物理机，食物会一直生成，其速度可控，也就是可以控制负载的变化速度。比如，当负载变化加快时，即食物增长，虚拟机的平均负载会增高，甚至会增加新的虚拟机（也就是细菌繁殖），当然，物理机总数保持不变。

那么这个模型中我们可以得到什么信息，从而达到最高的性价比呢，即虚拟机的变化量最小？

1\. 选择合适的繁殖参数（什么时候创建新虚拟机）；

2\. 可以尝试将细菌固定在一个或者一些网格中，即虚拟机的亲和性，可以观察影响；

3\. 课以观察物理机宕机状态下的虚拟机承载与变化能力；

基于这个模型，我们可以搞个controller独立运行，会通过平台API获取资源的即时信息，然后再去做优化动作。
