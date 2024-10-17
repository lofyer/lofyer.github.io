---
title: "ZStack Kubernetes/k8s/Docker Intergration"
date: 2017-07-15
categories: 
  - "cloud-infra"
  - "draft"
---

# 云计算笔记

---

## "Use dhcp or dnsmasq as DHCP server in libvirt-based environment" "2015-08-26"

Default dhcp and dnsmasq are conflict in a OS. Just change to another if one failed.

```
# yum install dhcp
```

/etc/sysconfig/dhcpd is no longer needed in RHEL-7. However, it is still working in other distributions. **Just assign an IP to the interface which in the subnet you want dhcp serve**.

```
[root@localhost ~]# vi /etc/sysconfig/dhcpd
DHCPDARGS="eth0 eth1";

[root@localhost ~]# yum -y install dhcp
[root@localhost ~]# vi /etc/dhcp/dhcpd.conf
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
[root@localhost ~]# systemctl start dhcpd 
[root@localhost ~]# systemctl enable dhcpd 
ln -s '/usr/lib/systemd/system/dhcpd.service' '/etc/systemd/system/multi-user.target.wants/dhcpd.service'
```

`Dnsmasq` is very useful in libvirt/qemu/docker based environment. To bind 192.168.200.0/24 to interface br0(192.168.200.22-bridge2eth1):

```
#!/bin/bash
/usr/sbin/dnsmasq --strict-order --pid-file=/var/run/libvirt/network/br0--conf-file= --except-interface lo --bind-interfaces br0 --listen-address 192.168.200.22 --dhcp-range 192.168.200.2,192.168.200.254 --dhcp-leasefile=/var/lib/libvirt/dnsmasq/br0.leases --dhcp-lease-max=253 --dhcp-no-override --dhcp-hostsfile=/var/lib/libvirt/dnsmasq/br0.hostsfile --addn-hosts=/var/lib/libvirt/dnsmasq/br0.addnhosts
```

---

## "《KVM私有云架构设计与实践》目录" "2015-09-18"

书籍目前已出版，购买链接在下文提供，也可直接联系作者购买，微信lofyer_org。

[当当](http://product.dangdang.com/25074443.html) [亚马逊](https://www.amazon.cn/%E7%A7%81%E6%9C%89%E4%BA%91%E6%9E%B6%E6%9E%84%E8%AE%BE%E8%AE%A1%E4%B8%8E%E5%AE%9E%E8%B7%B5-%E8%92%8B%E8%BF%AA/dp/B07143VQ2M/ref=sr_1_1?ie=UTF8&qid=1495023849&sr=8-1&keywords=kvm%E7%A7%81%E6%9C%89%E4%BA%91) [京东](https://search.jd.com/Search?keyword=kvm%E7%A7%81%E6%9C%89%E4%BA%91&enc=utf-8&wq=kvm%E7%A7%81%E6%9C%89%E4%BA%91&pvid=fab47274675745f28319953ba27d24c5)

[![](/blog/images/21495029670_.pic_-1024x1024.jpg)](https://blog.lofyer.org/wp-content/uploads/21495029670_.pic_.jpg)

---

## "OpenStack自动化部署与高可用虚拟控制节点设计" "2017-05-05"

为了大家的部署方便与维护着想，我决定把这俩东西再完善一下然后再把实现源码放出来，虽不能做到产品级但是也能对付很多场景了。

自动部署（类似mirantis但更友好） 0. livecd+独立配置分区 1. 环境配置 2. PXE安装 3. 自动化脚本

Hosted-crotroller（控制节点虚拟化） 1. etcd 2. 健康检查与评分 3. 脑裂防护 4. 使用Paxos写事务机制，如果时间差机制不工作的话。

下面给大家简单解释一Paxos和时间差机制的原理（解释的不够到位，有兴趣的看实现吧）：

1. Paxos，原文可参考[https://www.quora.com/Distributed-Systems-What-is-a-simple-explanation-of-the-Paxos-algorithm](https://www.quora.com/Distributed-Systems-What-is-a-simple-explanation-of-the-Paxos-algorithm)

**此场景实用于一写多。**

两人结婚，牧师问他俩，“你们愿意吗？”，“我愿意”，“我愿意”。 那么OK，就结了。

这时候假如有一对姐妹同时喜欢一个男的，牧师问他仨，“你们愿意吗”，“我愿意”，“我愿意”，“我愿意”。 如果这样，那么男的就能娶俩，如果任何一个没回答，那抱歉了一个都娶不成。

在实现时，主节点问下面节点，我要给你们都写个东西，你们都准备好没？“好了”，“好了”，“好了”，“没好”。 75%（>50%）（Quorum）的人都准备好了，那么我就写了，没准备好的那个我记住你了。

2. Paxos时间差机制（sanlock变种） **这个机制我设计成只针对这个场景的，要求时间同步至数秒级别（这要求已经极低了。。），多写一，也可以扩展。**

控制节点要你们去存储上填个表格，你们现在都能写，对吧？“对”，“对”，“对”，“不能”。。 OK，不能的那个我记住你了，其他人都来写吧。

控制节点看看表，再看看这个地方之前记的时间，嗯，过去2分钟了，写一下，我在A身上跑着； 然后A看看表，再看看这个地方之前记的时间，嗯，还没过去2分钟，不写，之前体检合格，控制节点在我身上； 然后B看看表，再看看这个地方之前记的时间，嗯，过去2分钟了，签个到留个时间，体检优秀； 然后C看看表，再看看这个地方之前记的时间，嗯，过去2分钟了，签个到留个时间，体检合格；

然后控制节点每隔1分钟看一下，嗯，你们都很健康。 又过了3分钟，哎？那个C你没写啊，去跟D站一起。

忽然，大家在填表的时候发现，控制节点都过了3分钟没写了，B就启动一下控制节点。 A发现自己与外界失去联系，就把自己身上都控制节点关了，等待救援。

3. 基于etcd的时间差机制 **上述的简化版，填的表格更少，时间差一致** 大家一起写etcd的那个地方。“好”，“好”，“好”。 哎？C你没写，再见。 哎？控制节点呢？来A你检查一下启一下控制节点，B现在状态不佳。 由于控制节点之前在你A上，它现在不行了，扣A个10分，现在分没B高啊。 A你清理下现场让B去启动吧。

试图设计一个任何场景都能应对的功能。
---
title: "Openstack 配置及其API的简单使用"
date: 2013-07-18
categories: 
  - "cloud-infra"
---

## 安装除Neutron（Quantum）以外所有东西

在开始之前，假设有三个NIC

禁用selinux，并修改网络配置/etc/sysconfig/network-scripts/ifcfg-ethX

# Internal
DEVICE=eth0
TYPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.1.11
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=192.168.1.1
DEFROUTE=yes
ONBOOT=yes

# External
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

/*安装完成后管理员界面为http://localhost/dashboard用户名admin，密码secrete*/
# openstack-demo-install

以上两种方法把除了Neutron（quantum）其他的服务都配置好了，关于quantum的配置见文末

## 添加虚拟机实例

可参考文档
[Getting started with OpenStack on Fedora 18](https://fedoraproject.org/wiki/Getting_started_with_OpenStack_on_Fedora_18 "Getting started with OpenStack on Fedora 18")
在终端操作之前需要设置环境变量，将它们两者之一写到一个文件，然后source一下

export OS_USERNAME=admin
export OS_PASSWORD=secrete
export OS_TENANT_NAME=admin
export OS_AUTH_URL=http://127.0.0.1:5000/v2.0/

export ADMIN_TOKEN=$(openssl rand -hex 10)
export SERVICE_ENDPOINT=http://127.0.0.1:35357/v2.0/
export SERVICE_TOKEN=$ADMIN_TOKEN

source FileA

这个libvirt支持kvm以及xen虚拟化，所以支持的镜像也比较多，这里下载一个已经安装好的f17的qcow2镜像

# axel -n 5 http://berrange.fedorapeople.org//blog/images/2012-11-15/f17-x86_64-openstack-sda.qcow2
/*注册镜像*/
# glance add name=f17-jeos is_public=true disk_format=qcow2 container_format=bare < f17-x86_64-openstack-sda.qcow2

然后以demo用户进入dashboard，会看到添加进来的image，根据image启动实例，打开vnc界面（非localhost访问要改ip）。

## 安装操蛋的Neutron（quantum）服务

这东西刚改名，nova自带的有network服务，可这个可以虚拟L2 L3 switch，那就比较有意思了

/*注册quantum*/

# keystone service-create --name quantum --type network --description 'OpenStack Networking Service'
# keystone endpoint-create --region myregion --service-id 26a55b340e254ad5bb78c0b14391e153 --publicurl "http://192.168.1.11:9696/" --adminurl "http://192.168.1.11:9696/" --internalurl "http://192.168.1.11:9696/"

/*(可选)添加quantum服务用户*/
get_id.sh:

$ function get_id () {
     echo `"$@" | awk '/ id / { print $4 }'`
}
# ADMIN_ROLE=$(get_id keystone role-create --name=admin)
# QUANTUM_USER=$(get_id keystone user-create --name=quantum --pass="servicepass" --email=demo@example.com --tenant-id service)
# keystone user-role-add --user_id $QUANTUM_USER --role_id $ADMIN_ROLE --tenant_id service

/*服务插件二选一，linuxbrideg和openvswitch，这里使用后者，先安装插件*/

# yum install openstack-quantum-openvswitch

/*启动并添加服务*/

# quantum-server-setup --plugin openvswitch
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
# chkconfig quantum-metadata-agent on
# service quantum-metadata-agent start

修改并配置网络 /etc/sysconf/network-scripts/ifcfg-eth1，/etc/sysconf/network-scripts/ifcfg-br-ex

# External
DEVICE=eth1
TYPE=Ethernet
BOOTPROTO=none
NM_CONTROLLED=no
BRIDGE=br-ex
ONBOOT=yes

#Public Bridge
DEVICE=br-ex
TYPE=Bridge
BOOTPROTO=static
IPADDR=192.168.2.11
NETMASK=255.255.255.0
NM_CONTROLLED=no
ONBOOT=yes

# ip addr del 10.0.0.9/24 dev eth1
# ip addr add 10.0.0.9/24 dev br-ex

这里类似VPN的重写HEADER

echo 1 > /proc/sys/net/ipv4/conf/all/forwarding
sysctl -w net.ipv4.ip_forward=1 
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
-d '{"server": {"name": "instance1", "imageRef": "992f5732-af50-40fc-987f-25951cbce943", "key_name": "damion-flybook", "flavorRef": "3", "max_count": 1, "min_count": 1}}'


---
title: "A design of user frontend for kinds of Cloud with accounting."
date: 2015-05-06
categories: 
  - "cloud-infra"
tags:
  - "Cloud Computing"
  - "TBD"
---

I like Linode.com very much, so I'd like to make a frontend with something like that in functions.

Here's the draft.

[![Untitled Page](/blog/images/Untitled-Page-1024x650.png)](http://blog.lofyer.org/a-design-of-user-frontend-for-kinds-of-cloud-with-accounting/untitled-page/)

更新一下，两年前的架构设计，终于在2017年的客户那用上了，而且上了人工干预的运行前调度器，哇哈哈。
---
title: "Cloud Init how to"
date: 2015-03-10
categories: 
  - "cloud-infra"
---

OS: CentOS 6

# yum install -y cloud-init
---
title: "How to solve the 'boot storm' problem: BCACHE"
date: 2013-05-27
categories: 
  - "cloud-infra"
  - "linux-admin"
---

**Some solutions** Way 1. Add more cpus.(ABANDONED) Way 2. Add a SSD as "boot cache" Way 3. Sort the boot process

**POC:** **Way 2:** Sometimes we need cache the boot section of the OS into a SSD, since no SSD on hand, let's try to use a block device made in /dev/shm

**Way 3:** Considering that the parallelization of the "boot action", we have to predict the action in the near future

Experiment: Using BCACHE now...or flashcache
---
title: "A light-weighted VM webadmin"
date: 2013-06-18
categories: 
  - "cloud-infra"
  - "linux-admin"
---

The infrastructure of it: Management: Based on django, communicate with Agent Agent: A daemon, used for communicating with Management and managing the VMs

TO MAKE GREAT USE OF KVM. [https://github.com/lofyer/webadmin](https://github.com/lofyer/webadmin)
---
title: "MyIDC--Everything's traceable"
date: 2013-12-11
categories: 
  - "cloud-infra"
---

相关手册参考[MyIDC_Book](https://cloud.lofyer.org/public.php?service=files&t=ad02817153cc4e4602921447eb49d10a "MyIDC_Book")

细节索引： gluster hadoop(with ARM as an option) drbd/heartbeat foreman/puppet nagios ovirt openstack owncloud asterisk

配置细节参考： [MyIDC Source](https://github.com/lofyer/myidc-main "MyIDC Source")

**Infrastructure:**

Board：
    x86小板
    SBSA小板
Cabinet：
    亚克力自制
网络：
    电信拨号接入外网
    SDN
资源分配：
    高可用监视与控制服务（os1-2）
    高可用存储(os3-n)
    并行计算（os3-n）
    高可用虚拟机服务（os1/2+os3-n）
    独立外设接入设备
安全：
    证书认证（在量子计算机面向大众之前这个还是很靠谱的）
应用商店：
    社会工程学测试
    leapmotion控制插件
    语音控制插件
    手机控制插件
---
title: "openvswitch howto"
date: 2013-07-25
categories: 
  - "linux-admin"
---

Here, I use kernel-3.4.58.

# emerge =net-misc/openvswitch-1.11.0

---
title: "硬盘、RAID组与Ceph分布式存储IOPS计算公式"
date: 2017-06-04
categories: 
  - "linux-admin"
tags:
  - "Storage"
  - "Performance"
---

**防怼说明：本文用于速算，想把难以量化的环境变量以及各种组合拿来说事儿的请自行Google，或者问厂商和IDC要报告去。**

**机械硬盘**

```
7200硬盘IOPS = 1000/(3 + 1000*(7200/60)/2) = 140
10k硬盘IOPS = 1000/(3 + 60000/10000/2) = 167
15k硬盘IOPS = 1000/(3 + 60000/15000/2) = 200
```

其中3为寻道延迟，7200/10k/15k为转速（rpm），1000*(7200/60)/2为旋转延迟（旋转延迟一般用转一圈所需时间的1/2表示），结果为理论峰值，实际还会有系统延迟导致测得IOPS一般低于此值。

**RAID组** 由于RAID组需要校验以提供恢复功能，所以会存在一定写惩罚，这个系数如下： RAID0: 1 RAID1: 2 RAID5: 4 RAID6: 6 RAID1-0: 2

所以RAID组IOPS = 硬盘写IOPS*硬盘数量*写操作百分比/写惩罚系数 + 硬盘读IOPS*硬盘数量*读操作百分比。

以4块IOPS为180的SAS硬盘组RAID 6然后百分百随机写操作为例：

IOPS = 180*4/6 = 120

**Ceph的IOPS经验公式** 由于Ceph存储结构不同于物理硬件，所以影响其IOPS的因素主要有网络、副本数量、日志、OSD（硬盘）数量、OSD服务器数量、OSD IOPS等，这里给出一个来自Mirantis的经验公式：

IOPS = 硬盘IOPS * 硬盘数量 * 0.88 / 副本数量，
其中0.88为4-8k随机读操作占比（88%）。

关于Ceph的IOPS计算仅供参考，计算结果可能会跟物理环境实测有较大偏差。
