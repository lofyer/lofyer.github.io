---
title: "Porting KVM to NVIDIA Jetson Nano"
date: "2019-04-14"
categories: 
  - "cloud"
  - "nvidia-jetson-nano"
---

因为Jetson如果作为边缘设备，那么我们需要进一步探索虚拟化在其上的可能性，从而使FT有更容易的路线可走，还有既然它的芯片是PCIE的，那理应可以透传。

参考：[https://elinux.org/Jetson/Nano/Upstream](https://elinux.org/Jetson/Nano/Upstream)

什么build rootfs、uboot之类的就不要了，那是后期嵌入式的活，我们在现有环境上build kernel即可。

# 1\. 准备环境

访问链接[https://developer.nvidia.com/embedded/downloads](https://developer.nvidia.com/embedded/downloads)并下载源码包，包括Jetson自有以及L4T源码，也可以点击如下链接直接下载。

https://developer.nvidia.com/embedded/dlc/l4t-sources-32-1-jetson-nano

解压其中的kernel部分。

https://developer.nvidia.com/embedded/dlc/l4t-jetson-driver-package-32-1-jetson-nano

下载并解压后，得到如下文件系统。

# 2\. 准备kernel

Host:
sudo su
sudo apt install nfs-kernel-server
sudo echo "/home/lofyer/Downloads \*(rw,no\_root\_squash,no\_subtree\_check)" >> /etc/exports
sudo exportfs -avf

Jetson Nano:
sudo su
apt instlal libncurses-dev
mount root@192.168.0.59:/home/lofyer/Downloads /mnt
cd /mnt/
cp /proc/config.gz .
gunzip config.gz
mv config .config
make menuconfig # find and enable kvm, tegra hypervisor
make -j4; make -j4 modules\_install
make -j4 Image
cp arch/arm64/boot/Image /boot/Image-kvm

然后编辑启动项，默认从新kernel启动。

\# vi /boot/extlinux/extlinux.conf

TIMEOUT 10
DEFAULT secondary

MENU TITLE p3450-porg eMMC boot options

LABEL primary
      MENU LABEL primary kernel
      LINUX /boot/Image
      INITRD /boot/initrd
      APPEND ${cbootargs} rootfstype=ext4 root=/dev/mmcblk0p1 rw rootwait

LABEL secondary
      MENU LABEL kernel with kvm
      LINUX /boot/Image-kvm
      INITRD /boot/initrd
      APPEND ${cbootargs} rootfstype=ext4 root=/dev/mmcblk0p1 rw rootwait

# 3\. 尝试qemu-kvm

自带的：

apt install qemu-kvm
kvm --help

自己编的：

git clone https://github.com/qemu/qemu
cd qemu
./configure --enable-kvm
make -j4

只能使用machine类型为arm进行加速。

# 4\. 看看FT

算了，现在不看了，等下半年。
