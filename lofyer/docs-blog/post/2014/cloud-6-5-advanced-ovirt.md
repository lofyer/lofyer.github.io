---
title: "14. oVirt使用进阶"
date: "2014-05-11"
categories: 
  - "inthecloud"
---

## ovirt-shell

使用ovirt-shell在一定程度上适合于某些场景。

\# ovirt-shell -I -u admin@internal -l https://server\_ip/api

  ============================================================================
                      >>> connected to oVirt manager 3.4.0.0 <<<
  ============================================================================

  ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

                             Welcome to oVirt shell

  ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

\[oVirt shell (connected)\]#

## 主机hooks

参考[vdsm-hooks](https://github.com/oVirt/vdsm/tree/master/vdsm_hooks)。

## 集群策略

[参考这个PDF](http://www.ovirt.org/images/2/2a/Scheduler-Deep-Dive-oVirt.pdf)。

## libguestfs扩容

**LVM扩容** **普通扩容**

## UI plugin

**ShellInABox**

## 平台插件

**Foreman** **OpenStack Network** **OpenStack Image**

## 手动创建导出域

构造目录，形如：

\# tree exports
.
├── 37e0e64b-5445-4bc3-8675-ceff4637e8e3/
│   ├── dom\_md/
│   │   ├── ids
│   │   ├── inbox
│   │   ├── leases
│   │   ├── metadata
│   │   └── outbox
│   ├── images/
│   └── master/
│       ├── tasks/
│       └── vms/
└── \_\_DIRECT\_IO\_TEST\_\_

创建_leases_文件：

\# echo 2d2d2d2d2d2d465245452d2d2d2d2d2d3030303030303030303030303030
303000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
0000 | xxd -r -p > leases

添加如下内容至_metadata_文件：

CLASS=Backup
DESCRIPTION=Export
IOOPTIMEOUTSEC=10
LEASERETRIES=3
LEASETIMESEC=60
LOCKPOLICY=
LOCKRENEWALINTERVALSEC=5
MASTER\_VERSION=0
POOL\_UUID=
REMOTE\_PATH=192.168.1.108:/home/nfs\_exports
ROLE=Regular
SDUUID=37e0e64b-5445-4bc3-8675-ceff4637e8e3
TYPE=NFS
VERSION=0
#\_SHA\_CKSUM=5737f1270bf93fdd660fea819655b01a34c315b9

使用如下脚本（参考源码中packaging/setup/plugins/ovirt-engine-setup/config/iso\_domain.py）计算SHA校验值，并将其填入_metadata_中的\_SHA\_CKSUM段：

#!/usr/bin/python

import hashlib
from optparse import OptionParser

if \_\_name\_\_  == "\_\_main\_\_":
    parser = OptionParser()
    (options, args) = parser.parse\_args()
    if len(args) != 1:
        parser.error("Missing metadata file")
    f = open(args\[0\], "r")
    mds = {}
    for line in f:
        line = line.strip()
        if not line or line.startswith('#'):
            continue
        try:
            key,value = line.split('=', 1)
            if key == '\_SHA\_CKSUM':
                continue
            mds\[key\] = value
        except Exception, e:
            continue
    f.close()
    #print mds
    csum = hashlib.sha1()
    keys = mds.keys()
    keys.sort()
    for key in keys:
        value = mds\[key\]
        line = "%s=%s" % (key, value)
        csum.update(line)
    print(csum.hexdigest())

更改权限：

\# chown -R vdsm.kvm exports

然后可以作为空导出域进行导入。
