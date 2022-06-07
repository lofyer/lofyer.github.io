---
title: "Openstack 配置及其API的简单使用"
date: "2013-07-18"
categories: 
  - "cloud-infra"
---

## 安装除Neutron（Quantum）以外所有东西

在开始之前，假设有三个NIC

禁用selinux，并修改网络配置/etc/sysconfig/network-scripts/ifcfg-ethX

\# Internal
DEVICE=eth0
TYPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.1.11
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=192.168.1.1
DEFROUTE=yes
ONBOOT=yes

\# External
DEVICE=eth1
TYPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.1.12
NETMASK=255.255.255.0
DEFROUTE=yes
ONBOOT=yes

#Public Bridge
DEVICE=eth2
TYPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.1.13
NETMASK=255.255.255.0
DEFROUTE=yes
ONBOOT=yes

于Feodra18中安装Openstack，可参考文档，按照这个来的话对Openstack的设计理念将会有所了解
[Getting started with OpenStack on Fedora 18](https://fedoraproject.org/wiki/Getting_started_with_OpenStack_on_Fedora_18 "Getting started with OpenStack on Fedora 18")
或者运行

/\*安装完成后管理员界面为http://localhost/dashboard用户名admin，密码secrete\*/
# openstack-demo-install

以上两种方法把除了Neutron（quantum）其他的服务都配置好了，关于quantum的配置见文末

## 添加虚拟机实例

可参考文档
[Getting started with OpenStack on Fedora 18](https://fedoraproject.org/wiki/Getting_started_with_OpenStack_on_Fedora_18 "Getting started with OpenStack on Fedora 18")
在终端操作之前需要设置环境变量，将它们两者之一写到一个文件，然后source一下

export OS\_USERNAME=admin
export OS\_PASSWORD=secrete
export OS\_TENANT\_NAME=admin
export OS\_AUTH\_URL=http://127.0.0.1:5000/v2.0/

export ADMIN\_TOKEN=$(openssl rand -hex 10)
export SERVICE\_ENDPOINT=http://127.0.0.1:35357/v2.0/
export SERVICE\_TOKEN=\\$ADMIN\_TOKEN

source FileA

这个libvirt支持kvm以及xen虚拟化，所以支持的镜像也比较多，这里下载一个已经安装好的f17的qcow2镜像

\# axel -n 5 http://berrange.fedorapeople.org//blog/images/2012-11-15/f17-x86\_64-openstack-sda.qcow2
/\*注册镜像\*/
# glance add name=f17-jeos is\_public=true disk\_format=qcow2 container\_format=bare < f17-x86\_64-openstack-sda.qcow2

然后以demo用户进入dashboard，会看到添加进来的image，根据image启动实例，打开vnc界面（非localhost访问要改ip）。

## 安装操蛋的Neutron（quantum）服务

这东西刚改名，nova自带的有network服务，可这个可以虚拟L2 L3 switch，那就比较有意思了

/\*注册quantum\*/

\# keystone service-create --name quantum --type network --description 'OpenStack Networking Service'
# keystone endpoint-create --region myregion --service-id 26a55b340e254ad5bb78c0b14391e153 --publicurl "http://192.168.1.11:9696/" --adminurl "http://192.168.1.11:9696/" --internalurl "http://192.168.1.11:9696/"

/\*(可选)添加quantum服务用户\*/
get\_id.sh:

$ function get\_id () {
     echo \`"$@" | awk '/ id / { print $4 }'\`
}
# ADMIN\_ROLE=$(get\_id keystone role-create --name=admin)
# QUANTUM\_USER=$(get\_id keystone user-create --name=quantum --pass="servicepass" --email=demo@example.com --tenant-id service)
# keystone user-role-add --user\_id $QUANTUM\_USER --role\_id $ADMIN\_ROLE --tenant\_id service

/\*服务插件二选一，linuxbrideg和openvswitch，这里使用后者，先安装插件\*/

\# yum install openstack-quantum-openvswitch

/\*启动并添加服务\*/

\# quantum-server-setup --plugin openvswitch
# quantum-node-setup --plugin openvswitch
# service quantum-server start
# chkconfig quantum-server on

# service openvswitch start
# chkconfig openvswitch on

# ovs-vsctl add-br br-int
# chkconfig quantum-openvswitch-agent on
# service quantum-openvswitch-agent start

# quantum-dhcp-setup --plugin openvswitch
# chkconfig quantum-dhcp-agent on
# service quantum-dhcp-agent start

# ovs-vsctl add-br br-ex
# ovs-vsctl add-port br-ex eth1
# quantum-l3-setup --plugin openvswitch
# chkconfig quantum-l3-agent on
# service quantum-l3-agent start
\# chkconfig quantum-metadata-agent on
# service quantum-metadata-agent start

修改并配置网络 /etc/sysconf/network-scripts/ifcfg-eth1，/etc/sysconf/network-scripts/ifcfg-br-ex

\# External
DEVICE=eth1
TYPE=Ethernet
BOOTPROTO=none
NM\_CONTROLLED=no
BRIDGE=br-ex
ONBOOT=yes

#Public Bridge
DEVICE=br-ex
TYPE=Bridge
BOOTPROTO=static
IPADDR=192.168.2.11
NETMASK=255.255.255.0
NM\_CONTROLLED=no
ONBOOT=yes

\# ip addr del 10.0.0.9/24 dev eth1
# ip addr add 10.0.0.9/24 dev br-ex

这里类似VPN的重写HEADER

echo 1 > /proc/sys/net/ipv4/conf/all/forwarding
sysctl -w net.ipv4.ip\_forward=1 
iptables -A FORWARD -i eth0 -o br-ex -s 10.10.10.0/24 -m conntrack --ctstate NEW -j ACCEPT
iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A POSTROUTING -s 10.10.10.0/24 -t nat -j MASQUERADE

最后重启机器即可

## 通过curl使用openstack api

[参考这篇文章](http://catn.com/2013/04/23/create-an-openstack-instance-with-just-curl/ "http://catn.com/2013/04/23/create-an-openstack-instance-with-just-curl/")
注意区分token和固定id

### 获取可用镜像

 

curl -s http://10.199.0.250:8774/v2/51ad87714b86442d9a74537d6f890060/images 
-X GET 
-H "X-Auth-Project-Id: admin" 
-H "Accept: application/json" 
-H "X-Auth-Token: 6083193874684f38865b086a8a5f4b7b" | python -mjson.tool

 

### 创建虚拟机

 

curl -i http://10.199.0.250:8774/v2/51ad87714b86442d9a74537d6f890060/servers 
-X POST -H "X-Auth-Project-Id: admin" 
-H "Content-Type: application/json" 
-H "Accept: application/json" 
-H "X-Auth-Token: 6083193874684f38865b086a8a5f4b7b" 
-d '{"server": {"name": "instance1", "imageRef": "992f5732-af50-40fc-987f-25951cbce943", "key\_name": "damion-flybook", "flavorRef": "3", "max\_count": 1, "min\_count": 1}}'
