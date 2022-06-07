---
title: "使用Xilinx/Intel FPGA加速虚拟化"
date: "2019-04-29"
categories: 
  - "cloud-infra"
  - "devices"
tags:
  - "FPGA"
  - "Bare Metal"
---

UPDATE: [老王的笔记](http://tech.mytrix.me/2019/05/devconf-19-virtio-%E7%A1%AC%E4%BB%B6%E5%8A%A0%E9%80%9F/)中也总结了一些关于devconf的内容。

This article is just a collection of ideas and posts.

Recently I was doing some performance-tunning of QEMU/KVM with kinds of pure-software ways. However, it ended with the existence of QEMU/KVM's process load.

I just remembered that I used to do some co-sim work with National Instruments LabView about ten years ago...(during college life...fk...I'm still young...)

# Trial No.1

TBD: Start QEMU instance(s) with passthrough-ed PCI(PCI-SRIOV) devices like ethernet controller or NVMe controller designed in PCI FPGA to offload the emulated works.

# Trial No.2

TBD: Start QEMU instance with devices ported to passthrough-ed PCI FPGA as many as possible, i.e. usb controller, ethernet controller and etc..(Like a dock with kinds of devices...)

But I think it will be replaced by Trial No.1.

# Trial No.3

TBD: Co-Sim.

# Trial No.4

SoC FPGA, as DOM0.

# Trial No.5

ASIC offload with slight host management.

# References

\[1\] Running Xilinx in QEMU, [https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842060/QEMU](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842060/QEMU)

\[2\] Building Xen Hypervisor with Petalinux 2019.1, [https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/99188792/Building+Xen+Hypervisor+with+Petalinux+2019.1](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/99188792/Building+Xen+Hypervisor+with+Petalinux+2019.1)

\[3\] QEMU SystemC and TLM CoSimulation, [https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842109/QEMU+SystemC+and+TLM+CoSimulation](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842109/QEMU+SystemC+and+TLM+CoSimulation)
