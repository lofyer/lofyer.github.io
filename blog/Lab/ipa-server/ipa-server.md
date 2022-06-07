---
title: "IPA服务器搭建"
date: "2012-11-27"
categories: 
  - "linux-admin"
---

IPASERVER+DNS(DDWRT)+IPACLIENT

SERVER: ipa-server-install时这里可以不要内部dns 注意host以及domain要在dns里有记录 添加用户 ipa user-add 设置密码 ipa passwd demo DNS: 下面是ddwrt的dnsmasq配置 `domain=ovirt.engine local=/ovirt.engine/ expand-hosts address=/ovirtmgmt.ovirt.engine/192.168.1.106 ptr-record=106.1.168.192.in-addr.arpa,"ovirtmgmt.ovirt.engine" address=/ipa.ovirt.engine/192.168.1.108 ptr-record=108.1.168.192.in-addr.arpa,"ipa.ovirt.engine" srv-host=_kerberos-master._tcp,ipa.ovirt.engine,88,0,100 srv-host=_kerberos-master._udp,ipa.ovirt.engine,88,0,100 srv-host=_kerberos._tcp,ipa.ovirt.engine,88,0,100 srv-host=_kerberos._udp,ipa.ovirt.engine,88,0,100 srv-host=_kpasswd._tcp,ipa.ovirt.engine,464,0,100 srv-host=_kpasswd._udp,ipa.ovirt.engine,464,0,100 srv-host=_ldap._tcp,ipa.ovirt.engine,389,0,100` [![](/blog/images/QQ截图20121127132532.png "QQ截图20121127132532")](http://69.164.197.168/wp-content/uploads/2012/11/QQ截图20121127132532.png)

IPACLIENT: install时注意域名及主机名正确

OVIRT: 初次使用要在SERVER运行 kinit admin

reinstall出错时： # ipa-server-install --uninstall -U # ls -ld /var/lib/pki-ca If it exists run: # pkiremove -pki\_instance\_root=/var/lib -pki\_instance\_name=pki-ca --force # yum reinstall pki-selinux
