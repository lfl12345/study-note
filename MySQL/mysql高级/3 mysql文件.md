# 文件

* 参数文件：告诉MySQL实例启动时在哪里可以找到数据库文件，并且指定某些初始化参数，这些参数定义了某种内存结构的大小设置，还会介绍各种参数的类型。
* 日志文件：用来记录MySQL实例对某种条件作出响应时写入的文件，如错误日志文件、二进制日志文件、慢查询日志文件、查询日志文件等。
* socket文件：当使用UNIX域套接字方式进行连接时需要的文件。
* pid文件：MySQL实例的进程ID文件
* MySQL表结构文件：用来存放MySQL表结构定义文件

以上的文件为MySQL的文件

下面的文件是Innodb特有的文件

* 存储引擎文件：因为MySQL表存储引擎的关系，每个存储引擎都会有自己的文件来保存各种数据。这些存储引擎真正存储类记录和索引等数据。

## 参数文件

在默认情况下，MySQL实例会按照一定的顺序在指定的位置读取配置文件，可以通过一下命令寻找配置文件的位置以及读取顺序。

```shell
mysql --help | grep my.cnf
```

MySQL实例可以不需要参数文件，这时所有的参数值取决于编译MySQL时指定的默认值和源码中指定参数的默认值。

### 参数（变量）类型

* 动态参数
* 静态参数

静态参数是只读的，在实例声明周期内都不可以进行修改。例如：

```sql 
set global datadir='/db/mysql'# 会报错
```

## 日志文件

日志文件记录了影响MySQL数据库的各种类型活动，MySQL数据库中常见的日志文件有：

* 错误日志（error log）
* 二进制日志（binlog）
* 慢查询日志（slow query log）
* 查询日志（log）

### 错误日志

错误日志对MySQL的启动、运行、关闭过程进行记录。（我Linux本机mysql的错误日志存放在data目录下）

![image-20211211112531826](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211211112531826.png)

![image-20211211112546782](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211211112546782.png)

### 慢查询日志

可以通过错误日志得到一些关于数据库优化的信息，而慢查询日志（slow query log）可以帮助我们定位可能存在问题的SQL语句，从而进行SQL语句层面的优化。

默认情况下MySQL数据库并不启动慢查询日志，用户需要手动将参数log_slow_queries设置为YES。

#### 作用：

1. 记录运行时间超（大于）过指定时间（单位微妙）的SQL语句
2. 记录没有使用索引的SQL语句
3. 统计SQL逻辑读取和物理读取的统计，物理读取是进行磁盘IO读取的次数，逻辑读取包含所有的读取，不管是磁盘还是缓冲池。
4. 记录超过指定逻辑IO次数的SQL语句

### 查询日志

记录所有对MySQL数据库请求的信息，无论这些信息是否得到了正确的执行。

### 二进制日志

binlog记录了对MySQL数据库执行更改的所有操作，但是不包括select和show这类操作，因为这类操作对数据本身并没有修改。如果操作本身并没有造成数据库发生变化，那么操作可能也会写入binlog。

#### 作用

* 恢复：某些数据的恢复需要二进制文件，在数据库进行全备文件恢复后，用户可以通过二进制文件进行point-in-time的恢复。
* 复制：通过复制和执行binlog使一台远程的MySQL数据库与一台MySQL数据库进行实时同步
* 审计：用户可以通过binlog中的信息来进行审计，判断是否有对数据库进行注入的攻击。

#### 性能影响

开启binlog的确会影响性能，但是根据MySQL官方手册中的测试表名，开启binlog会使性能下降1%，但是如果开启binlog我们可以有恢复和复制的功能，因此这些性能损失可以接受。

#### binlog缓冲写

当使用事务的表存储引擎时，所以未提交二进制日志文件会被记录到一个缓冲中去，等事务提交时直接将缓冲中的binlog写入磁盘。缓冲大小默认32k

但是并不是每次事务提价binlog都直接持久化到磁盘，此时如果发生宕机，可能会有数据不一致情况发生，这会给恢复和赋值带来问题，所以建议把sync_binlog设置为1，表示采用同步写磁盘的方式写binlog，这时写操作不使用操作系统的缓存来写二进制日志。

#### binlog日志格式

使用binlog_format 参数设置，取值为statement、row和mixed

* statement格式记录的是日志的逻辑SQL语句，如果使用这个格式下的日志文件，当InnoDB存储引擎的默认事务隔离级别为read committed时，会出现类似丢失更新的现象。
* row格式的binlog文件记录的不再是简单的SQL语句，而是记录表行更改情况。同时对statement格式下复制的问题予以解决。所以如果设置了binlog格式为row，可以设置Innodb事务隔离级别为read committed，以获取更好的并发。
* mixed格式下，MySQL默认采用statement格式进行binlog的记录，但是在一些情况下会使用row格式，可能的情况有：
  * 表的存储引擎为NDB，这时对标的DML操作都会以ROW格式记录。
  * 使用了UUID()、USER()等不确定的函数
  * 使用了insert delay语句
  * 使用了用户自定义函数（UDF）
  * 使用了临时表（temporary table）

InnoDB支持row和statement格式的binlog，可能有些存储引擎不是两种都支持。

row类型的binlog会增大binlog日志文件的大小，也会减缓操作的时间

## 套接字文件

在UNIX系统下本地连接MySQL可以采用UNIX域套接字方式，这种方式只需要一个套接字文件，套接字文件可由参数socket控制，一般在/tmp目录下名为mysql.sock

## pid文件

内容：记录了该进程的ID

作用：放置启动多个进程副本

原理：进程运行后会给.pid文件加一个文件锁，只有或得pid文件（固定路径固定文件名）写入权限（F_WRLCK）的进程才能正常启动并把自身的PID写入该文件中，其他同一个程序的多余进程则自动退出。

pid文件可由pid_file参数控制。

## 表结构定义文件

因为MySQL插件式存储引擎的体系结构关系，MySQL数据的存储是根据表进行的，每个表都会有与之对应的文件，但是不论采用何种存储引擎，MySQL都有一个以frm为后缀名的文件，这个文件记录了该表的表结构定义。

MySQL8取消了frm文件，元数据都存到表空间

## InnoDB存储引擎文件

### 表空间文件

InnoDB采用将存储的数据按照表空间（tablespace）进行存放的设计。在默认配置下会有一个初始大小为10MB，名为ibdata1的文件。该文件就是默认的表空间文件。

当参数innodb_file_per_table，用户可以将每个基于InnoDB存储引擎的表产生一个独立表空间。这就是以ibd结尾的InnoDB的表数据文件。

这些单独的表空间文件仅仅存储该表的数据、索引和插入缓冲bitmap等信息，其余信息还是存放在默认的表空间中

### redo log

默认情况下，在InnoDB存储引擎的数据目录下会有两个名为ib_logfile0和ib_logfile1的文件，这两个文件就是InnoDB存储引擎的重做日志（redo log）。

![image-20211211141801510](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211211141801510.png)

每个innodb存储引擎至少有1个重做日志文件组，每个文件组下面至少有2个重做日志文件.

redo log的大小对Innodb引擎的性能有非常大的影响。如果太大，那么crash后恢复时可能需要很长时间。如果太小，那么可能导致一个事务的日志需要多次切换redo log，此外如果太小还可能会导致频繁的发生async checkpoint，导致性能的抖动。

### binlog和redo log对比

1. binlog会记录所有与MySQL有关的日志记录，包括所有的存储引擎的日志。而redo log只记录innodb存储引擎本身的日志。
2. 记录的内容不同。无论bin log的记录格式设置为statement、row或mixed，他记录的都是关于一个事务的具体操作内容，是逻辑日志。而redo log记录的是关于每个页（Page）的更改，是物理情况
3. 写入的时间不同，bin log仅在事务提交前进行提交，即只写磁盘一次，而在事务进行的过程中，不断有redo log被写入redo log中。

### binlog和redo log的缓存和二段提交

redo log首先写入缓冲，然后按512字节写入磁盘（主线程每秒执行写入磁盘或设置了innodb_flush_log_at_trx_commit参数为1表示每次事务提交都写入磁盘），因此redo log并不是每次写都同步写入磁盘，因此就不能保证crash safe，所以我们需要设置innodb_flush_log_at_trx_commit参数使得每次事务提交redo log都同步到磁盘。

binlog 是在事务提交的时候写的，但是也不是直接同步到磁盘，可以设置sync_binlog参数为1表示每写一次缓冲就写一次磁盘，这样就可以保证binlog的每一次写都同步到磁盘。

但是尽管设置了sync_binlog为1，binlog和redo log还是有可能发生不一致的情况，这种情况就是没有设置二段提交的情况，之前已经分析过了这里就不分析了。可以通过设置innodb_support_xa为1来设置二段提交，这样就可以保证binlog 和 redo log的一致性