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

由于qemu的ARM模拟的VGA设备由于其天生架构问题，不能正常使用，因而暂时需要使用virtio-vga设备方可显示（https://www.linux-kvm.org//blog/post/images/0/09/Qemu-gfx-2016.pdf）。

## 3.1. 带内协议

VNC

SPICE

RDP/ICA

PCoIP

## 3.2. 带外协议

VNC

SPICE

PCoIP