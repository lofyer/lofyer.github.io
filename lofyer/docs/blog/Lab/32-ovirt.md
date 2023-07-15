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

here's the engine arch [![](/blog/images/Engine-arch.png "Engine-arch")](http://69.164.197.168/wp-content/uploads/2012/10/Engine-arch.png)

and here's the engine-core arch [![](/blog/images/Engine-arch2.png "Engine-arch2")](http://69.164.197.168/wp-content/uploads/2012/10/Engine-arch2.png)

运行engine-manage-domain需要ipa-server，类似windows的ActiveDirectory，需要不同于engine的主机上安装ipa [安装ipa-server](http://blog.jaurola.fi/2011/12/28/implementing-ipa-server-with-windowslinux-environment "ipa-server")
---
title: "Add nat to ovirt via vdsm hooks"
date: "2014-05-04"
categories: 
  - "cloud-infra"
---

OK, I do not like control group very much for now.

## Comment device section in control group config, sorry for my laziness...

mount {
        cpuset  = /cgroup/cpuset;
        cpu     = /cgroup/cpu;
        cpuacct = /cgroup/cpuacct;
        memory  = /cgroup/memory;
#       devices = /cgroup/devices;
        freezer = /cgroup/freezer;
#       net\_cls = /cgroup/net\_cls;
        blkio   = /cgroup/blkio;
}

## Enable ip forward

net.ipv4.ip\_forward = 1

**Reboot the host**

## Connect to virsh to enable libvirt's default virbr0(NAT)

If you don't know the password for your account, just use command below to create one.

# saslpasswd2 -a libvirt root

Create a nat network.

 nat
  b42e377d-e849-4c36-bd98-3d090def5ecc 

# virsh net-create /etc/libvirt/qemu/networks/nat.xml
# virsh net-autostart nat
# virsh net-start nat

## Create tun device and add it to virbr0

**UPDATE: This could be ignored if you use extnet.py**

# tunctl -t nat0 -u qemu
# brctl addif virbr1 nat0

## Add hook file to vdsm

**UPDATE: use extnet from github with a little modification (Only the first vNIC will be NAT, the second one still keeps its way).**

```
#!/usr/bin/python

import os
import sys
import traceback
import xml.dom
import hooking

def replaceSource(interface, newnet):
    source, = interface.getElementsByTagName('source')
    source.removeAttribute('bridge')
    source.setAttribute('network', newnet)
    interface.setAttribute('type', 'network')

def main():
    params = "default"
    os.environ.\_\_setitem\_\_("extnet",params)
    newnet = os.environ.get('extnet')
    if newnet is not None:
        doc = hooking.read\_domxml()
        interface = doc.getElementsByTagName('interface')[0]
        replaceSource(interface, newnet)
        hooking.write\_domxml(doc)
def test():

    interface = xml.dom.minidom.parseString("""
    """).getElementsByTagName('interface')[0]

    print "Interface before forcing network: %s" % \\
        interface.toxml(encoding='UTF-8')

    replaceSource(interface, 'yipee')
    print "Interface after forcing network: %s" % \\
        interface.toxml(encoding='UTF-8')

if \_\_name\_\_ == '\_\_main\_\_':
    try:
        if '--test' in sys.argv:
            test()
        else:
            main()
    except:
        hooking.exit\_hook('extnet hook: [unexpected error]: %s\\n' %
                          traceback.format\_exc()) 
```

**QEMU-CMD way:**

```
import os
import sys
import hooking
import traceback
import json
import shutil

def addQemuNs(domXML):
    domain = domXML.getElementsByTagName('domain')[0]
    domain.setAttribute('xmlns:qemu',
                        'http://libvirt.org/schemas/domain/qemu/1.0')

def injectQemuCmdLine(domXML, qc):
    domain = domXML.getElementsByTagName('domain')[0]
    qctag = domXML.createElement('qemu:commandline')

    for cmd in qc:
        qatag = domXML.createElement('qemu:arg')
        qatag.setAttribute('value', cmd)

        qctag.appendChild(qatag)

    domain.appendChild(qctag)
domxml = hooking.read\_domxml()

# Get vm uuid, just in case

cur\_vm\_uuid = domxml.getElementsByTagName('uuid')[0].firstChild.nodeValue

macaddr = "94:de:80:ea:30:f5"
natname = "nat0"
params = '["-netdev","tap,ifname=%s,script=no,id=hostnet0,downscript=no","-device","virtio-net-pci,mac=%s,netdev=hostnet0,bus=pci.0,addr=0x10"]' % (natname, macaddr)
os.environ.\_\_setitem\_\_("qemu\_cmdline",params)

# Modify Qemu Parameter

if 'qemu\_cmdline' in os.environ:
    try:
        domxml = hooking.read\_domxml()

        qemu\_cmdline = json.loads(os.environ['qemu\_cmdline'])
        addQemuNs(domxml)
        injectQemuCmdLine(domxml, qemu\_cmdline)

        hooking.write\_domxml(domxml)
    except:
        sys.stderr.write('qemu\_cmdline: [unexpected error]: %s\\n'
                         % traceback.format\_exc())
        sys.exit(2)
```

Then you should start the vm **WITHOUT ANY NIC** if you are using _nat.py_.
---
title: "Migrate vm from ESXi to oVirt"
date: "2014-04-25"
categories: 
  - "cloud-infra"
---

oVirt: v3.3, CentOS, 192.168.1.111 ESXi: 5.x, 192.168.1.135

**For Windows vm, do this** [http://pve.proxmox.com/wiki/Migration\_of\_servers\_to\_Proxmox\_VE#VMware\_to\_Proxmox\_VE\_.28KVM.29](http://pve.proxmox.com/wiki/Migration_of_servers_to_Proxmox_VE#VMware_to_Proxmox_VE_.28KVM.29) Before you begin make a copy of the VMware image. **Remove VMware tools** Start the Windows virtual machine on VMware and remove the VMware tools via the Windows control panel. Reboot. **Enable IDE** Start the Windows virtual machine on VMware and execute the mergeide.reg (File:[Mergeide](http://blog.lofyer.org/migrate-vm-esxi-ovirt/mergeide/)). Now the registry is changed that your Windows can boot from IDE, necessary for KVM. Make sure Atapi.sys, Intelide.sys, Pciide.sys, and Pciidex.sys are in the %SystemRoot%\\System32\\Drivers folder. If any are missing they can be extracted from %SystemRoot%\\Driver Cache\\I386\\Driver.cab which can be opened in Windows file Exlorer like a directory and then the missing files can be copied out. [see Microsoft KB article for details.](https://support.microsoft.com/en-us/kb/314082) **Shutdown Windows.**

1. Create a nfs export domain on your oVirt datacenter.

/vdsm/export	0.0.0.0/0.0.0.0(rw)

2. Install virt-v2v on CentOS and add authentication.

# yum install virt-v2v

machine 192.168.1.135 login root password 1234567

Change permission of _.netrc_ as saying of manual or you will get a warning with wrong permission.

# chmod 600 ~/.netrc

3. Import myvm. **Make sure that your vm in ESXi is powered off.**

# virt-v2v -ic esx://192.168.1.135/?no\_verify=1 -o rhev -os 192.168.1.111:/virtfan/export --network mgmtnet myvm
myvm\_myvm: 100% [====================================================]D 0h04m48s

virt-v2v: myvm configured with virtio drivers.

4. Import myvm from export domain to data domain.

**Import.**

[![import](/blog/images/import.png)](http://blog.lofyer.org/migrate-vm-esxi-ovirt/import/)

**Run.**

[![run](/blog/images/run.png)](http://blog.lofyer.org/migrate-vm-esxi-ovirt/run/)

## "VDSM" "2012-10-29"

不喜欢java，就是不喜欢。。但还是要干的。。 4000多个文件构建UML图的话有点困难，都是小类，看了些tools的代码，然后转到vdsm内容，通讯方式是xml-rpc，当初我猜对了。

vdsm是oVirt的节点代理，功能可订制，也可移植到其他管理平台，基于KVM，储存VM各种暂态数据 vdsm提供的api通过xmlrpc使用，架构如下

```
------------+------VDSM-------+------+-----
            |                 |      |
    sysfs---+                 |      |
      LVM---+              libvirt   |
net-tools---+              qmp|      |Virtio serial
      xxx---+             ---+      +-------------/
           ...                |      |
                      -------/        \-------
                     /                        \
                    |  KVM-QEMU VM             |
                     \________________________/
```

主要包括vdsm,vdsm\_cli,vds\_bootstrap,vdsm\_reg,vdsm\_hooks 其中lifecycle hooks有针对vdsm和vm在before和after期间 bootstrap用于验证兼容性，包括网络，cpu，认证等，目前只支持RH自家产品
