---
title: "ARM/X86服务器的安卓市场（虚拟化、容器）"
date: "2019-11-17"
categories: 
  - "cloud-infra"
tags:
  - "Android"
---

# 0. 背景

随着国产化进程的推进，相当的应用已经可在国产化服务器（ARM/X86）上运行，本文将使用容器以及虚拟化两种技术对ARM/X86服务器上运行高性能的Android桌面进行探索。

调研了一圈实现，业界性价比最高的还是用板卡。。但是初始研究成本高一些，决心做的话可以先买一些现成的深圳货，但是对于入门的厂商来说还是用arm服务器跑容器合适，毕竟是安卓。

# 1. ARM/X86服务器

## 1.1. 虚拟化

使用X86服务器去虚拟化Android的厂商确实不多，社区有提供X86版本Android模，使用X86去模拟ARM版本的Android几乎没人做（效率极差）。

但是随着国内这几年ARM服务器市场上来，不少厂商早就开始探索ARM服务器去虚拟ARM版Android了，虽然效率较X86有很大提升，但是相比容器技术代价仍然很高，模拟出的手机定价低导致大家都是探索性的尝试。

## 1.2. 容器

这已经是一个较为成熟的技术了，但是缺点在于虚拟出的设备不完善。

Docker-Android

Anbox(LXC)

Xdroid

## 2. GPU

### NVIDIA

### Mali

# 3. 桌面协议

凡上上规模的Android模拟都需要成熟的桌面协议，而这又与他采用GPU设备相关。

由于qemu的ARM模拟的VGA设备由于其天生架构问题，不能正常使用，因而暂时需要使用virtio-vga设备方可显示（https://www.linux-kvm.org//blog/images/0/09/Qemu-gfx-2016.pdf）。

## 3.1. 带内协议

VNC

SPICE

RDP/ICA

PCoIP

## 3.2. 带外协议

VNC

SPICE

PCoIP---
title: "Building the infrastructure for cloud security"
date: "2015-02-08"
categories: 
  - "cloud-infra"
---

**Host** TPM Attesation Mt. Wilson Geo-tag HyTrust McAfee ePO

**VM management** SSO SDN VLAN Firewall

**VM Appliance** Mystery Hill
---
title: "Home-based hybridcloud（家庭作坊式混合云）"
date: "2017-10-24"
categories: 
  - "cloud-infra"
  - "linux-admin"
---

名字起的不好听，不过无所谓，也是混合云了，做到了什么地步呢？ 在数据层面，家中机器和linode以及gcp公有云全通，任意地点的客户端可以通过局域网地址访问家中和公有云，而这一且，只需要一个公网IP。那么如何组建呢？

1. **选择一个趁手的VPN**，这里我使用的是SoftEther，全平台全功能，图形界面客户端全都有，自带域名反向解析，自带公网，又那么稳定，为啥不用。 只要在家中的一台PC上装好服务端，把5555端口通过路由器（有公网IP）映射出去即可完成VPN服务器的搭建。

2. **Linode服务器集群选择一台作为网关（边界路由器）**，负责作为客户端接入VPN服务器，那么它就有了192.168.0段的地址，其它机器上因为linode没有VPC的概念，所以得加条到192.168.0.0/24的路由。

3. 总结下来，接入到home的VPN服务器会给所有客户端一个home的IP地址，然后加的路由表（使linode集群的10段暴露出去）都围绕这个地址展开达到互通的目的。

4. 目前我把linode上分散在全球8个机房的私有服务器都加了进来，当然，安全线路。尝试了一次kcptun加速VPN连接，但是linux下失败了，windows成功。

这是图，PC-Server为VPN服务器，LINODE为公有云，也加入了Google的公有云进去（没画），Windows-PC为个人服务器。

[![](/blog/images/homebasedhybridcloud-1.png)](https://blog.lofyer.org/wp-content/uploads/homebasedhybridcloud-1.png)

联通之后，Google的CDN、DNS可以混合到Linode去使用了，再展开点，大数据、数据库都可以结合Linode去跑了。