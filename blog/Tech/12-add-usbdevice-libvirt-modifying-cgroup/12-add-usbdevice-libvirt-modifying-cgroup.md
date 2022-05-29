---
title: "Add usbdevice to libvirt by modifying cgroup"
date: "2013-10-31"
categories: 
  - "cloud-infra"
tags:
  - "Libvirt"
  - "Device Redirection"
---

CGROUP means Control group. In my case, usb-redir works fine while usb-host not. [https://www.kernel.org/doc/Documentation/cgroups/devices.txt](https://www.kernel.org/doc/Documentation/cgroups/devices.txt "Cgroup HowTO")

## Just do it

\# sed -i 's/devices /#devices /'
# reboot

## Add fine control list

### Review the rules to libvirt, and leave all the character device to it

\# cgget libvirt
To remove "c" device
# echo c > /sys/fs/cgroup/libvirt/devices.deny
To add all the "c" devices
# echo "c \*:\* rwm" > /sys/fs/cgroup/libvirt/devices.allow

devices.deny/allow is something like end-point.

### About hierarchy

**A child will not have more permission than its parent.**
