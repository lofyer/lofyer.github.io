---
title: "Build NetLogo for ARM(NVIDIA-JETSON-NANO)"
date: "2019-04-06"
categories: 
  - "abm"
  - "nvidia-jetson-nano"
---

本文主要目的为测试这块板子的性能，看看其是否有作为边缘节点的能力。

## Env

Ubuntu 18.04(jetson-nano-sd-r32.1-2019-03-18)

## Prepare

$ sudo set -i 's/ports.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
$ sudo apt install -y curl x11vnc openjdk openjfx libopenjfx-jni libopenjfx-java

## Build

$ git clone https://github.com/NetLogo/NetLogo
$ cd NetLogo
$ git submodule update --init
$ ./sbt netlogo/compile

Run and Package

$ ./sbt netlogo/run
$ ./sbt dist/buildNetLogo

## Addon: Add VNC server to your board

$ sudo apt install -y x11nvc
$ x11vnc # to generate ~/.vnc files
$ echo 'x11vnc --loop &' >> ~/.xsessionrc

## Ref

[https://github.com/NetLogo/NetLogo/wiki/Building](https://github.com/NetLogo/NetLogo/wiki/Building)

[https://github.com/NetLogo/NetLogo/wiki/Releasing](https://github.com/NetLogo/NetLogo/wiki/Releasing)
