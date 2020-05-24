---
title: Percolator分布式事务解决方案
mathjax: true
comments: true
date: 2020-05-23 22:27:15
tags: [分布式系统]
summary: 针对Bigtable没有跨行事务的缺点，Google提出了Percolator分布式事务解决方案。
---

本文假设读者大致了解[Bigtable](https://longfangsong.github.io/2020/03/17/Bigtable简介/)。

在Bigtable中，事务是不能跨行的，这为一些操作带来了不便，于是Google在Bigtable上搭建了一个称为Percolator的分布式事务解决方案，实现了 Snapshot Isolation 级别的跨行事务。

##快照隔离（Snapshot Isolation）

快照隔离通过Bigtable中Cell的timestamp，实现了：

- 在整个事务开始时，记录一个开始时间戳，事务中所有数据的读写都是针对数据库在这个时间戳时的内容进行的
- 在事务结束时，如果发现了已经提交的，和这个事务相互冲突的另一个事务，则认为此次事务执行失败

可以看到这里运用了MVCC的想法。

### 时间戳

我们在事务进行时需要记录时间戳，这个时间戳必须是严格递增的，且不能受到网络延迟、机器物理时钟的差异等等的影响。

财大气粗的Google为了解决这个问题，直接砸钱为每个数据中心部署了原子钟，然后实现了一个Ture Time API，而各个开源实现中普遍应用了Lamport 逻辑时钟等等方式来解决。我将在其他文章中介绍Lamport 逻辑时钟。

### Write-skew问题

Write-skew是快照隔离中比较大的一个问题。

我们考虑下面的情况，有一张表T：

| id   | value |
| ---- | ----- |
| 1    | true  |
| 2    | true  |
| 3    | false |
| 4    | false |

我们分别在两个事务中执行：

```sql
UPDATE T
SET T.value=true
WHERE T.value=false;
```

和

```sql
UPDATE T
SET T.value=false
WHERE T.value=true;
```

那么就会分别产生结果：

| id   | value            |
| ---- | ---------------- |
| 1    | true             |
| 2    | false (modified) |
| 3    | false            |
| 4    | false            |

和

| id   | value           |
| ---- | --------------- |
| 1    | true            |
| 2    | true            |
| 3    | true (modified) |
| 4    | true (modified) |

而合并时，我们只会合并修改过的部分，故得到

| id   | value |
| ---- | ----- |
| 1    | true  |
| 2    | false |
| 3    | true  |
| 4    | true  |

而这样一种情况在串行操作中是无法得到的，所以说快照隔离无法达到串行化的隔离级别。

这一问题Percolator并没有去解决，但是确实存在一个显然的解决方案：要求一个事务读的数据的最近一个版本[^1]的提交时间要早于事务的开始时间，否则认为事务失败，但这样做显然会降低系统的并发度，是否采用需要权衡。

## 执行流程

### 事务开始

记录开始时间戳$start\_timestamp$即可。

### 事务中的读

读某个Cell时：

1. 本事务中的修改可以直接读到
2. 首先检查在时间范围$[0, start\_timestamp]$内对这个Cell的写锁，发现了就尝试执行清理操作，见下文的故障恢复一节

2. 读取在$[0, start\_timestamp]$中最后一个写记录[^2]对应的数据

### 事务中的写

写操作会直接缓存在本地。

### Commit过程

Commit过程基本上是一个[两阶段提交](https://longfangsong.github.io/2020/04/28/分布式系统（5）——-2PC协议/)的过程：

1. 第一阶段

   这一阶段要：

   1. 锁定所有要写的Cell，这一步骤包括：
      1. 检测是否存在冲突的Cell，冲突则事务失败，可能的冲突包括：
         - 在当前事务的开始时间戳后发现了对这个Cell的写记录
         - 发现了任意时间对这个Cell的锁
      2. 写入这次Commit希望写入的值
      3. 锁定这个Cell，同时记录对应的“Primary”锁（Primary锁是从这一堆Cell上的锁中按照一定规则选取的，一般就选第一个），“Primary”锁产生原因见故障恢复一节。
   2. 根据“Primary”锁确定事务是否确实要commit
      - 在事务Commit前，它必须检查自己是否依然拥有“Primary”锁
        - 如果有这个锁，将其替换为一个写记录（体现为一个`write`列）。
        - 如果没有这个锁了，那么此次事务失败。

2. 第二阶段

   将Primary以外的各个Cell上的锁也替换为写记录。

### 故障恢复

#### 清理因Client挂掉而未释放的锁

在事务发现被其他事务由于崩溃而遗留在某个Cell上的锁时，如果对应的“Primary”锁已经被替换为写记录，则roll forward，否则roll back。

[^1]: 注意是真正“当前”的版本，而不是事务工作所在的版本

[^2]: 其实我更愿意称这里的“写记录”为“commit记录”