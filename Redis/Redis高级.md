# 数据结构

## SDS简单动态字符串

Redis中没有使用C语言的字符串（使用\0作为结尾的字符），而是自己构建了一种名为简单动态字符串（simple dynamic string，SDS）的抽象结构，SDS是Redis的默认字符串表示。

在redis中，包含字符串的键值对在底层都是使用SDS实现的。除了字符串值之外，SDS还被用作缓冲区：AOF中的AOF缓冲区，客户端状态的输入缓冲区，都是SDS实现的。

### SDS的定义

Redis针对不同长度的字符串，定义了不同的sdshdr结构体，最基本的结构体内容如下：

* len：已使用字节长度，即字符串长度。Redis中限制字符串最大长度不能超过512M
* alloc：已申请的字节长度，即sds总长
* flag：低3位表示sdshdr类型，高5位表示字符串长度
* buf：字符串内容，

sds遵循C语言字符串结尾习惯，使用空字符结尾，并且不计入len，alloc的计算，这样的话，sds可以直接使用C语言中的一些函数。

### SDS和C字符串的区别

C语言使用长度为N+1的字符数组来表示长度为N的字符串，并且字符数组的最后一个元素总是空字符'\0'。 

#### 常数复杂度获取字符串长度

C字符串并不记录字符串的信息，如果想要获取字符串的长度，必须遍历整个字符串，然后计数。

而SDS中记录了字符串的长度len，可以将获取字符串长度的复杂度降到常数级别，从而使得获取字符串长度不会影响Redis的性能。

#### 杜绝缓冲区溢出

由于C语言字符串不记录字符串长度信息，如果另个字符串进行拼接的话，容易造成内存溢出。

但是SDS的内存分配策略杜绝了发生内存溢出的可能性：

* 当对sds进行修改时，会先检查sds的空间是否满足修改要求，如果不满足，自动将sds的空间扩容为原来的两倍，但是有一个最大值，然后再进行实际的修改操作。

#### 减少修改字符串带来的内存分配次数

因为C语言字符串长度和底层数组的长度之间存在着相等的关系，因此对于C语言的字符串的每次增长或缩短都对应着一次内存的重新分配，这样性能是不高的。

为了避免C语言的这种缺陷，redis通过未使用空间解除了字符串长度和底层数组长度之间的关联：在sds中，buf数组的长度不一定就是字符串长度加一，数组中可以包含没有使用的字节，而这些字节的数量就是alloc-len的值。

**空间预分配**

空间预分配用于优化sds字符串增长的操作：当sds的api对一个sds进行修改，并且需要对sds进行空间扩展的时候，程序不仅会为sds分配修改所需要的空间，还会为sds分配额外的未使用的空间。

* 如果修改后sds长度小于1MB，那么程序分配的值是len值的两倍
* 如果修改后sds长度大于1MB，那么程序会分配1MB的free空间

**惰性内存释放**

惰性内存释放用于优化sds字符串缩短操作：当sds的api需要缩短sds保存的字符串时，程序并不立即使用内存重分配来后手缩短后多出来的字节，而是使用alloc和len属性的差值将这些字节记录起来，并等待将来使用。

#### 二进制安全

C字符串中的字符必须符合某种编码，并且除了字符串的末尾外，不能有空字符，要不就会被当做结尾处理，这限制了C字符串只能保存文本类的信息。

redis为了确保sds可以适用于各种不同的保存场景，sds的api都是二进制安全的，所有sds的api都会以处理二进制的方式来处理sds存放在buf数组中的数据，程序不会对其中的数据做任何限制，过滤，或者其他操作，数据在写入的时候什么样，出来的时候就是什么样。

这也就是我们将buf属性称为字节数组的原因，redis不是用这个数组来保存字符，而是用来保存一系列二进制数据。

#### 兼容部分C字符串

因为sds最后一个字符遵循C字符的规范，是一个空字符，因此如果sds中保存的是纯文本的话，可以使用C中关于字符串的函数，这样就不用重写函数，例如：比较函数，追加字符串的函数

#### 总结

![image-20220218112642303](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220218112642303.png)

## 链表list

### 链表和链表结点的实现

每个链表结点使用一个listNode结构来实现：

```C
typedef struct listNode { 
    // 前置节点
    struct listNode * prev; 
    // 后置节点
    struct listNode * next; 
    // 节点的值
    void * value; 
}listNode;
```

保存链表信息的结构体：

```C
typedef struct list { 
    // 表头节点
    listNode * head;
    // 表尾节点
    listNode * tail; 
    // 链表所包含的节点数量 
    unsigned long len; 
    // 节点值复制函数 
    void *(*dup)(void *ptr); 
    // 节点值释放函数 
    void (*free)(void *ptr); 
    // 节点值对比函数 
    int (*match)(void *ptr,void *key); 
} list;
```

### ziplist

Redis内部使用该链表保存运行时的数据，如主服务下所有的从服务器信息。但是Redis并不使用该链表保存用户列表数据，因为它对内存管理不够友好：

* 链表中的每个结点都使用独立的一块内存，导致内存碎片过多
* 链表结点中前后结点指针占用过多的额外空间

因此，Redis使用ziplist来保存用户列表数据，ziplist是一种类似于数组的紧凑链表格式，他会申请一整块内存，在这个内存上放该链表所有的数据，这就是ziplist的设计思想

ziplist总体布局如下：

zlbytes+zltail+zllen+entry+entry+...+entry+zlend

* zlbytes记录整个ziplist占用的字节数，包括zlbytes占用的4字节
* zltail记录从ziplist起始位置到最后一个字节的偏移量，用于支持链表从尾部弹出或反向遍历
* zllen记录结点数量，两字节，如果超过最大值，需要使用遍历获取结点数量
* zlend特殊的标志结点，等于255，表示结尾
* entry就是ziplist中保存的结点

entry的布局如下：

prevlen+encoding+entry-data

* entry-data结点存储的数据
* prevlen：记录前驱结点长度，这个属性长度为1字节或5字节
  * 如果前驱长度下小于254，使用1个字节
  * 否则，使用5个字节，第一个字节固定254，剩下4字节存储前驱结点长度
* encoding，表示当前结点元素的编码格式，包含编码类型和节点长度

### quicklist

虽然ziplist可以解决list的内存问题，但是他也有缺点：

* 因为存储紧凑，所以新增元素时，需要扩展内存，并将之前的数据拷贝过来
* 如果内容过多，内存分配和元素拷贝就会消耗很大，因此它不适合存储大量数据

quicklist的设计思想很简单，讲一个长ziplist拆分为多个端ziplist，避免插入或删除元素时导致大量的内容拷贝。

多个ziplist使用双向链表串联起来，每个ziplist的大小为8K



## 字典hash

Redis数据库就是使用字典作为底层实现的；字典也是哈希键的底层实现之一。当一 个哈希键包含的键值对比较多，又或者键值对中的元素都是比较长的字 符串时，Redis就会使用字典作为哈希键的底层实现。

### 字典的实现

字典中的键值对定义如下：

```C
typedef struct dictEntry {
    // 键 
    void *key; 
    // 值 
    union{
        void *val; 
        uint64_tu64; 
        int64_ts64; 
    } v; 
    // 指向下个哈希表节点，形成链表 
    struct dictEntry *next; 
} dictEntry;
```

字典中Hash表的定义如下：

```C
typedef struct dictht { 
    // 哈希表数组 ,负责存储数据
    dictEntry **table; 
    // 哈希表大小 
    unsigned long size; 
    // 哈希表大小掩码，用于计算索引值 
    // 总是等于size-1 
    unsigned long sizemask; 
    // 该哈希表已有节点的数量 
    unsigned long used; 
} dictht;
```

Redis中的字典定义如下：

```C
typedef struct dict { 
    // 指定操作数据的函数指针 
    dictType *type; 
    // 私有数据
    void *privdata; 
    // 定义两个hash表用于字典扩容机制，通常情况下只会使用ht[0],扩容时会创建ht[1]，并在操作数据时逐步将ht[0]的数据移到ht[1]中
    dictht ht[2]; 
    // rehash 索引 
    // 当rehash 不在进行时，值为-1 
    long rehashidx; /* rehashing not in progress if rehashidx == -1 */ 
    // 当前运行的迭代器数量，迭代器用于遍历字典键值对
    unsigned long iterators;
} dict;
```

dictType定义了字典中用于操作数据的函数指针，这些函数负责实现数据复制，比较等操作。

```C
typedef struct dictType { 
    // 计算哈希值的函数 
    unsigned int (*hashFunction)(const void *key); 
    // 复制键的函数 
    void *(*keyDup)(void *privdata, const void *key); 
    // 复制值的函数 
    void *(*valDup)(void *privdata, const void *obj); 
    // 对比键的函数 
    int (*keyCompare)(void *privdata, const void *key1, const void *key2); 
    // 销毁键的函数 
    void (*keyDestructor)(void *privdata, void *key); 
    // 销毁值的函数 
    void (*valDestructor)(void *privdata, void *obj); } dictType;
```

通过dictType指定操作数据的函数指针，字典就可以存放不同类型的数据了，但是在一个字典中，键，值可以是不同的类型，但是键必须类型相同，值也必须类型相同。

### 哈希算法

使用SIPHash算法，该算法能有效的放置Hash表碰撞攻击，并提供不错的性能

然后通过&运算来计算index，因此redishash表的大小应该也是2的整数倍

### 解决哈希冲突

Redis的哈希表使用链地址法（separate chaining）来解决键冲突，使用头插的方式

### 扩容

Redis使用一种**渐进式扩容方式**，这样设计，是因为Redis是单线程的，如果在一个操作将ht[0]所有数据都迁移到ht[1]，那么可能会引起线程长期阻塞。所以Redis字典扩容是在每次操作数据时都执行一次扩容单步操作，扩容操作即将ht[0].table[rehashidx]的数据迁移到ht[1]。等到ht[0]的所有数据都迁移到ht[1]，便将ht[0]指向ht[1]，完成扩容。

redis扩容后的大小是大于现在存储的键值对的2倍的最小的2的整数幂次方

hash表在一下情况下回执行扩容操作：

* hash存储的键值对大于等于hash数组的长度，也就是负载因子（等于 已经保存结点数量/哈希表大小）大于等于1、
* 负载因子大于dict_force_resize_ratio

当负载因子小于0.1时，redis对哈希表进行缩容

### 渐进式扩容

为了避免rehash对服务器性能造成影响，服务器不是一次性将ht[0]里面的所有键值对全部rehash到ht[1]，而是分多次、渐进式地将ht[0]里面的键值对慢慢地rehash到ht[1]。

1. 为1分配空间，字典同时具有0，1两个表
2. 在字典中维护rehashindex，将它的值设为0，表示rehash开始
3. rehash期间，每次对字典执行添加，删除，查找，或者更新操作时，程序除了执行指定的操作之外，还会顺带将0表在rehashidx索引上的所有键值对rehash到ht1中，当rehash工作完成之后，程序将rehashrdx属性的值加一
4. 当ht0全部被移到ht1之后，程序将rehashidx设置为-1，表示rehash操作已经完成

**渐进式rehash期间执行的哈希表操作**

在rehash过程中，字典的增删改查会在两个哈希表中进行，查找的话先查找ht0，再查找ht1；

添加操作全部加到ht1中，

## 跳跃表

跳跃表是一种有序数据结构，它通过在每个结点中维持多个指向其他结点的指针，从而达到快速访问结点的目的。

跳跃表支持平均O(logN)，最坏O(N)复杂度的结点查找，还可以通过顺序性操作来批量处理结点

在大部分情况下，跳跃表的效率可以和平和树相媲美，并且因为跳跃表的实现比平衡树更为简单，主要体现在插入和删除结点之后的维护上，所以有不少程序都使用跳跃表来代替平衡树。

Redis使用跳跃表作为有序集合键（Zset）底层实现之一（实际是hash字典+跳跃表），如果一个有序集合包含的元素数量比较多，又或者有序集合元素的成员是比较长的字符串时，Redis就会使用跳跃表来作为有序集合的底层实现。

Redis只在两个地方使用到了跳跃表：实现有序集合，集群结点中用作内部数据结构。

### 跳跃表的实现

zskiplist结构用于保存跳跃表结点的相关信息：

```C
typedef struct zskiplist { 
    // 表头节点和表尾节点 
    struct zskiplistNode *header, *tail; 
    // 表中节点的数量 
    unsigned long length; 
    // 表中层数最大的节点的层数 ，最大为32层
    int level; 
} zskiplist;
```

zskiplistNode 用于表示跳跃表结点：

```C
typedef struct zskiplistNode { 
    // 结点值
    sds ele;
    // 分数，用于排序结点
    double score; 
    // 层级
    struct zskiplistLevel { 
        // 后驱结点 
        struct zskiplistNode *forward; 
        // 本层的后继结点跨过第一层多少结点，用于计算节点索引
        unsigned long span; 
    } level[]; 
    // 指向前驱结点，一个结点只有第一层有前驱结点，因此skiplist的第一层是一个双向链表
    struct zskiplistNode *backward; 
} zskiplistNode;
```

### 插入操作

## 整数集合

整数集合intset是集合键（set）的底层实现之一。当一个集合值包含整数元素，并且这个集合的元素数量不多时，Redis就会使用整数集合作为集合键的底层实现。

### 整数集合的实现

```C
typedef struct intset { 
    // 编码方式
    uint32_t encoding; 
    // 集合包含的元素数量 
    uint32_t length; 
    // 保存元素的数组 
    int8_t contents[]; 
} intset;
```

存储的各个项在contents中从小到大排序，并且不包含重复的内容

### 升级

每当我们要将一个新元素添加到整数集合里面时，并且新元素的类型比整数集合现在所有元素的类型都要长时，整数集合需要先进行升级，然后才能将新元素添加到整数集合里面。

升级步骤：

1. 根据新元素的类型，扩展整数集合底层数组的空间大小，并为新元素分配空间
2. 将底层数组的元素都转换成和新元素相同的类型，并将类型转换后的元素放置到正确位置上，而且在放置元素的过程中，需要继续维持底层数组的有序性不变。
3. 将新元素添加到底层数组里面。



# 持久化

## RDB

### RDB持久化方式—save

使用命令`save`可以使用RDB方式持久化数据。

### RDB持久化方式—bgsave













































































































































































