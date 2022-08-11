---
title: "Using Gpu in Pi"
date: "2013-06-14"
categories: 
  - "devices"
---

**We had a wiki here.** [http://elinux.org/RPi_Xorg_rpi_Driver#Design](http://elinux.org/RPi_Xorg_rpi_Driver#Design "RPi gpu driver design")

**And vc sdk on github. It's also in /opt/vc.** [https://github.com/raspberrypi](https://github.com/raspberrypi "raspberry on github")

**X using gpu.** [https://github.com/simonjhall/fbdev_exa](https://github.com/simonjhall/fbdev_exa "X using gpu")

**Playback accelerating.** [http://wiki.matthiasbock.net/index.php/Hardware-accelerated_video_playback_on_the_Raspberry_Pi](http://wiki.matthiasbock.net/index.php/Hardware-accelerated_video_playback_on_the_Raspberry_Pi "RPi playback")

**Compile gstreamer{\*} in Pi** [http://permalink.gmane.org/gmane.comp.video.gstreamer.devel/43011](http://permalink.gmane.org/gmane.comp.video.gstreamer.devel/43011)

**Using wayland instead of X** Here's the build instruction [http://wayland.freedesktop.org/raspberrypi.html](http://wayland.freedesktop.org/raspberrypi.html "Build Wayland") For recently PI_DEBIAN img, we should just

apt-get install weston weston-dev

Using xwayland to run X application in wayland..

Notes: 1. If you're going to compile cairo using egl, you shall meet a problem like undeclared 'None', just DEFINE one as 0L. 2. Using soft link rather than copying one when library is missing.

**Waiting4Test** **Tuning Rasbian** Speedup CPU: raspi-config arm_freq=900 I always use this hack and my rpi run without problem Change scheduler/elevator at boot time: we use more responsive/less disk io usage scheduler: modify the /boot/cmdline.txt

dwc_otg.lpm_enable=0 root=/dev/mmcblk0p2 rootfstype=ext4 rootflags=commit=120,data=writeback elevator=deadline rootwait quiet

Tuning sysctl.conf: as manual say, sysctl.conf is the “Configuration file for setting system variables”... well there are lot of variables you can put inside this file and for everyone we can write a book. Our goal is to gain more speed and tell our system to use less io/ram. Let’s put in the /etc/sysctl.conf:

vm.dirty_background_ratio = 20
vm.dirty_expire_centisecs = 0
vm.dirty_ratio = 80
vm.dirty_writeback_centisecs = 1200
vm.overcommit_ratio = 2
vm.laptop_mode = 5
vm.swappiness = 10

**Tuning SDCard:** Quite hard to understand, but changing the partition alignment of the SDCard can highly improve the write speed of our sdcard. Basically we have to align the first partition to the erase block size of our sdcard. I suggest to read an reread the reference link. To do that in our sdcard that contain our RaspberryPI Debian distro we have to: backup our filesystem from linux: cd /media/mymmcblk0p2/ (the partitition that’s contain debian fs) tar -cvpzf /home/gibbio/RPI-TC_fs.tgz --exclude=./proc --exclude=./lost+found --exclude=./sys --exclude=./mnt --exclude=./media --exclude=./dev ./ cd /media/mymmcblk0p1/ (the partitition that’s contain boot kernel etc) tar -cvpzf /home/gibbio/RPI-TC_boot.tgz ./ Now use printcsd.py to find the erase block size, most sd have 128K so we use 128K/Sector size = 256 sectors (mine have 64k so we have to use 64k/512b = 128 sector alignement)

sfdisk -f -H224 -S56 /dev/mmcblk0 Checking that no-one is using this disk right now ... OK Disk /dev/mmcblk0: 244448 cylinders, 224 heads, 56 sectors/track Old situation: Units = cylinders of 6422528 bytes, blocks of 1024 bytes, counting from 0 Device Boot Start End #cyls #blocks Id System /dev/mmcblk0p1 0 - 0 0 0 Empty /dev/mmcblk0p2 0 - 0 0 0 Empty /dev/mmcblk0p3 0 - 0 0 0 Empty /dev/mmcblk0p4 0 - 0 0 0 Empty Input in the following format; absent fields get a default value. Usually you only need to specify and (and perhaps ). /dev/mmcblk0p1 :,8,c /dev/mmcblk0p1 0+ 7 8- 50175+ c W95 FAT32 (LBA) (enter enter) /dev/mmcblk0p2 : /dev/mmcblk0p2 8 1246 1239 7771008 83 Linux /dev/mmcblk0p3 : /dev/mmcblk0p3 0 - 0 0 0 Empty /dev/mmcblk0p4 : /dev/mmcblk0p4 0 - 0 0 0 Empty New situation: Units = cylinders of 6422528 bytes, blocks of 1024 bytes, counting from 0 Device Boot Start End #cyls #blocks Id System /dev/mmcblk0p1 0+ 7 8- 50175+ c W95 FAT32 (LBA) /dev/mmcblk0p2 8 1246 1239 7771008 83 Linux /dev/mmcblk0p3 0 - 0 0 0 Empty /dev/mmcblk0p4 0 - 0 0 0 Empty Warning: no primary partition is marked bootable (active) This does not matter for LILO, but the DOS MBR will not boot this disk. Do you want to write this to disk? \[ynq\] y Successfully wrote the new partition table Re-reading the partition table ... If you created or changed a DOS partition, /dev/foo7, say, then use dd(1) to zero the first 512 bytes: dd if=/dev/zero of=/dev/foo7 bs=512 count=1 (See fdisk(8).)

gibbio # fdisk -l... Disk /dev/mmcblk0: 8010 MB, 8010072064 bytes 224 heads, 56 sectors/track, 1247 cylinders, total 15644672 sectors Units = sectors of 1 \* 512 = 512 bytes Sector size (logical/physical): 512 bytes / 512 bytes I/O size (minimum/optimal): 512 bytes / 512 bytes Disk identifier: 0x000ee283 Device Boot Start End Blocks Id System /dev/mmcblk0p1 1 100351 50175+ c W95 FAT32 (LBA) /dev/mmcblk0p2 100352 15642367 7771008 83 Linux

now create ext4 FS: mke2fs -t ext4 -E stripe-width=32 -m 0 /dev/mmcblk0p2 make FAT32 partition via gparted gibbio # fdisk /dev/mmcblk0 Command (m for help): p Disk /dev/mmcblk0: 8010 MB, 8010072064 bytes 224 heads, 56 sectors/track, 1247 cylinders, total 15644672 sectors Units = sectors of 1 \* 512 = 512 bytes Sector size (logical/physical): 512 bytes / 512 bytes I/O size (minimum/optimal): 512 bytes / 512 bytes Disk identifier: 0x000ee283 Device Boot Start End Blocks Id System /dev/mmcblk0p1 1 100351 50175+ c W95 FAT32 (LBA) /dev/mmcblk0p2 100352 15642367 7771008 83 Linux Command (m for help): x Expert command (m for help): p Disk /dev/mmcblk0: 224 heads, 56 sectors, 1247 cylinders Nr AF Hd Sec Cyl Hd Sec Cyl Start Size ID 1 00 0 2 0 223 56 7 1 100351 0c 2 00 0 1 8 223 56 1023 100352 15542016 83 3 00 0 0 0 0 0 0 0 0 00 4 00 0 0 0 0 0 0 0 0 00 Expert command (m for help): b Partition number (1-4): 1 New beginning of data (1-100351, default 1): 128 Expert command (m for help): w The partition table has been altered! Calling ioctl() to re-read partition table. Syncing disks. Now eject the sdcard, reinsert and make the restore from the tgz: cd /media/mymmcblk0p2/ tar -xvpzf RPI-TC_fs.tgz -C ./ mkdir proc mnt sys boot dev umount /media/mymmcblk0p2/ cd /media/mymmcblk0p1/ tar -xvpzf RPI-TC_boot.tgz -C ./ umount /media/mymmcblk0p1/ Eject the sdcard, plug into our Raspberry Pi and power on!
