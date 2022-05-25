---
title: "15. ovirt-shell"
date: "2014-05-22"
categories: 
  - "inthecloud"
---

和许多虚拟化平台一样，ovirt也有自己的shell，并且在功能上有自己的特点。

## 连接ovirt-shell

\# yum install ovirt-engine-cli
# ovirt-shell -l https://192.168.1.190 -u admin@internal -I

连接以后，可双击tab以自动补全。 **你可以看出来，在ovirt中，vm、host、cluster等均可以看作“对象”，而其操作比如“start”、“stop”等可看作方法。**
