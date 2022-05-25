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

\# saslpasswd2 -a libvirt root

Create a nat network.

 nat
  b42e377d-e849-4c36-bd98-3d090def5ecc 

\# virsh net-create /etc/libvirt/qemu/networks/nat.xml
# virsh net-autostart nat
# virsh net-start nat

## Create tun device and add it to virbr0

**UPDATE: This could be ignored if you use extnet.py**

\# tunctl -t nat0 -u qemu
# brctl addif virbr1 nat0

## Add hook file to vdsm

**UPDATE: use extnet from github with a little modification (Only the first vNIC will be NAT, the second one still keeps its way).**

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
        interface = doc.getElementsByTagName('interface')\[0\]
        replaceSource(interface, newnet)
        hooking.write\_domxml(doc)
def test():

    interface = xml.dom.minidom.parseString("""
    """).getElementsByTagName('interface')\[0\]

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
        hooking.exit\_hook('extnet hook: \[unexpected error\]: %s\\n' %
                          traceback.format\_exc()) 

**QEMU-CMD way:**

import os
import sys
import hooking
import traceback
import json
import shutil

def addQemuNs(domXML):
    domain = domXML.getElementsByTagName('domain')\[0\]
    domain.setAttribute('xmlns:qemu',
                        'http://libvirt.org/schemas/domain/qemu/1.0')

def injectQemuCmdLine(domXML, qc):
    domain = domXML.getElementsByTagName('domain')\[0\]
    qctag = domXML.createElement('qemu:commandline')

    for cmd in qc:
        qatag = domXML.createElement('qemu:arg')
        qatag.setAttribute('value', cmd)

        qctag.appendChild(qatag)

    domain.appendChild(qctag)
domxml = hooking.read\_domxml()

# Get vm uuid, just in case

cur\_vm\_uuid = domxml.getElementsByTagName('uuid')\[0\].firstChild.nodeValue

macaddr = "94:de:80:ea:30:f5"
natname = "nat0"
params = '\["-netdev","tap,ifname=%s,script=no,id=hostnet0,downscript=no","-device","virtio-net-pci,mac=%s,netdev=hostnet0,bus=pci.0,addr=0x10"\]' % (natname, macaddr)
os.environ.\_\_setitem\_\_("qemu\_cmdline",params)

# Modify Qemu Parameter

if 'qemu\_cmdline' in os.environ:
    try:
        domxml = hooking.read\_domxml()

        qemu\_cmdline = json.loads(os.environ\['qemu\_cmdline'\])
        addQemuNs(domxml)
        injectQemuCmdLine(domxml, qemu\_cmdline)

        hooking.write\_domxml(domxml)
    except:
        sys.stderr.write('qemu\_cmdline: \[unexpected error\]: %s\\n'
                         % traceback.format\_exc())
        sys.exit(2)

Then you should start the vm **WITHOUT ANY NIC** if you are using _nat.py_.
