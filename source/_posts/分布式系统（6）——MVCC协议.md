---
title: 分布式系统（6）——MVCC协议
mathjax: true
comments: true
date: 2020-04-29 08:14:46
tags: [分布式系统]
summary: 多版本并发控制是一种实现分布式事务的简单方式，如果2PC对应于悲观锁，那么MVCC就是乐观锁。
---

> **多版本并发控制**(Multiversion concurrency control， **MCC** 或 **MVCC**)，是[数据库管理系统](https://zh.wikipedia.org/wiki/数据库管理系统)常用的一种[并发控制](https://zh.wikipedia.org/wiki/并发控制)，也用于程序设计语言实现[事务内存](https://zh.wikipedia.org/wiki/事务内存)。
>
> MVCC意图解决[读写锁](https://zh.wikipedia.org/wiki/读写锁)造成的多个、长时间的读操作饿死写操作问题。每个事务读到的数据项都是一个历史快照（snapshot)并依赖于实现的[隔离级别](https://zh.wikipedia.org/wiki/事務隔離)。写操作不覆盖已有数据项，而是创建一个新的版本，直至所在操作提交时才变为可见。[快照隔离](https://zh.wikipedia.org/wiki/快照隔离)使得事物看到它启动时的数据状态。—— wikipedia

~~这都是些什么玩意。~~

MVCC往简单了说，就是一个不给你处理merge confict机会的Git。

## 过程

### 事务执行

1. 为事务分配一个全局递增的序号
2. 将事务分配到任何一个节点上执行
3. 在全局元信息中记录本次事务的编号

### 读取

1. 读取元信息中已成功的最大事务编号
2. 于各个节点上读取数据，只读取更新操作编号小于等于最后最大已成功提交事务编号的操作
3. 将这些操作应用到基础数据形成读取结果。

