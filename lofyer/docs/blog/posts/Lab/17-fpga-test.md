---
title: "fpga-test"
date: 2018-05-23
categories: 
  - "fpga"
---

## "PetaLinux/Pynq中使用QEMU（裸金属探索的路子）" "2019-08-22"

### Background

Before stepping into the virtio acceleration, we will build and run a Linux(PetaLinux) in FPGA.

Despite the poor performance of simulation in X86, it's very convenient to create POC.

We choose MPSoC zcu106(PS) to test ARM, Zynq zc702(PS) to test ARM and Kintex UltraScale kcu705(PL) to test MicroBlaze softcore.

### 0. Essential tools

OS: Ubuntu 16.04 Desktop

Vivado: 2019.1

Xilinx SDK: 2019.1

PetalLinux SDK:  PetaLinux 2019.1

BSP: Evaluation Board BSP or generated from Vivado/Xilinx SDK

### 1. Build QEMU

QEMU is in the PetaLinux SDK directory, it's not necessary to rebuild if you do not wanna modify anything.

git clone git://github.com/Xilinx/qemu.git
cd qemu
apt install libglib2.0-dev libgcrypt20-dev zlib1g-dev autoconf automake libtool bison flex libpixman-1-dev
git submodule update --init dtc
./configure --target-list="aarch64-softmmu,microblazeel-softmmu" --enable-fdt --disable-kvm --disable-xen
make -j4

 

### 2. PetaLinux Project

#### 2.1. Create Project

petalinux-create -t project -n zcu106\_arm\_a53 --template zynqMP -s ../bsp/xilinx-zcu106-v2019.1-final.bsp
petalinux-config # Decide what to build

 

After the project created, pre-built images is in directory _pre-built_. You can test it via petalinux-boot

petalinux-boot --qemu --prebuilt 3 # Start ZCU106 virtual machine with prebuilt kernel

 

To accelerate the sstate mirror check process, you need download the sstate cache in Ref [5].

If you are using petalinux as **root**, you need modify _sanity.conf_ like this:

cat /opt/petalinux/v2019.1/components/yocto/source/aarch64/layers/core/meta/conf/sanity.conf

```
# Sanity checks for common user misconfigurations
#
# See sanity.bbclass
#
# Expert users can confirm their sanity with "touch conf/sanity.conf"
BB\_MIN\_VERSION = "1.39.1"

SANITY\_ABIFILE = "${TMPDIR}/abi\_version"

SANITY\_VERSION ?= "1"
LOCALCONF\_VERSION  ?= "1"
LAYER\_CONF\_VERSION ?= "7"
SITE\_CONF\_VERSION  ?= "1"

#INHERIT += "sanity"
```

#### 2.2. Build Kernel/Rootfs

 

### 3. Boot from QEMU

#### 3.1. Debug

petalinux-boot --qemu --prebuilt 3

 

#### 3.2. Run with self-compiled-QEMU

#!/bin/bash
/root/Desktop/qemu\_zynq\_devices/qemu/aarch64-softmmu/qemu-system-aarch64 \\
-M arm-generic-fdt-7series -machine linux=on -smp 2 -m 1G \\
-serial /dev/null -serial mon:stdio -display none \\
-kernel /blog/images/linux/uImage -dtb /blog/images/linux/system.dtb

Now we'll add sdcard and try to boot Ubuntu Core OS.

qemu-img create qemu\_sd.img 2G
-M arm-generic-fdt-7series -machine linux=on -smp 2 -m 1G \\
-serial /dev/null -serial mon:stdio -display none \\
-kernel /blog/images/linux/uImage -dtb /blog/images/linux/system.dtb

 

### 4. Co-Sim

 

### 5. Boot from Evaluation Board SDCard

Ref:

[1] Build QEMU: https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842060/QEMU

[2] Zynq UltraScale+ QEMU: https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18841606/QEMU+-+Zynq+UltraScalePlus

[3] CoSim: https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18842109/QEMU+SystemC+and+TLM+CoSimulation

[4] Xilinx Evaluation Board BSP files and Yocto local mirror: https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/embedded-design-tools.html

---

## "Xilinx Pynq的overlay网络测试" "2020-04-14"

### 1. Preparation

[https://github.com/Xilinx/PYNQ-Networking](https://github.com/Xilinx/PYNQ-Networking)

### 2. Build