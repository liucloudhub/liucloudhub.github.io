---
layout: post
title: 每天10分钟系列之 --- 了解MapReduce
image: /img/hello_world.jpeg
date:  2016-07-19 15:00:00 +0800  
description: 开发工具
img: post-9.jpg # Add image post (optional)
tags: [arc]
labels: [MapReduce, 每天10分钟]
author: # Add name author (optional)
arc: true
---
### MapReduce简介
- MapReduce是一种分布式计算模型，是Google提出的，主要用于搜索领域，解决海量数据的计算问题。
- MR有两个阶段组成： Map和Reduce， 用户只需要实现map()和reduce()两个函数，即可实现分布式计算。

#### MR框架
- MapReduce框架采用Master/Slave架构，包括一个Master和多个二Slave.  `Master上运行JobTracker， Slave上面运行TaskTracker.`, MapReduce 采用分治策略，一个大规模数据集会被切分成许多独立的分片，这些分片可以被多个Map任务并行处理。 两个阶段都是以键值对（key/value）形式作为输入(input)和输出(output)的。

- MapReduce 主要由4个部分组成：
    1. clien
        - 用户编写的MapReduce程序通过Client提交到JobTracker端。用户可通过Client提供的一些接口查看作业运行状态。
    2. JobTracker
        - JobTracker负责资源监控和作业调度，监控所有TaskTracker 与Job的健康状况，一旦发现失败，就将相应的任务转移到其他节点。`跟踪任务的执行进度，资源使用量等信息，并将这些信息告诉任务调度器，而调度器会在资源出现空闲时，选择合适的任务去使用这些资源`
    3. TaskTeacker
        - 周期性地通过“心跳”将本节点上资源的使用情况和任务的运行进度汇报给JobTracker, 同时接收JobTracker发送过来的命令并执行相应的操作。
        - 其使用“slot”等量划分节点的资源（CPU, 内存）,一个Task 获取得到一个slot后才有机会运行，而Hadoop调度器的作用就是将各个TaskTracker上的空闲slot分配给Task使用。`slot 分为Map Slot 和 Reduce slot 两种，`分别提供Map Task 和 Reduce Task使用。
    4. HDFS
        - 保存作业的数据，配置信息等，最后的结果也是保存在HDFS上面

- MapReduce架构:

![](http://p6jsga0vv.bkt.clouddn.com/18-11-10/33785770.jpg)

- MapReduce运行过程：

![](http://p6jsga0vv.bkt.clouddn.com/18-11-10/83564062.jpg)


- MapReduce的计算流程：

![](http://p6jsga0vv.bkt.clouddn.com/18-11-10/14357410.jpg)

- Combine： 作用是把一个map函数产生的同一个Key的键值对合并一起， 并将新的<key, value> 作为输入到reduce函数中。

- partition：把数据归类，主要在Shuffle 过程中按照key值将中间结果分为R份，其中每份都有一个Reduce去负责。

- Shuffle: shuffle是map和reduce中间的过程，包含两端的Combine和Partition

#### 优缺点
- 优点
    - `易于编程`： 更写一个简单的串行程序是一摸一样的。
    - `良好的扩展性`: 简单增加机器来扩展计算能力
    - `高容错性`: 一台机器挂了，它上面的计算任务会转移到另外一个节点上运行，完全不需人工参与。
    - `适合PB级以上海量数据的离线处理`: 不适合在线处理

- 缺点
    1. 实时计算，无法像Mysql一样，在毫秒或者秒级内返回结果。
    2. 流式计算， 流的数据是动态的，MR的数据是静态的
    3. DAG（有向图）计算，不是不可以，只是每个结果要写入磁盘，会造成大量的磁盘I/O.

### 总结
- Hadoop MapReduce是一个软件框架，用于轻松编写应用程序，以可靠，容错的方式在大型集群（数千个节点）的商用硬件上并行处理大量数据（多TB数据集）。

MapReduce 作业通常将输入数据集拆分为独立的块，这些块由map任务以完全并行的方式处理。框架对地图的输出进行排序，然后输入到reduce任务。通常，作业的输入和输出都存储在文件系统中。该框架负责调度任务，监视它们并重新执行失败的任务。

通常，`计算节点和存储节点是相同的`，即`MapReduce框架和Hadoop分布式文件系统`在同一组节点上运行。此配置允许框架有效地在已存在数据的节点上调度任务，从而在集群中产生非常高的聚合带宽。

MapReduce框架由`单个主 JobTracker和每个集群节点的一个从属TaskTracker组成`。主服务器负责在从服务器上调度作业的组件任务，监视它们并重新执行失败的任务。从设备按照主设备的指示执行任务。

最低限度，应用程序通过适当的接口和/或抽象类的实现来指定输入/输出位置并提供 映射和减少功能。这些和其他作业参数包括作业配置。然后，Hadoop 作业客户端将作业（jar /可执行程序等）和配置提交给JobTracker，然后JobTracker负责将软件/配置分发给从，调度任务并监视它们，为作业提供状态和诊断信息 。

尽管Hadoop框架是在Java 实现的，但MapReduce应用程序不需要用Java编写

### 参考资料
[MapReduce官方文档](http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html.)

{: .box-note}
**Note:** 本文非作为商业用途，侵删. 如果你遇到任何问题，欢迎下面留言哈！ 整理不易，欢迎打赏！
