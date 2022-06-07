---
title: "Migrate vm from ESXi to oVirt"
date: "2014-04-25"
categories: 
  - "cloud-infra"
---

oVirt: v3.3, CentOS, 192.168.1.111 ESXi: 5.x, 192.168.1.135

**For Windows vm, do this** [http://pve.proxmox.com/wiki/Migration\_of\_servers\_to\_Proxmox\_VE#VMware\_to\_Proxmox\_VE\_.28KVM.29](http://pve.proxmox.com/wiki/Migration_of_servers_to_Proxmox_VE#VMware_to_Proxmox_VE_.28KVM.29) Before you begin make a copy of the VMware image. **Remove VMware tools** Start the Windows virtual machine on VMware and remove the VMware tools via the Windows control panel. Reboot. **Enable IDE** Start the Windows virtual machine on VMware and execute the mergeide.reg (File:[Mergeide](http://blog.lofyer.org/migrate-vm-esxi-ovirt/mergeide/)). Now the registry is changed that your Windows can boot from IDE, necessary for KVM. Make sure Atapi.sys, Intelide.sys, Pciide.sys, and Pciidex.sys are in the %SystemRoot%\\System32\\Drivers folder. If any are missing they can be extracted from %SystemRoot%\\Driver Cache\\I386\\Driver.cab which can be opened in Windows file Exlorer like a directory and then the missing files can be copied out. [see Microsoft KB article for details.](https://support.microsoft.com/en-us/kb/314082) **Shutdown Windows.**

1\. Create a nfs export domain on your oVirt datacenter.

/vdsm/export	0.0.0.0/0.0.0.0(rw)

2\. Install virt-v2v on CentOS and add authentication.

\# yum install virt-v2v

machine 192.168.1.135 login root password 1234567

Change permission of _.netrc_ as saying of manual or you will get a warning with wrong permission.

\# chmod 600 ~/.netrc

3\. Import myvm. **Make sure that your vm in ESXi is powered off.**

\# virt-v2v -ic esx://192.168.1.135/?no\_verify=1 -o rhev -os 192.168.1.111:/virtfan/export --network mgmtnet myvm
myvm\_myvm: 100% \[====================================================\]D 0h04m48s

virt-v2v: myvm configured with virtio drivers.

4\. Import myvm from export domain to data domain.

**Import.**

[![import](/blog/images/import.png)](http://blog.lofyer.org/migrate-vm-esxi-ovirt/import/)

**Run.**

[![run](/blog/images/run.png)](http://blog.lofyer.org/migrate-vm-esxi-ovirt/run/)
