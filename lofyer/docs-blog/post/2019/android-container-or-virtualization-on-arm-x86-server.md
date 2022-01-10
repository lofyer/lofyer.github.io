---
title: "Android Container or Virtualization on ARM/X86 Server"
date: "2019-11-17"
categories: 
  - "cloud"
---

# 0\. 背景

随着国产化进程的推进，相当的应用已经可在国产化服务器（ARM/X86）上运行，本文将使用容器以及虚拟化两种技术对ARM/X86服务器上运行高性能的Android桌面进行探索。

# 1\. X86服务器

## 1.1. 虚拟化

## 1.2. 容器

 

# 2\. ARM服务器

## 2.1. 虚拟化

## 2.2. 容器

Docker-Android

Anbox(LXC)

Xdroid

# 3\. 桌面协议

由于qemu的ARM模拟的VGA设备由于其天生架构问题，不能正常使用，因而暂时需要使用virtio-vga设备方可显示（https://www.linux-kvm.org/images/0/09/Qemu-gfx-2016.pdf）。

## 3.1. 带内协议

## 3.2. 带外协议
