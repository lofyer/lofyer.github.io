---
title: "Use dhcp or dnsmasq as DHCP server in libvirt-based environment"
date: "2015-08-26"
categories: 
  - "cloud"
  - "linux-admin"
---

Default dhcp and dnsmasq are conflict in a OS. Just change to another if one failed.

# yum install dhcp

/etc/sysconfig/dhcpd is no longer needed in RHEL-7. However, it is still working in other distributions. **Just assign an IP to the interface which in the subnet you want dhcp serve**.

\[root@localhost ~\]# vi /etc/sysconfig/dhcpd
DHCPDARGS="eth0 eth1";

\[root@localhost ~\]# yum -y install dhcp
\[root@localhost ~\]# vi /etc/dhcp/dhcpd.conf
# create new
# specify domain name
option domain-name "example.com";
# specify name server's hostname or IP address
option domain-name-servers dhcp.example.com;
# default lease time
default-lease-time 600;
# max lease time
max-lease-time 7200;
# this DHCP server to be declared valid
authoritative;
# specify network address and subnet mask
subnet 10.0.0.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.0.0.200 10.0.0.254;
    # specify broadcast address
    option broadcast-address 10.0.0.255;
    # specify default gateway
    option routers 10.0.0.1;
    optiondomain-name-servers 192.168.188.11, 192.168.188.12
}
\[root@localhost ~\]# systemctl start dhcpd 
\[root@localhost ~\]# systemctl enable dhcpd 
ln -s '/usr/lib/systemd/system/dhcpd.service' '/etc/systemd/system/multi-user.target.wants/dhcpd.service'

# dnsmasq

Dnsmasq is very useful in libvirt/qemu/docker based environment. To bind 192.168.200.0/24 to interface br0(192.168.200.22-bridge2eth1):

#!/bin/bash
/usr/sbin/dnsmasq --strict-order --pid-file=/var/run/libvirt/network/br0--conf-file= --except-interface lo --bind-interfaces br0 --listen-address 192.168.200.22 --dhcp-range 192.168.200.2,192.168.200.254 --dhcp-leasefile=/var/lib/libvirt/dnsmasq/br0.leases --dhcp-lease-max=253 --dhcp-no-override --dhcp-hostsfile=/var/lib/libvirt/dnsmasq/br0.hostsfile --addn-hosts=/var/lib/libvirt/dnsmasq/br0.addnhosts
