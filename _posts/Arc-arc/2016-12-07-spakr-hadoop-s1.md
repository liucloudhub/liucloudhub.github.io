---
layout: post
title: 每天10分钟系列之 --- 了解Spark与Hadoop对比
image: /img/hello_world.jpeg
date:  2016-12-07 16:00:00 +0800  
description: 开发工具
img: post-9.jpg # Add image post (optional)
tags: [arc]
labels: [Spark, Hadoop, 每天10分钟]
author: # Add name author (optional)
arc: true
---

### 简述

Spark是和Hadoop MapReduce对比的，那么它们之间有什么区别呢？

- Spark确实速度很快(最多比Hadoop MapReduce快100倍)。Spark还可以执行批量处理，然而它真正擅长的是处理流工作负载、交互式查询和机器学习。

- 相比MapReduce基于磁盘的批量处理引擎，Spark赖以成名之处是其数据实时处理功能。Spark与Hadoop及其模块兼容。实际上，在Hadoop的项目页面上，Spark就被列为是一个模块。

- Spark有自己的页面，因为虽然它可以通过YARN(另一种资源协调者)在Hadoop集群中运行，但是它也有一种独立模式。它可以作为 Hadoop模块来运行，也可以作为独立解决方案来运行

    MapReduce和Spark的主要区别在于，MapReduce使用持久存储，而Spark使用弹性分布式数据集(RDDS)。

### 性能对比

- Spark之所以如此快速，原因在于它在内存中处理一切数据。没错，它还可以使用磁盘来处理未全部装入到内存中的数据。

- Spark的内存处理为来自多个来源的数据提供了近乎实时分析的功能：营销活动、机器学习、物联网传感器、日志监控、安全分析和社交媒体网站。另 外，MapReduce使用批量处理，其实从来就不是为惊人的速度设计的。它的初衷是不断收集来自网站的信息，不需要这些数据具有实时性或近乎实时性。

### 易用性对比
- spark支持Scala(原生语言)、Java、Python和Spark SQL。Spark SQL非常类似于SQL 92，所以几乎不需要经历一番学习，马上可以上手。

- Spark还有一种交互模式，那样开发人员和用户都可以获得查询和其他操作的即时反馈。MapReduce没有交互模式，不过有了Hive和Pig等附加模块，采用者使用MapReduce来得容易一点.

### 成本
- “Spark已证明在数据多达PB的情况下也轻松自如。它被用于在数量只有十分之一的机器上，对100TB数据进行排序的速度比Hadoop MapReduce快3倍。”这一成绩让Spark成为2014年Daytona GraySort基准。

### 兼容性
- MapReduce和Spark相互兼容;MapReduce通过JDBC和ODC兼容诸多数据源、文件格式和商业智能工具，Spark具有与MapReduce同样的兼容性。

### 数据处理
- MapReduce是一种批量处理引擎。MapReduce以顺序步骤来操作，先从集群读取数据，然后对数据执行操作，将结果写回到集群，从集群读 取更新后的数据，执行下一个数据操作，将那些结果写回到结果，依次类推。Spark执行类似的操作，不过是在内存中一步执行。它从集群读取数据后，对数据 执行操作，然后写回到集群。

- Spark还包括自己的图形计算库GraphX。GraphX让用户可以查看与图形和集合同样的数据。用户还可以使用弹性分布式数据集(RDD)，改变和联合图形，容错部分作了讨论。

### 容错
- 至于容错，MapReduce和Spark从两个不同的方向来解决问题。MapReduce使用TaskTracker节点，它为 JobTracker节点提供了心跳(heartbeat)。如果没有心跳，那么JobTracker节点重新调度所有将执行的操作和正在进行的操作，交 给另一个TaskTracker节点。这种方法在提供容错性方面很有效，可是会大大延长某些操作(即便只有一个故障)的完成时间。

- Spark使用弹性分布式数据集(RDD)，它们是容错集合，里面的数据元素可执行并行操作。RDD可以引用外部存储系统中的数据集，比如共享式文件系统、HDFS、HBase，或者提供Hadoop InputFormat的任何数据源。Spark可以用Hadoop支持的任何存储源创建RDD，包括本地文件系统，或前面所列的其中一种文件系统。
- RDD拥有五个主要属性：
    - 分区列表
    - 计算每个分片的函数
    - 依赖其他RDD的项目列表
    - 面向键值RDD的分区程序(比如说RDD是散列分区)，这是可选属性
    - 计算每个分片的首选位置的列表(比如HDFS文件的数据块位置)，这是可选属性
- RDD可能具有持久性，以便将数据集缓存在内存中。这样一来，以后的操作大大加快，最多达10倍。Spark的缓存具有容错性，原因在于如果RDD的任何分区丢失，就会使用原始转换，自动重新计算。

### 可扩展性
- 据称雅虎有一套42000个节点组成的Hadoop集群，可以说扩展无极限。最大的已知Spark集群是8000个节点，不过随着大数据增多，预计集群规模也会随之变大，以便继续满足吞吐量方面的预期.


### 安全性
- Hadoop支持Kerberos身份验证，这管理起来有麻烦。然而，第三方厂商让企业组织能够充分利用活动目录Kerberos和LDAP用于身份验证。同样那些第三方厂商还为传输中数据和静态数据提供数据加密。

Hadoop分布式文件系统支持访问控制列表(ACL)和传统的文件权限模式。Hadoop为任务提交中的用户控制提供了服务级授权(Service Level Authorization)，这确保客户拥有正确的权限。

Spark的安全性弱一点，目前只支持通过共享密钥(密码验证)的身份验证。Spark在安全方面带来的好处是，如果你在HDFS上运行Spark，它可以使用HDFS ACL和文件级权限。此外，Spark可以在YARN上运行，因而能够使用Kerberos身份验证。

### 总结
- Spark与MapReduce是一种相互共生的关系。Hadoop提供了Spark所没有的功能特性，比如分布式文件系统，而Spark 为需要它的那些数据集提供了实时内存处理。完美的大数据场景正是设计人员当初预想的那样：让Hadoop和Spark在同一个团队里面协同运行。

### 参考资料
- [spark官网](http://spark.apache.org/)
- [Hadoop官网](https://hadoop.apache.org/docs/r3.1.1/)


{: .box-note}
**Note:** 本文非作为商业用途，侵删. 如果你遇到任何问题，欢迎下面留言哈！ 整理不易，欢迎打赏！
