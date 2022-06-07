---
title: "arduino"
date: "2012-10-21"
categories: 
  - "devices"
tags:
  - "Arduino"
---

# Arduino相关

目前最流行的是uno系列，而leonardo好象是在1.0.1后才加入的，其ide开发是在github上进行。 设计很清爽（因为没有UI设计师吧），希望别像keil一样越来越冗余。 [![](/blog/images/Screenshot-from-2012-10-22-200253.png "Screenshot from 2012-10-22 20:02:53")](http://69.164.197.168/wp-content/uploads/2012/10/Screenshot-from-2012-10-22-200253.png)

## Arduino IO生成波形的问题(2012)

```
DigitalWrite(pin,HIGH);
DELAY(10);
DigitalWrite(pin,LOW)
DELAY(10);
```

观察波形（上升沿触发）频率是51Hz，峰值为0.520V（使用外接电源的情况下，USB作电源为0.505V）。 除掉市电滤波的原因，应该是128本身Timer造成的问题，因DELAY使用的是Timer，选中的端口同样为Timer可用的端口，故在电平维持时可看到波形有毛刺（示波器太烂了吧）。

## 使用Arduino显示主机CPU

https://github.com/lofyer/arduino-hostcpuload-led

用libgtop2的库，已完成。 注意： 数据以ascii类型传输，调用led时转化为int cpu利用率算式:

```
(float)(100\*(cpu\_time2.(sys+user)-cpu\_time2.(sys+user))/(cpu\_time2.total-cpu\_time1.total))
```
