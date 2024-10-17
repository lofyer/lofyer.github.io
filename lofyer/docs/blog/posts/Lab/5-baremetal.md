---
title: "裸金属笔记"
date: 2021-02-25
categories: 
  - "cloud-infra"
  - "devices"
---

# 裸金属笔记

裸金属相关，简单点就是尽量不用计算虚拟化。

---

## 动手做自己的Nitro SmartNIC(FPGA/ARM-based Bare Metal Hypervisor(2021-02-25)

### 0. BackGround

裸金属服务很酷，甲方都想要，但是最好不要有绑定。BlueField 1/2、AWS Nitro、Aliyun X-Dragon MOC之类的智能网卡也不错，虽然ZStack已经纯软件实现了类似功能，但是这篇文章仍然会探索一个如何低成本（不到100块）的“智能网卡”方案（要不然前期的调研都白瞎了）。

**做弹性裸金属并不难，但是要高性能的同时又要安全，而且在私有云、产品化的语境下，我们就需要好好思考一番了。**

### 1. 功能定义

#### 1.1. 带外管理

带外管理也不是个问题，如果:

1. 服务器自带BMC。
2. 你有能力让服务器厂商给你定制，因为你需要定制BIOS部分功能以让其在启动时认到智能网卡提供的虚拟设备。
3. 自己做OpenBMC。

但是，由于多数用户没有让服务器厂商高度定制服务器的能力，因而我们需要在“无米”的状态下做“炊”。

##### 1.1.1. 电源管理

通过“智能网卡”我们至少要能实现主机的开关机，这个相对来说还比较容易实现，无论何种主板，电源控制接口现在服务器上都有，若干个高/低电平就可以控制。

但是考虑到集群断电的情况，“智能网卡”要优先启动才能进一步控制它管理的物理机，所以这个设备我们一定要轻、快、稳。

如果使用服务器内PCIE供电，那么我们又要修改BIOS来保证智能网卡优先启动，但是这个。。。成本不止100块了。

所以考虑使用独立供电的嵌入式设备，无论使用插到服务器PCIE插槽上的ARM/FPGA/X86 SoC还是树莓派，我们都要给它独立供电。

##### 1.1.2. 与主机通信

这里的通信仅仅涉及控制面的内容，数据面（网络、存储）不涉及。

我们尊重传统，仅考虑两种情况，即与主机OS通信和与主机设备通信。

前者一个agent走TCP/IP就能搞定，后者我们需要与主板的I3CI2C/SPI接口通信。

#### 1.2. 设备模拟/透传

*存储*

存储侧我们目前可以直接提供的baremetal设备有 SCSI（iSCSI）、NVMe（oF）、VirtIO等。

由于产品化必须考虑到适配各种硬件的问题，所以我们不会定制修改BIOS，这里只要一个小小的trick即可让服务器从智能网卡提供的vda或者其他接口的“虚拟”硬盘启动，从而保证云平台存储资源可以以较高性能提供给baremetal。

??? "Option Rom"

    Option Rom 也是智能网卡带的选项了，启动后可以直接认到virtio/nvme的虚拟硬盘。

*网络*

网络多数情况下是问题但又不是问题，我们选择智能网卡的原因是因为它可以解决大多数问题。虚拟网络资源直接给物理机的目的可以很容易地通过SDN交换机实现，但是我们既然用了一个智能网卡设备那么就应该通过它来回避各种外部环境仍然需要定制的问题。以BF2为例，它内部集成的ovs-dpdk以及Mellanox传统的内部虚拟交换技术加持，我们可以给baremetal一个“虚拟”接口，但是所有流量都在云平台的管控下，用户极难自行修改。

如果不选择智能网卡，那么只有SDN交换机和系统内SDN两种方案，SDN交换机甚至比智能网卡的适配都简单，技术不是难点，同智能网卡一样你很难说服客户的采购来一套裸金属专用的SDN交换机，更不要说这个交换机不在他们的网工管辖范围内了。

那么只剩系统内的overlay网络接入方案了，Linux一直不是问题，Windows系统需要agent做一些小工作才可以，即使用户自己改了，我们也可以“内挂”anti-spoofing。

#### 1.3. Hypervisor模拟

“智能网卡”中要模拟hypervisor，这里的模拟是为了让多数云平台的agent比如libvirt或者基于libvirt的定制agent能够对弹性裸金属设备的调用行为与虚拟机保持一致。为什么要做这个工作呢，我单独的控制面不行吗？

也行，但是不酷，更何况你后续的存储与网络设备不仅给裸金属也要给虚拟机，两套API两套数据库管理同一个网络/存储就是在给云平台的管控面开发找事儿。

所以这个我们一般要对agent（libvirt）做一些修改，这个难度相比下来不大。

### 2.1. Proof of Concept

#### 2.1. ARM or X86作为智能网卡

如果使用ARM或者X86主机作为“智能网卡”，那么存储方案有iSCSI和NVMe-over-TCP，网络方案目前看只有系统内SDN的方法了，好处在于“智能网卡”的门槛低。

我们先做一个OpenBMC的原型机，准备如下材料:

笔记本;
树莓派/NVIDIA Jeston;
视频采集卡;
杜邦线;
网线；
rtl-sdr（谁能拒绝一个可以听广播看电视的裸金属呢）；

当然，一台服务器也是必要的。。。不会吧，你家里连台服务器都没有？你还做锤子钢铁侠哦？赶紧上咸鱼500块买个二手服务器吧，不想做钢铁侠了还可以卖掉。

算了，毕竟太吵，一个ATX或者其他类型主板的台式机就可以了。

![](/blog/images/WechatIMG2-1024x768.jpeg)

TBD

#### 2.2. FPGA ARM SoC作为智能网卡（有点小贵，1000多块，但是来都来了）

1000多一块的有PCI-E插槽的Xilinx ARM SoC开发版，贵，但是你可以基于PCI/PCI-E接口实现独立的网络、存储，非常有意思，但是难度在于FPGA编程。

不过还好，我们先尝试用Pynq写一些简单的PCI/PCI-E通信，然后再堆上复杂的，但是中间会有很多IP我们要购买，没钱怎么整，社区找现成的，low点，能用。

因为我们的目标是要基于这个FPGA实现存储和网络设备的接入，那么总归是一个大点的工程，如果做的先进点实现virtio网络/存储的接入，那对于提供虚拟化的云平台将善莫大焉。

---

## "Windows中使用overlay网络的一些方案" "2020-05-21"

### 引言

21世纪了，智能网卡还是贵，交换机客户没法直接买，软件技术栈都好找，性能再加强，ebpf不错，要是Windows的Linux子系统里支持这个那软裸金属方案至此可以画上一个句（分）号了。

关于如何在Windows裸金属中给用户交付流畅且易配置的云平台overlay网络，从而跟虚拟化打通，也是个最近要考虑的点。

### SDN交换机与智能网卡

我们可以用国内品牌，他们也提供ovs接口，but，对于小体量的客户或者采购严格的客户就很难，仅适合自有IDC且有决心的客户。

为了使用一个功能，软件绑定就算了，硬件你还要我买你的，固定资产无法二次利用，别想了。

### GRE/VXLAN(Windows)

Windows从08/12后自带了NVGRE，VXLAN的话是从16开始支持，听起来也不错，所以这个对于新晋用户是**可以接受的**。

### OVS

Windows下有ovs，但是仅支持2012后的，这样就没法解决Windows2008的问题（再过几年就不用操心这个问题了），当然主要的问题还是没有官方背书。

---

## "使用Xilinx/Intel FPGA加速虚拟化" "2019-04-29"

UPDATE: [老王的笔记](http://tech.mytrix.me/2019/05/devconf-19-virtio-%E7%A1%AC%E4%BB%B6%E5%8A%A0%E9%80%9F/)中也总结了一些关于devconf的内容。

This article is just a collection of ideas and posts.

Recently I was doing some performance-tunning of QEMU/KVM with kinds of pure-software ways. However, it ended with the existence of QEMU/KVM's process load.

I just remembered that I used to do some co-sim work with National Instruments LabView about ten years ago...(during college life...fk...I'm still young...)

### Trial No.1

TBD: Start QEMU instance(s) with passthrough-ed PCI(PCI-SRIOV) devices like ethernet controller or NVMe controller designed in PCI FPGA to offload the emulated works.

### Trial No.2

TBD: Start QEMU instance with devices ported to passthrough-ed PCI FPGA as many as possible, i.e. usb controller, ethernet controller and etc..(Like a dock with kinds of devices...)

But I think it will be replaced by Trial No.1.

### Trial No.3

TBD: Co-Sim.

### Trial No.4

SoC FPGA, as DOM0.

### Trial No.5

ASIC offload with slight host management.

### References

[1] Running Xilinx in QEMU, [https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842060/QEMU](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842060/QEMU)

[2] Building Xen Hypervisor with Petalinux 2019.1, [https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/99188792/Building+Xen+Hypervisor+with+Petalinux+2019.1](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/99188792/Building+Xen+Hypervisor+with+Petalinux+2019.1)

[3] QEMU SystemC and TLM CoSimulation, [https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842109/QEMU+SystemC+and+TLM+CoSimulation](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842109/QEMU+SystemC+and+TLM+CoSimulation)

---

## "Windows中使用eBPF" "2022-02-10"

### 背景

也挺有意思的，前年念叨着[Windows内的SDN方案咋做](https://blog.lofyer.org/sdn-windows-linux-baremetal/)，去年微软终于就搞了个[eBPF for Windows](https://github.com/Microsoft/ebpf-for-windows)，如此以来结合Windows新版本自带的NVGRE/VxLAN网络，安全组、EIP、LB啥的在纯软裸金属里都好说了。

另外，迫于适配智能与半智能网卡的压力，ZStack已经提供了ovs与linux bridge两个技术栈的网络（新建集群的时候可以选，虽然不是第一个但早些时候收到的需求确实不硬开发也忙。。），所以现在看起来一切都很合理。

当然，产品的东西跟新技术并不是什么强绑定关系，技术只是锦上添花实现产品的工具，稳定优先。

### 测试

## 编译eBPF for Windows

## 测试程序

## VxLAN/NVGRE集成
---
title: "Xilinx FPGA中应用P4网络数据面编程"
date: 2019-07-11
categories: 
  - "cloud-infra"
  - "devices"
---

# 0. Background

As we all know, P4 is about to be the future of OpenFlow 2.0, and to achieve an totally software defined network with programmable control plane and data plane.

# 1. Basic Knowledge

Since we've got P4 working on X86 server

[![](/blog/images/WechatIMG1-768x1024.jpeg)](https://blog.lofyer.org/wp-content/uploads/WechatIMG1.jpeg)

In SDN controller ONOS.

# 2. Software/Hardware Plant

## 2.1. Pureway: P4 bitstream

## 2.2. Easyway: P4 on PetaLinux

# 3. Further Exploration
