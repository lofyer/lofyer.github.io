---
title: "Create a dummy VGA adpter"
date: "2013-12-19"
categories: 
  - "others"
tags:
  - "VDI"
---

创建一个“虚拟”显示适配器，适用于那些“你知道你想干什么并且在干什么”的人。 原文参考 [http://www.geeks3d.com/20091230/vga-hack-how-to-make-a-vga-dummy-plug/](http://www.geeks3d.com/20091230/vga-hack-how-to-make-a-vga-dummy-plug/) [http://blog.zorinaq.com/?e=11](http://blog.zorinaq.com/?e=11)

手头有两块ATI显卡，而且都有这个DVI-I接口，并且目前我的状况是两个显示器都接到了两个主板显示（集显）接口。 [![sldldvi](/blog/images/sldldvi.jpg)](http://blog.lofyer.org/2013/12/create-dummy-vga-adpter/sldldvi/) 原文说需要一个75欧姆的电阻，我手里暂时只有100欧姆的，而且不多，如果强烈按照原作者的意思的话效果可能会有所不同，参考现有任意款VGA控制器原理。 \[连接示意图\] 来自第一个链接 [![vga_dummy_electronic_schema](/blog/images/vga_dummy_electronic_schema.jpg)](http://blog.lofyer.org/2013/12/create-dummy-vga-adpter/vga_dummy_electronic_schema/) \[效果图\] 请无视我的背景，如果有人好奇的话，那是上篇文章的eZ430以及一包十块白沙。 [![QQ图片20131220001003](/blog/images/QQ图片20131220001003-757x1024.jpg)](http://blog.lofyer.org/2013/12/create-dummy-vga-adpter/qq%e5%9b%be%e7%89%8720131220001003/) \[结果\] [![QQ截图20131220001602](/blog/images/QQ截图20131220001602.png)](http://blog.lofyer.org/2013/12/create-dummy-vga-adpter/qq%e6%88%aa%e5%9b%be20131220001602/)

看见第3个显示器没？ 就是它了，你可以拿它做很多事，比如你有多块显卡，并且你想充分利用其性能，参考lucid mvp，效果上类似吧。---
title: "Gpu Passthrough in Xen"
date: "2013-07-22"
categories: 
  - "cloud-infra"
---

Considering that Xen's gpu passthrough is about one year ahead of kvm, so let us try that. The process is something like kvm one as I wrote before. [KVM GPU-Passthrough, VGA-Passthrough](http://blog.lofyer.org/2013/05/pass-host-gpu-to-guest-via-qemu-ncursescurses/ "KVM GPU-Passthrough, VGA-Passthrough")

## Result: I failed in trying passthrough the graphic card to Win7 or XP. Now I'm try some patchs and re-compile Xen with my graphic card bios.bin.

HOST: CPU: i5-3470 GPU: ATI Radeon HD 7850 OS: openSuse 12.3 KDE

## 0\. Disable Radeon Driver

# echo "blacklist radeon" >> /etc/modprobe.d/modprobe.conf
# mkinitrd

## 1\. Install Xen-utils

Install Xen and its utilities via Yast.

## 2\. Modify grub.cfg

...
 echo    'Loading Xen 4.3.0\_06-1.3 ...'
        multiboot       /xen-4.3.0\_06-1.3.gz placeholder iommu=1 
        echo    'Loading Linux 3.10.1-3.g0cd5432-xen ...'
        module  /vmlinuz-3.10.1-3.g0cd5432-xen placeholder root=UUID=da6ac064-2a5e-48c7-8834-307cbcb551c3 ro   resume=/dev/disk/by-id/ata-ST500DM002-1BD142\_Z3THR3V3-part3 splash=silent quiet showopts xen-pciback.hide=(00:00.0)(01:00.1)
        echo    'Loading initial ramdisk ...'
        module  /initrd-3.10.1-3.g0cd5432-xen
...

Then reboot the host.

## 3\. Bind the device

### Using Scripts

File: bind\_lib.sh

   #!/bin/bash
   #
   # License: GPLv2
   # Author: Peter Maloney
   #
   # Script to bind devices to pciback (or pci-stub)
   
   find\_new\_id() {
       device="$1"
       len=${#device}
       if \[ "$len" -eq 12 \]; then
           device="${device:5:12}"
       fi
       lspci -n | grep "${device}" | cut -d' ' -f3 | sed -r "s/:/ /"
   }
   
   bindstub() {
       device="$1"
       echo "binddevice $device"
       
       if \[ ! -e "/sys/bus/pci/devices/$device" \]; then
           echo "    ERROR: Device does not exist... cancelling"
           return
       fi
       
       # with pci-stub, you do new\_id, then unbind, then bind
       
       echo "create new\_id"
       chmod +w /sys/bus/pci/drivers/pci-stub/new\_id
       new\_id="$(find\_new\_id "$device")"
       echo "    echo \\"$new\_id\\" > /sys/bus/pci/drivers/pci-stub/new\_id"
       echo "$new\_id" > /sys/bus/pci/drivers/pci-stub/new\_id
       
       echo "unbind"
       if \[ ! -e "/sys/bus/pci/devices/$device/driver" \]; then
           echo "    no driver to unbind"
       else
           chmod +w "/sys/bus/pci/devices/${device}/driver/unbind"
           echo "    echo -n \\"$device\\" > \\"/sys/bus/pci/devices/$device/driver/unbind\\""
           echo -n "$device" > "/sys/bus/pci/devices/$device/driver/unbind"
       fi
       
       echo "bind"
       chmod +w /sys/bus/pci/drivers/pci-stub/bind
       echo "    echo -n \\"$device\\" > /sys/bus/pci/drivers/pci-stub/bind"
       echo -n "$device" > /sys/bus/pci/drivers/pci-stub/bind
       
       echo
   }
   
   bindback() {
       device="$1"
       echo "binddevice $device"
       
       if \[ ! -e "/sys/bus/pci/devices/$device" \]; then
           echo "ERROR: Device does not exist... cancelling"
           return
       fi
       
       # with pciback, you do unbind, then new\_slot, then bind
       
       echo "unbind"
       if \[ ! -e "/sys/bus/pci/devices/$device/driver" \]; then
           echo "    no driver to unbind"
       else
           chmod +w "/sys/bus/pci/devices/${device}/driver/unbind"
           echo "    echo -n \\"$device\\" > \\"/sys/bus/pci/devices/$device/driver/unbind\\""
           echo -n "$device" > "/sys/bus/pci/devices/$device/driver/unbind"
       fi
       
       echo "create new\_slot"
       chmod +w /sys/bus/pci/drivers/pciback/new\_slot
       echo "    echo -n \\"$device\\" > /sys/bus/pci/drivers/pciback/new\_slot"
       echo -n "$device" > /sys/bus/pci/drivers/pciback/new\_slot
       
       echo "bind"
       chmod +w /sys/bus/pci/drivers/pciback/bind
       echo "    echo -n \\"$device\\" > /sys/bus/pci/drivers/pciback/bind"
       echo -n "$device" > /sys/bus/pci/drivers/pciback/bind
       
       echo
   }

# source bind\_lib.sh
# modprobe xen-pciback
# bindback "0000:01:00.0"
# bindback "0000:01:00.1"

### Using xen-tools

# xl pci-assignable-add 01:00.0
# xl pci-assignable-add 01:00.1
# xl pci-attach Domain-0 01:00.0
# xl pci-attach Domain-0 01:00.1

## 4\. Create VM

File: win7

name="win77"
description="None"
uuid="db64c1a6-1f84-4eff-48df-53c4b03b389e"
memory=2048
maxmem=2048
vcpus=4
on\_poweroff="destroy"
on\_reboot="destroy"
on\_crash="destroy"
localtime=1

builder="hvm"
device\_model="/usr/lib/xen/bin/qemu-dm"
kernel="/usr/lib/xen/boot/hvmloader"
boot="dc"
disk=\[ 'file:/var/lib/xen//blog/images/windowsvistax64/disk0.raw,hda,w','file:/home/lofyer/Driver.iso,hdc:cdrom,r' \]
#vif=\[ 'bridge=virbr0, mac=00:12:3e:6c:2f:02, model=rtl8139' \]

stdvga=1
vnc=1
vnclisten="0.0.0.0"
vncconsole=0
#vncunused=1
soundhw="all"

monitor=1
viridian=1
sdl=0
#usb=1
acpi=1
apic=1
pae=1

#usbdevice='tablet'

serial="pty"
# Add by lofyer
device\_model\_version = "qemu-xen-traditional"
gfx\_passthru=0
pci\_power\_mgmt=1
xen\_platform\_pci=1
pci\_msitranslate=1
hap=1
pci = \[ '01:00.0' , '01:00.1'  \]

# xl create win7 # remote-viewer vnc://localhost:5900

Then you should see Windows boot logo in vnc for few seconds, after that it will switch to the second monitor that attached to your ATI card.

### gfx\_gpupassthru

As Xen wiki said:

> When you specify "gfx\_passthru=1" the passthru graphics card will be made the primary graphics card in the VM, and the Xen Qemu-dm emulated Cirrus graphics card is disabled. If you use "gfx\_passthru=0", or don't have gfx\_passthru= option at all, then the Xen Qemu-dm emulated Cirrus graphics card will be the primary in the VM, and the passthru graphics card will be secondary.

So, alter gfx\_passthru to 1.
---
title: "mingw编译remoteviewer"
date: "2012-11-17"
categories: 
  - "cloud-infra"
---

**UPDATE 2015.3.18 fedora 21中编译： # yum install mingw\* libtool\* auto-buildrequires intltool glib2-devel icoutils msitools # ./autogen.sh # mingw64-configure # mingw64-make -j4 # rpmbuild -bb mingw-virt-viewer.spec 编译msi过程中，可能会提示缺少storageencryption.rng，从以下地址下载即可。 https://raw.githubusercontent.com/wido/libvirt/master/docs/schemas/storageencryption.rng**

在fedora环境中，需要的有spice-gtk，libusbx，usbredir，remote-review，全部下载最新git源 spice-gtk，usbredir http://cgit.freedesktop.org/spice libusbx git://github.com/libusbx/libusbx.git remote-viewer git.fedorahosted.org/git/virt-viewer.git

libusbx和usbredir：

mingw32-configure;mingw32-make;mingw32-make install

spice-gtk： mingw32-configure --with-sasl=no --with-audio=gstreamer --enable-smartcard=no --with-gtk=2.0 --without-python 因为virtviewer要gtk2.0的，所以这也就2.0的；mingw32下没找到pygtk，图省事儿就不要python了，其他的缺啥下啥

mingw32-make
mingw32-make install

virt-viewer：

mingw32-configure
mingw32-make
mingw32-make install

生成remote viewer.exe

安装包 运行nsiswrapper生成windows安装包

nsiswrapper --run 
    --name "Virt-Viewer" 
    --outfile "Virt-Viewer-for-Windows.exe" 
    --with-gtk 
    /usr/i686-pc-mingw32/sys-root/mingw/bin/virt-viewer.exe

缺少的dll从网上下或者windows环境中安装的virtviewer加到$PATH中 这样会缺少三个xml文件，在virtviewer/src里，直接copy过去

export PATH=$PATH:/usr/i686-pc-mingw32/sys-root/mingw/bin:.

windows client中使用usbredir： 需要libwdi或者zadig或者usbclerk [链接](http://lists.freedesktop.org/archives/spice-devel/2012-November/011629.html "usbredir in windows client")

todo: 只有libvirt不是最新的了，可是目前来看没影响
---
title: "NVIDIA GRID vGPU and KVMGT for KVM"
date: "2017-10-25"
categories: 
  - "cloud-infra"
---

This article will tell you how to use NVIDIA vGPU and KVMGT in KVM hypervisor with mdev. I'll use NVIDIA M60 and RHEL 7.4 for testing.

# 1\. NVIDIA vGPU

## 1.1. Prepare drivers and utilities

## 1.2. Create vGPU with command line

## 1.3. Passthrough vGPU to vm

# 2\. KVMGT

## 2.1. Prepare drivers and utilities

## 2.2. Create vGPU with command line

## 2.3. Passthrough vGPU to vm

# 3\. AMD vGPU

## 3.1. Prepare drivers and utilities

## 3.2. Create vGPU with command line

## 3.3. Passthrough vGPU to vm

# Ref:

[https://www.kraxel.org/blog/tag/vgpu/](https://www.kraxel.org/blog/tag/vgpu/)
---
title: "QEMU 3D"
date: "2016-12-29"
categories: 
  - "cloud-infra"
---

Except from virgl(virtio-vga), KVMGT/XenGT, CUDA vGPU, I will introduce you another way of using 3D app in QEMU, c'est la [XQEMU](http://xqemu.com/)

[Download prebuilt EmuCR-XQEMU-20170109](https://blog.lofyer.org/wp-content/uploads/EmuCR-XQEMU-20170109.7z)

[Download games!](https://www.emuparadise.me/roms-isos-games.php)

As you can see, this is a emulator forked from QEMU to let you playing XBOX games in PC.(not interested though...)

## Get XQEMU

git clone https://github.com/espes/xqemu.git

## [](https://github.com/espes/xqemu/wiki/Getting-Started#compile-xqemu)Compile XQEMU

### [](https://github.com/espes/xqemu/wiki/Getting-Started#for-mac-os-x)For Mac OS X

./configure --cc=clang --enable-opengl --disable-vnc --disable-user --target-list=xbox-softmmu --extra-cflags="-march=native" --enable-debug
make

### [](https://github.com/espes/xqemu/wiki/Getting-Started#for-windows)For Windows

1. Install MSYS2 x86\_64: Follow all the steps on [http://msys2.github.io/](http://msys2.github.io/)
2. Open the "MinGW-w64 Win64 Shell" and run:
    
    pacman -S git python2 make autoconf automake-wrapper mingw-w64-x86\_64-libtool mingw-w64-x86\_64-gcc mingw-w64-x86\_64-pkg-config mingw-w64-x86\_64-glib2 mingw-w64-x86\_64-glew mingw-w64-x86\_64-SDL
    git clone https://github.com/espes/xqemu
    cd xqemu
    git submodule update --init pixman
    
    ./configure --cc=gcc --python=python2 --enable-opengl --disable-vnc --disable-user --target-list=xbox-softmmu --extra-cflags="-march=native" --enable-debug
    make
    

**There's a bug with timers on Windows that makes it run much slower than it should. YOU should fix it for me.**

### [](https://github.com/espes/xqemu/wiki/Getting-Started#for-linux)For Linux

#### [](https://github.com/espes/xqemu/wiki/Getting-Started#requirements)Requirements:

- [Everything QEMU requires](http://wiki.qemu.org/Hosts/Linux)
- OpenGL with GLX and GLEW

For **Debian** most build dependencies can be installed by running:

apt-get build-dep qemu
apt-get install libglew-dev libtxc-dxtn-s2tc0

#### [](https://github.com/espes/xqemu/wiki/Getting-Started#building)Building:

./configure --python=python2 --enable-opengl --disable-vnc --disable-user --enable-kvm --disable-xen --audio-drv-list=alsa --target-list=xbox-softmmu --extra-cflags="-march=native" --disable-werror --enable-debug 
make

## [](https://github.com/espes/xqemu/wiki/Getting-Started#run-xqemu)Run XQEMU

XQEMU is a Low-Level Emulator, so you need to find a copy of the stuff the Xbox runs when it turns on:

#### [](https://github.com/espes/xqemu/wiki/Getting-Started#xbox-mcp-x-boot-rom)Xbox MCP-X boot rom

MD5 (mcpx\_1.0.bin) = d49c52a4102f6df7bcf8d0617ac475ed

If your mcpx dump has an MD5 of 196a5f59a13382c185636e691d6c323d you dumped it badly and it's a couple bytes off. It should start with 0x33 0xC0 and end with 0x02 0xEE.

#### [](https://github.com/espes/xqemu/wiki/Getting-Started#xbox-10-compatible-bios-cromwell-3944-4034-4036-)Xbox 1.0 compatible bios (cromwell, 3944, 4034, 4036, ...)

You can use a retail bios _or_ a modified 'debug' bios. Just like a real xbox, running a retail bios will not boot unofficial software.

##### [](https://github.com/espes/xqemu/wiki/Getting-Started#debug-bios)Debug BIOS

People have reported success with the 'COMPLEX 4627' modified debug bios. It's convenient to note that this bios does _not_ necessarily require a _populated_ hard disk image to load an application from DVD (though an empty drive still needs to be attached), so you can skip the next step in some cases.

v1.0.2 1M dump: MD5 (Complex\_4627Debug.bin) = 19b5c6d3d42a707bba620634fe6d4baf

_or sometimes_

1MB dump: MD5 (complex\_4627debug.bin) = e8dd61cc6abdbd06aac185e371312dc1

##### [](https://github.com/espes/xqemu/wiki/Getting-Started#retail-bios)Retail BIOS

1M dump: MD5 (3944.bin) = e8b39b98cf775496c1c76e4f7756e6ed

_or sometimes_

256k dump: MD5 (3944.bin) = 542c62cb976a4993c8c5027dff9638ce

#### [](https://github.com/espes/xqemu/wiki/Getting-Started#xbox-dashboard-files-on-a-hard-disk-image)Xbox dashboard files on a hard disk image

Create an Xbox hard-disk image, using xboxhdm or otherwise:

- Create an [xboxhdm](http://sourceforge.net/projects/xboxhdm2/) cd-rom with the dashboard files
- Create a blank hard-disk file: `qemu-img create -f qcow2 xbox_harddisk.qcow2 8G`
- Run xboxhdm with qemu or something: `i386-softmmu/qemu-system-i386 -hda xbox_harddisk.qcow2 -cdrom linux.iso`

* * *

Launch XQEMU with something like...

xbox-softmmu/qemu-system-xbox -cpu pentium3 -machine xbox,bootrom=mcpx\_rom.bin -m 64 -drive file=xbox\_harddisk.qcow2,index=0,media=disk,locked=on -drive index=1,media=cdrom -bios xbox\_bios.bin -usb -device usb-hub,bus=usb-bus.0,port=3 -device usb-xbox-gamepad,bus=usb-bus.0,port=3.2

On Linux, you can use [KVM](http://www.linux-kvm.org/page/Main_Page) by passing `accel=kvm,kernel_irqchip=off` to -machine. This is not yet recommended.

To skip the Xbox logo animation you can pass `short_animation` to the -machine option (Example: `-machine xbox,short_animation,bootrom=mcpx_rom.bin`).

To run a game from a Xbox DVD image you must specify a `file` for the DVD device
---
title: "qemu uefi"
date: "2015-06-08"
categories: 
  - "cloud-infra"
---

Here's a collection of kinds of bioses.

[https://www.kraxel.org/repos/jenkins/](https://www.kraxel.org/repos/jenkins/)

Download **edk2.git-ovmf-x64\*.rpm**, and extract it:

(root)# rpm2cpi edk2.git-ovmf-x64-0-20150606.b1038.g5d832d6.noarch.rpm|cpio -mvid
./usr/share/doc/edk2.git-ovmf-x64-0
./usr/share/doc/edk2.git-ovmf-x64-0/License.txt
./usr/share/doc/edk2.git-ovmf-x64-0/README
./usr/share/edk2.git
./usr/share/edk2.git/ovmf-x64
./usr/share/edk2.git/ovmf-x64/OVMF-pure-efi.fd
./usr/share/edk2.git/ovmf-x64/OVMF-with-csm.fd
./usr/share/edk2.git/ovmf-x64/OVMF\_CODE-pure-efi.fd
./usr/share/edk2.git/ovmf-x64/OVMF\_CODE-with-csm.fd
./usr/share/edk2.git/ovmf-x64/OVMF\_VARS-pure-efi.fd
./usr/share/edk2.git/ovmf-x64/OVMF\_VARS-with-csm.fd
./usr/share/edk2.git/ovmf-x64/UefiShell.iso
22227 blocks

Run qemu with these fd bios files like this:

qemu --boot loader\_type=pflash,loader\_ro=yes,loader=OVMF\_CODE-pure-efi.fd,nvram\_template=OVMF\_VARS-pure-efi.fd XXX

or

qemu -bios OVMF-pure-efi.fd XXX
---
title: "Add usbdevice to libvirt by modifying cgroup"
date: "2013-10-31"
categories: 
  - "cloud-infra"
tags:
  - "Libvirt"
  - "Device Redirection"
---

CGROUP means Control group. In my case, usb-redir works fine while usb-host not. [https://www.kernel.org/doc/Documentation/cgroups/devices.txt](https://www.kernel.org/doc/Documentation/cgroups/devices.txt "Cgroup HowTO")

## Just do it

# sed -i 's/devices /#devices /'
# reboot

## Add fine control list

### Review the rules to libvirt, and leave all the character device to it

# cgget libvirt
To remove "c" device
# echo c > /sys/fs/cgroup/libvirt/devices.deny
To add all the "c" devices
# echo "c \*:\* rwm" > /sys/fs/cgroup/libvirt/devices.allow

devices.deny/allow is something like end-point.

### About hierarchy

**A child will not have more permission than its parent.**
---
title: "Intel iGVT-g vGPU in KVM - KVMGT howto"
date: "2015-01-22"
categories: 
  - "cloud-infra"
---

As previous article says, this is a vGPU solution in KVM by Intel.(All these genius)

[https://01.org/zh/kvm/blogs/albcamus/2014/kvmgt-first-release](https://01.org/zh/kvm/blogs/albcamus/2014/kvmgt-first-release "https://01.org/zh/kvm/blogs/albcamus/2014/kvmgt-first-release")

CPU: i5-4460 OS: Ubuntu 14.04 Desktop
---
title: "KVMGT in Linux 4.10 - 流媒体服务器、游戏、车载大屏等场景的应用技术预研"
date: "2017-04-12"
categories: 
  - "cloud-infra"
---

UPDATE: 2018-04，现在KVMGT相关upstream基本已全部进入master，可以参考[GVTg\_Setup\_Guide](http://GVTg_Setup_Guide)或者[GVTd\_Setup\_Guide](https://github.com/intel/gvt-linux/wiki/GVTd_Setup_Guide)，如果需要本地显示参考[DMA\_BUF User Guide](https://github.com/intel/gvt-linux/wiki/Dma_Buf_User_Guide)。

UPDATE: 2017-08-08，Intel有了X系列处理器i9，这个核数肯定适合跑桌面了。

UPDATE：鉴于KVMGT的产品化集成难度较之前有所降低，所以最近有很多人都打算搞搞集成，那我先来集成一把吧。

果不其然，直接并进内核了（书要改吗。。当然不，本文简单增补即可。。）

4.10非常具有的代表性即是添加了Mediated Device(vfio-mdev)，Intel有自家方案，nVidia也要支持，但Linux社区说你俩给个统一框架吧，要不太乱，于是就有了它。如此以来，N家的显卡便可以很快开发出对应的驱动（不要相信他们家销售说的话，14年偶尔问了一下说要支持的，都过去80年啦），可以看看N家去年(2016.9)放的PPT，[02x03-Neo\_Jia\_and\_Kirti\_Wankhede-vGPU\_on\_KVM-A\_VFIO\_based\_Framework](https://blog.lofyer.org/wp-content/uploads/02x03-Neo_Jia_and_Kirti_Wankhede-vGPU_on_KVM-A_VFIO_based_Framework.pdf)。

相较于之前的文章[intel-igvt-g-vgpu-in-kvm-kvmgt-howto](https://blog.lofyer.org/intel-igvt-g-vgpu-in-kvm-kvmgt-howto/)，这次的并入减少了我们的不少工作。

4.10 ChangeLog：[https://kernelnewbies.org/Linux\_4.10](https://kernelnewbies.org/Linux_4.10)

参考[https://github.com/01org/gvt-linux/wiki/GVTg\_Setup\_Guide](https://github.com/01org/gvt-linux/wiki/GVTg_Setup_Guide)

另外，RHEL 7.4也已加入了KVMGT/XenGT，可以试用一下。

来看看支持哪些CPU吧：

> As a long-standing member of the open source community, Intel works upstream to ensure that full, open source implementations of Intel® GVT exist for open source virtualization hypervisors, KVM\* and Xen\*, known respectively as KVMGT and XenGT. KVMGT and XenGT deliver excellent virtual GPU performance in VMs across 5th and 6th generation Intel® Core™ processors (Intel® Core™ i3, Intel® Core™ i5, and Intel® Core™ i7 processors) with Intel Processor Graphics as well as Intel® Xeon® processors E3 v4 family with Intel Processor Graphics.

首先安装并编译4.10的内核：

$ git clone https://github.com/01org/gvt-linux
$ sudo yum install openssl-devel ncurses-devel
$ cd gvt-linux
$ git checkout gvt-stable-4.10
$ echo ""|make oldconfig
$ make menuconfig

搜索关键字kvmgt, mdev,vfio-iommu-type1，并将其标记为“M”。 模块标记完成后，进行编译安装。

$ make -j4
$ sudo make modules\_install
$ sudo make install
$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg

从elrepo安装的缺少模块和编译环境。

# rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
# rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
# yum --enablerepo=elrepo-kernel install kernel-ml kernel-ml-devel

然后修改grub文件：

menuentry 'CentOS Linux (4.10.10) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry\_id\_option 'gnulinux-3.10.0-514.10.2.el7.x86\_64-advanced-6a3987e2-9cb7-4b38-9a53-1642aefada46' {
        load\_video
        set gfxpayload=keep
        insmod gzio
        insmod part\_msdos
        insmod ext2
        set root='hd0,msdos1'
        if \[ x$feature\_platform\_search\_hint = xy \]; then
          search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  3fc34ea6-30d7-491e-931d-d0941884fb6b
        else
          search --no-floppy --fs-uuid --set=root 3fc34ea6-30d7-491e-931d-d0941884fb6b
        fi
        linux16 /vmlinuz-4.10.10 root=/dev/mapper/cl-root ro rd.lvm.lv=cl/root rd.lvm.lv=cl/swap rhgb quiet LANG=en\_US.UTF-8 i915.enable\_gvt=1 kvm.ignore\_msrs=1
        initrd16 /initramfs-4.10.10.img
}

为什么添加ignore\_msrs呢？

> Since some windows guest 3rd patry application / tools (like GPU-Z / Passmark9.0) will trigger MSR read / write directly, if it access the unhandled msr register, guest will trigger BSOD soon. So we added the "kvm.ignore\_msrs=1" into grub for workaround.

然后继续编译QEMU，这次只要下载upstream的即可。

$ git clone git://git.qemu.org/qemu.git
$ cd qemu
$ git checkout v2.8.1
$ git submodule update --init roms/seabios
$ ./configure --prefix=/usr \\
    --enable-kvm \\
    --disable-xen \\
    --enable-debug-info \\
    --enable-debug \\
    --enable-sdl \\
    --enable-vhost-net \\
    --enable-spice \\
    --disable-debug-tcg \\
    --target-list=x86\_64-softmmu
$ make -j4
$ cd roms/seabios
$ make -j8
$ cd -
$ sudo make install
$ sudo cp roms/seabios/out/bios.bin /usr/bin/bios.bin
---
title: "Slic for qemu-kvm"
date: "2013-12-25"
categories: 
  - "cloud-infra"
---

This article is a howto for activation-ready of Windows. SLIC 2.0 is for 2003 & XP, and 2.1 for Win7 & 2008 Original seabios reads slic table from exactly the host. However, if your motherboard(not OEM) happen to own none, you will most probably make one by your self.

### Seabios

You could get the lastest code from here.

git clone git://git.seabios.org/seabios.git seabios

Or, you can download from here.

[seabios-1.7.2.2.tar.gz](http://code.coreboot.org/p/seabios/downloads/get/seabios-1.7.2.2.tar.gz "seabios-1.7.2.2.tar.gz")

### SLIC-BIN

Here's a collection of various slic table. [SLIC 2.1 BINS](http://blog.lofyer.org/slic-for-qemu-kvm/slic-2-1-bins/)

### Seaslic patch

This is patch for seabios to make it slic table enabled. Download from here.

[Seaslic.tar.xz, seabios-1.7.2 compatible](https://cloud.lofyer.org/public.php?service=files&t=3aa0db051506d88bce8e6d03d621f47e "Seaslic.tar.xz") Here's the patch content.

\--- a/src/acpi.c	2013-01-19 06:44:54.000000000 +0600
+++ b/src/acpi.c	2013-05-07 01:16:30.000000000 +0600
@@ -214,6 +214,11 @@

 #include "acpi-dsdt.hex"

+#define CONFIG\_OEM\_SLIC
+#ifdef CONFIG\_OEM\_SLIC
+#include "acpi-slic.hex"
+#endif
+
 static void
 build\_header(struct acpi\_table\_header \*h, u32 sig, int len, u8 rev)
 {
@@ -226,6 +231,10 @@
     h->oem\_revision = cpu\_to\_le32(1);
     memcpy(h->asl\_compiler\_id, CONFIG\_APPNAME4, 4);
     h->asl\_compiler\_revision = cpu\_to\_le32(1);
+    #ifdef CONFIG\_OEM\_SLIC
+    if (sig == RSDT\_SIGNATURE) // only RSDT is checked by win7 & vista
+	memcpy(h->oem\_id, ((struct acpi\_table\_header\*)SLIC)->oem\_id, 14);
+    #endif
     h->checksum -= checksum(h, len);
 }

@@ -827,6 +836,15 @@
     ACPI\_INIT\_TABLE(build\_srat());
     if (pci->device == PCI\_DEVICE\_ID\_INTEL\_ICH9\_LPC)
         ACPI\_INIT\_TABLE(build\_mcfg\_q35());
+    #ifdef CONFIG\_OEM\_SLIC
+	void \*buf = malloc\_high(sizeof(SLIC));
+	if (!buf)
+	    warn\_noalloc();
+	else {
+	    memcpy(buf, SLIC, sizeof(SLIC));
+	    ACPI\_INIT\_TABLE(buf);
+	}
+    #endif

     u16 i, external\_tables = qemu\_cfg\_acpi\_additional\_tables();

### Compile

You don't have to apply the seaslic patch with patch.sh, you can do that by hand. Before you start, do this:

xxd -i /sys/firmware/acpi/tables/SLIC | grep -v len | sed 's/unsigned ch   ar.\*/static char SLIC\[\] = {/' > seabios.submodule/src/acpi-slic.hex

Or,

xxd -i DELL.BIN | grep -v len | sed 's/unsigned ch   ar.\*/static char SLIC\[\] = {/' > seabios.submodule/src/acpi-slic.hex

After applying the patch, you can compile the bios.bin, and copy that to **/usr/share/qemu-kvm/my-bios.bin** or rewrite **bios.bin** instead. Here's my bios.bin with Dell\[DELL-QA09-NVDA\]2.1.BIN from SLIC BIN

[my-bin.tar.xz](https://raw.githubusercontent.com/lofyer/qemu-cmd-reloaded/master/qemu-slic/my-bin.tar.xz)

### Qemu-cmd

qemu-kvm XXX -bios /usr/share/qemu-kvm/my-bios.bin -acpitable file=Dell\[DELL-QA09-NVDA\]2.1.BIN

In the guest, you could see that SLIC by /sys/firmware/acpi/tables/SLIC in Linux or SLIC\_Toolkit in Windows.
---
title: "GPU Passthrough, VGA Passthrough in KVM"
date: "2013-05-23"
categories: 
  - "cloud-infra"
  - "linux-admin"
---

To inspire you, I've got a video from someone else. Better mutt the volume by the way. [Arch Linux KVM Crysis HD Gpu Passthrough](http://www.youtube.com/watch?v=Qi1LdFkRzIs "Arch Linux KVM Crysis HD Gpu Passthrough") Or you can download it to see. [Download the video in HD](http://blog.lofyer.org/wp-content/uploads/Arch-Linux-KVM-Crysis-HD.flv "Download the video") 

Here's the links I refer to: [http://thread.gmane.org/gmane.comp.emulators.kvm.devel/71981](http://thread.gmane.org/gmane.comp.emulators.kvm.devel/71981 "http://thread.gmane.org/gmane.comp.emulators.kvm.devel/71981") [https://bbs.archlinux.org/viewtopic.php?id=162768](https://bbs.archlinux.org/viewtopic.php?id=162768 "https://bbs.archlinux.org/viewtopic.php?id=162768") [https://docs.google.com/document/d/1ef\_nfl652L0HLn\_wGvnpgjsBJd9LZzaV\_-rIcEEoK8Y/edit?pli=1](https://docs.google.com/document/d/1ef_nfl652L0HLn_wGvnpgjsBJd9LZzaV_-rIcEEoK8Y/edit?pli=1 "https://docs.google.com/document/d/1ef_nfl652L0HLn_wGvnpgjsBJd9LZzaV_-rIcEEoK8Y/edit?pli=1") [http://www.linux-kvm.org/page/VGA\_device\_assignment](http://www.linux-kvm.org/page/VGA_device_assignment "http://www.linux-kvm.org/page/VGA_device_assignment") [http://www.linux-kvm.org/page/How\_to\_assign\_devices\_with\_VT-d\_in\_KVM](http://www.linux-kvm.org/page/VGA_device_assignment "http://www.linux-kvm.org/page/VGA_device_assignment")

## Result: VGAPassthrough: success in host F19, guest Windows7 GPUPassthrough: success in Fedora-Rawhide

HOST: CPU: Core i5 3470 GPU: ATI HD Radeon 7850 OS: Fedora-Rawhide QEMU: qemu-1.5.1 [![kvm-vgapassthrough](/blog/images/kvm-vgapassthrough.png)](http://blog.lofyer.org/2013/05/pass-host-gpu-to-guest-via-qemu-ncursescurses/kvm-vgapassthrough/) So, here's the steps

## 0\. Enable the mainboard VxT, iommu and alter the video device to Intel HD

## 1\. See what we have got now.

lspci;lspci -n

We have output below

...
01:00.0 VGA compatible controller: Advanced Micro Devices, Inc. \[AMD/ATI\] Pitcairn PRO \[Radeon HD 7850\]
01:00.1 Audio device: Advanced Micro Devices, Inc. \[AMD/ATI\] Cape Verde/Pitcairn HDMI Audio \[Radeon HD 7700/7800 Series\]
...

...
01:00.0 0300: 1002:6819
01:00.1 0403: 1002:aab0
...

You can see the pci bus and vendor.

## 2\. Modify the kernel parameter, morprobe.d and libvirt.conf

Add follow parameters to grub.conf

intel\_iommu=on pci-stub.ids=1002:6819,1002:aab0,vfio\_iommu\_type1.allow\_unsafe\_interrupts=1

NOTE: If you have got an AMD cpu, please replace "interl\_iommu=on" with "iommu=pt iommu=1" Add modprobe.conf to /etc/modprobe.d/ with this content:

blacklist radeon
options kvm ignore\_msrs=1
options kvm allow\_unsafe\_interrupts=1
options kvm-amd npt=0
options kvm\_intel emulate\_invalid\_guest\_state=0
options vfio\_iommu\_type1 allow\_unsafe\_interrupts=1

change the following options in /etc/libvirt/qemu.conf:

# The user ID for QEMU processes run by the system instance.
user = "root"

# The group ID for QEMU processes run by the system instance.
group = "root"

......

# If clear\_emulator\_capabilities is enabled, libvirt will drop all
# privileged capabilities of the QEmu/KVM emulator. This is enabled by
# default.
#
# Warning: Disabling this option means that a compromised guest can
# exploit the privileges and possibly do damage to the host.
#
clear\_emulator\_capabilities = 0

Reboot.

## 3\. Using scripts below

### Version 1: VFIO-Passthrough

File: vfio-bind

#!/bin/bash
modprobe vfio-pci
for var in "$@"; do
        for dev in $(ls /sys/bus/pci/devices/$var/iommu\_group/devices); do
                vendor=$(cat /sys/bus/pci/devices/$dev/vendor)
                device=$(cat /sys/bus/pci/devices/$dev/device)
                if \[ -e /sys/bus/pci/devices/$dev/driver \]; then
                        echo $dev > /sys/bus/pci/devices/$dev/driver/unbind
                        fi
                echo $vendor $device > /sys/bus/pci/drivers/vfio-pci/new\_id
        done
done

Bind the device

./vfio-bind 0000:01:00.0 0000:01:00.1

Start VM

#!/bin/bash
sudo modprobe vfio-pci

sudo qemu-system-x86\_64 -no-user-config -nodefaults -m 2048M -smp 4 -boot menu=on \\
-net nic -net user -enable-kvm -monitor stdio -vga qxl -global qxl-vga.vram\_size=67108864 \\
-spice port=6000,ipv4,disable-ticketing \\
-device intel-hda,id=sound0,bus=pcie.0,addr=0x4 -device hda-duplex,id=sound0-codec0,bus=sound0.0,cad=0 \\
-drive file=Windows7.iso,if=none,id=drive-ide0-1-0,readonly=on,format=raw -device ide-cd,bus=ide.1,unit=0,drive=drive-ide0-1-0,id=ide0-1-0 \\
-drive file=/home/lofyer/gpu\_passthrough/f17.qcow2,if=none,id=drive-virtio-disk0,format=qcow2,cache=none,werror=stop,rerror=stop,aio=threads -device virtio-blk-pci,scsi=off,bus=pcie.0,addr=0x7,drive=drive-virtio-disk0,id=virtio-disk0,bootindex=1 \\
-device virtio-balloon-pci,id=balloon0,bus=pcie.0,addr=0x8 \\
-M q35 \\
-device piix4-ide,bus=pcie.0 \\
-device ioh3420,bus=pcie.0,addr=1c.0,multifunction=on,port=1,chassis=1,id=root.1 \\
-device vfio-pci,host=01:00.0,bus=root.1,addr=00.0,multifunction=on,x-vga=on \\
-device vfio-pci,host=01:00.1,bus=root.1,addr=00.1 \\
-fda virtio.vfd

### Version 2: PCI-Passthrough

Bind device

#!/bin/bash
modprobe pci-stub
for id in 6819 aab0; do
    echo 1002 $id > /sys/bus/pci/drivers/pci-stub/new\_id
done
for pci in 0000:01:00.{0,1}; do
    echo $pci > "/sys/bus/pci/devices/$pci/driver/unbind"
    echo $pci > /sys/bus/pci/drivers/pci-stub/bind
done

Start VM

#!/bin/bash
qemu-system-x86\_64 \\
-hda ../f17.qcow2 \\
-cdrom /run/media/lofyer/Cache/OS\_ISO/cn\_windows\_7\_ultimate\_with\_sp1\_x64\_dvd\_u\_677408.iso \\
-m 2048 -balloon virtio -smp 4 -enable-kvm \\
-device pci-assign,host=01:00.0
---
title: "kvm perfermance test, including periphery virtual device"
date: "2013-09-14"
categories: 
  - "cloud-infra"
  - "linux-admin"
---

I mean _**test**_, not _**profiling**_.

## To virtual server and virtual desktop, we should inspect them from different standards.

For an server: response time, throughput, concurrent, utilization For and desktop: response time, video/audio latency, utilization

## Tools we can use to test

### CPU:

SPEC(open, but not free) Unixbench Super PI Compile linux kernel pcmark(not open, not free, but cracked can be found..) ffmpeg convert

### graphic:

3dmark

### fs,hd:

hdparm iozone blogbench dbench

### net:

iperf

### ram

ramspeed

## How to start

Initial host env: i5-3470, 8G, gentoo-linux-3.11+, qemu-1.6.0 Kernel para:

TBD
