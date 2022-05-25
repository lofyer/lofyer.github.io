---
title: "ovirt notes"
date: "2012-10-18"
categories: 
  - "cloud-infra"
---

Ovirt Just make some notes: # need net qemu-kvm -m 1024 -localtime -M pc -smp 2 -drive file=ovirt.qcow2,cache=writeback,boot=on -boot d -name kvm-ovirt,process=ovirt -usb -usbdevice tablet Ovirt provided a fedora.iso with node in it. Ao is building the engine.

#on virtfan spicec -h localhost -p 5910

node应该是自动加入engine的，结果是现在能加入，但是不能安装 nmap后engine确实是有8443，node只有22，安装过程是交互的（node通过外网获取安装包，返回状态到engine）

改变端口为443，按照troubleshooting更改engine上的nfs服务。f17上nfs默认建立服务为v4，要改为v3，并且添加group和user id分别为36的vdsm与kvm，用提供的脚本测试下。 [Troubleshooting\_NFS\_Storage\_Issues](http://wiki.ovirt.org/wiki/Troubleshooting_NFS_Storage_Issues "fix the nfs")

ok，等明天加入节点就可以了。

Building ovirt from source [with cmdline](http://wiki.ovirt.org/wiki/Building_oVirt_engine "with cmdline") [within an ide](http://wiki.ovirt.org/wiki/Building_Ovirt_Engine/IDE "within an ide") [install jboss maven plugin on eclipse](https://community.jboss.org/en/tools/blog/2011/02/21/getting-started-with-jboss-tools-jboss-maven-integration-and-weld "install jboss maven plugin on eclipse")

here's the engine arch [![](/blog/post/images/Engine-arch.png "Engine-arch")](http://69.164.197.168/wp-content/uploads/2012/10/Engine-arch.png)

and here's the engine-core arch [![](/blog/post/images/Engine-arch2.png "Engine-arch2")](http://69.164.197.168/wp-content/uploads/2012/10/Engine-arch2.png)

运行engine-manage-domain需要ipa-server，类似windows的ActiveDirectory，需要不同于engine的主机上安装ipa [安装ipa-server](http://blog.jaurola.fi/2011/12/28/implementing-ipa-server-with-windowslinux-environment "ipa-server")
