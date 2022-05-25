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
