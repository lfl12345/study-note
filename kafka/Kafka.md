## 概述

### 定义

kafka是一个分布式的局域发布/订阅模式的消息队列（Message Queue），主要应用于大数据实时处理领域。

### 消息队列

![image-20211202153102335](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211202153102335.png)

#### 使用消息队列的好处：

作用：异步，解耦，削峰（减少峰值）

可恢复性

缓冲：解决生产者和消费者者速度不一致的情况，生成大于消费

灵活性峰值处理能力：灵活性因为kafka是分布式的，可以动态增加减少

异步通信

#### 消息队列的两种模式

**1、点对点模式**（一对一，消费者主动拉取数据，消息收到后消息清除）

**2、发布订阅模式**（一对多，消费者消费数据后不会删除信息）

发布订阅模式有两种，一种是消息队列推送消息给订阅者，另一种是订阅者拉取数据。kafka是第二种发布订阅模式。

第一种方式的缺点：消息队列推送速度和消费者处理能力不匹配，可能会造成处理不过来或者资源浪费（处理能力太强）

第二种方式的缺点：消费者需要一直询问是否存在消息，当没有消息的时候，需要不断地问，造成资源浪费

此时可能有第三种选择，消息队列通知消费者，然后消费者去拉取数据，但是这种方式缺点更多。首先消息队列必须存储所有的消费者，其次如果消费者不在线是无法通知的，这时有产生了很多问题。

### kafka架构

partition提高每一个topic的负载均衡能力，同时也可以提高并发。topic可以只有一个分区

leader：在partition和topic出现之后出现，leader针对分区的leader，有leader也应该有follower，follower相当于leader的备份，因为follower是leader的备份，所以follower一定和leader在不同的机器上。无论是生产者还是消费者只找leader，只有leader挂了，才会用follower。

topic中的某一个分区只能被消费者组中的一个消费者消费

hdfs的副本数为最大副本数，有几个机器就有最多只能有几个副本，如果机器在增加，hdfs的副本数会动态增加，但是kafka的副本数最多只能和机器的数量一样

## kafka key的作用 请你简述一下Kafka[中的分区分配](https://blog.csdn.net/weixin_39530838/article/details/110801844)

## **Kafka** 架构深入

首先得有生产者和消费者或消费者组，然后中间是kafka集群

如果生产者往没有创建的主题发数据，默认创建一个分区一个副本的topic，这些都是server.properties配置文件定义的。

![image-20211202195942111](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211202195942111.png)

副本数是leader+follower个数

每一个分区维护了一个从头开始的偏移量。

follower主动的寻找leader同步数据

生产消息的顺序和消费消息的顺序有可能不一样，因为消息可能会发送到不同的分区，而消费者是从不同的分区获取数据的，所以获取数据的顺序可能会和生产的顺序不一样。

默认log文件到达1G然后新创建一个文件，但是创建的文件叫什么？如果定位数据？这两个问题要依赖index文件。一个segment对应一个log和index文件。

为了防止log文件过大导致数据定位效率低下，kafka采取了**分片和索引机制**

![image-20211202201139261](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211202201139261.png)

![image-20211202201349141](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211202201349141.png)

Kafka在选择follower加入或踢出ISR的时候为什么不在考虑数据条数的大小，而只考虑相应的时间？如果考虑了数据条数的大小，如果一个批次的数据来了，写入follower，这时有可能出现所有的follower数据条数都不满足条件，所有的follower都被踢出ISR，然后follower同步过后，数据条数又满足了要求，此时需要加入ISR，并且元数据有是存储在zookeeper上的，所以这种情况就会很消耗内存，因此这个条件被踢出了

## 分区分配策略（消费者组和topic的多个partition）

## offset的维护









## 生产者

### [生产者不同分区策略](https://www.cnblogs.com/yuexiuping/p/14815300.html)

### 不同类型的ACK——0、1、-1

## broker

实际上是一个kafka进程

存东西存在topic里，但是topic只是一个逻辑上的概念

### topic的分区和副本

### topic分区的作用

### topic-partition备份的作用

### ISR问题

### HW和LEO

## 消费者

### 消费者组

```
public static final String AUTO_OFFSET_RESET_DOC = "What to do when there is no initial offset in Kafka or if the current offset does not exist any more on the server (e.g. because that data has been deleted): <ul><li>earliest: automatically reset the offset to the earliest offset<li>latest: automatically reset the offset to the latest offset</li><li>none: throw exception to the consumer if no previous offset is found for the consumer's group</li><li>anything else: throw exception to the consumer.</li></ul>";
```

