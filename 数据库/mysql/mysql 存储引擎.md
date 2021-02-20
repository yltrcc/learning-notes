### 概述

```text
数据库的存储引擎是数据库的底层软件组织，数据库管理系统（DBMS）使用存储引擎创建、查询、更新和删除数据。

不同的存储引擎提供了不同的存储机制、索引技巧、锁定水平等功能，都有其特定的功能。

现在，许多数据库管理系统都支持多种存储引擎，常用的存储引擎主要有
MyISAM
InnoDB
Memory
Archive
Federated
```

### MyIASM

```text
MyIASM 是mysql默认的存储引擎，不支持数据库事务、行级锁和外键，因此是 INSERT（插入）或UPDATE（更新）数据即写操作时需要锁定整个表，效率较低。

MyIASM的特点：执行读取操作速度快，占用内存和存储资源较少。

它在设计之初就假设数据被组织成固定长度的记录，并且是按顺序存储的。在查找数据时，MyIASM直接查找文件的 OFFSET，定位比 InnnoDB 要快（InnoDB 寻址时要先映射到块，在映射到行）。

**总体来说，MyIASM的缺点是更新数据慢并且不支持事务处理，优点是查询速度快。**
```

### InnoDB

```text
InnoDB 为 mysql 提供了事务（Transaction）支持、回滚（Rollback）、崩溃修复能力（Crash Recovery Capabilities）、多版本并发控制（Multi-versioned Concurrency Control））、事务安全（Transaction-safe）的操作。

InnoDB的底层存储结构为B+树，B+树的每个节点都对应InnoDB的一个Page, Page大小是固定的，一般被设为16KB。其中，非叶子节点只有键值，叶子节点包含完整的数据，如图7-1所示。
```

![](https://secure1.wostatic.cn/static/58j6THCn3WmvcWuxoyT4MZ/9704e4ea49f79af4df1b913d103c154b.png)

```text
**InnoDB 适用于有以下需求的场景：**
**1. 经常有数据更新的表，适合处理多重并发更新请求**
**2. 支持事务**
**3. 支持灾难恢复（通过bin-log日志等）**
**4. 支持外键约束，只有InnoDB支持外键**
**5. 支持自动增加列属性 auto_increment**
```

### TokuDB

```text
TokuDB 的底层存储结构为 Fractal Tree。Fractal Tree 的结构与B+树有些类似，只是在 Fractal Tree 中除了每一个指针（key），都需要指向一个 child（孩子）节点，child节点带一个 Message Buffer，这个 Message Buffer 是一个先进先出队列，用来缓存更新操作，具体的数据结构如下图
```

![](https://secure1.wostatic.cn/static/ixbm29DZykMaRJZMmgDGrZ/67f77272507eb9c9f6075ac595bd72e1.png)

```text
这样，每一次插入操作都只需落在某节点的 Message Buffer 上，就可以马上返回，并不需要搜索到叶子节点。
这些缓存的更新操作会在后台异步合并并更新到对应的节点上。

**TokuDB在线添加索引，不影响读写操作，有非常高的写入性能，主要适用于要求写入速度快、访问频率不高的数据或历史数据归档。**
```

### Memory

```text
Memory 表使用内存空间创建。每个Memory表实际上都对应一个磁盘文件用于持久化。

Memory表因为数据是存放在内存中的，因此访问速度非常快，通常使用Hash索引来实现数据索引。Memory表的缺点是一旦服务关闭，表中的数据就会丢失。

Memory还支持散列索引和B树索引。B树索引可以使用部分查询和通配查询，也可以使用不等于和大于等于等操作符方便批量数据访问，散列索引相对于B树索引来说，基于Key的查询效率特别高，但是基于范围的查询效率不是很高。
```

