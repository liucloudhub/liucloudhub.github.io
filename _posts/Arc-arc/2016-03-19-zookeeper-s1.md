---
layout: post
title: 每天10分钟系列之 --- 了解Zookeeper
image: /img/hello_world.jpeg
date:  2016-03-19 11:00:00 +0800  
description: 开发工具
img: post-9.jpg # Add image post (optional)
tags: [arc]
labels: [zookeeper, 每天10分钟]
author: # Add name author (optional)
arc: true
---
### 目录

#### 一. [ZooKeeper功能简介](#jump1)

#### 二. [ZooKeeper基本概念](#jump2)

#####  &nbsp; 2.1 集群角色
#####  &nbsp; 2.2 集群节点分工
#####  &nbsp; 2.3 Session
#####  &nbsp; 2.3 数据节点
#####  &nbsp; 2.3 状态信息
#####  &nbsp; 2.3 事务操作
#####  &nbsp; 2.3 Watcher(事件监听器)

#### 三. [Zookeeper应用的典型场景](#jump3)
#####  &nbsp; 3.1 数据发布与订阅(配置中心)
#####  &nbsp; 3.2 命名服务
#####  &nbsp; 3.3 分布式协调服务/通知
#####  &nbsp; 3.4 Master选举
#####  &nbsp; 3.5 分布式锁

#### 四. [总结](#jump4)


### <span id="jump1">1. ZooKeeper功能简介</span>
---
- Zookeeper是一个开源的分布式协调服务，本质上是一个分布式的小文件存储系统，由雅虎创建，是Google Chubby 的开源实现。分布式应用程序可以基于Zookeeper实现诸如数据发布/订阅、负载均衡、命名服务、分布式协调/通知、 集群管理、Master选举、配置维护、名字服务、分布式同步、分布式锁和分布式队列等功能。

### <span id="jump2">2. ZooKeeper基本概念</span>
---
- 在介绍Zookeeper之前，有必要了解它的几个核心概念。
#### 1. 集群角色
  - Zookeeper中，有三种角色
    - Leader
    - Follower
    - Observer
    
  - 一个Zookeeper 集群同一时刻只会有一个Leader, 其他都是Follower或Observer.
    - ZooKeeper 配置很简单，每个节点的配置文件（zoo.cfg)都是一样的，只有myid文件不一样。myid的值必须是zoo.cfg中server.{数值}的{数值}部分。
    - Zoo.cfg 配置文件示例：
  ![zookeeper](http://p6jsga0vv.bkt.clouddn.com/18-11-3/40621374.jpg)
    
- 在装有ZooKeeper的机器的终端执行Zookeeper-server status 可以看当前结点的Zookeeper是什么角色。Zookeeper 默认只有Leader和Follower两种角色，没有Observer角色。为了使用Observer模式，在任何想变成Observer的结点的配置文件中加入`:peerType:observer` 并在所有server的配置文件中，配置observer模式的server的那行配置追加 `:observer`


#### 2. 节点读写分工
-  Zookeeper 集群的所有机器通过一个Leader 选举过程来选定一台被称为【Leader】的机器，Leader 服务器为客户端提供读写服务。
    
-  Follower 和 Observer 都提供读服务，不提供写服务。两者唯一的区别在于Observer机器不参与Leader选举过程，也不参与写操作的 `过半写成功` 策略，因此Observer可以在不影响写性能的情况下提升集群的读性能。

#### 3. Session
- Session 是指客户端会话，在讲解客户端会话之前，我们先来了解下客户端连接。在Zookeeper中，一个客户端连接是指客户端和ZooKeeper服务器的TCP长连接
    
- ZooKeeper对外的服务端口默认是2181， 客户端启动时，首先会与服务器建立一个TCP连接，从第一次连接建立开始，客户端会话
    的生命周期也开始了，通过这个连接，客户端能够通过心跳检测和服务器保持有效的会
    话，也能够向ZooKeeper服务器发送请求并接受响应，同时还能通过该连接接收来自服务
    器的Watch事件通知。
    
- Session 的SessionTimeout 值用来设置一个客户端会话的超时时间。当
    由于服务器压力太大，网络故障或是客户端主动断开连接等各种原因导致客户端连接断
    开，只要在SessionTimeout规定的时间内能够重新连接上集群中任意一台服务器，那么之前创建的会话仍然有效。

#### 4. 数据结点
- ZooKeeper的结构其实就是一个树形结构，leader 就相当于其中的根节点，其它节点就相当于follow节点，每个节点都保留自己的内
    容。
    
- ZooKeeper 的节点分两类： 持久节点和临时节点
-   持久节点：
    -   所谓持久节点是指一旦这个`树形结构` 被创建了，除非主动进行对树节点的移除操作，否则这个节点将一直保存在ZooKeeper上。
        
-   临时节点：
    - 临时节点的生命周期跟客户端会话绑定，一旦客户端会话失效，那么这个客户端创建的所有临时节点都会被移除。

#### 5. 状态信息
-  每一个节点除了存储内容之外，还存储了几点本身的一些状态信息。 用get命令可以同时获得某个节点的内容和状态信息。
-  在ZooKeeper中，version属性是用来实现乐观锁机制中的【写入校验】的`保证分布式数据原子性操作`。
    
#### 6. 事务操作
- 在ZooKeeper中，能改变Zookeeper服务器状态的操作称为事务操作。一般包括数据节
    点创建与删除、数据内容更新和客户端会话创建与失效等操作。对应每一个事物请求，ZooKeeper都会为其分配一个全局唯一的事务ID,用ZXID表示, 通常是一个64位的数字。 每一个ZXID对应一次更新操作，从这些ZXID
    中可以间接地识别出ZooKeeper处理这些事务操作请求的全局顺序。
    
#### 7. Watcher（事件监听器）
- 是ZooKeeper中一个很重要的特性。ZooKeeper允许用户在指定节点注册一些Watcher，并且在一些特定事件触发的时候，ZooKeeper服务端会将事件通知到感兴趣的客户端上去。该机制是ZooKeeper实现分布式协调服务的重要特性。

### <span id="jump3">三. Zookeeper应用的典型场景</span>
---
- ZooKeeper 是一个高可用的分布式数据管理与协调框架。基于对ZAB算法的实现，该框架能够很好地保证分布式环境中数据的一致性。也是基于这样的特性，使得ZooKeeper成为了解决分布式一致性问题的利器。

#### 1. 数据发布与订阅 （配置中心）

- 数据发布与订阅，即所谓的配置中心，顾名思义就是发布者将数据发布到ZooKeeper节点上，供订阅者进行数据订阅，进而到达动态获取数据的目的，实现配置信息的集中式管理和动态更新。

- 特点： 数据量通常比较小。数据内容在运行时动态变化。集群中各机器共享，配置一致。这样的全局配置信息就可以发布到ZooKeeper上，让客户端（集群的机器）去订阅该信息。

- 发布/订阅系统一般有两种设计模式，分别是推（Push) 和 拉（pull)模式。
    - 推模式
        - 服务端主动将数据更新发送给所有订阅的客户端
        
    - 拉模式
        - 客户端主动发起请求来获取最新数据，通常客户端都采用定时轮询拉取得方式
    
- ZooKeeper采用得是推拉相结合的方式:
    - 客户端向服务端注册自己需要关注的节点，一旦该节点的数据发生变更，那么服务端就会向相应的客户端发送Watcher事件通知，客户端接收到这个消息通知后，需要主动到服务端获取最新的数据。
 
#### 2. 命名服务

- 命名服务也是分布式中比较常见的一类场。在分布式系统中，通过使用命名服务，客户端应用能够根据指定名字来获取资源或服务的地址，提供者等信息。被命名的实体通常可以是集群中的机器，提供服务，远程对象等等 --- `就是起了个名字`.

- 其中较为常见的就是一些分布式服务框架（如RPC)中的服务地址列表。通过在ZooKeeper里创建顺序节点，能够很容易创建一个全局唯一的路径，这个路径就可以作为一个名字。

- ZooKeeper的命名服务即生成全局唯一的ID
    
#### 3. 分布式协调服务/通知
- ZooKeeper中特有Watcher注册与异步通知机制，能够很好的实现分布式环境下不同机器，甚至不同系统之间的通知与协调，从而实现对数据变更的实时处理。使用方法通常是不同的客户端，如果机器节点发生了变化，那么所有订阅的客户端都能够接收到相应的Watcher通知，并做出相应的处理。

- ZooKeeper的分布式协调/通知，是一种通用的分布式系统机器间的通信方式。

#### 4. Master选举
- Master选举可以说是Zookeeper最典型的应用场景了。比如HDFS中Active NameNode 的选举，YARN中Active ResourceMAnager的选举和HBase中Active HMaster的选举等。


- 针对Master选举的需求，通常情况下，我们可以选择常见的关系型数据库中的主键特性来实现：
    希望成为Master的机器都向数据库中`插入一条相同主键ID的记录`，数据库会帮我们进行主键冲突检查，也就是说，只有一台机器能插入成功--那么，我们就认为向数据库中成功插入数据的客户机器成为Master。

#### 唯一性   
    依靠关系型数据库的主键特性，确实能够很好地保证在集群中选举出唯一的一个Master。
    
- 但是，如果当前选举出的Master挂了，那么该如何处理？谁来告诉我Master挂了呢？显然，关系型数据库无法通知我们这个事件。但是，ZooKeeper可以做到！

- 利用ZooKeeper 的强一致性，能够很好地保证在分布式高并发情况下节点的创建一定能够保证全局唯一性，即ZK将会保证客户端无法创建一个已经存在的数据单元节点。

- 也就是说，如果同时有多个客户端请求创建同一个临时节点，那么最终一定只有一个客户端请求能够创建成功。利用这个特性，就能很容易地在分布式环境中进行Master选举了。

- 成功创建该节点的客户端所在的机器就成为了 Master。同时，其他没有成功创建该节点的
客户端，都会在该节点上注册一个子节点变更的 Watcher，用于监控当前 Master 机器是否存
活，一旦发现当前的Master挂了，那么其他客户端将会重新进行 Master 选举。


    这样就实现了Master的动态选举。


#### 5. 分布式锁
- 分布式锁是控制分布式系统之间同步访问共享资源的一种方式。
- 分布式锁又分为排它锁和共享锁两种：
    - 排它锁  --- zk 如何实现排它锁？
        - 定义锁
            - zk上的一个机器节点可以表示一个锁
        - 获得锁
            - 把ZooKeeper上的一个节点看作是一个锁，获得锁就通过`创建临时节点的方式`来实现。 ZooKeeper 会保证在所有客户端中，最终只有一个客户端能够创建成功，那么就可以 
认为该客户端获得了锁。同时，所有没有获取到锁的客户端就需要到/exclusive_lock 
节点上注册一个子节点变更的Watcher监听，以便实时监听到lock节点的变更情况。
        - 释放锁
            - 因为锁是一个临时节点，释放锁有两种方式
            1. 当前获得锁的客户端机器发生宕机或重启，那么该临时节点就会被删除，释放锁
            2. 正常执行完业务逻辑后，客户端就会主动将自己创建的临时节点删除，释放锁。
    - 共享锁
        - 共享锁在同一个进程中很容易实现，但是在跨进程或者在不同 Server 之间就不好实现了。Zookeeper 却很容易实现这个功能，实现方式也是需要获得锁的 Server 创建一个 EPHEMERAL_SEQUENTIAL 目录节点，然后调用 getChildren方法获取当前的目录节点列表中最小的目录节点是不是就是自己创建的目录节点，如果正是自己创建的，那么它就获得了这个锁，如果不是那么它就调用 exists(String path, boolean watch) 方法并监控 Zookeeper 上目录节点列表的变化，一直到自己创建的节点是列表中最小编号的目录节点，从而获得锁，释放锁很简单，只要删除前面它自己所创建的目录节点就行了

### <span id="jump4">4. 总结</span>

- 本文介绍的 Zookeeper 的基本知识，以及介绍了几个典型的应用场景。这些都是 Zookeeper
 的基本功能，最重要的是 Zoopkeeper 提供了一套很好的分布式集群管理的机制，就是它这种基于
 层次型的目录树的数据结构，并对树中的节点进行有效管理，从而可以设计出多种多样的分布式的数
 据管理模型，而不仅仅局限于上面提到的几个常用应用场景


{: .box-note}
**Note:** 本文非作为商业用途，侵删. 如果你遇到任何问题，欢迎下面留言哈！ 整理不易，欢迎打赏！
