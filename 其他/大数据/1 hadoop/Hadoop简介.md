# Hadoop

## [hadoop生态圈](https://blog.csdn.net/sanmi8276/article/details/113825053)

### 大数据技术生态系统

![img](https://img-blog.csdnimg.cn/20201206145055637.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMzYzMDMy,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20201206145117617.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMzYzMDMy,size_16,color_FFFFFF,t_70)

![image-20211116111107394](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211116111107394.png)

### 推荐系统框架

![image-20211116111240663](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211116111240663.png)

## 概念

Hadoop是由Apache基金会所开发的**分布式系统基础架构**。用户可以在不了解分布式底层细节的情况下，开发分布式程序。充分利用集群的威力进行高速运算和存储。

Hadoop是**基于Java语言开发**的，具有很好的**跨平台特性**，并且可以**部署在廉价的计算机集群**中

Hadoop框架最核心的设计就是：**HDFS**和**MapReduce**。HDFS为海量的数据提供了存储，而MapReduce则为海量的数据提供了计算。

## Hadoop的特性（优势）

Hadoop是一个能够对大量数据进行分布式处理的软件框架，并且是以一种可靠、高效、可伸缩的方式进行处理的，它具有一下几个方面的特性：

* 高可靠性：Hadoop底层维护多个数据的副本，所以即使Hadoop的某个计算元素或存储出现故障，也不会导致数据的丢失。
* 高可扩展性：在集群间分配任务数据，可以扩展数以千计的结点。
* 高效性：在MapReduce的思想下，hadoop是并行工作的，可以加快任务的处理速度。
* 高容错性：Hadoop可以将失败的任务自动重新分配。
* 低成本
* 运行在Linux平台上
* 支持多种编程语言

## Hadoop（组成）版本演变

Hadoop1.x时代，Hadoop中的MapReduce同时处理业务逻辑运算和资源的调度，耦合行较大。

Hadoop2.x时代，增加了Yarn(Yet Another Resource Negotiator，另一个资源协调者)，Yarn只负责资源的调度，MapReduce只负责运算。

Hadoop3.x在组成上没有变化。

![image-20211116104306639](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211116104306639.png)

## Hadoop的核心架构

Hadoop 由许多元素构成。其最底部是 Hadoop Distributed File System（HDFS），它存储 Hadoop 集群中所有存储节点上的文件。HDFS的上一层是[MapReduce](https://baike.baidu.com/item/MapReduce) 引擎，该引擎由 JobTrackers 和 TaskTrackers 组成。通过对Hadoop分布式计算平台最核心的[分布式文件系统](https://baike.baidu.com/item/分布式文件系统/1250388)HDFS、MapReduce处理过程，以及数据仓库工具Hive和[分布式数据库](https://baike.baidu.com/item/分布式数据库/1238109)Hbase的介绍，基本涵盖了Hadoop分布式平台的所有技术核心

## HDFS

* Hadoop实现了一个分布式文件系统（Distributed File System），其中一个组件是HDFS（Hadoop Distributed FIle System）。
* HDFS有高容错性的特点，并且设计用来部署在低廉的硬件上；它提供高吞吐量来访问应用程序的数据，适合那些有着超大数据集的应用程序。