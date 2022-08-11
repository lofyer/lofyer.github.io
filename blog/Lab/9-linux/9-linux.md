---
title: "Yacy"
date: "2012-03-15"
categories: 
  - "others"
---

寒假前没事儿给翻译成了中文，在1.02中出现。三千多行。。。

[http://gitorious.org/yacy](http://gitorious.org/yacy)

https://github.com/yacy/yacy\_search\_server/blob/master/locales/zh.lng
---
title: "Unix/Linux/MacOS Monitor Tools"
date: "2013-11-29"
categories: 
  - "linux-admin"
---

## System

**Web** munin Nagios Icinga foreman monit monitorix Suricata

## Network

**Web** ntop cacti **Terminal** iftop tcpdump netstat iptraf vnstat

## Cpu/Memory

**Terminal** top htop vmstat pmap

## IO

**Terminal** iotop iostat
---
title: "All about text terminal"
date: "2016-08-04"
categories: 
  - "linux-admin"
---

[http://www.tldp.org/HOWTO/html\_single/Text-Terminal-HOWTO/](http://www.tldp.org/HOWTO/html_single/Text-Terminal-HOWTO/)

控制台(console)和终端(terminal)比较容易混淆，一般来说与主机显示输出与控制接口直接相连的显示器和键盘叫做控制台，其他不与主机控制接口直接相连的则叫做终端。 现代PC的模拟终端(tty in Linux)是模拟当年接到主机上的客户机之间的串口输入输出行为，准确的说是含有键盘作为输入和VDU(video display unit)作为输出与回显的客户端。 有些真实的终端是能够显示黑白图片的，也就是位图，这些终端协议包括Tektronix Vector Graphics, ReGIS (DEC), Sixel (DEC), and NAPLPS (North American Presentation Level Protocol Syntax)。更为炫酷的是在Apollo、Sun、SGI主机上的矢量终端(http://www.cca.org/vector/)，早期电影里你看到的那些能画图的绿色显示器就是它了。 瘦客户端早期是文字终端，后来才开始有图形界面和鼠标的终端。根据其与主机的计算量相对大小可分为胖客户端和瘦客户端。 Windows NT版本开始提供的RDP服务是基于ITU T.120协议，ICA协议是在其基础上的兼容扩展。Linux可使用免费的ICA客户端，但需要向MS支付许可费。 现代的终端，包括X-Windows、Windows、Gnome等全部叫做窗口终端(Window Terminal)。 上世纪80年代的内存价格约为每兆数千美元，所以文字终端更容易流行。 终端一般都遵循一定的控制标准，即escape(ESC)控制序列，最常见的例子是我们常用的银行密码键盘。 美国的终端多以ASCII作为字母标准(除了IBM的EBCDIC)，但是这些终端的ESC控制序列并不都一样，所以往往引起兼容性的问题。为此引入了termcap("terminal capabilities")，也就是现在的环境变量TERM。 现代的模拟终端最早是由DEC引入的VT系列(vt100、vt220)，Linux中的终端模型linux也与vt100类似。 模拟软件可模拟的终端类型一般有Wyse60, 50; VT 220, 102, 100, 52: TV950, 925, 912; PCTERM; ANSI; IBM3101; ADM-1l; WANG 2110。 流控(flow control)是用来防止终端、电脑、猫或其他设备处理过多字符而导致信息丢失等现象的发生，可分为软件控制和硬件控制，软件控制即发送DC1/DC3控制序列，硬件控制即分别控制串口/并口的RTS/CTS或DTR/DSR引脚高低电平。当然，现在模拟终端的流控全由软件控制了。
---
title: "tcp协议之上的CCNx"
date: "2012-10-26"
categories: 
  - "linux-admin"
---

刚在lwn上看到了个有意思的东西，ccnx，主要作用于媒体中心传播数据（视频）。 在06年于google的演讲中，Jacobson说设计了一种新的媒体传输协议，举了奥运会期间NBC传播同一赛事，大家都卡卡的例子。 类似p2p或者bt吧，几千用户请求同一视频，对于已经从server中出去的数据，就让用户互传，当然，数据包里带有签名，防止新用户的重复请求。 [目前最新的是0.62版本](http://www.ccnx.org/software-download-information-request/download-releases/ "CCNx")，用于linux发行版下载效果应该不错，目测缺点是路由负载可能会高那么点。
---
title: "RTX内核"
date: "2012-10-13"
categories: 
  - "devices"
---

下载了最新的MDK后，发现里面有个RTX内核，玩了一下，和UCOSII很像。

semaphore，message，mailbox，mutex等同步与通信机制，代码易读，可移植性也好，在mini2440上运行完美，我只用了4个led 观察。

喜欢的话可以看看，这是RTX内核源码，移植需要更改MDK中board 部分内容。

点击下载 [RTX](http://69.164.197.168/wp-content/uploads/2012/10/RTX.rar)
---
title: "OpenVPN"
date: "2013-02-05"
categories: 
  - "linux-admin"
---

服务端: 生成证书等相关文件 /etc/openvpn/easy-rsa/2.0 生成证书

# ./build-ca

生成服务器认证

# ./build-key-server server

生成客户端

# ./build-key client1

更新数据库

# ./build-dh

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

# openvpn --config /etc/openvpn/server.conf --daemon

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

# echo 1 > /proc/sys/net/ipv4/ip\_forward 

#保存

sysctl -a | grep ip\_forward
sysctl -w net.ipv4.ip\_forward=1 

#在server上将来自子网192.168.0.0的转换为来自192.168.1.100

iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j SNAT --to-source 192.168.1.100
---
title: "Git Exp."
date: "2013-04-08"
categories: 
  - "linux-admin"
tags:
  - "Git"
---

Supposing that you had a git server, we can use exist git as your own working bare.  
**[On your own git server] Create a repo.** Clone an exist git.

$ git clone git@exist-server:exist.git

Make its master branch writable.

$ echo -e "\[receive\]ntdenyCurrentBranch = ignore" >> exist.git/.git/config

  
**[On your client] Create a branch & make a change.** Clone exist.git as your own git-src, in which you can see the old commits and branch.

$ git clone git@your-server:exist.git
$ cd exist

We create a new branch based on its master.

$ git checkout -b mybranch
$ echo "Add README" > README
$ git add README
$ git commit -m "Add READEME in ROOT"

Since this is our first commit, we should push our branch to origin. Next time you should just type 'git push'

$ git push origin mybranch

  
**\[On your own git server\] An update in master.** Let's get some new commits.

$ git pull

Here we get something like this... [![git2](/blog/images/git21.png)](http://69.164.197.168/wp-content/uploads/2013/04/git21.png)  
Now we get commit0 and commitA on mybranch, with commit0 and commit1 on branch master. What we want is something like this. [![git3](/blog/images/git3.png)](http://69.164.197.168/wp-content/uploads/2013/04/git3.png)  
**\[On your client\] Merge commit1 to mybranch** Way 1.Just merge them from your own git server

$ git pull origin master

Way 2.Pull commit1 to local master, then rebase **_or_** merge it to mybranch Merge:

$ git merge master

Rebase:

$ git rebase master

Final step:

$ git push

  
_There's a little difference between merge and rebase in history. Reference: [http://gitbook.liuhui998.com/4\_2.html](http://gitbook.liuhui998.com/4_2.html "GitBook - Rebase")_
---
title: "Build a robot assistant with nowadays cloud apis"
date: "2018-12-11"
categories: 
  - "ai"
  - "cloud-infra"
tags:
  - "AI"
---

好多年前就想做个Jarvis，当时技术所限，构建出一个比较初级的机器人后觉得它还不如一条狗。

现在随着AI以及计算技术的成熟，尤其Google Assistant已经可以跟人自然语音对话了，所以觉得可以再尝试一下，加之工作需要，所以便又再开启。

现在随着AI以及计算技术的成熟，尤其Google Assistant已经可以跟人自然语音对话了，所以觉得可以再尝试一下，加之工作需要，所以便又再开启。

当然，这里我们首先选取交互平台为微信（Web API），交互形式为语音、文字等不限形式。然后再来看看哪些平台提供的最好用且最像人。

# 1. Google Assistant

# 2. 小爱同学（小米）/天猫精灵

# 3. 图灵/XiaoI

# 4. 自建

结论（对，有结论了）：

1没中文，2最难用，3比较傻，4需要各种训练。直接用3+pyweixin自定义回复就OK了。
---
title: "incrontab howto - filesystem status change notify"
date: "2015-03-31"
categories: 
  - "linux-admin"
---

**1\. Install incrontab.**

**2\. Edit /etc/incron.allow**

root

**3\. Use incrontab command**

<path> <mask> <command>
IN\_ACCESS File was accessed (read) (\*)
IN\_ATTRIB Metadata changed (permissions, timestamps, extended attributes, etc.) (\*)
IN\_CLOSE\_WRITE File opened for writing was closed (\*)
IN\_CLOSE\_NOWRITE File not opened for writing was closed (\*)
IN\_CREATE File/directory created in watched directory (\*)
IN\_DELETE File/directory deleted from watched directory (\*)
IN\_DELETE\_SELF Watched file/directory was itself deleted
IN\_MODIFY File was modified (\*)
IN\_MOVE\_SELF Watched file/directory was itself moved
IN\_MOVED\_FROM File moved out of watched directory (\*)
IN\_MOVED\_TO File moved into watched directory (\*)
IN\_OPEN File was opened (\*)

When monitoring a directory, the events marked with an asterisk (\*) above can occur for files in the directory, in which case the name field in the returned event data identifies the name of the file within the directory.

The IN\_ALL\_EVENTS symbol is defined as a bit mask of all of the above events. Two additional convenience symbols are IN\_MOVE, which is a combination of IN\_MOVED\_FROM and IN\_MOVED\_TO, and IN\_CLOSE which combines IN\_CLOSE\_WRITE and IN\_CLOSE\_NOWRITE.

The following further symbols can be specified in the mask:
IN\_DONT\_FOLLOW Don't dereference pathname if it is a symbolic link
IN\_ONESHOT Monitor pathname for only one event

IN\_ONLYDIR Only watch pathname if it is a directory

Additionaly, there is a symbol which doesn't appear in the inotify symbol set. It it IN\_NO\_LOOP. This symbol disables monitoring events until the current one is completely handled (until its child process exits). 

Command translation

$$   dollar sign
$@   watched filesystem path (see above)
$#   event-related file name
$%   event flags (textually)
$&   event flags (numerically)

# incrontab -l
# incrontab -e
/var/www IN\_ACCESS echo "/var/www/ wass accessed at $$date"

Access your web server in a browser and watch the status change.
# tail /var/log/syslog
---
title: "Something about curl -- Connecting IPA Server using json as an example"
date: "2013-04-10"
categories: 
  - "linux-admin"
tags:
  - "curl"
---

With Arduino as a server.

What we want is to keep a cookie and build a HEADER [http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html "HTTP/1.1") \[P1\]

curl -e
curl -H referer:whereicamefrom.com
curl -d @file.txt
curl -d "somecmd"
curl -cookie
curl -D

\[P2\] **Communicating with ipa server** [https://git.fedorahosted.org/cgit/freeipa.git/tree/API.txt](https://git.fedorahosted.org/cgit/freeipa.git/tree/API.txt "freeipa api")

Get ca.crt

curl -k https://$YOURHOST/ipa/config/ca.crt >> /tmp/ipa.ca.cert

Get sessionid

sessid=$(curl -v 
-H referer:https://ipa.test.net/ipa/ui/index.html 
-H "Content-Type:application/x-www-form-urlencoded" 
-H "Accept:\*/\*" 
--negotiate -u : 
--cacert ./ca.crt 
-d "user=admin" -d "password=12345678" 
-D cookie.txt 
-X POST 
-k      
https://ipa.test.net/ipa/session/login\_password 
2>&1 | grep -o "ipa\_session=\[a-zA-Z0-9\]\*")

Post a json file with cmd in it

curl -v 
-H referer:https://ipa.test.net/ipa/ui/index.html 
-H "Content-Type:application/json" 
-H "Accept:applicaton/json" 
-negotiate -u : 
--cacert ./ca.crt 
--cookie $sessid 
-d @ipa.json 
-X POST 
-k      
https://ipa.test.net/ipa/session/json

Here's a json file

{
"method":"user\_find",
"params":\[
        \[""\],
        {"uid":"admin"}
        \],
"id":0
}

{
"method":"user\_add",
"params":\[
        \[\],
        {
         "uid":"test1",
         "cn":"cn",
         "givenname":"test1",
         "sn":"test1"
        }
        \],
"id":0
}
---
title: "如何正确计算CPU利用率？"
date: "2017-07-05"
categories: 
  - "linux-admin"
tags:
  - "Performance"
---

我们平时使用的CPU利用率方法是极具误导性的，并且一年更甚一年。那么什么是CPU利用率？是你的CPU到底有多忙，是像“% CPU”这样到处在用的指标所显示的那样吗？

在top命令里，你看到90%的CPU利用率是这样：

[![](/blog/images/cpubusyidle.png)](https://blog.lofyer.org/wp-content/uploads/cpubusyidle.png)

然而它真正想表达的是这个意思： [![](/blog/images/cpubusystalledidle.png)](https://blog.lofyer.org/wp-content/uploads/cpubusystalledidle.png)

Stall(这里译作“怠速”)是说这个处理器没有在跑指令，比如在等待内存I/O的时候。我上图所画的比例（“忙”与“怠速”之间）是我在真实生产环境中遇到的，并且你的CPU也很可能是处于“怠速”状态。

这些对你有什么意义呢？理解CPU怠速多少，会直接影响到你在减少代码或者减少内存I/O的调优工作。

**那么真正的CPU利用率怎么算呢？**

平时的CPU利用都是非空闲时间，即CPU不运行idle线程（比如Windows里的空闲进程）的时间。你的操作系统那会平时会在上下文切换的时候跟踪它，但是假如一个非idle线程开始运行100毫秒后停止，那内核会认为后面这段时间CPU也在这个非idle线程上。

在老旧的分时系统里，这么算没毛病。阿波罗登月舱的导航系统计算机将这里的idle线程叫做“DUMMY JOB”，工程师用利用它来测算计算机的利用率，可以参考之前我写过的[这样一篇文章](http://www.brendangregg.com/usemethod.html#Apollo)。

**那么它有什么毛病呢？**

现在的CPU比内存已经快了很多倍，但等待内存的时间仍然被算进CPU时间中。当你在top命令中看到较高的“%CPU”的时候，你可能认为它到达了一个性能瓶颈，就是散热片和风扇下面的那个CPU，但实际上，这是那一根根内存条的锅。

**如何分辨CPU到底在忙啥？**

使用性能监测计数器（PMC）——一种能够用perf或者其他工具命令查看的硬件计数器。比如，观测整个系统10秒钟：

# perf stat -a -- sleep 10

 Performance counter stats for 'system wide':

     641398.723351      task-clock (msec)         #   64.116 CPUs utilized            (100.00%)
           379,651      context-switches          #    0.592 K/sec                    (100.00%)
            51,546      cpu-migrations            #    0.080 K/sec                    (100.00%)
        13,423,039      page-faults               #    0.021 M/sec                  
 1,433,972,173,374      cycles                    #    2.236 GHz                      (75.02%)
   <not supported>      stalled-cycles-frontend  
   <not supported>      stalled-cycles-backend   
 1,118,336,816,068      instructions              #    0.78  insns per cycle          (75.01%)
   249,644,142,804      branches                  #  389.218 M/sec                    (75.01%)
     7,791,449,769      branch-misses             #    3.12% of all branches          (75.01%)

      10.003794539 seconds time elapsed

这里的一个关键指标就是instructions per cylce（IPC，每CPU周期执行指令数），它能够显示每CPU周期内每个CPU运行了多少指令，越高说明效率越高。上述示例中，这一值为0.78，但这并不说明CPU利用率为78%，因为现代CPU的IPC最大值为4.0（新的已经到了5.0），也就是4-wide。CPU在执行指令时，单个指令会被分割为多个步骤，比如取指令、解码、执行、内存访问、写寄存器等，这些命令如果在单个CPU周期内最多执行一个，那么需要5个CPU周期来完成一条命令，IPC就是0.2，如果采用指令流水线，即3~5-wide的CPU，那么完美状态下1个CPU周期就可以完成一条命令，IPC就是1。（译者注：作者文中使用了CPU clock cycle表示通常所说的CPU周期，为了避免与晶振时钟周期混肴我并没有将其译为CPU时钟周期。）

当然，还有数百个其他你可以用来测量的性能计数器。

如果在虚拟化环境中，guest一般不能直接访问PMC，这取决于hypervisor是否支持。我最近写的一篇[The PMCs of EC2: Measuring IPC](http://www.brendangregg.com/blog/2017-05-04/the-pmcs-of-ec2.html)展示了AWS EC2中基于Xen的虚拟机如何使用PMC。

**最佳实践**

如果你的IPC小于1.0，你可能遇到了内存操作密集型，软件调优策略可以有减少内存I/O，增强内存本地访问性，尤其是在NUMA系統上。硬件调优策略则是使用CPU cache较大以及更快的内存、总线和内联技术。

如果你的IPC > 1.0，你可能是指令密集型。可以试图减少指令的执行数量，比如消除不必要的工作和缓存操作等，可以用一下[CPU火焰图](http://www.brendangregg.com/FlameGraphs/cpuflamegraphs.html)。硬件调优方面，可以尝试高主频、多核、超线程的CPU。

**性能检测产品应该告诉你什么呢？**

性能检测工具都应该显示出每个进程的IPC，或者是按照指令周期与怠速周期，比如%INS和%STL，下图为Linux中的tiptop命令：

tiptop - \[root\]
Tasks:  96 total,   3 displayed                               screen  0: default

  PID \[ %CPU\] %SYS    P   Mcycle   Minstr   IPC  %MISS  %BMIS  %BUS COMMAND
 3897   35.3  28.5    4   274.06   178.23  0.65   0.06   0.00   0.0 java
 1319+   5.5   2.6    6    87.32   125.55  1.44   0.34   0.26   0.0 nm-applet
  900    0.9   0.0    6    25.91    55.55  2.14   0.12   0.21   0.0 dbus-daemo

**CPU利用率具有误导性的其他理由**

使得这个%CPU指标错误的理由除了CPU在内存的怠速周期外，还有如下因素： 1. 温度也能使CPU进入怠速； 2. Turboboost（睿频）引起时钟频率变化； 3. SpeedStep引起时钟频率变化； 4. 一分钟内的80%的平均利用率并不能表示100%的突发利用率（类似网络QoS）； 5. 自旋锁：CPU在很严肃地瞎忙；

**Update: CPU利用率真的错了吗？** 自这篇文章发布以后，留言讨论非常激烈，已经有了上百条了。首先谢谢你们对这话题感兴趣并花时间阅读，但我在这里还是要统一回复：**我对disk的iowait并不关心（译者注：PC CPU不能直接操作外部存储）**，并且文中也已经给出了内存操作密集型的对应调优措施。

然后，CPU利用率到底是从本质上错了还是仅仅是了？我认为需要人将高CPU利用率视为处理单元的瓶颈的事儿，是错的。那么这个指标的计算方法从技术上讲正确吗？如果CPU在怠速期间不能被其他任何进程使用，那么这不就是所谓的“使用等待”（听起来有点矛盾）。某些情况下，%CPU作为一个操作系统层面的指标是技术正确但是容易误导人的。在超线程中，怠速周期可以被其他线程使用，所以%CPU的算法也会将其算在内，而实际上并没有利用。那样是不对的，这篇文章中我强调的是解释问题并提出对策，并且，这个指标也有技术上的问题。 **结论**

CPU利用率已成为一个极具误导性的指标：它算进了等待主存的周期，而这类周期在现代的CPU负载中占据不少。如果使用额外指标，你就能搞清楚%CPU到底意味着什么，包括每CPU周期执行指令数（IPC）。IPC < 1.0可能意味着你的应用是内存密集型，而IPC > 1.0则可能是指令密集型。我在之前的一篇文章

显示%CPU的性能监控产品也应该显示PMC测量指标，并给予充分解释，这样才不会误导用户。比如，它们可以一起显示%CPU和IPC，或者指令周期与怠速周期。有了这些指标，开发或管理人员才能在应用和操作系统中选择正确的调优方式。

本文翻译自Brendan Gregg的博客文章《CPU Utilization is Wrong》，原文链接为[http://www.brendangregg.com/blog/2017-05-09/cpu-utilization-is-wrong.html](http://www.brendangregg.com/blog/2017-05-09/cpu-utilization-is-wrong.html)，就是那本《性能之巅（中译）》的作者，调试工具dtrace的作者，现就职于NetFlix。

PS：为什么要翻译这个文章呢？因为很多时候总感觉PC的这个CPU利用率的百分比显示没能真实反应我的CPU到底忙不忙，在学校的时候用单片机也是算idle，但到了PC后隐约感觉这么算不对，看了BG的文章后才恍然大悟。另外这篇文章之前已经被翻译过，但作者又有更新，也挺有意思的，我就重新翻了一遍，并加了一些弹幕。
---
title: "inspire设计"
date: "2013-02-25"
categories: 
  - "linux-admin"
---

client : android/pc/arduino

server : linux

node : with client service installed

action : cmd src dest

backend: recognize the cmd, get src type from the dest, search src in the node

SCHEDULE \[DONE\]1. voice recognization test complete \[TODO\]2. use its api in C/python \[\*Abort\*\] No idea...
---
title: "docker using linux bridge"
date: "2018-11-08"
categories: 
  - "cloud-infra"
---

A. Change docker bridge dockerd -H unix:// -b br\_eth0 --fixed-cidr=172.20.230.1/24 --default-gateway 172.20.0.1

B. Create manually, but flexibly Recreate veth pair if your docker instance was restarted or exited.

\[root@172-20-17-247 ~\]# docker run --network none -it tianon/network-toolbox /bin/bash \[root@172-20-17-247 ~\]# docker inspect --format '{{.State.Pid}}' 3 74406 \[root@172-20-17-247 ~\]# ip link add centos3-cont type veth peer name centos3-host \[root@172-20-17-247 ~\]# brctl addif br\_eth0 centos3-host \[root@172-20-17-247 ~\]# ifconfig centos3-host up \[root@172-20-17-247 ~\]# ip link set netns 74406 dev centos3-cont \[root@172-20-17-247 ~\]# nsenter -t 74406 -n ip link set centos3-cont up \[root@172-20-17-247 ~\]# nsenter -t 74406 -n ip addr add 172.20.77.141/16 dev centos3-cont \[root@172-20-17-247 ~\]# nsenter -t 74406 -n ip route add default via 172.20.0.1

\[root@172-20-17-247 ~\]# docker exec -it 3 bash root@39eb1da6e842:/# ping google.com PING google.com (172.217.25.206): 56 data bytes 64 bytes from 172.217.25.206: icmp\_seq=0 ttl=55 time=87.434 ms ^C--- google.com ping statistics --- 1 packets transmitted, 1 packets received, 0% packet loss round-trip min/avg/max/stddev = 87.434/87.434/87.434/0.000 ms

C. With pipework script https://github.com/jpetazzo/pipework \[root@172-20-17-247 ~\]# pipework br\_eth0 -i eth2 CONTAINERID 172.20.100.22/16 \[root@172-20-17-247 ~\]# pipework route CONTAINERID replace default via 172.20.0.1

Ref: \[1\] [https://clcnetwork.wordpress.com/2017/05/31/docker-container-and-vm-networking/](https://clcnetwork.wordpress.com/2017/05/31/docker-container-and-vm-networking/)
---
title: "Using solr+nutch+django to build your own search engine portal"
date: "2015-01-14"
categories: 
  - "linux-admin"
---

# Using Solr

### Solr tutorial

### Deploy Solr in Tomcat

# Using Nutch

# Using Nutch with Solr
---
title: "Using Gpu in Pi"
date: "2013-06-14"
categories: 
  - "devices"
---

**We had a wiki here.** [http://elinux.org/RPi\_Xorg\_rpi\_Driver#Design](http://elinux.org/RPi_Xorg_rpi_Driver#Design "RPi gpu driver design")

**And vc sdk on github. It's also in /opt/vc.** [https://github.com/raspberrypi](https://github.com/raspberrypi "raspberry on github")

**X using gpu.** [https://github.com/simonjhall/fbdev\_exa](https://github.com/simonjhall/fbdev_exa "X using gpu")

**Playback accelerating.** [http://wiki.matthiasbock.net/index.php/Hardware-accelerated\_video\_playback\_on\_the\_Raspberry\_Pi](http://wiki.matthiasbock.net/index.php/Hardware-accelerated_video_playback_on_the_Raspberry_Pi "RPi playback")

**Compile gstreamer{\*} in Pi** [http://permalink.gmane.org/gmane.comp.video.gstreamer.devel/43011](http://permalink.gmane.org/gmane.comp.video.gstreamer.devel/43011)

**Using wayland instead of X** Here's the build instruction [http://wayland.freedesktop.org/raspberrypi.html](http://wayland.freedesktop.org/raspberrypi.html "Build Wayland") For recently PI\_DEBIAN img, we should just

apt-get install weston weston-dev

Using xwayland to run X application in wayland..

Notes: 1. If you're going to compile cairo using egl, you shall meet a problem like undeclared 'None', just DEFINE one as 0L. 2. Using soft link rather than copying one when library is missing.

**Waiting4Test** **Tuning Rasbian** Speedup CPU: raspi-config arm\_freq=900 I always use this hack and my rpi run without problem Change scheduler/elevator at boot time: we use more responsive/less disk io usage scheduler: modify the /boot/cmdline.txt

dwc\_otg.lpm\_enable=0 root=/dev/mmcblk0p2 rootfstype=ext4 rootflags=commit=120,data=writeback elevator=deadline rootwait quiet

Tuning sysctl.conf: as manual say, sysctl.conf is the “Configuration file for setting system variables”... well there are lot of variables you can put inside this file and for everyone we can write a book. Our goal is to gain more speed and tell our system to use less io/ram. Let’s put in the /etc/sysctl.conf:

vm.dirty\_background\_ratio = 20
vm.dirty\_expire\_centisecs = 0
vm.dirty\_ratio = 80
vm.dirty\_writeback\_centisecs = 1200
vm.overcommit\_ratio = 2
vm.laptop\_mode = 5
vm.swappiness = 10

**Tuning SDCard:** Quite hard to understand, but changing the partition alignment of the SDCard can highly improve the write speed of our sdcard. Basically we have to align the first partition to the erase block size of our sdcard. I suggest to read an reread the reference link. To do that in our sdcard that contain our RaspberryPI Debian distro we have to: backup our filesystem from linux: cd /media/mymmcblk0p2/ (the partitition that’s contain debian fs) tar -cvpzf /home/gibbio/RPI-TC\_fs.tgz --exclude=./proc --exclude=./lost+found --exclude=./sys --exclude=./mnt --exclude=./media --exclude=./dev ./ cd /media/mymmcblk0p1/ (the partitition that’s contain boot kernel etc) tar -cvpzf /home/gibbio/RPI-TC\_boot.tgz ./ Now use printcsd.py to find the erase block size, most sd have 128K so we use 128K/Sector size = 256 sectors (mine have 64k so we have to use 64k/512b = 128 sector alignement)

sfdisk -f -H224 -S56 /dev/mmcblk0 Checking that no-one is using this disk right now ... OK Disk /dev/mmcblk0: 244448 cylinders, 224 heads, 56 sectors/track Old situation: Units = cylinders of 6422528 bytes, blocks of 1024 bytes, counting from 0 Device Boot Start End #cyls #blocks Id System /dev/mmcblk0p1 0 - 0 0 0 Empty /dev/mmcblk0p2 0 - 0 0 0 Empty /dev/mmcblk0p3 0 - 0 0 0 Empty /dev/mmcblk0p4 0 - 0 0 0 Empty Input in the following format; absent fields get a default value. Usually you only need to specify and (and perhaps ). /dev/mmcblk0p1 :,8,c /dev/mmcblk0p1 0+ 7 8- 50175+ c W95 FAT32 (LBA) (enter enter) /dev/mmcblk0p2 : /dev/mmcblk0p2 8 1246 1239 7771008 83 Linux /dev/mmcblk0p3 : /dev/mmcblk0p3 0 - 0 0 0 Empty /dev/mmcblk0p4 : /dev/mmcblk0p4 0 - 0 0 0 Empty New situation: Units = cylinders of 6422528 bytes, blocks of 1024 bytes, counting from 0 Device Boot Start End #cyls #blocks Id System /dev/mmcblk0p1 0+ 7 8- 50175+ c W95 FAT32 (LBA) /dev/mmcblk0p2 8 1246 1239 7771008 83 Linux /dev/mmcblk0p3 0 - 0 0 0 Empty /dev/mmcblk0p4 0 - 0 0 0 Empty Warning: no primary partition is marked bootable (active) This does not matter for LILO, but the DOS MBR will not boot this disk. Do you want to write this to disk? \[ynq\] y Successfully wrote the new partition table Re-reading the partition table ... If you created or changed a DOS partition, /dev/foo7, say, then use dd(1) to zero the first 512 bytes: dd if=/dev/zero of=/dev/foo7 bs=512 count=1 (See fdisk(8).)

gibbio # fdisk -l... Disk /dev/mmcblk0: 8010 MB, 8010072064 bytes 224 heads, 56 sectors/track, 1247 cylinders, total 15644672 sectors Units = sectors of 1 \* 512 = 512 bytes Sector size (logical/physical): 512 bytes / 512 bytes I/O size (minimum/optimal): 512 bytes / 512 bytes Disk identifier: 0x000ee283 Device Boot Start End Blocks Id System /dev/mmcblk0p1 1 100351 50175+ c W95 FAT32 (LBA) /dev/mmcblk0p2 100352 15642367 7771008 83 Linux

now create ext4 FS: mke2fs -t ext4 -E stripe-width=32 -m 0 /dev/mmcblk0p2 make FAT32 partition via gparted gibbio # fdisk /dev/mmcblk0 Command (m for help): p Disk /dev/mmcblk0: 8010 MB, 8010072064 bytes 224 heads, 56 sectors/track, 1247 cylinders, total 15644672 sectors Units = sectors of 1 \* 512 = 512 bytes Sector size (logical/physical): 512 bytes / 512 bytes I/O size (minimum/optimal): 512 bytes / 512 bytes Disk identifier: 0x000ee283 Device Boot Start End Blocks Id System /dev/mmcblk0p1 1 100351 50175+ c W95 FAT32 (LBA) /dev/mmcblk0p2 100352 15642367 7771008 83 Linux Command (m for help): x Expert command (m for help): p Disk /dev/mmcblk0: 224 heads, 56 sectors, 1247 cylinders Nr AF Hd Sec Cyl Hd Sec Cyl Start Size ID 1 00 0 2 0 223 56 7 1 100351 0c 2 00 0 1 8 223 56 1023 100352 15542016 83 3 00 0 0 0 0 0 0 0 0 00 4 00 0 0 0 0 0 0 0 0 00 Expert command (m for help): b Partition number (1-4): 1 New beginning of data (1-100351, default 1): 128 Expert command (m for help): w The partition table has been altered! Calling ioctl() to re-read partition table. Syncing disks. Now eject the sdcard, reinsert and make the restore from the tgz: cd /media/mymmcblk0p2/ tar -xvpzf RPI-TC\_fs.tgz -C ./ mkdir proc mnt sys boot dev umount /media/mymmcblk0p2/ cd /media/mymmcblk0p1/ tar -xvpzf RPI-TC\_boot.tgz -C ./ umount /media/mymmcblk0p1/ Eject the sdcard, plug into our Raspberry Pi and power on!
---
title: "deploy & debug ovirt in the intellij ide"
date: "2013-04-05"
categories: 
  - "cloud-infra"
---

Before we start, we need intellij enterprise version and a register tool.. \[url\]

\[Summary\] 1. We should open the pom.xml file instead of the project directory. 2. Modify the extension of the directory in the project properties. 3. Modify the .m2 file in your personal directory to make jboss know where to deploy the project. 4. We need seperate run/debug config to make it clearer.

\[Steps\] _OVIRT\_HOME=/home/demo JBOSS\_HOME=/usr/jboss-as JDK=/usr/java/jdk1.7.0\_17 $ cmd as demo # cmd as root_ **1.Compile the ovirt from src** in $OVIRT\_HOME

$ mvn clean install -Pgwt-admin,gwt-user -DskipTests=true

**2.Deploy ovirt** You need ~/.m2/settings.xml file in /root/ and /home/demo directory.

 oVirtEnvSettings 

        oVirtEnvSettings
                        /usr/jboss-as
                           /usr/java/jdk1.7.0\_17 

in $OVIRT\_HOME/ear

# mvn clean install -Pdep,setup

**3.Run jboss** You should export an environment variant and a runtime configuration file.

$ echo -e "ENGINE\_USR=admin111nENGINE\_ETC=/etc/ovirt-engine" > $OVIRT\_HOME/backend/manager/conf/engine.conf.defaults
# export ENGINE\_DEFAULTS=$OVIRT\_HOME/backend/manager/conf/engine.conf.defaults
# /usr/jboss-as/bin/standalone.sh

UPDATE: Now there is README.developer in the source dir, just follow it.
---
title: "Compile Android Spice(aSpice)"
date: "2013-09-02"
categories: 
  - "cloud-infra"
---

Ref: [http://comments.gmane.org/gmane.comp.emulators.spice.devel/13944](http://comments.gmane.org/gmane.comp.emulators.spice.devel/13944 "http://comments.gmane.org/gmane.comp.emulators.spice.devel/13944") [https://github.com/iiordanov/remote-desktop-clients/blob/master/BUILDING](https://github.com/iiordanov/remote-desktop-clients/blob/master/BUILDING)

Before following the BUILDING instruction in the repo, you need download [android-ndk-r9d](http://dl.google.com/android/ndk/android-ndk-r9d-linux-x86_64.tar.bz2), or else you will get lots of error.

I was using Eclipse for android neon, android-sdk-25.

cebero is no longer needed. **update 2016**

编译环境
ubuntu-14.04，按理12.04、16.04也可以

1. 下载所需包并解压
android-ndk-r9d-linux-x86\_64.tar.bz2
gstreamer-1.0-android-arm-release-1.4.5.tar.bz2

链接：http://pan.baidu.com/s/1c1AWPmo 密码：t4ku

demo@ubuntu:~$ tar xf Downloads/android-ndk-r9d-linux-x86\_64.tar.bz2 -C /home/demo/
demo@ubuntu:~$ sudo tar xf Downloads/gstreamer-1.0-android-arm-release-1.4.5.tar.bz2 -C /opt/gstreamer

2. 修改bVNC为aSPICE
在文件~/remote-desktop-clients/eclipse\_projects/bVNC/AndroidManifest.xml中，
将android:label="bVNC" and android:name="bVNC"修改为android:label="aSPICE" and android:name="aSPICE" respectively.

然后在这个bVNC目录中执行命令：
demo@ubuntu:bVNC$ find jni/src -name \\\*.h -o -name \\\*.c -exec sed -i 's/aSPICE/bVNC/' {} \\;

3. 编译
demo@ubuntu:bVNC$ /home/demo/android-ndk-r9d/ndk-build GSTREAMER\_SDK\_ROOT\_ANDROID=/opt/gstreamer -j 4

1\. Install/update sqlitegen plugin v0.1.19 into Eclipse from: https://github.com/iiordanov/sqlitegen-secure/releases/download/v0.1.19/sqlitegen\_eclipse\_site\_0.1.19.jar

NOTE: Using an older version of the plugin may not work because of the switch to the sqlcipher release of sqlite.

2\. Clone the FreeRDP project in the parent directory of where you cloned bVNC project: git clone git://github.com/FreeRDP/FreeRDP.git

Check out a commit known to work with bVNC: git checkout stable-1.1

You should now have bVNC and FreeRDP side by side in the same directory.

Add bVNC, ZoomerWithKeys and PubkeyGenerator from the bVNC/eclipse\_projects/ directory to Eclipse as existing Android projects. Add FreeRDPCore from the FreeRDP/client/Android/ directory to Eclipse as an Existing Android Project.

3\. Ensure ZoomerWithKeys, PubkeyGenerator, and FreeRDPCore projects are called exactly as written here by right-clicking on each of them, then selecting Refactor->Rename

3\. Add the ZoomerWithKeys, PubkeyGenerator, and FreeRDPCore projects as Library References to bVNC (right-click on the bVNC project and select Properties -> Android -> Library). If they are not, add them.

4\. Ensure that ZoomerWithKeys, PubkeyGenerator, and FreeRDPCore are specified as "Required projects on the build path" (right- click on the bVNC project, and select Properties -> Java Build Path -> Projects). If they are not, add them.

5\. Ensure that ZoomerWithKeys, PubkeyGenerator, and FreeRDPCore are referenced in Properties -> Project References (put checkmarks on projects).

6\. To build and successfully run aSPICE, you need to: - Unpack gstreamer-sdk 1.4.5 into /opt/gstreamer. I obtained my copy from: https://gstreamer.freedesktop.org/data/pkg/android/1.4.5/gstreamer-1.0-android-arm-release-1.4.5.tar.bz2 Newer versions either crash or have GNUtls errors that need work.

\- Edit AndroidManifest.xml and change android:label="bVNC" and android:name="bVNC" to android:label="aSPICE" and android:name="aSPICE" respectively.

\- cd bVNC/eclipse\_projects/bVNC

\- Replace all instances of aSPICE with bVNC in native code (.c and .h files). This requirement should go away in the future. You can use the following command: find jni/src -name \\\*.h -o -name \\\*.c -exec sed -i 's/aSPICE/bVNC/' {} \\;

\- ndk-build GSTREAMER\_SDK\_ROOT\_ANDROID=/opt/gstreamer -j 4

\- Unfortunately, currently ndk-build destroys other pre-existing libraries in eclipse\_projects/bVNC/libs, so after successfully building libspice and libgstreamer, one has to git checkout the files which were deleted.

\- Force one boolean variable to be true. You can use the following command: find ./ -name \\\*.java -exec sed -i 's/isSpice = .\*;/isSpice = true;/' {} \\;

\- You may have to select the "Android private libraries" entry in Project Properties->Java Build Path->Order and Export if you have it.

\- Enable requesting the RECORD\_AUDIO permission in AndroidManifest.xml with the following sed command: sed -i 's//<\\/uses-permission>/g' AndroidManifest.xml

Please inform me if (6) needs to be expanded (i.e. if you needed to do anything else in addition to the above.
