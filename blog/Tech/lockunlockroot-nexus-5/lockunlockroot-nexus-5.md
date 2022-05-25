---
title: "how to lock/unlock/root nexus 5"
date: "2013-11-16"
categories: 
  - "devices"
---

今日入手Nexus5美版（D820） blahblahblah... 好吧，开始unlock以及root

先下载这个包，里面包含CM的镜像，unlock/lock的apk http://cloud.lofyer.org/public.php?service=files&t=af14640b4f32d2e491d80a117db2bbe9

下载完成后，同时按住音量上下键再按开机，进入bootloader的前奏（相当于bios选项），此时电脑上应该会有所表示（比如windows的弹出要安装驱动啦，没驱动下个sdk搜索一下，比如linux的dmesg啦）

解压下载的文件，运行里面的root-$YOUROS（win、mac、linux）脚本，然后你看到请求unlock的选项，选择unlock并按电源键确认，此时pc上的脚本也会让你下一步； 下一步就是出现红色的机器人，说正在写脚本以及镜像，在写的时候pc上的脚本再下一步就可以自动让它退出了，然后稍等几分钟让它重启，重启的时候你会看到google标志下面的锁标志。

出于莫名其妙的安全感，在root以后可以安装压缩包里面的apk文件将bootloader再次锁上，这样，就这样。
