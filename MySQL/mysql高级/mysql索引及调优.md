# 索引及调优

## 索引原理

索引被用来快速找出一个特定的行，如果没有索引，MySQL不得不首先以第一条记录开始，然后读完整个表知道它找出相关的行。表越大，花费的时间越多。对于一个有序的字段，可以使用二分查找，这就是为什么使用索引可以得到提升的原因。MyISAM和Innodb都是使用B+Tree作为索引结构。

（主键约束和unique约束都会默认创建索引）

## 索引操作

什么时候需要给字段添加索引：

* 表中该字段中的数据量庞大
* 经常被检索，经常出现在where子句中的字段
* 经常被DML操作的字段不建议添加索引

主键和unique字段和外键字段会自动添加索引，所以使用这三种字段查询效率会高。

### 创建索引

```sql
create index index_name on table_name(column_name)
```

![image-20211203114302267](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211203114302267.png)

### 查看索引

```sql
show index from table_name
```

### 查看是否使用索引

```sql
explain <查询语句>;
```

![image-20211203114409793](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211203114409793.png)

### 删除索引

```sql
drop index index_name on table_name
alter table table_name drop index index_name
# 删除主键索引的方式
alter table table_name drop primary key
```

## 索引的优点

- 可以大大加快数据的检索速度，这也是创建索引的最主要的原因。
- 可以加速表和表之间的连接，特别是在实现数据的参考完整性方面特别有意义。
- 在使用分组和排序进行检索的时候，可以减少查询中分组和排序的时间

## 索引的用处

* 快速查找匹配where子句的行

## 索引的类型

### B-Tree索引

