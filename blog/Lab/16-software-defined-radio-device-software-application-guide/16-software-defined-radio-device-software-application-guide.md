---
title: "软件定义无线电（SDR）的设备、软件与应用指南"
date: "2019-05-10"
categories: 
  - "cloud-infra"
  - "devices"
tags:
  - "Software Defined Radio"
---

[![](/blog/images/www.youtube.com_watch_v-mks0TAwqQUlistPLbBQHMnVMR41tOomyB0o7UKAxwKINkFFnindex4.png)](https://blog.lofyer.org/wp-content/uploads/www.youtube.com_watch_v-mks0TAwqQUlistPLbBQHMnVMR41tOomyB0o7UKAxwKINkFFnindex4.png)

**注意：本文内容仅限于实验室安全测试目的，禁止用于任何商业或违反当地法律法规的活动。**

**不管是较贵的Ettus还是入门的HackRF，抑或是最初级的RTL-SDR设备，都可以使用这篇教程中的绝大部分内容。**

[GRCon2019](https://www.youtube.com/watch?v=Wt_c_H6HQAU&list=PLbBQHMnVMR41tOomyB0o7UKAxwKINkFFn)

https://www.gnuradio.org/grcon/grcon17/presentations/

https://www.gnuradio.org/grcon/grcon18/presentations/

https://www.gnuradio.org/grcon/grcon19/presentations/

https://github.com/mossmann/hackrf/wiki

https://www.hackrf.net/hackrf%E4%B8%8Egnuradio%E5%85%A5%E9%97%A8%E6%8C%87%E5%8D%97/

http://www.hackrf.net/faq/

https://wiki.myriadrf.org/LimeSDR

https://myriadrf.org/news/limesdr-made-simple-part-1/

[雪碧0xroot的PPT](https://blog.lofyer.org/software-defined-radio-device-software-application-guide/)

# 0\. 环境准备

## 0.1 硬件

|  | HackRF One | Ettus B200 | Ettus B210 | BladeRF x40 | LimeSDR | LimeSDR mini |
| --- | --- | --- | --- | --- | --- | --- |
| Frequency Range | 1MHz-6GHz | 70MHz-6GHz | 70MHz-6GHz | 300MHz-3.8GHz | 100kHz-3.8GHz | 100kHz-3.5GHz |
| RF Bandwidth | 20MHz | 61.44MHz | 61.44MHz | 40MHz | 61.44MHz | 30.72MHz |
| Sample Depth | 8 bits | 12 bits | 12 bits | 12 bits | 12 bits | 12 bits |
| Sample Rate | 20MSPS | 61.44MSPS | 61.44MSPS | 40MSPS | 3.2MSPS | 61.44MSPS |
| Transmitter Channels | 1 | 1 | 2 | 1 | 2 | 1 |
| Receivers | 1 | 1 | 2 | 1 | 2 | 1 |
| Duplex | Half | Full | Full | Full | Full | Full |
| Interface | USB 2.0 | USB 3.0 | USB 3.0 | USB 3.0 | USB 3.0 | USB 3.0 |
| Programmable Logic Gates | 64 macrocell CPLD | 75k | 100k | 40k (115k avail) | 40k | 40k |
| Chipset | MAX5864, MAX2837, RFFC5072 | AD9364 | AD9361 | LMS6002M | LMS7002M | LMS7002M |
| Open Source | Full | Schematic, Firmware | Schematic, Firmware | Schematic, Firmware | Full | Full |
| Oscillator Precision | +/-20ppm | +/-2ppm | +/-2ppm | +/-1ppm |  +/-1ppm initial
+/-4ppm stable |  +/-1ppm initial

+/-4ppm stable |
| Transmit Power | \-10dBm+ (15dBm @ 2.4GHz) | 10dBm+ | 10dBm+ | 6dBm |  0 to 10dBm | 0 to 10dBm |
| Price | 249€ euros VAT Exc. | 991€ euros VAT Exc. | 1658€ euros VAT Exc. | 625€ euros VAT Exc. | 332€ euros VAT Exc. | 190€ euros VAT Exc. |

## 0.2 驱动

## 0.3 软件

https://unicorn.360.com/blog/2017/04/12/LimeSDR-Getting-Started-Quickly/

https://oneguyoneblog.com/2016/09/15/sdrsharp-sdr-installing-windows-10/

下载SDR#后，重启按F7进入“禁用驱动签名”的运行模式，运行其中的install-rtlsdr.bat，替换第0个驱动

 

# 1\. 接收信号

接收信号建议使用gqrx（MacOS、Linux），也可以用sdrsharp（Windows）。

https://www.rtl-sdr.com/big-list-rtl-sdr-supported-software/

$ port info gqrx

$ sudo port install gqrx

接收信号以后，你可以做的内容就比较多了，这里我会举一些比较有意思的例子。

## 1.1. 听广播/看电视

http://dalvikplanet.blogspot.com/2017/03/how-to-get-working-rtl2832u-r820t2-on.html

## 1.2. 接收气象云图

SDR软件

虚拟声卡

WXtoimg

gpredict/orbitron

https://www.rtl-sdr.com/rtl-sdr-tutorial-receiving-noaa-weather-satellite-/blog/images/

https://wischu.com/archives/528.html

 

GSM嗅探

https://www.cnblogs.com/k1two2/p/7000942.html

## 1.3. 接收GPS信息

https://swling.com/blog/2016/04/guest-post-using-the-hackrf-one-for-dgps-beacon-reception/

http://sdrgps.blogspot.com/2016/12/rtl-sdr-to-orbit-with-limesdr.html

## 1.4. 方向探测与被动雷达 Direction Finding and Passive Rador

https://www.rtl-sdr.com/ksdr/

## 1.5. 接收whatever you want LEGALLY

zigbee https://github.com/bastibl/gr-ieee802-15-4

https://github.com/BastilleResearch/scapy-radio/tree/master/gnuradio/gr-zigbee

# 2\. 发送信号

## 2.1. 发送GPS信号

https://gist.github.com/gyaresu/343ae51ecbb70486e270

https://www.cnblogs.com/k1two2/p/5477291.html#4245780

https://gorgias.me/2017/07/30/HackRF-GPS-%E6%AC%BA%E9%AA%97/

https://github.com/osqzss/LimeGPS

## 2.2. 发送文字/音视频

Windows软件sdrangel

http://gareth.codes/hackrf-transmit/

https://github.com/fsphil/hacktv

http://www.irrational.net/2014/03/02/digital-atv/

http://www.hackrf.net/2014/06/hackrf\_nbfm\_tx\_n\_ctcss\_squelch/

http://www.xn--hrdin-gra.se/blog/wp-content/uploads/2015/08/nbfm-tx.grc

https://gist.github.com/gyaresu/343ae51ecbb70486e270

https://nuclearrambo.com/wordpress/transferring-a-text-file-over-the-air-with-limesdr-mini/

https://github.com/martinmarinov/TempestSDR

# 3\. 收发信号

## GSM

https://www.evilsocket.net/2016/03/31/how-to-build-your-own-rogue-gsm-bts-for-fun-and-profit/

https://yatebts.com/open\_source/

https://cn0xroot.com/2017/01/10/iot-mode-fuzzing-with-openbt/

## LTE

https://yq.aliyun.com/articles/310348

https://www.cnblogs.com/k1two2/p/5666667.html

https://cn0xroot.com/2017/04/12/limesdr-getting-started-quickly/

 

## OpenBTS+LimeSDR

Prepare:

Ubuntu Desktop 16.04 & LimeSDR 1.4s with **LimeSuite 17.12(If not, OpenUSRP will fail.)**

### Install build-essential packages

#packages for soapysdr available at myriadrf PPA
sudo add-apt-repository -y ppa:myriadrf/drivers
sudo apt-get update

#install core library and build dependencies
sudo apt-get install -y git g++ cmake libsqlite3-dev

#install hardware support dependencies
sudo apt-get install -y libsoapysdr-dev libi2c-dev libusb-1.0-0-dev

#install graphics dependencies
sudo apt-get install -y libwxgtk3.0-dev freeglut3-dev

# Install for building uhd
sudo apt-get install libboost-all-dev libusb-1.0-0-dev python-mako doxygen python-docutils cmake build-essential

 

### Change to UHD driver via uhd

$ cd ~                 # build and install limesuite
$ git clone https://github.com/myriadrf/LimeSuite.git
$ cd LimeSuite
$ mkdir builddir && cd builddir
$ cmake ../
$ make -j4
$ sudo make install
$ sudo ldconfig

$ cd ~ # build uhd, install, enable lime, rebuild
$ git clone https://github.com/EttusResearch/uhd.git
$ cd uhd/host/
$ mkdir build && cd build
$ cmake ../
$ make -j4
$ sudo make install
$ git clone https://github.com/jocover/OpenUSRP.git lib/ursp/OpenUSRP # DO NOT GO OUT
$ echo "INCLUDE\_SUBDIRECTORY(OpenUSRP)">>lib/ursp/CMakeLists.txt
$ cmake ../
$ make -j4
$ sudo make install

 

### Or, Change to UHD driver via SoapySDR

$ git clone https://github.com/pothosware/SoapySDR
$ cd SoapySDR
$ mkdir builddir;cd builddir; cmake ../
$ make -j4
$ sudo make install

$ git clone https://github.com/myriadrf/LimeSuite
$ cd LimeSuite

### Build OpenBTS

# 4\. SDR

软件定义无线电的内容即是可以灵活定义信号的处理过程，比如输出到TCP/UDP、文字音视频解码等。其中比较有名的有GNURadio、SoapySDR、Pothos（IDE）等（这里以GNURadio为例）。推荐在Linux中安装，当然也可在MacOS或者Windows中使用MacPorts进行安装，除此之外，也有[PyBombs](https://mirrors.tuna.tsinghua.edu.cn/help/pybombs/)可选。

在MacOS中安装需要使用MacPorts、XQuartz，MacPorts安装内容如下。

$ port info gnuradio
$ sudo port install gnuradio+wxgui gr-osmosdr sox
$ port content gnuradio
$ sudo port install hackrf
$ sudo port install rtl-sdr
$ sudo port search gr- # if you wanna more modules in gnuradio, don't be shy

然后打开XQuartz，将/opt/local/bin/gnuradio-companion加入到X自定义应用程序菜单中（建议修改默认的X终端程序内容为_xterm -e "source ~/.bash\_profile;/bin/bash"_）。

https://greatscottgadgets.com/sdr/

https://gist.github.com/machinaut/addf3438ef0c1a9cad38

https://osmocom.org/projects/gr-osmosdr/wiki/GrOsmoSDR#RTL-SDRSource

https://pypi.org/project/pyrtlsdr/#description
