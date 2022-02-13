## Redis相关知识

默认端口号：6379

默认16个数据库，默认使用0号数据库，可以使用命令select <dbid>来切换数据库

![image-20211204220248312](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211204220248312.png)

所有数据库都有相同的密码，统一密码管理。

dbsize 查看当期那数据库的key的数量

flushdb清空当前数据库

flushall通杀所有数据库

### **Redis是单线程+多路IO复用技术**

多路复用指的是使用一个线程来检查多个文件描述符（Socket）的就绪状态，比如调用select和poll函数，传入多个文件描述符，如果有一个文件描述符就绪，则返回，否则阻塞知道超时。得到就绪状态后进行真正的操作可以在一个线程里执行，也可以启动另外线程执行（比如使用线程池）

串行 vs 多线程+锁（memorycache） vs 单线程+多路IO复用技术

## 常用五大数据类型

### Redis键（key）

Redis中的key是二进制安全的。因此使用任何类型的数据都是可以的，包括空字符串以及JPEG文件。

![image-20211205222642487](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211205222642487.png)

不建议使用很长的key，不仅仅不利于存储，还有网络传输以及比较。**较大的key会花费更多的时间进行比较，可以使用散列的方式优化**

key的最大值为512M。

#### 二进制安全

二进制安全主要用于描述字符串操作。一个二进制安全的功能（函数），它会将操作的输入作为原始的、没有任何格式化的数据流，也就是说如果一个字符占一个字节，那么这个字符取值有256种可能的值。

#### 常用操作

copy、del

keys * 查看当前库的所有key

![image-20211204221915137](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211204221915137.png)

判断一个key是否存在——exists <key_name>

![image-20211204221953898](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211204221953898.png)

type key 查看key是什么类型

![image-20211204222114482](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211204222114482.png)

del key 删除指定key数据

**unlink key 根据value选择非阻塞删除：仅将key 从keyspace元数据中删除，真正的删除会在后续异步操作**

![image-20211204222216091](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211204222216091.png)

expire key 10 10秒钟，为给定key设置过期时间

ttl key 查看key还剩多少秒过期，-1 表示永不过期，-2表示已经过期

![image-20211204222602186](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211204222602186.png)

select 1 切换数据库到1号数据库

dbsize 查看当前数据库中的key的数量

flushdb 清空当前数据库

flushall 清空所有数据库

### Redis字符串（String value的类型）

#### 简介

String是Redis最基本的类型，可以理解为和Memcached一模一样的类型，一个key对应一个value。

String是**二进制安全的**。意味着Redis的string可以包含任何数据。比如jpg图片或者序列化的对象。只要是能编程字符串的东西都可以以string类型存储。

String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是512M。

#### 常用命令

set <key> <value> 添加键值对

get <key> 查询key对应的value

append <key> <value> 将给定的value追加到原始key对应的value末尾，原来没有指定的key，则会新创建一个key value

strlen <key> 获取key对应的value的长度

setnx <key> <value> 只有在key不存在时，设置key的值。

incr <key> 将key中存储的数字加1，只能对数字值操作，如果为空，新增值为1。数字的最大值为Long.MAX_VALUE

decr <key> 将key中存储的数字减1

incrby / decrby <key> <步长> 将key中存储的数字增减，自定义步长

mset <k1> <v1> <k2> <v2> .... 同时设置一个或多个key-value对

mget<k1> <k2> .... 同时获取一个或多个value

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211205150015154.png" alt="image-20211205150015154" style="zoom:50%;" />

msetnx <k1> <v1> <k2> <v2> .... 同时设置一个或多个key-value对，当且仅当所有跟定key都不存在。这是一个原子性的操作，只有当所有的key都没有对应的value时，操作才能成功，只要有一个key有对应的value，所有的操作都不会成功。

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211205150432233.png" alt="image-20211205150432233" style="zoom:50%;" />

getrange <key> <start> <end> 获取key对应的value的范围，类似于java中的substring，左闭右开。

setrange <key> <start> <value> 用value 覆写key 锁存储的字符串值，从start开始（**索引从0开始**）

setex <key> <过期时间> <value> 设置键值的同时，设置过期时间，单位秒

getset <key> <value> 以新换旧，设置新值的同时获取旧值，如果没有旧值，返回nil

#### Redis原子操作性<img src="file:///C:\Users\lfl\AppData\Local\Temp\SGPicFaceTpBq\9524\0844A0EF.png" alt="img" style="zoom:50%;" />

#### String的数据结构（value的类型）

string的数据结构为简单动态字符串（Simple Dynamic String，缩写为SDS）。是可以修改的字符串，内部结构实现上类似于Java的ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配。

字符串实际分配的空间一般要高于实际字符串大小，当字符串长度小于1M时，扩容都是加倍现有的空间，如果超过1M，扩容时一次只会多扩容1M的空间，需要注意的是字符串的最大长度为512M。

### Redis列表（List）

#### 简介

单键多值

Redis列表是简单的字符串列表，按照插入顺序排序。可以添加一个元素到列表的头部或者尾部。

List的底层实际是一个**双向链表**，对双端的操作性能高，通过索引下标操作中间结点性能差

#### 常用命令

lpush / rpush <key> <value1> <value2> ... 从左边/右边插入一个或多个值

lpop/rpop <key> 从左边或者右边弹出一个值，**值在健在，值光键亡**

rpoplpush <key1> <key2> 从key1右边取值插入key2左边

lrange <key> <start> <stop> 按照索引下标获取元素（从左边开始）。支持负数索引。（0，-1）表示全部

lindex <key> <index> 按照索引下标获取元素（从左到右）

llen <key> 获得列表长度

linsert <key> before|after <value> <newvalue> 在value前面或后面插入newvalue

lrem <key> <n> <value> 从左边删除第n个value（从左到右），删除的是指定的value，如果没有，则返回0，如果要删除的个数多于value的个数，则返回删除的个数。

lset <key> <index> <value> 将列表key下标为index的值替换成value

#### List的数据结构

List的数据结构为快速链表（quickList）

首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是ziplist，就是压缩列表。它将所有的元素紧挨着一起存储，分配的是一块连续的内存。

当数据量比较多的时候，才会改成quickList。

因为普通的链表需要附加指针空间太大，会比较浪费空间，比如这个列表里存的只是int类型的数据，结构上还需要两个额外的指针prev和next。

Redis将链表和ziplist结合起来组成了quickList，也就是将多个ziplist使用双向指针穿起来使用，这样既满足了快速的插入操作和删除性能，又不会出现太大的空间冗余。

### Redis集合（Set）

#### 简介

Redis set 对外提供的功能和list类似，是一个列表的功能，特殊之处在于set是可以自动排重的，当需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合中的重要接口，这个是list不能提供的。

Redis的Set是string类型的无序集合，它地城其实是一个value为null的hash表，所以添加、删除、查找的复杂度都是O(1)。

#### 常用命令

sadd <key> <value> <value> ... 将一个或多个member元素加入到集合key中，已经存在的member元素将被忽略。

smembers <key> 取出该集合的所有元素

sismember <key> <value> 判断key集合中是否存在value，如果存在返回1，不存在返回0

scard <key> 返回该集合的元素个数

srem <key> <value1> <value2> ... 删除集合中的某个元素

spop <key> 随机从该集合中吐出一个值（会删除）

srandmember <key> <count> 随机从集合中选出n个值，不会从集合中删除。

- 如果 count 为正数，且小于集合基数，那么命令返回一个包含 count 个元素的数组，数组中的元素各不相同。如果 count 大于等于集合基数，那么返回整个集合。
- 如果 count 为负数，那么命令返回一个数组，数组中的元素可能会重复出现多次，而数组的长度为 count 的绝对值。

smove <source> <destination> value 把集合中一个值从一个集合移动到另一个集合

sinter <key1> <key2> 返回两个集合的交集元素

sunion <key1> <key2> 返回两个集合的并集元素 

sdiff <key1> <key2> 返回两个集合的差集元素（key1中不包含在key2中的）

#### Set的数据结构

Set的数据结构是dict字典，字典使用哈希表实现的。

Java中HashSet的内部实现使用的是HashMap，只不过所有的value都指向同一个对象，Redis的set结构也是一样，它内部也是用hash结构，所有的value都指向同一个内布值。

### 哈希（Hash）

Redis hash 是一个键值对集合

Redis Hash是一个string类型的filed和value的映射表，hash 特别适合用于存储对象。类似于Java中的Map<String,Object> , String对应的是Redis中的key，Object对应Redis中的Hash类型。

####  常用操作

hset <key> <field> <value> ..... 向key对应的hash中添加数据

hget <key> <field> 获取key对应的hash 中field对应的值

hmset <key1><field1><value><field><value> 向key对应的hash中批量添加数据

hexists <key><field> 判断key对应的hash中键field是否存在

hkeys <key> 获取key对应的hash中所有field

hvals <key> 获取key对应的hash所有的value

hincreby <key> <field> <increment>  

hsetnx <key><field> <value>

#### Hash的数据结构

Hash类型对应的数据结构是两种：ziplist（压缩链表），hashtable（哈希表）。当field-value长度较短且个数较少时，使用ziplist，否则只用hashtable。

### 有序集合（Zset、Sorted Set）

#### 常用操作

zadd <key> <num> <value> ... 添加一个值，这个值的相对排序为num

zrangebyscore

#### Zset的数据结构

#### 跳跃表

## Redis配置文件详解

![image-20211205193437258](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211205193437258.png)

## Redis的发布和订阅

## Redis6新数据类型

### Bitmaps

