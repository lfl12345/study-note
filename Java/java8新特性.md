![image-20211011113810400](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211011113810400.png)

## 强大的Stream API

### 什么是Stream

Stream是数据渠道，用于操作数据源（集合、数组）所生成的元素序列。

“集合讲的是数据，流讲的是计算”。

注意：

1. Stream自己不会存储元素
2. Stream不会改变原对象，相反的他们会返回一个持有结果的新Stream
3. Stream的操作时延迟执行的，这意味着他们会等到需要结果的时候在运行。

### Stream操作的三个步骤

1. 创建Stream：从一个数据源（集合、数组）获取一个数据流
2. 中间操作：对数据源的数据进行处理
3. 终止操作：一个终止操作，执行中间的操作链，并产生结果。

### Stream的创建

#### 通过Collection创建

* 通过`default Stream<E> stream()`方法返回一个顺序流
* 通过`default Stream<E> parallelStream()`方法返回一个并行流

#### 通过Arrays中的静态方法创建

* `public static <T> Stream<T> stream(T[] array)`返回一个流，只能处理引用类型。
* 重载形式，可以处理基本类型数组。
  * `public static IntStream stream(int[] array)`
  * `public static LongStream stream(long[] array)`
  * `public static DoubleStream stream(double[] array)`

#### 通过Stream类中的静态方法of

* `public static Stream of(T... values) `：返回一个流

#### 由函数创建流：创建无限流

* 迭代：`public static Stream iterate(final T seed, final  UnaryOperator f)`
* 生成：`public static Stream generate(Supplier s) : `

### Stream的中间操作

多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止操作，否则中间操作不会执行任何处理，而在终止操作时一次性全部处理，这种方式称为“惰性求值”。

#### 筛选与切片

`Stream<T> filter(Predicate<? super T> predicate);`方法

接收lambda，从流中排除某些元素。

`Stream<T> distinct();`方法

筛选，通过流所生成元素的 hashCode() 和 equals() 去 除重复元素

`Stream<T> limit(long maxSize);`方法

截断流，使其元素不超过给定数量。

`Stream<T> skip(long n);`方法

跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素 不足 n 个，则返回一个空流。与 limit(n) 互补

#### 映射

| 方法 | 描述 |
| ---- | ---- |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |

