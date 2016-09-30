---
title: Kafka 基础
date: 2016-09-30 14:33:57
tags: [每周总结, kafka]
---

## Kafka 基础概念

Kafka是由LinkedIn开发的一个分布式的，基于发布/订阅的消息系统。

### 关键词

- **Broker**

  Kafka 集群包含一个或多个服务器，这种服务器被称为broker

- **Topic**

  每条发布到 Kafka 集群的消息都有一个类别，这个类别被称为 Topic。（物理上不同 Topic 的消息分开存储，逻辑上一个Topic的消息虽然保存于一个或多个 broker 上但用户只需指定消息的 Topic 即可生产或消费数据而不必关心数据存于何处）

- **Partition**

  Parition 是物理上的概念，每个 Topic 包含一个或多个 Partition

- **Producer**

  负责发布消息到 Kafka broker

- **Consumer**

  消息消费者，向 Kafka broker 读取消息的客户端。

- **Consumer Group**

  每个Consumer 属于一个特定的 Consumer Group（可为每个 Consumer 指定 group name，若不指定 group name 则属于默认的 group）。

### 拓扑结构

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/learn-kafka/kafka-01.jpeg)

Kafka 通过 Zookeeper 管理集群配置，选举 leader，以及在 Consumer Group 发生变化时进行 rebalance。Producer 使用 push 模式将消息发布到 broker，Consumer 使用 pull 模式从 broker 订阅并消费消息。

### 概念说明

#### Topic & Partition

Topic 在 **逻辑上** 可以认为是一个 queue，每条消息必须指定一个 topic，也就是将这条消息放进某一个 queue。

为了使得 Kafka 的吞吐率可以线性提高，**物理上** 把 Topic 分成一个或多个Partition，每个 Partition 在物理上对应一个文件夹，该文件夹下存储这个 Partition 的所有消息和索引文件。因为每条消息都被 append 到该 Partition 中，属于顺序写磁盘，因此效率非常高（经验证，顺序写磁盘效率比随机写内存还要高，这是Kafka高吞吐率的一个很重要的保证）。

如果一个 Topic 对应一个文件，那这个文件所在的机器 I/O 将会成为这个 Topic 的性能瓶颈，而有了 Partition后，不同的消息可以并行写入不同 broker 的不同 Partition 里，极大的提高了吞吐率。可以在$KAFKA_HOME/config/server.properties 中通过配置项 num.partitions 来指定新建 Topic 的默认 Partition 数量：

```dockerfile
# The default number of log partitions per topic. More partitions allow greater
# parallelism for consumption, but this will also result in more files across
# the brokers.
num.partitions=1
```

也可在创建 Topic 时通过参数指定，同时也可以在 Topic 创建之后通过 Kafka 提供的工具修改。

#### Product

Product 负责发布消息到 Kafka broker，每次发布消息时，如上所述，Product 必须指定该消息的 Topic，至于消息会放到哪个 Partition，会根据 Paritition 机制选择将其存储到哪一个 Partition。如果 Partition 机制设置合理，所有消息可以均匀分布到不同的 Partition 里，这样就实现了负载均衡。

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/learn-kafka/kafka-02.jpeg)

常见的机制有：

- 随机
- 设置 key 和 partition num 取余
- 顺序循环

等等。

#### 信息消费

对于传统的 message queue 而言，一般会删除已经被消费的消息，而 Kafka 集群会保留所有的消息，无论其被消费与否。当然，因为磁盘限制，不可能永久保留所有数据（实际上也没必要），因此 Kafka 提供两种策略删除旧数据。一是基于时间，二是基于 Partition 文件大小。例如可以通过配置 $KAFKA_HOME/config/server.properties，让 Kafka 删除一周前的数据，也可在 Partition 文件超过1GB时删除旧数据，配置信息如下：

```dockerfile
# The minimum age of a log file to be eligible for deletion
log.retention.hours=168

# A size-based retention policy for logs. Segments are pruned from the log as long as the remaining
# segments don't drop below log.retention.bytes.
#log.retention.bytes=1073741824

# The maximum size of a log segment file. When this size is reached a new log segment will be created.
log.segment.bytes=1073741824

# The interval at which log segments are checked to see if they can be deleted according
# to the retention policies
log.retention.check.interval.ms=300000
```

这里要注意，因为 Kafka 读取特定消息的时间复杂度为 O(1)，即与文件大小无关，所以这里删除过期文件与提高 Kafka 性能无关。选择怎样的删除策略只与磁盘以及具体的需求有关。

#### Consumer Group

通常来讲，消息模型可以分为两种， 队列和发布-订阅式。 队列的处理方式是 一组消费者从服务器读取消息，一条消息只有其中的一个消费者来处理。在发布-订阅模型中，消息被广播给所有的消费者，接收到消息的消费者都可以处理此消息。Kafka为这两种模型提供了单一的消费者抽象模型： 消费者组 （consumer group）。

消费者用一个消费者组名标记自己。 一个发布在 Topic 上消息被分发给此消费者组中的一个消费者。那么显而易见地：

- 假如所有的消费者都在一个组中，那么这就变成了queue模型。
- 假如所有的消费者都在不同的组中，那么就完全变成了发布-订阅模型。

更通用的， 我们可以创建一些消费者组作为逻辑上的订阅者。每个组包含数目不等的消费者， 一个组内多个消费者可以用来扩展性能和容错。

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/learn-kafka/kafka-03.jpeg)

#### Consumer

Consumer 是消息的消费者，Consumer 在从 broker 读取消息后，可以选择 commit，该操作会在 Zookeeper 中保存该 Consumer在该 Partition 中读取的消息的 offset。该 Consumer 下一次再读该 Partition 时会从下一条开始读取。如未 commit，下一次读取的开始位置会跟上一次 commit 之后的开始位置相同。当然可以将 Consumer 设置为 autocommit，即 Consumer 一旦读到数据立即自动 commit。

Kafka 会为每一个 Consumer Group 保留一些 metadata 信息包括 offset。这个 offset 由Consumer控制。正常情况下 Consumer 会在消费完一条消息后递增该 offset。当然，Consumer 也可将 offset 设成一个较小的值，重新消费一些消息。因为 offet 由 Consumer 控制，所以 Kafka broker 是无状态的，它不需要标记哪些消息被哪些消费过，也不需要通过 broker 去保证同一个 Consumer Group 只有一个 Consumer 能消费某一条消息，因此也就不需要锁机制，这也为 Kafka 的高吞吐率提供了有力保障。

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/learn-kafka/kafka-05.jpeg)

#### High Level Consumer

High Level 的意思实际上是更高 Level 的 API，而不是说这种 Consumer 更高级。很多时候，客户程序只是希望从 Kafka 读取数据，不太关心消息 offset 的处理。同时也希望提供一些语义，例如同一条消息只被某一个 Consumer 消费（单播）或被所有 Consumer 消费（广播）。因此，High Level Consumer 提供了一个从 Kafka 消费数据的高层抽象，从而屏蔽掉其中的细节并提供丰富的语义。它提供了一个从 Kafka 消费数据的高层抽象，从而屏蔽掉其中的细节并提供丰富的语义。

使用 Consumer high level API 时，同一Topic的一条消息只能被同一个 Consumer Group 内的一个 Consumer 消费，但多个 Consumer Group 可同时消费这一消息。

假设有一个 Topic (名为 topic1，包含3个 Partition)，有两个 Consumer Group：group1 包含 consumer 1，group 2 包含 consumer2, consumer3, consumer 4)。现通过 Producer 向 topic1 发送key分别为1，2，3的消息。那么将发现属于 group1 的 Consumer 收到了所有的这三条消息，同时 group2 中的 3 个 Consumer 分别收到了 key 为 1，2，3 的消息。如下图所示。

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/learn-kafka/kafka-04.png)

### 安装kafka

google，安装 kafka 之前需安装 jdk

### 查看所有topic

```bash
# 打开 kafka 所在目录
cd YOUR_KAFKA_HOME
# 列出所有 topic
./bin/kafka-topics.sh --list --zookeeper YOUR_ZOOKEEPR_IPS
```

## 业务背景

需要订阅业务平台的 Kafka 中的 c1 和 c2 的通话记录。

## 技术方案

使用 node 包：[kafka-node](https://github.com/SOHU-Co/kafka-node)﻿

```javascript
const kafka = require('kafka-node');
// 创建连接
const client = new kafka.Client(KAFKA_SERVER_IP);      
// 创建 HLC
const consumer = new HighLevelConsumer(
	client,
	[
		// 订阅话题，如果需要从指定 offset 读起，填 offset 参数
		{ topic: TOPIC_NAME, offset: 0}
	],
	{
		// 指定 groupId
        groupId: 'eagle_eye',
        autoCommit: true,
        autoCommitIntervalMs: 5000,
        fetchMaxWaitMs: 100,
        fetchMinBytes: 1,
        fetchMaxBytes: 1024 * 1024,
        // 需要从指定 offset 读起
        fromOffset: true,
        encoding: 'utf8'
	}
);

consumer.on('message', function (message) {
  // 订阅消息
})
```



## Zookeeper

### 安装 Zookeeper

mac 下可以直接使用 brew 安装 zookeeper，安装前需要先安装 jdk

```shell
brew install zookeeper
```

### 连接到 Zookeeper 服务

```shell
zkCli -server YOUR_ZOOKEEPR_IPS
```
###  Zookeeper 命令

```shell
# 使用ls命令查看当前Zookeeper中所包含的内容
ls /	# 输出 [brokers, consumers, config 等]
ls /consumers	# [consumer-group-1, test-group 等]
ls /consumers/test-group
# 依次类推

# 删除某个节点
delete /you-want-to-delete
```

## 参考
1. [Kafka架构原理](http://wenku.baidu.com/link?url=67RIEmD9ZesDewsvFuuALe1AF4yNdXN7e4eeca0FxpjA9voYrV1NkKOTdtmKIH9xAp0wzbO0nBCUsYK7HinB68Lmk2_zxQP0W_OGTTihEB3)
2. [Kafka剖析系列文章](http://www.infoq.com/cn/articles/kafka-analysis-part-1)
3. [[Kafka分区机制介绍与示例](http://lxw1234.com/archives/2015/10/538.htm)](http://lxw1234.com/archives/2015/10/538.htm)
4. [Kafka快速入门](http://colobu.com/2014/08/06/kafka-quickstart/)
