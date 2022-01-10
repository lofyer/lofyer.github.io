---
title: "KVMGT in Linux 4.10 - 流媒体服务器、游戏、车载大屏等场景的应用技术预研"
date: "2017-04-12"
categories: 
  - "cloud"
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

\# rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
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
