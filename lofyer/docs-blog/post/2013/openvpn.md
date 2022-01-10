---
title: "OpenVPN"
date: "2013-02-05"
categories: 
  - "linux-admin"
---

服务端: 生成证书等相关文件 /etc/openvpn/easy-rsa/2.0 生成证书

\# ./build-ca

生成服务器认证

\# ./build-key-server server

生成客户端

\# ./build-key client1

更新数据库

\# ./build-dh

**/etc/openvpn/server.conf**

local 192.168.1.132
port 1194
proto udp
dev tap
ca /etc/openvpn/easy-rsa/2.0/keys/ca.crt
cert /etc/openvpn/easy-rsa/2.0/keys/server.crt
key /etc/openvpn/easy-rsa/2.0/keys/server.key  # This file should be kept secret
dh /etc/openvpn/easy-rsa/2.0/keys/dh1024.pem
server 192.168.0.0 255.255.255.0
ifconfig-pool-persist ipp.txt
push "route 192.168.1.0 255.255.255.0"
push "redirect-gateway def1 bypass-dhcp"
push "dhcp-option DNS 116.228.111.118"
push "dhcp-option DNS 180.168.255.18"
client-to-client
duplicate-cn
keepalive 10 120
comp-lzo
user nobody
group nobody
persist-key
persist-tun
status openvpn-status.log
verb 3

运行:

\# openvpn --config /etc/openvpn/server.conf --daemon

客户端: Windows:

client1.ovpn

client
dev tap
proto udp
remote hostname 1194
persist-key
persist-tun
ca ca.crt
cert client1.crt
key client1.key
comp-lzo

其他需要文件为ca.crt, client1.crt, client1.key 放到安装目录的config中

连接外网: #打开ipv4的ipforwad #当次

\# echo 1 > /proc/sys/net/ipv4/ip\_forward 

#保存

sysctl -a | grep ip\_forward
sysctl -w net.ipv4.ip\_forward=1 

#在server上将来自子网192.168.0.0的转换为来自192.168.1.100

iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j SNAT --to-source 192.168.1.100
