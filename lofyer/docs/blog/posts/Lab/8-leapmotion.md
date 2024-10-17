---
title: "LeapMotion相关"
date: 2013-07-28
categories: 
  - "devices"
tags:
 - "LeapMotion"
---

# LeapMotion相关

## LeanMotion入手

卖家太给力了，昨天就顺丰发货，今天下午我冒着炎炎烈日去取货，激动。。

先来张盒子，看着就感觉那帮老外工业设计功底深厚。 [![DSC_0003](/blog/images/DSC_0003-1024x680.jpg)](http://blog.lofyer.org/2013/07/leapmotion-%e5%85%a5%e6%89%8b%ef%bc%8c%e6%99%92%e4%b8%80%e4%b8%8b%e5%bc%80%e7%ae%b1%e4%bb%a5%e5%8f%8a%e5%9f%ba%e6%9c%ac%e6%93%8d%e4%bd%9c/dsc_0003/)

开箱：Leapmotion一台，数据线两根（一长一短），说明书一张 [![DSC_0006](/blog/images/DSC_0006-1024x680.jpg)](http://blog.lofyer.org/2013/07/leapmotion-%e5%85%a5%e6%89%8b%ef%bc%8c%e6%99%92%e4%b8%80%e4%b8%8b%e5%bc%80%e7%ae%b1%e4%bb%a5%e5%8f%8a%e5%9f%ba%e6%9c%ac%e6%93%8d%e4%bd%9c/dsc_0006/)

看着感觉好爽的样子~~~ [![DSC_0008](/blog/images/DSC_0008-1024x680.jpg)](http://blog.lofyer.org/2013/07/leapmotion-%e5%85%a5%e6%89%8b%ef%bc%8c%e6%99%92%e4%b8%80%e4%b8%8b%e5%bc%80%e7%ae%b1%e4%bb%a5%e5%8f%8a%e5%9f%ba%e6%9c%ac%e6%93%8d%e4%bd%9c/dsc_0008/)

我拍的一段简单使用视频。

---

## LeapMotion记录(2017-07)

已经在官网注册并下载了sdk（windows、linux，我没Mac），设备不出意外下个月到。 等到了再更新文章，到时会更新sdk配置，与arduino交互的相关代码。 可控的目前想到了飞行器、机械手（夹持器 - -）。
---
title: "LeapMotion controlled X4"
date: 2013-09-15
categories: 
  - "devices"
tags:
 - "LeapMotion"
---

两者都入手已经一个月了，可平时有些忙（惰者多忙），也就只零零散散准备了一些资料，还没开始整合。 整个流程大概是这个我用文字描述的粗略的样子：

空间建模：获取飞行器初始坐标，与leapmotion的可视区域进行map。 一级控制：leapmotion获得的手掌运动信息（可预处理）进入arduino通过nfr2401发送给X4，进行粗略运动指令传达。 二级控制：根据摄像头采集的飞行器位置数据与手掌位置信息对比，进行二阶指令传达。 回馈控制：在二级控制的基础上进行微调。 错误返回：如果超出leapmotion可视范围，则进行回馈控制将X4返回初始位置（0,0,0），超出摄像头范围，则需。。。人工重置。
---
title: "Leapmotion 相关"
date: 2013-08-02
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
