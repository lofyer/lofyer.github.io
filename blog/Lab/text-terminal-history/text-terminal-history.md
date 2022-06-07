---
title: "All about text terminal"
date: "2016-08-04"
categories: 
  - "linux-admin"
---

[http://www.tldp.org/HOWTO/html\_single/Text-Terminal-HOWTO/](http://www.tldp.org/HOWTO/html_single/Text-Terminal-HOWTO/)

控制台(console)和终端(terminal)比较容易混淆，一般来说与主机显示输出与控制接口直接相连的显示器和键盘叫做控制台，其他不与主机控制接口直接相连的则叫做终端。 现代PC的模拟终端(tty in Linux)是模拟当年接到主机上的客户机之间的串口输入输出行为，准确的说是含有键盘作为输入和VDU(video display unit)作为输出与回显的客户端。 有些真实的终端是能够显示黑白图片的，也就是位图，这些终端协议包括Tektronix Vector Graphics, ReGIS (DEC), Sixel (DEC), and NAPLPS (North American Presentation Level Protocol Syntax)。更为炫酷的是在Apollo、Sun、SGI主机上的矢量终端(http://www.cca.org/vector/)，早期电影里你看到的那些能画图的绿色显示器就是它了。 瘦客户端早期是文字终端，后来才开始有图形界面和鼠标的终端。根据其与主机的计算量相对大小可分为胖客户端和瘦客户端。 Windows NT版本开始提供的RDP服务是基于ITU T.120协议，ICA协议是在其基础上的兼容扩展。Linux可使用免费的ICA客户端，但需要向MS支付许可费。 现代的终端，包括X-Windows、Windows、Gnome等全部叫做窗口终端(Window Terminal)。 上世纪80年代的内存价格约为每兆数千美元，所以文字终端更容易流行。 终端一般都遵循一定的控制标准，即escape(ESC)控制序列，最常见的例子是我们常用的银行密码键盘。 美国的终端多以ASCII作为字母标准(除了IBM的EBCDIC)，但是这些终端的ESC控制序列并不都一样，所以往往引起兼容性的问题。为此引入了termcap("terminal capabilities")，也就是现在的环境变量TERM。 现代的模拟终端最早是由DEC引入的VT系列(vt100、vt220)，Linux中的终端模型linux也与vt100类似。 模拟软件可模拟的终端类型一般有Wyse60, 50; VT 220, 102, 100, 52: TV950, 925, 912; PCTERM; ANSI; IBM3101; ADM-1l; WANG 2110。 流控(flow control)是用来防止终端、电脑、猫或其他设备处理过多字符而导致信息丢失等现象的发生，可分为软件控制和硬件控制，软件控制即发送DC1/DC3控制序列，硬件控制即分别控制串口/并口的RTS/CTS或DTR/DSR引脚高低电平。当然，现在模拟终端的流控全由软件控制了。
