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

\# perf stat -a -- sleep 10

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
