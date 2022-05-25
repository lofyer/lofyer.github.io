---
title: "arduino+cpuload+serial+led"
date: "2012-12-09"
categories: 
  - "devices"
---

https://github.com/lofyer/arduino-hostcpuload-led

用libgtop2的库，已完成。 注意： 数据以ascii类型传输，调用led时转化为int cpu利用率算式:

<pre>(float)(100\*(cpu\_time2.(sys+user)-cpu\_time2.(sys+user))/(cpu\_time2.total-cpu\_time1.total))</pre>
