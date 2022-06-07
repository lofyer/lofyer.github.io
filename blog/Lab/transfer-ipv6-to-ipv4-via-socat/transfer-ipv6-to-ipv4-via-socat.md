---
title: "transfer IPv6 to IPv4 via socat"
date: "2018-10-09"
categories: 
  - "linux-admin"
---

Suppose that you have an ipv6-only server A with ip \[ipv6-A\], and ipv4-ipv6 server B with ip (ipv4-B, \[ipv6-B\]).

If you wanna visit http://\[ipv6-A\]:80, but your ISV does not provide some ipv6 addresses. Now then, you'll make B be your "ipv6-ipv4 relay server" with following guide.

If TCP:

root@B:~# socat tcp4-listen:2222,fork tcp6:\[2001:19f0:7001:5a34:5400:01ff:feb5:5bcd\]:80

If UDP:

root@B:~# socat udp4-listen:2222,fork udp6:\[2001:19f0:7001:5a34:5400:01ff:feb5:5bcd\]:80

Now you can visit http://ipv4-B:2222 to access http://\[ipv6-A\]:80
