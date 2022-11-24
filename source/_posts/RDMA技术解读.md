---
title: RDMA技术解读
date: 2022-11-18 21:11:09
tags: [体系结构, 网络]
categories: [清浅录]
cover: https://img-blog.csdnimg.cn/1b2704ac4511494e8c4c1348d7ad4529.png
typora-root-url: RDMA技术解读
---

# RDMA技术解读

本文参考[技术蛋老师讲解RDMA的视频]([揭秘网络技术中的“爱马仕”，12分钟看懂RDMA_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1ZY4y1L7Xq/?spm_id_from=333.337.search-card.all.click&vd_source=aad0e5152e96b4bf095d0017d639153b)), [It_server技术分享的视频]([学习分享：浅谈RDMA技术_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1QL411G7wo/?spm_id_from=333.337.search-card.all.click&vd_source=aad0e5152e96b4bf095d0017d639153b))侵删

## 传统Socket通信

![image-20221118211738044](image-20221118211738044.png)

用户将应用发送出去, 需要先到操作系统内核, 再到网络接口, 然后在接收方收到信息再次经过操作系统内核, 并在用户态分析数据.

## RDMA通信模式

![image-20221118211907180](image-20221118211907180.png)

绕过内核态, 直接发送数据给硬件. 可以满足高带宽, 低延迟, 低CPU消耗的需求.

RDMA可以释放CPU的负载, 减少数据拷贝, 内存访问, 实现零拷贝和内核旁路.

- 零拷贝:应用程序能够直接执行数据传输, 无需涉及到网络软件栈的情况下, 数据能够被直接发送到缓冲区或者能够直接从缓冲区里被接收
- 内核旁路: 应用程序可以直接在用户态执行数据传输, 不需要在内核态与用户态之间做上下文切换

### IB

RDMA的原生网络协议, 通过专用硬件实现最优的性能, 但是由于专用硬件的原因, InifiniBand要求从L到L4 需要使用自己专用的硬件, 设备成本非常高



### RoCE

RDMA跑在以太网上的一种网络, RoCE v1还没有摆脱Infiniband的束缚, RoCE v2使用UDP+IP, 既可以使用以太网交换机, 可以兼容现有以太网, IP协议使得数据可以被路由.

![image-20221118212411801](image-20221118212411801.png)

### iWARP

可以更大规模的部署和组网, iWARP是基于TCP的, 传统的iWARP厂商实现时需要兼容的完整的协议栈, 设计和实现成本很高, 不需要交换机支持无损以太网传输, 虽然适用于当前的互联网传输, 但是性能比RoCE要差.

![img](https://img-blog.csdnimg.cn/1b2704ac4511494e8c4c1348d7ad4529.png)

