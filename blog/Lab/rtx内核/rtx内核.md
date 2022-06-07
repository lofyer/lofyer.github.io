---
title: "RTX内核"
date: "2012-10-13"
categories: 
  - "devices"
---

下载了最新的MDK后，发现里面有个RTX内核，玩了一下，和UCOSII很像。

semaphore，message，mailbox，mutex等同步与通信机制，代码易读，可移植性也好，在mini2440上运行完美，我只用了4个led 观察。

喜欢的话可以看看，这是RTX内核源码，移植需要更改MDK中board 部分内容。

点击下载 [RTX](http://69.164.197.168/wp-content/uploads/2012/10/RTX.rar)
