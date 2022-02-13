# 2.4Hadoop集群的部署和使用

## 两大核心组件

* HDFS
* MapReduce

## 集群硬件配置

* namenode：访问数据先去访问namenode，获得关于地址存储的具体地址信息，再到数据节点上取数据
* secondNameNode：nameNode的冷备份，nameNode一出问题，secondNameNode不能直接顶上去
* DataNode

## Mapreduce两大核心组件

* JobTracker：相当于作业管家，每次以一个MapReduce作业的形式去完成具体计算任务，由JobTracker去管理这个计算任务，去协调不同的机器执行计算任务。
* TaskTracker：不同的机器上安装的就是TaskTracker，每一个负责跟踪和执行自己分配的作业

![image-20210725131504398](C:\Users\三夏三十\AppData\Roaming\Typora\typora-user-images\image-20210725131504398.png)

