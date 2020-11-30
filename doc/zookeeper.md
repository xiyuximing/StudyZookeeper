# Zookeeper

## 1.Zookeeper简介

![image-20201130230718671](https://gitee.com/xiyuximing/image/raw/master/image-20201130230718671.png)

zookeeper通过保证分布式系统信息一致性，解决协同问题。

当主节点对某个从节点分工信息做出改变后，相关订阅节点得到zookeeper的通知，获取自己最新的任务。当完成后，将结果存储至zookeeper。

主节点订阅了该任务的完成情况，从zookeeper得到完工通知。

### zookeeper的基本概念

Zookeeper是⼀个开源的分布式协调服务，其设计⽬标是将那些复杂的且容易出错的分布式⼀致性服务封装起来，构成⼀个⾼效可靠的原语集，并以⼀些简单的接⼝提供给⽤户使⽤。zookeeper是⼀个典型的分布式数据⼀致性的解决⽅案，分布式应⽤程序可以基于它实现诸如数据订阅/发布、负载均衡、命名服务、集群管理、分布式锁和分布式队列等功能。

**1、集群角色：**

- Leader

  通过leader选举产生，负责进行投票的发起和决议，更新系统状态

- Follower

  用于接收客户端请求并向客户端返回结果，在选举过程中参与投票

- Observer

  不参与投票，只同步leader状态。为了扩展系统和提高读取速度。

 接收请求流程：

1. follower接收客户端的写请求后，将写请求转发给leader处理

2. leader接收到follower发送的写请求后，将写请求转换成带有各种状态的事务，对事务进行广播，发送proposal
3. 所有接收到proposal的follower进行投票，向leader发送ACK
4. 大部分follower返回后，leader发送事务提交请求给各follower，各follower执行事务。

**2、会话（session）:**

Session指客户端会话，⼀个客户端连接是指客户端和服务端之间的⼀个TCP⻓连接，Zookeeper对外的服务端⼝默认为**2181**，客户端启动的时候，⾸先会与服务器建⽴⼀个TCP连接，从**第⼀次连接建⽴开始**，客户端会话的⽣命周期也开始了，通过这个连接，客户端能够⼼跳检测与服务器保持有效的会话，也能够向Zookeeper服务器发送请求并接受响应，同时还能够通过该连接接受来⾃服务器的Watch事件通知。

**3、数据节点（Znode）：**

在ZooKeeper中，“节点”分为两类，第⼀类是指构成集群的机器，我们称之为机器节点；第⼆类则是指数据模型中的**数据单元**，我们称之为数据节点——ZNode。ZooKeeper将所有数据存储在内存中，数据模型**是⼀棵树**（ZNode Tree），由斜杠（/）进⾏分割的路径，就是⼀个Znode，例如/app/path1。每个ZNode上都
会保存⾃⼰的数据内容，同时还会保存⼀系列属性信息。

**4、版本：**

Zookeeper的每个Znode上都会存储数据，对于每个ZNode，Zookeeper都会为其维护⼀个叫作**Stat**的数据结构，Stat记录了这个ZNode的三个数据版本，分别是version（当前ZNode的版本）、cversion（当前ZNode⼦节点的版本）、aversion（当前ZNode的ACL版本）。

**5、Watcher（事件监听器）**

Zookeeper允许⽤户在**指定节点上**注册⼀些Watcher，并且在⼀些特定事件触发的时候，Zookeeper服务端会将事件通知到感兴趣的客户端，该机制是Zookeeper实现分布式协调服务的重要特性。

**6、ACL**

Zookeeper采⽤ACL（Access Control Lists）策略来进⾏权限控制，其定义了如下五种权限：

- CREATE：创建⼦节点的权限。
- READ：获取节点数据和⼦节点列表的权限。
- WRITE：更新节点数据的权限。
- DELETE：删除⼦节点的权限。
- ADMIN：设置节点ACL的权限。

其中需要注意的是，CREATE和DELETE这两种权限都是**针对⼦节点**的权限控制