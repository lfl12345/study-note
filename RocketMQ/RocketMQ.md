# RocketMQ

## RocketMQ的安装与启动

### 基本概念

#### 消息

消息是指，消息系统所传输信息的物理载体，生产和消费数据的最小单位，每条消息必须属于一个主题。

#### 主题（Topic）

Topic表示一类消息的集合，每个主题包含若干条消息，每条消息只能属于一个主题，是RocketMQ进行消息订阅的基本单位。Topic和message在数量上是一对多的关系。

一个生产者可以同时发送多种Topic的消息；而一个消费者只对某种特定的Topic感兴趣，即只可以订阅和消费一种Topic的消息。生产者和主题是一对多的关系；消费者和主题是一对一的关系。

#### 标签（Tag）

为消息设置的标签，用于同一主题下区分不同类型的消息。来自同一业务单元的消息，可以根据不同业务目的在同一主题设置不同标签。标签能有效地保持代码的清晰度和连贯性，并优化RocketMQ提供的查询系统。消费者可以根据Tag实现对不同子主题的不同消费逻辑，实现更好的扩展性。

#### 队列（Queue）

存储消息的物理实体。一个Topic中可以包含多个Queue，每个Queue中存放的就是该Topic的消息。一个Topic的Queue也被称为一个Topic中消息的分区。

一个Topic中的一个Queue中的消息只能被一个消费者组的消费者消费，不同消费者组的消费者可以重复消费一个消息。

<img src="/Users/bytedance/Library/Application Support/typora-user-images/image-20220606133140367.png" alt="image-20220606133140367" style="zoom:50%;" />

分片（Sharding）：分片不同于分区。在RocketMQ中，分片指的是存放相应Topic的Broker。每个分片中会创建出相应数量的分区，即Queue，每个Queue的大小都是相同的。

<img src="/Users/bytedance/Library/Application Support/typora-user-images/image-20220606134200790.png" alt="image-20220606134200790" style="zoom:50%;" />

#### 消息标识（MessageId/Key）

RocketMQ中每个消息拥有唯一的MessageId，且可以携带具有业务标识的Key，以方便对消息的查询。不过需要注意的是，MessageId有两个：在生产者send()消息时会自动生成一个MessageId（msgId），当消息到达Broker后，Broker也会自动生成一个MessageId(offsetMsgId)。msgId、offsetMsgId与Key都称为消息的标识。

* msgId：有producer端生成，生成规则为：

  producerIp+进程pid+MessageClientIDSetter类的ClassLoader的hashCode+当前时间+AutomicInteger自增计数器

* offsetMsgId：由broker端生成，生成规则为：brokerIp+物理分区的offset（Queue中的偏移量）

* key：由用户指定的业务相关的唯一标识。

### 系统架构

