# string类型

## 单数操作和多数操作的选择

## string类型扩展操作

利用 incr和decr这类型的原子性的操作，因为redis采用单线程处理所有业务，没有并发的情况，因此可以是作为mysql数据库获取id字段的方式，但是只是按照数值进行操作，内部还是一个string类型的

利用setex和psetex这类的操作可以控制数据的声明周期，通过数据是否失效控制业务行为，适合所有具有失效性限定控制的操作

## string类型的应用场景

主页高频访问信息显示控制，redis应用于各种结构型和非结构性高热数据的加速访问

![image-20220216104826470](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220216104826470.png)

## redis中key的设置约定

![image-20220216104946708](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220216104946708.png)

# hash类型

## hash存储结构优化

底层使用哈希表结构实现数据存储，会做优化

* 如果field数量较少，存储结构优化为类数组结构
* 如果field数量多，存储结构使用hashMap结构

hash类型也可以用来存储对象的结构

## hash类型应用场景

电商的购物车，用户id为key，商品id为field，商品数量为value，

同时可以使用两个field来保存商品的信息，field1为商品id:nums，value为商品数量。field2为商品id:info ，value为商品信息JSON格式。

## string存对象和hash存对象

string存储对象讲究的是整体性，一次性可以读取全部，

hash讲究的是分散，更新key对应的每个field的时候会很方便

# list类型

## list阻塞式获取数据

![image-20220216120504789](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220216120504789.png)

规定时间内获取并移除数据，如果不能获取到，返回nil 	

# set类型







































































































