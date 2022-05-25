---
title: "《KVM私有云架构设计与实践》目录"
date: "2015-09-18"
categories: 
  - "inthecloud"
  - "my-books"
---

书籍目前已出版，购买链接在下文提供，也可直接联系作者购买，微信lofyer\_org。

[当当](http://product.dangdang.com/25074443.html) [亚马逊](https://www.amazon.cn/%E7%A7%81%E6%9C%89%E4%BA%91%E6%9E%B6%E6%9E%84%E8%AE%BE%E8%AE%A1%E4%B8%8E%E5%AE%9E%E8%B7%B5-%E8%92%8B%E8%BF%AA/dp/B07143VQ2M/ref=sr_1_1?ie=UTF8&qid=1495023849&sr=8-1&keywords=kvm%E7%A7%81%E6%9C%89%E4%BA%91) [京东](https://search.jd.com/Search?keyword=kvm%E7%A7%81%E6%9C%89%E4%BA%91&enc=utf-8&wq=kvm%E7%A7%81%E6%9C%89%E4%BA%91&pvid=fab47274675745f28319953ba27d24c5)

[![](/blog/post/images/21495029670_.pic_-1024x1024.jpg)](https://blog.lofyer.org/wp-content/uploads/21495029670_.pic_.jpg)

前言 1 第1章私有云行业现状 8 1.1 私有云概念 8 1.2 国内私有云企业与行业客户 9 1.2.1 行业垂直细分 9 1.2.2 落地场景 11 1.3 总结 23 第2章基础架构设计 24 2.1 基本架构原则 24 2.1.1 合理的存储配置 25 2.1.2 稳定的网络基础 32 2.1.3 可靠的计算资源 37 2.2 架构安全 42 2.2.1 认证与授权 42 2.2.2 服务安全 44 2.3 “云”化架构 48 2.3.1 池化资源 48 2.3.2 SLA管理 49 2.4 OpenStack基础架构设计实例 57 2.5 总结 63 第3章软件架构设计 64 3.1 开源云架构概览 65 3.1.1 混合云——OpenStack 65 3.1.2 IaaS——oVirt 68 3.1.3 PaaS——OpenShift 69 3.2 集群架构与软件设计原则 70 3.2.1 集群架构 70 3.2.2 控制单元与服务代理 74 3.2.3 平台设计 78 3.3 服务实现 84 3.3.1 服务要素 85 3.3.2 框架示例 86 3.3.3 IaaS扩展 86 3.3.4 PaaS扩展 87 3.4 PaaS平台部署示例——OpenShift 88 3.5 总结 91 第4章 KVM虚拟化基础 92 4.1 QEMU 92 4.1.1 QEMU/KVM简介 92 4.1.2 机器模型 106 4.1.3 设备清单 111 4.1.4 QEMU控制台 122 4.1.5 QAPI 127 4.2 Libvirt 133 4.2.1 基本概念与用例 133 4.2.2 对象描述方法 142 4.2.3 Virsh控制台 159 4.2.4 编程示例 163 4.3 快速入门 168 4.3.1 搭建VirtManager 169 4.3.2 学习建议 173 第5章容器技术基础 175 5.1 容器简介 175 5.1.1 发展历史 175 5.1.2 技术实现 176 5.2 Docker 184 5.2.1 基本架构 184 5.2.2 主要元素 186 5.2.3 周边工具 228 5.3 安全隐患与对应措施 231 第6章 私有云网络基础 233 6.1 网络模型关键字 233 6.2 经典虚拟化网络 239 6.2.1 桥接网络 239 6.2.2 NAT网络 242 6.2.3 VLAN网络 246 6.3 软件定义网络 254 6.3.1 技术基础 256 6.3.2 虚拟化组网示例 300 第7章 私有云存储基础 309 7.1 存储基本元素 309 7.1.1 VFS 311 7.1.2 文件系统 313 7.1.3 块设备 320 7.2 虚拟机硬盘存储 322 7.2.1 虚拟硬盘 323 7.2.2 无状态存储 340 7.2.3 存储池 341 7.3 分布式存储后端 350 7.3.1 Glusterfs 353 7.3.2 Ceph 359 第8章 行业案例分析 371 8.1 Vmware与Citrix组建银行私有云 371 8.1.1 用户需求简述 371 8.1.2 架构设计 371 8.1.3 项目实施 372 8.2 OpenStack构建高校私有云 372 8.2.1 用户需求简述 372 8.2.2 架构设计 373 8.2.3 项目实施 373 8.3 oVirt构建中学教学云桌面 373 8.3.1 用户需求简述 373 8.3.2 架构设计 373 8.3.3 项目实施 373 第9章 桌面云最佳实践 375 9.1 设备透传与重定向 375 9.1.1 PCI/PCI-E设备 375 9.1.2 SR-IOV 376 9.1.3 USB 382 9.1.4 串口与并口 386 9.2 GPU与桌面协议 387 9.2.1 物理显卡透传 388 9.2.2 开源vGPU实现 391 9.2.3 3D远程桌面协议 397 9.3 文件带外管理 403 9.3.1 技术基础 403 9.3.2 文件监控与审计 405 9.3.3 病毒集中扫描 405 9.3.4 软件增量分发 405 9.4 虚拟机体验优化 406 9.4.1 CPU 406 9.4.2 内存 409 9.4.3 硬盘 411 9.4.4 网络 413 9.4.5 VirtIO多队列 414 9.4.6 Guest Tools 414 9.4.7 单点登录 415 9.4.8 QEMU FT 415 9.5 服务器系统优化 418 9.5.1 性能监控工具 418 9.5.2 存储 420 9.5.3 网络 422 9.5.4 主板选项 422 9.6 客户端部署 423 9.7 P-V互迁 424 9.8 数据备份 430 9.8.1 离线备份 431 9.8.2 在线备份 431 第10章运维与测试工具 433 10.1 监控与日志 433 10.1.1 监控 433 10.1.2 日志 433 10.2 运维工具 433 10.2.1 主机配置管理 433 10.2.2 自动配置工具 433 10.3 负载均衡与高可用 433 10.3.1 负载均衡 434 10.3.2 高可用 434 10.4 测试 434 10.4.1 性能指标测试 435 10.4.2 功能单元测试 435 附录1 问题解决方法与建议 435 附录2 参考书目与项目 437