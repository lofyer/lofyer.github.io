---
title: "openflow & openvswitch in openwrt"
date: "2015-11-29"
categories: 
  - "devices"
---

I've got a NetGear R6300 V2 in hand.

As you already know, installing openvswitch in openwrt is very simple now. But for openflow, you need rebuild the openwrt image.

# Install openvswitch

In any openwrt system you can just do:

root@OpenWrt:~# opkg update
root@OpenWrt:~# opkg install kmod-openvswitch
root@OpenWrt:~# opkg install openvswitch
root@OpenWrt:~# ovsdb-tool create

To build the openflow supported openwrt, you need:

## Build openwrt

OS: Ubuntu 14.04 Openwrt source: https://downloads.openwrt.org/chaos\_calmer/15.05/bcm53xx/generic/OpenWrt-SDK-15.05-bcm53xx\_gcc-4.8-linaro\_uClibc-0.9.33.2\_eabi.Linux-x86\_64.tar.bz2

Prepare your build env:

root@ubuntu# apt-get install build-essential binutils flex bison autoconf gettext texinfo sharutils subversion libncurses5-dev ncurses-term zlib1g-dev gawk git ccache
root@ubuntu# git clone https://github.com/CPqD/openflow-openwrt.git
root@ubuntu# tar xf OpenWrt-SDK-15.05-bcm53xx\_gcc-4.8-linaro\_uClibc-0.9.33.2\_eabi.Linux-x86\_64.tar.bz2
root@ubuntu# cd OpenWrt-SDK-15.05-bcm53xx\_gcc-4.8-linaro\_uClibc-0.9.33.2\_eabi.Linux-x86\_64
root@ubuntu# ln -s openflow-openwrt/openflow-1.3/files
root@ubuntu# cd packages/
root@ubuntu# ln -s ~/openflow-openwrt/openflow-1.3/
root@ubuntu# cd ..
root@ubuntu# make menuconfig

Select the OpenFlow package under Network and build the image.

root@ubuntu# make -j4 V=s

Bugfix: gdbm build ERROR

$ make V=s package/gdbm/{clean,configure}
change prefix from "...buildbot..." to "/home/demo/OpenWrt..."
$ make V=s

[![屏幕快照 2015-12-01 上午12.41.59](/blog/images/屏幕快照-2015-12-01-上午12.41.59.png)](http://blog.lofyer.org/openflow-openvswitch-in-openwrt/%e5%b1%8f%e5%b9%95%e5%bf%ab%e7%85%a7-2015-12-01-%e4%b8%8a%e5%8d%8812-41-59/)

[![屏幕快照 2015-12-01 上午10.18.25](/blog/images/屏幕快照-2015-12-01-上午10.18.25.png)](http://blog.lofyer.org/openflow-openvswitch-in-openwrt/%e5%b1%8f%e5%b9%95%e5%bf%ab%e7%85%a7-2015-12-01-%e4%b8%8a%e5%8d%8810-18-25/)

[![屏幕快照 2015-12-01 上午10.16.45](/blog/images/屏幕快照-2015-12-01-上午10.16.45.png)](http://blog.lofyer.org/openflow-openvswitch-in-openwrt/%e5%b1%8f%e5%b9%95%e5%bf%ab%e7%85%a7-2015-12-01-%e4%b8%8a%e5%8d%8810-16-45/)

[![屏幕快照 2015-12-01 上午2.02.02](/blog/images/屏幕快照-2015-12-01-上午2.02.02.png)](http://blog.lofyer.org/openflow-openvswitch-in-openwrt/%e5%b1%8f%e5%b9%95%e5%bf%ab%e7%85%a7-2015-12-01-%e4%b8%8a%e5%8d%882-02-02/)

[![屏幕快照 2015-12-01 上午1.10.52](/blog/images/屏幕快照-2015-12-01-上午1.10.52.png)](http://blog.lofyer.org/openflow-openvswitch-in-openwrt/%e5%b1%8f%e5%b9%95%e5%bf%ab%e7%85%a7-2015-12-01-%e4%b8%8a%e5%8d%881-10-52/)

files:

[backup-OpenWrt-2015-11-30.tar](http://blog.lofyer.org/openflow-openvswitch-in-openwrt/backup-openwrt-2015-11-30-tar/)

root@OpenWrt:~# ovs-vsctl show
ebcae155-3c74-4088-a0bd-1802438f7148
    Bridge "ovsbr0"
        Port "eth0.3"
            Interface "eth0.3"
        Port "ovsbr0"
            Interface "ovsbr0"
                type: internal
        Port "wlan0"
            Interface "wlan0"
        Port "eth0.4"
            Interface "eth0.4"

config interface 'loopback'
	option ifname 'lo'
	option proto 'static'
	option ipaddr '127.0.0.1'
	option netmask '255.0.0.0'

config globals 'globals'
	option ula\_prefix 'fd6d:a096:2bef::/48'

config interface 'lan'
	option force\_link '1'
	option proto 'static'
	option ipaddr '192.168.1.1'
	option netmask '255.255.255.0'
	option ip6assign '60'
	option \_orig\_ifname 'eth0.1 radio0.network1'
	option \_orig\_bridge 'true'
	option ifname 'eth0.1'

config interface 'wan'
	option ifname 'eth0.2'
	option force\_link '1'
	option proto 'dhcp'

config interface 'wan6'
	option ifname 'eth0.2'
	option proto 'dhcpv6'

config switch
	option name 'switch0'
	option reset '1'
	option enable\_vlan '1'

config switch\_vlan
	option device 'switch0'
	option vlan '1'
	option ports '3 5t'

config switch\_vlan
	option device 'switch0'
	option vlan '2'
	option ports '4 5t'

config switch\_vlan
	option device 'switch0'
	option vlan '3'
	option ports '2 5t'

config switch\_vlan
	option device 'switch0'
	option vlan '4'
	option ports '1 5t'

config switch\_vlan
	option device 'switch0'
	option vlan '5'
	option ports '0 5t'

config interface 'port3\_eth04'
	option proto 'none'
	option ifname 'eth0.4'
	option auto '1'

config interface 'port4\_eth05\_admin'
	option proto 'static'
	option ifname 'eth0.5'
	option netmask '255.255.255.0'
	option ipaddr '192.168.2.1'

config route

config interface 'port2\_eth03\_nm'
	option proto 'static'
	option ifname 'eth0.3'

config interface 'ovsbr0'
	option proto 'static'
	option ifname 'ovsbr0'
	option ipaddr '192.168.77.1'
	option netmask '255.255.255.0'
	option type 'bridge'

config interface 'wlan0'
	option proto 'none'

config wifi-device 'radio0'
	option type 'mac80211'
	option channel '11'
	option hwmode '11g'
	option path '18000000.axi/bcma0:7/pci0000:00/0000:00:00.0/0000:01:00.0/bcma1:1'
	option txpower '20'
	option country '00'

config wifi-iface
	option device 'radio0'
	option mode 'ap'
	option ssid 'OpenWrt-1'
	option key 'password'
	option encryption 'none'
	option network 'wlan0'

sleep 10
/etc/init.d/openvswitch restart
