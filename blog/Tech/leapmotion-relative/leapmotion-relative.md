---
title: "Leapmotion 相关"
date: "2013-08-02"
categories: 
  - "devices"
tags:
 - "LeapMotion"
---

## 在Windows中

夹持器与四轴飞行器都是Arduino-Based，通信就用串口。 代码放在[git.lofyer.org](http://git.lofyer.org/cgit.cgi/fun/robohand/ "http://git.lofyer.org/cgit.cgi/fun/robohand/") 感觉VS好难。。试试在gentoo下驱动leapmotion

## 在Gentoo下使用，这个方式应该适用于所有非debian系

先下载sdk包，下载后解压。 看到里面有个deb的包，用alien转化为tar包

alien -t Leap-0.8.0-x64.deb
tar xf leap-0.8.0.tgz
cp -irf usr/* /usr/local/
cp -irf lib/udev/rules.d/25-com-leapmotion-leap.rules /etc/udev/rules.d

然后将普通用户加入plugdev组

sudo usermod -a -G plugdev $USER

刷新组关系，注销当前用户也可

exec su -l $USER

运行leapd与LeapControlPanel即可

在64位机器上缺少32位库文件 freetype.so.6, libasound.so.2

sudo emerge -avt app-emulation/emul-linux-x86-xlibs
sudo emerge emul-linux-x86-soundlibs

实例：编译MotionVisualizer

Make -C Builds/Linux
