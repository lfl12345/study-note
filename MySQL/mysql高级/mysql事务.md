# 事务

## 1、概述

MySQL 事务主要用于处理操作量大，复杂度高的数据。

**在 MySQL 中只有使用了 Innodb 数据库引擎的数据库或表才支持事务。**

事务是用户定义的一个数据库操作序列，这些操作要么全做，要么全部做，是一个不可分割的工作单位。

## ACID四个特性

事务的四个特征ACID：

* 原子性（Atomicity）

  整个事务（transaction）中的所有的操作，要么全部完成，要么全部不完成，不会因为某些原因结束在中间的环节。当事务在执行的过程中发生错误，会被回滚到事务开始前的状态。

* 一致性（consistency）

  一致性和原子性是密切相关的。当数据库只包含成功事务提交结果时，就说数据库处于一致性状态，**事务执行的结果必须是使数据库从一个一致性状态变为另一个一致性状态。**

  **在事务开始之前和事务结束之后，数据库的完整性约束没有被破坏**，例如，在表中有一个字段为姓名，为唯一约束，即在表中姓名不能重复，如果一个事务对姓名字段进行了修改，但是在事务提交或事务操作发生回滚后，表中的姓名变的非唯一了，这就破坏了事务的一致性要求，即事务将数据库从一种状态变味了一种不一致的状态。

* 隔离性（isolation）

  一个事务的执行不能被其他事务干扰，也就是一个事务的内部操作以及使用的数据对其他并发事务是隔离的，并发执行的各个事务之间不能互相干扰。

  当前数据库系统中都提供了一种**粒度锁**的策略，允许事务仅锁住一个实体对象的子集，以此来提高事务之间的并发度。

* 持久性（持续性）（Durability）

  一个事务一旦提交，它对数据库中的数据的改变就应该是永久的，接下来的其他操作或故障不应该对其执行结果有任何影响。 

MySQL事务的回滚应该是支持DML和DDL语句的。（还没有实验成功）

注意：rollback或者commit后事务就结束了。

## 2、事务控制语句

* begin或start transaction显式地开启一个事务；

* commit也可以使用commit work，commit会提交事务，并使已对数据库进行的修改称为永久性的。

* rollback 或rollback work，回滚会结束事务，并撤销正在进行的所有未提交的修改。

* savepoint identifier savepoint允许在事务中创建一个保存点，一个事务可以有多个保存点

  > savepoint是在数据库事务处理中实现“子事务”（subtransaction），也称为嵌套事务的方法。事务可以回滚到savepoint而不影响savepoint创建之前的变化，不需要放弃整个事务。

* release savepoint identifier 删除一个事务的保存点，当没有指定的保存点时，语句会抛出一个异常。

* rollback to identifier 把事务回滚到标记点

* set transaction 用来设置事务的隔离级别。Innodb存储引擎提供事务的隔离级别有 read uncommitted、read commited、repeatable read、serializable。

## 3、自动提交模式

MySQL的每一个连接都是默认自动提交事务的，每一句语句的执行就提交一句。

在自动提交模式下，仍然可以使用start transaction语句来显示的启动事务，这时一个事务仍然可以包含多条语句，知道这些语句被统一提交或回滚。

## 4、多线程事务

### 4.1 多线程事务中出现的问题

原因：当多个客户端并发的访问同一个表时，可能会出现下面的一致性问题。

#### 4.1.1 脏读（dirty read）

T1事务的线程中访问了一个数据，这个数据是T2事务线程中修改了但是还没有提交的数据。这时读取的数据是提交之前的数据，但是T2事务有可能回滚，也就是这个数据有可能设置不成功，此时T1读取的数据就是不对的数据，这时候就发生了脏读。

select+update

#### 4.1.2 不可重复读（non-repeatable read）

在同一个事务中，同一个读操作对于同一个数据的前后两次读取产生了不同的结果，这就是不可重复读。这个数据在另一个事务线程中被修改了并被提交了。

select+update|delete

#### 4.1.3 幻读（phantom read）

在同一个事务中同一个查询语句进行查询，第一次查询没有的行在第二次差查询的时候出现了，由于其他事务提交而出现了新行或者少了一些行。

select+insert

#### [不可重复度和幻读的区别](https://www.cnblogs.com/itcomputer/articles/5133254.html)

从总的结果来看，似乎这两个者都表现为两次读取的结果不一样，但是不可重复度强调的是读取一行数据的结果不一致，重点在于update和delete，幻读强调的是读取一张表的不一致性也就是行的不一致，重点在于insert。

**不可重复读和幻读的最大区别在于如果通过锁机制来解决他们产生的问题**

如果使用锁机制来实现这两种隔离级别，在可重复读中，一条sql第一次读取到数据之后，就将这些数据加锁，其他事务没有办法修改这些数据，就可以实现可重复读了。但是使用这种方法却无法锁住insert的数据，所以当事务T1先读取了数据，或者修改了全部数据，事务T2还是可以insert数据然后提交，这时候事务T1就会发现莫名其妙多了一条之前没有的数据，这就是幻读，幻读不能通过行锁避免，这时就需要serializable隔离级别，读用读锁，写用写锁，读锁和写锁互斥，这么做可以避免幻读、不可重复读、脏读等问题，但是会降低数据库的并发能力。

### 4.2 解决方案 事务的隔离级别

Innodb实现了四个隔离级别，用于控制事务所做的修改，并将修改通告值其他并发的事务：

#### 4.2.1 读未提交（read uncommitted）

允许一个事务可以看到其他事务没有提交的修改，

这个隔离级别允许上面三个问题的发生。

#### 4.2.2 读已提交 （read committed）

允许一个事务只能看见其他事务已经提交的修改，未提交的修改是不可见的。

这种隔离级别避免了脏读，但是后面两个问题可能会发生。

#### 4.2.3 可重复读（repeatable read）

确保如果一个事务中执行两次相同的select语句，都能得到相同的结果，不管其他事务是否提交过这些修改。

一般的这个隔离级别的事务只能避免脏读和不可重复读，但是MySQL的Innodb引擎中的事务在这个隔离级别可以避免幻读。MySQL中的默认隔离级别也是这一级别

#### 4.2.4 可串行化

将一个事务与其他事务完全地隔离，可以避免以上三个问题。

#### 隔离级别和一致性之间的关系

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211201142822389.png" alt="image-20211201142822389" style="zoom:50%;" />

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211201142854822.png" alt="image-20211201142854822" style="zoom:50%;" />

## 具体操作

### 设置默认隔离级别

1、在my.ini文件中设置transaction-isolation选项来设置服务器的默认事务隔离级别

此选项的值可以为：

* read-uncommitted
* read-committed
* repeatable-read
* serializable

例如：

transaction-isolation=read-committed

2、通过命令动态设置隔离级别

使用set transaction isolation level语句。

```sql
set [global | session] transaction isolation level <isolation-level> 
```

isolation-level可以设置为：

* read uncommitted
* read committed
* repeatable read
* serializable

例如：set transaction isolation level repeatable read

### 隔离级别的作用范围

全局和会话

### 查看隔离级别

```sql
# 查看当前会话的隔离级别
select @@tx_isolation# 这是MySQL5版本的方式
select transaction_isolation # 这是
select @@session.tx_isolation

# 查看全局的隔离级别
select @@global.tx_isolation
```

# 事务的实现

事务的隔离性有锁来实现，原子性、一致性、持久性通过数据库的redo log和undo log来完成。redo log称为重做日志，用来保证事务的原子性和持久性，undo log用来保证事务的一致性。

undo log不是redo log的逆过程，redo和undo的作用都可以视为是一种恢复操作，redo恢复提交事务修改的页操作，而undo用来回滚行记录到某个特定版本。因此两者记录的内容不同，redo通常是物理日志，记录的是页的物理修改操作，undo是逻辑日志，根据每行记录进行记录。

## redo

### 基本概念

重做日志用来实现事务的持久性，其由两部分组成：

1. 内存中的重做日志缓冲（redo log buffer），容易失去的。
2. 重做日志文件（redo log file），持久的。

