# Spring

## Spring概述

* Spring是轻量级的开源的JavaEE（Java Enterprise Edition 企业版本）框架
* Spring可以解决企业应用开发的复杂性
* Spring有两个核心部分：IOC和AOP
  * IOC：控制反转，把创建对象过程交给Spring进行管理
  * AOP：面向切面，不修改源代码进行功能增强
* Spring特点
  * 方便解耦，简化开发
  * AOP编程支持
  * 方便编程测试
  * 方便和其他框架进行整合
  * 方便进行事务操作
  * 降低API开发难度

## IOC容器

什么是IOC

* 控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理
* 使用IOC的目的：为了降低耦合度

### IOC底层原理

IOC用到的技术：xml解析，工厂模式，反射

### IOC接口（BeanFactory）

1. IOC思想基于IOC容器完成，IOC容器底层就是对象工厂。
2. Spring提供了IOC容器实现的两种方式：（两个接口）
   1. BeanFactory：IOC容器最基本实现，Spring内部使用的容器，开发者一般不使用
   2. ApplicationContext：扩展了BeanFactory的功能，提供给开发者使用。



### IOC操作Bean管理（基于xml）

什么是Bean管理：

* Bean管理是指两个操作：Spring创建对象、Spring注入属性



### IOC操作Bean管理（基于注解）



































































































































