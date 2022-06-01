# Netty基础

Netty为什么大量的使用异步：

* 首先因为现在的服务器都是多核CPU，如果是单核CPU，那么Netty的异步优势并不能体现出来
* 异步并没有缩短相应时间，反而有所增加，也就是服务器的吞吐量得到了提升。

合理进行任务拆分也是理由异步的关键，只有把要完成的任务合理拆分之后，我们才能合理的应用异步来提升应用的吞吐量。

## 组件

### Future&Promise

netty中的Future和jdk中的Future同名，但是是两个接口，netty中的future继承自jdk中的Future，而Promise又对netty Future进行了扩展

* jdk Future只能同步等待任务结束（或成功，或失败）才能得到结果
* netty Future可以同步等待任务结束得到结果，也可以异步方式得到结果，但是都需要等待任务结束
* netty Promise不仅有netty Future的功能，而且脱离了任务独立存在，只作为两个线程间传递结果的容器。

