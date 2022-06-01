# Java必须掌握的基础类

## Object类

### 为什么java.lang包下的类不需要手动导入

首先，介绍一下java中的两种导包规则：

1. 单类型导入
2. 按需类型导入（也就是如果把整个包导入而不是一个类，那么这个包里面的所有类是不会一下子全导入的，在编译的时候，需要什么类再导入什么类）

Java编辑器会从启动目录（bootstrap）、扩展目录（extentsion）和用户类路径去定位需要导入的类，而这些目录仅给出了类的顶层目录，编译器的类文件定位法大致可以理解为：顶层路径名\包名\文件名.class = 绝对路径

编译器查找类的步骤：

1. 首先搜索无名包
2. 然后搜索当前包，
3. 最后搜索导入的包（此时会自动导入java.lang包）

**因为用的多，所以提前加载这个包文件，节省了资源**

import的作用：

import的机制和package有很大的关系，如果我们不使用package，那么java类名的冲突就会增加，因此增加了package使得java类的名字出现冲突的几率大大降低，

但是我们在使用类的时候如果把包名也加上，那么代码会变得十分冗余，因此为了降低代码的冗余程度，引入了import关键字，import就是在java文件开头的地方，先说明会用到那些类别，接着我们就能在代码中只用类名指定的某个类，也就是只称呼名字，不称呼它的姓名

### 1.1.2 类构造器

Object类中是没有构造器的，系统会自动添加一个无参构造器。

### 1.1.3 equals方法

```java
//Object类中的equals方法
public boolean equals(Object obj) {
        return (this == obj);
    }
```

> ==运算符用于比较基本类型的值是否相同，或者比较另个对象的引用是否相等
>
> 而equals方法用于比较两个对象是否相等（逻辑上或者事实上）。

在java规范中，对equals方法的使用必须遵循以下原则：

1. 自反性。
2. 对称性
3. 传递性
4. 一致性

对于任何非空引用的值x，x.equals(null)都应该返回false。

使用instanceof关键字达不到对称性的要求，一个父类的对象不是一个子类的实例（但是这个有时候是错的）。因此我们需要使用getClass()方法，它返回一个对象运行时的类

### 1.1.4 getClass方法

```java
public final native Class<?> getClass();
```

该方法返回的是一个对象的**运行时的类**，通过这个类对象可以获取**该运行时类**的相关方法和属性。也就是反射。

可以通过四种方式获取类对象：

1. 通过类名.class获取（获取该类**编译时**的类对象）
2. 通过当前**运行时类**的对象的getClass()方法获取
3. 通过Class类的静态方法.forName(String path)获取
4. 通过类加载器获取

### 1.1.5 hashCode方法

jvm源码提供了6种计算hash值的方案，有自增序列，随机数，关联内存地址等多种方式，其中官方默认的是最后一种，即随机数生成。因此可以看出hashCode也许和内存地址有关系，但是不是直接代表内存地址的，具体需要开虚拟机版本和设置。

#### 1. hashCode是什么？

```java
//hashCode 在object类中的定义代码
//object类中的hashCode方法返回值为对象的地址
public native int hashCode();
```

​		hashCode方法和equals方法其实作用一样，都是用来判断两个对象是否相等一致。在Object类中这两个方法使用地址来判断对象是否相等，也就是和==运算符效果一样。

​		但是在我们自己写的类中，两个对象相等不一定要地址相同，只要逻辑上相等就可以，所以此时我们需要重写Object类的hashCode和equals方法。

#### 2.hashCode 要求

1. 在运行期间，只要对象（字段的）的变化不会影响equals方法的决策结果，那么，在此期间无论调用多少次hashCode，都必须返回同一个散列码。
2. 通过equals放回为TRUE的两个对象的hashCode一定相同。
3. 通过equals方法返回为false的两个对象的散列码不需要不同，也就是不同的对象hashCode方法的返回值可以一样。

在hash容器中，比较两个对象是否相等：

1. 先使用hashCode方法。
2. 如果hashCode相等，再使用equals方法。

### 1.1.6 toString 方法

```java
public String toString() {
     return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

Integer.toHexString(hashCode())是以十六进制无符号整数形式返回此哈希码的字符串表示形式。

### 1.1.9 registerNatives 方法

不光是Object类，甚至System类、Class类、ClassLoader类、Unsafe类等等，都能在类代码中找到如下代码：

```java
private static native void registerNatives();
    static {
        registerNatives();
    }
```

这个方法的作用是在类加载的时候注册本地方法。

### native关键字使用规则

1. native标识符除了不能和abstract联用外，可以与其他标识符联用
2. native method方法可以返回任何java类型，包括非基本类型，也可以进行异常控制
3. 如果含有该native method方法的类被继承，子类会继承这个native method方法，也可以使用java语言重写这个方法
4. 如果一个native method方法被final标识，它被继承后不能被重写。

## 1.2 Java的深拷贝和浅拷贝

### 1.2.1 创建对象的五种方式

1. 通过new关键字
2. 通过Class类的newInstance方法
3. 通过Constructor类的newInstance方法
4. 利用Clone方法（需要实现Cloneable接口并且重写clone方法）
5. 反序列化

### 1.2.2 Clone方法

```java
/**
创建并返回此对象的副本。 “复制”的确切含义可能取决于对象的类别。 一般意图是，对于任何对象x ，表达式：
       x.clone() != x
将是真的，并且表达式：
       x.clone().getClass() == x.getClass()
会是true ，但这些不是绝对的要求。 虽然通常的情况是：
       x.clone().equals(x)
会是true ，这不是绝对的要求
*/
protected native Object clone() throws CloneNotSupportedException;
```

java 中的浅拷贝和深拷贝就是通过clone方法实现的

### 1.2.4 浅拷贝

创建一个对象，然后将当前对象的**非静态字段复制到该新对象**，如果字段是值类型的，那么对该字段执行复制；如果该字段是引用类型的，则复制引用但不复制引用的对象。因此，原对象及其副本引用同一个对象。

### 1.2.5 深拷贝

创建一个对象，任何东西都复制一份。

### 1.2.6 如何实现深拷贝

1. 让每个引用类型属性内部都重写clone（）方法。

   对象需要实现Cloneable接口

2. 利用序列化。

   对象需要实现Serializable接口，然后再使用对象输出流写出对象，最后使用对象读入流读入对象，从而实现对象的深拷贝。

   利用这种实现方式，如果对象中添加属性，那么这个属性只需要实现Serializable接口即可，不用实现clone方法

   ```java
   public class CloneUtil {
    
       public static <T extends Serializable> T clone(T obj) {
           T cloneObj = null;
           try {
               // 写入字节流
               ByteArrayOutputStream out = new ByteArrayOutputStream();
               ObjectOutputStream obs = new ObjectOutputStream(out);
               obs.writeObject(obj);
               obs.close();
    
               // 分配内存，写入原始对象，生成新对象
               ByteArrayInputStream ios = new ByteArrayInputStream(out.toByteArray());
               ObjectInputStream ois = new ObjectInputStream(ios);
               // 返回生成的新对象
               cloneObj = (T) ois.readObject();
               ois.close();
           } catch (Exception e) {
               e.printStackTrace();
           }
           return cloneObj;
       }
   }
   ```

## 1.3 最常用的引用类Integer类

## 1.4 String类

String字符串实际上是一个char数组

### 1.4.4 hashCode（）为什么选择31

1. 31 是一个不大不小的质数，是作为hashCode乘子的优质质数之一。
2. 更少的乘积结果冲突。
3. 31 可以被jvm优化，31 * i = （i << 5）- i。因此乘法运算可以利用移位和加减代替，提高效率

```java
31是质子数中一个“不大不小”的存在，如果你使用的是一个如2的较小质数，那么得出的乘积会在一个很小的范围，很容易造成哈希值的冲突。而如果选择一个100以上的质数，得出的哈希值会超出int的最大范围，这两种都不合适。而如果对超过 50,000 个英文单词（由两个不同版本的 Unix 字典合并而成）进行 hash code 运算，并使用常数 31, 33, 37, 39 和 41 作为乘子，每个常数算出的哈希值冲突数都小于7个（国外大神做的测试），那么这几个数就被作为生成hashCode值得备选乘数了。
```

### 1.4.13 intern方法

```java
public native String intern();
```

当调用intern方法时，如果常量池中已经包含了一个与该String确定的字符串相同equals的字符串，则返回该字符串。否则，将此String对象添加到池中，并返回此对象的引用。

### 1.4.15 String真的不可变吗

String源码中value字符数组被final修饰，但是被final修饰数组类型的只能保证引用不变，但是value所指向的堆中的数组才是真正的数据，只要能够操作堆中的数组，依旧可以改变数据。而且value是基本类型构成的，那么一定是可变的，即使被声明为private，也可以通过反射来改变。

### 1.4.16 String为什么被设计成不可变的

1. 如果可变会引发安全问题
2. 保证线程安全

# Java 集合类

List——ArrayList√，Vector√，LinkedList√

Set——HashSet√，LinkedHashSet，TreeSet

Queue，Deque——ArrayDeque，LinkedList√，PriorityQueue

Map——HashMap√，HashTable，TreeMap，LinkedHashMap，ConcurrentHashMap

![image-20210926100734655](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20210926100734655.png)

## Arrays类

此类包含用于操作数组（例如排序和搜索）的各种方法，还包含一个语序将数组视为列表的静态工厂

其中的方法主要包括：排序sort，并行累加parelladd，搜索search、两个数组相等判断equals、数组填充fill，数组复制copyof，asList，hashCode，deepHashCode，toString，deepToString，Streaming

### 2.1.1 asList 方法

```java
public static <T> List<T> asList(T... a) {
        return new ArrayList<>(a);
}
```

该方法的作用是返回由指定数组支持的固定大小列表。

> 注意：方法的返回值不是常用的集合类Java.util.ArrayList。这里的ArrayList是Arrays的一个内部类，此内部类的属性和方法如下：

![IMG_20210926_200216_edit_6738978469283](D:\刘丰利\笔记\图片\IMG_20210926_200216_edit_6738978469283.jpg)

* 此方法返回的数组是一个定长列表，只能对齐进行查看或者修改操作。
* 使用类型的数组和基本类型的数组的区别

```java
public static void main(String[] args) {
        int[] a = new int[]{1,2,3};
        Integer[] c = new Integer[]{1,2,3};
        List<Integer> b = Arrays.asList(c);
        List<int[]> d =  Arrays.asList(a);
        
        System.out.println(b.size());//返回3
        System.out.println(d.size());//输出1
    }
原因：asList方法接收的是一个泛型参数，基本数据类型是不能作为泛型的参数的，但是数组是引用类型，所以数组是可以泛型化的，于是使用基本类型数组作为参数的话，int[]最为整个参数类型，而不是int，因此造成大小为1.
```

* 返回的ArrayList里面的元素都是引用，所以通过操作返回的列表可以直接操作元数据，这也就间接证明了此方法接受的参数不可以是基本数据类型。
* 如果想要获取一个正儿八经的ArrayList，可以使用**此方法**加上**正儿八经的ArrayList的构造方法**。

### 2.1.2 sort方法

此方法可以对七种基本数据类型和Object类型进行排序。

### 2.1.3 binarySearch方法

此方法用于对有序数组进行查找（可以先使用Arrays.sort方法排序），找到之后返回下标，没有-1.

### 2.1.4 copyOf方法

底层使用System.arraycopy方法，这是一个native方法。

### 2.1.5 equals和deepEquals方法

1. equals

用来比较两个数组中对应位置的每个元素是否相等。

2. deepEquals

也是用来比较两个数组对应位置的元素是否相等，但是它可以比较多维数组，而且是任意层次的嵌套数组。

只能用来比较引用类型，基本类型是不能转化成Object[]类型的。

```java 
public static boolean deepEquals(Object[] a1, Object[] a2)
```

### 2.1.6 fill方法

该方法用于给数组赋值（可以大范围赋值），并能指定某个赋值范围。

### 2.1.7 toString和deepToString方法

和equals与deepEquals方法差不多，toString用来打印一维数组，deepToString用来打印多位数组。

## List接口

### ArrayList类

 #### 2.2.1 ArrayList的定义

用数组实现的集合，支持随机访问，元素有序且可以重复访问。

![IMG_20210927_094200_edit_23675520066178](D:\刘丰利\笔记\图片\IMG_20210927_094200_edit_23675520066178.jpg)

1. 实现了RandomAccess接口

```java
public interface RandomAccess {
}
```

#### RandomAccess接口的作用

这是一个标记接口（里面什么都没有），以表明实现这个接口的类支持快速的随机访问。**该接口的主要目的是允许通用算法改变其行为（比如在工具类Collections中，应用二分查找方法可以判断是否实现了RandomAccess接口，如果实现了这个接口，那么我就可以使用随机访问的方式去查找）。**

```java
int binarySearch(List<? extends Comparable<? super T>> list, T key) {
        if (list instanceof RandomAccess || list.size()<BINARYSEARCH_THRESHOLD)
            return Collections.indexedBinarySearch(list, key);
        else
            return Collections.iteratorBinarySearch(list, key);
    }
```

2. 实现Cloneable接口

```java
public interface Cloneable {
}
```

这个类也是一个标记类，如果要克隆对象必须实现这个接口。

实现这个接口之后，重写Object的clone方法则可以实现深拷贝或者浅拷贝。

3. 实现Serializable接口

```java
public interface Serializable {
}
```

标记接口，表示能被序列化。

4. 实现List接口

这个接口是List类集合的上层接口，定义了实现该接口的类都必须实现的一组方法。

#### 2.2.2 字段属性

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    private static final long serialVersionUID = 8683452581122892189L;

    /**
     * Default initial capacity.
     */
    private static final int DEFAULT_CAPACITY = 10;

    /**
     * Shared empty array instance used for empty instances.
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};

    /**
     * Shared empty array instance used for default sized empty instances. We
     * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
     * first element is added.
     */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    /**
     * The array buffer into which the elements of the ArrayList are stored.
     * The capacity of the ArrayList is the length of this array buffer. Any
     * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
     * will be expanded to DEFAULT_CAPACITY when the first element is added.
     */
    transient Object[] elementData; // non-private to simplify nested class access

    /**
     * The size of the ArrayList (the number of elements it contains).
     *
     * @serial
     */
    private int size;
}
```

其中Object[]类型的elementData属性被**transient**[Java transient 关键字 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/java-transient-keywords.html)关键字修饰。

> transient(转瞬即逝的)：java 的transient关键字为我们提供了便利，你只需要实现Serilizable接口，将不需要序列化的属性前添加关键字transient，序列化对象的时候，这个属性就不会序列化到指定的目的地中。
>
> 虽然elementData属性被transient关键字修饰，但是在对ArrayList对象进行序列化和反序列化的时候利用writeObject方法和readObject方法对对象的elementData属性中的值进行了写入和读出
>
> 这样做的目的是避免elementData中大量空闲的位置占用空间，序列化和反序列化的效率

有两个很相似的字段：EMPTY_ELEMENTDATA和DEFAULTCAPACITY_EMPTY_ELEMENTDATA，虽然说这两个字段都是一个空的Object[]对象，但是他两的用法却不一样。

* 如果使用默认的构造函数，那么elementDate属性将被赋值为DEFAULTCAPACITY_EMPTY_ELEMENTDATA，此时elementDate还没有被初始化；
* 如果使用带初始化容量参数的构造函数，并且初始化容量为0，那么elementData属性将被赋值为EMPTY_ELEMENTDATA, 如果初始化容量不为0，那么直接将elementdata初始化。
* 在以后的第一次add操作中，如果elementData等于DEFAULTCAPACITY_EMPTY_ELEMENTDATA，那么elementData的容量直接初始化为默认的10；但是如果不是，那么就按照扩容规则扩容。

#### 2.2.3 构造函数

ArrayList中有三种构造函数：

1. 默认无参构造函数：此时初始化容量为0。
2. 带有初始化容量的构造函数
3. 带有初始化集合参数的构造函数

```java
public ArrayList(Collection<? extends E> c)
```

#### 2.2.4 添加元素

添加元素需要使用数组的扩容。

扩容的核心是调用Arrays.copyOf方法来创建一个更大的数组，然后把原数组复制过去。

1. 首先调用ensureCapacityInternal来确保数组用容量添加元素
   1. 如果使用默认构造方法创建的ArrayList，ensureCapacityInternal方法中的minCapacity第一次会变为10
   2. 如果使用带有初始容量的构造方法构造ArrayList，ensureCapacityInternal方法中的minCapacity第一次会是1
2. 如果minCapacity大于了elementData的长度，进入grow方法进行扩容
3. grow方法中比较关键的就是每次容量扩大1.5倍，以及ArrayList有容量的最大值Integer.MAX_VALUE - 8
4. grow方法确保了newCapacity一定大于等于minCapacity，如果elementdata的旧长度经过乘1.5之后，超过了Integer的最大值，那么elementdata的容量仅仅只会增加1，
5. 如果elementdata的长度增加到Integer.Max_value-7，那么它的长度会直接增加到Integer.Max

如果容量到达了Integer.Max，那么会在hugeCapacity方法中抛出异常，因为minCapacity小于0了，此时为什么会进入hugeCapacity呢，因为newCapacity肯定是一个负数，然后减去Integer.Max-8，将会变成一个正数，因此会进入hugeCapacity这个函数，因此会在这个函数抛出异常

```Java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}

private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }

    ensureExplicitCapacity(minCapacity);
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
    MAX_ARRAY_SIZE;
}
```

#### 2.2.5 删除元素

**根据索引删除元素**

* 首先确定index范围，
* 然后使用System的arraycopy把index后面的元素复制到index开始的位置，
* 然后使最后一个位置的引用为null，
* 最后返回删除index位置的元素值。

```java
public E remove(int index) {
    rangeCheck(index);

    modCount++;
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
}

E elementData(int index) {
    return (E) elementData[index];
}
```

直接删除指定元素

分为两种情况，指定元素为null以及不为null。为什么要分这两种情况呢？因为删除元素我们应该使用equals方法，而不是==，但是如果元素为null，我们使用equals方法的时候就会报异常，所以如果我们传入的对象不为null，我们就是用传入的对象的equals方法来比较元素是否相等，如果为null，则直接使用==运算符。

根据指定元素删除的时候，只会删除第一次出现的相等的元素

```JAVA
public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work
}
```

#### 2.2.6 修改元素

通过set方法将指定索引index处的元素替换为element，并返回原数组元素。（自己本身就具有的set方法）

在Iter中的set方法会检查线程安全。

#### 2.2.7 查找元素

1. 根据索引查找
2. 根据元素查找——indexOf和lastIndexOf

#### 2.2.8 遍历集合

1. 普通for循环遍历
2. 迭代器iterator

在使用迭代器对集合进行遍历的时候，如果对集合进行删除和添加的操作，那么会报异常。但是设置操作不会报异常。如果使用Iterator的remove方法则不会报异常。

3. 迭代器ListIterator

ArrayList使用内部类ListItr继承内部类Itr实现了ListIterator接口，相比Iterator迭代器，ListIterator多了能向前迭代以及能够新增元素的功能。

#### 2.2.9 subList方法

```java
public List<E> subList(int fromIndex, int toIndex) {
        subListRangeCheck(fromIndex, toIndex, size);
        return new SubList(this, 0, fromIndex, toIndex);
    }
```

SubList类是ArrayList中的一个内部类。

此方法返回的是原来ArrayList的一个视图，也就是如果对函数返回的subList进行修改或者新增操作，那么原始集合也会发生同样的变化。

如果想要独立出来一个集合，可以使用ArrayList的第三种构造方法。

#### 集合转换为数组toArray方法

```java
public <T> T[] toArray(T[] a) {
    if (a.length < size)
        // Make a new array of a's runtime type, but my contents:
        return (T[]) Arrays.copyOf(elementData, size, a.getClass());
    System.arraycopy(elementData, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}
```

使用ArrayList的toArray方法可以很方便的将集合转换为相应类型的数组。

#### ArrayList类中的elementData字段为什么使用transient关键字修饰？



### Vector类

Vector类中维护了一个字段—capacityIncrement，这点是和ArrayList有很大的不同的，具体体现在一下：

* Vector在调用默认构造函数的时候，还是会给elementDate进行初始化，不会懒惰初始化，默认初始容量为10
* 当Vector在扩容的时候会先判断capacityIncrement字段是否大于0，如果大于零，那么新长度就会是旧长度加上capacityIncrement，如果小于等于0，新长度为旧长度的两倍，而不是ArrayList中的1.5倍

#### 字段属性

```Java
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    protected Object[] elementData;
    protected int elementCount;

    /**
在 Vector中，如果指定了这个 capacityIncrement，就会在原来容量的基础上增加 capacityIncrement；如果没有指定，则增加一倍容量。*/
    protected int capacityIncrement;
}
```

#### 添加元素

添加元素的方法是一个同步方法，因为vector是一个线程安全的List集合

vector添加元素的主要逻辑和ArrayList一样，唯一不同的一定是因为vector有一个capacityIncrement属性，当设置了这个属性之后，每次扩容的量不再是原来容量的1.5倍，而是原容量加上capacityIncrement；如果没有设置这个属性，那么扩容的容量是原容量的2倍。

```java
public synchronized boolean add(E e) {
    modCount++;
    ensureCapacityHelper(elementCount + 1);
    elementData[elementCount++] = e;
    return true;
}

private void ensureCapacityHelper(int minCapacity) {
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                     capacityIncrement : oldCapacity);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
    MAX_ARRAY_SIZE;
}
```

### Stack类

Stack继承自Vector类，内部直接调用Vector类的添加，删除，获取元素的方法，同时因为Vector类使用synchronized进行加锁，因此Stack类在进行push和pop，peek的时候会对线程进行加锁，因此感觉最好不要使用Stack

### LinkedList类

之前的ArrayList使用数组构成，但是LinkedList这是一个使用链表（Linked list）构成的类。

LinkedList总共有四个内部类分别是ListItr、Node、DescendingIterator、LLSpliterator。

#### 2.3.1 LinkedList 的定义

LinkedList是一个用链表实现的集合，元素有序且可以重复。LinkedList也实现了Cloneable接口和Serializable接口，分别用来支持克隆以及序列化。也实现了List接口。

但是LinkedList还实现了Deque接口，这是一个双向队列接口。

#### 2.3.2 字段属性

总共就三个属性，size、first、last，

* 其中size代表LinkedList大小，
* first指向LinkedList第一个结点的指针，
* last指向LinkedList最后一个结点的指针。

他们的类型是LinkedList内部类Node。Node 内部类中只有三个属性：

* 元素值 item
* 前驱结点prev
* 后继结点next

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
    transient int size = 0;
    transient Node<E> first;
    transient Node<E> last;
}

private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

#### 2.3.3 构造函数

有两个构造函数，一个是默认的构造函数，另一个是带有Collection类型参数的构造函数，用来直接初始化LinkedList，这个构造函数内部使用addAll方法把Collection集合中的元素全部初始化到LinkedList中。

#### 2.3.8 遍历集合

1. 普通for循环

如果使用普通for循环再加上get方法获取LinkedList中的元素，这样的效率很不好。因为LinkedList是一个双向链表，如果要get，那么就得遍历一遍链表。虽然说LinkedList中的get方法优化了，当查找前半段的时候从前面开始遍历，查找后半段的时候从后面开始遍历，但是需要的时间还是很多。

2. 迭代器

总共有两个迭代器，一个是ListItr另一个是DescendingIterator。ListItr的作用和ArrayList中的作用 差不多，都可以向前、向后遍历集合，设置，添加，删除元素。DescendingIterator内部使用的还是ListItr，只不过是从后往前遍历，应为ListItr内部提供了带有起始index的构造函数，所以DescendingIterator可以构造一个从链表最后起始的迭代器，从而实现反向迭代。

迭代器每次方位一个元素后，都会用游标记录当前访问元素的位置`private Node<E> next;`，遍历一个元素记录一个位置。

#### 重要的函数

在头部添加元素：

* 首先构造一个新结点，新节点的prev设置为null，next设置为原来的头结点
* 将头结点指针指向新结点
* 如果原来的头结点为null，将尾结点指向新的结点
* 如果原来的头结点不为null，将原来的头结点的前驱结点指向新的头结点

```java
private void linkFirst(E e) {
    final Node<E> f = first;
    final Node<E> newNode = new Node<>(null, e, f);
    first = newNode;
    if (f == null)
        last = newNode;
    else
        f.prev = newNode;
    size++;
    modCount++;
}
```

在尾部添加结点：

* 首先构造一个新节点，新节点的next设置为null，prev设置为原来的尾结点
* 将尾结点指向新的结点
* 如果原来的尾结点为null，将头结点指向尾结点
* 如果原来的尾结点不为null，将原来的尾结点的next指向新的尾结点

```java
void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    size++;
    modCount++;
}
```

在一个结点的前插入一个结点：

这个方法和在头部添加一个元素的实现方法一样，只是将原来操作的头部结点换成了当前要被插入的结点的前驱结点

```java
void linkBefore(E e, Node<E> succ) {
    // assert succ != null;
    final Node<E> pred = succ.prev;
    final Node<E> newNode = new Node<>(pred, e, succ);
    succ.prev = newNode;
    if (pred == null)
        first = newNode;
    else
        pred.next = newNode;
    size++;
    modCount++;
}
```

删除头结点

* 首先获取头结点的值
* 然后获取头结点的后继结点
* 将头结点的值和后继结点赋值为null，帮助垃圾回收
* 将头结点指向原来头结点的后继 结点
* 如果头结点的后继结点为null，那么将尾结点也赋值为null
* 如果头结点的后继结点不为null，将后结点的前驱结点赋值为null

```java
private E unlinkFirst(Node<E> f) {
    // assert f == first && f != null;
    final E element = f.item;
    final Node<E> next = f.next;
    f.item = null;
    f.next = null; // help GC
    first = next;
    if (next == null)
        last = null;
    else
        next.prev = null;
    size--;
    modCount++;
    return element;
}
```



### CopyOnWriteArrayList

利用“写入时复制”的理念，也就是说在并发情况下，如果有线程想要去进行修改操作时，不会再原有的数组上进行修改，而是会创建当前容器的一个副本，在副本上进行修改，操作完毕后，再将数据的引用指向新的数组。

然而读操作还是在原来的副本上进行的，这样做的好处是可以同时进行多个读操作。读操作使用原来的数据，写操作使用新的数组，这样读写之间就不会有冲突，读操作和写操作可以并发的执行。

但是多个线程的写操作仍然需要同步锁进行线程同步。

#### 优点

* 读操作性能高，因为不需要任何同步措施，比较适合读多写少的并发场景。

#### 缺点

* 内存占用问题，每次进行修改操作，都要将源容器拷贝一份，数据量大时，对内存压力较大，可能会引起频繁GC
* 无法保证实时性，Vector对读写操作均加锁同步，可以保证读和写的强一致性，而CopyOnWriteArrayList由于其实现策略的原因，写和读分贝作用在新老不同容器上，在写操作执行过程中，读不回阻塞但是读取到的却是老容器的数据。

值得注意的是，CopyOnWriteArrayList中的elementData——array是一个使用volatile修饰的变量，只有使用volatile修饰，才可以实现CopyOnWrite这种无锁的并发。因为写线程在写完array副本之后，将array的副本赋值给之前的array引用，此时需要将这个引用对任意线程可见；同时在读线程进行读取的时候，需要里面更新本地的array应用，所以，需要使用volatile修饰变量，实现这种内存的可见性。



## Map接口

## HashTable

不允许有Null的key，因为hashTable内部直接使用key的hashCode函数获取key的HashCode，但是HashMap可以有hashCode的key，因为HashMap中计算key的HashCode不是使用直接使用key的方法，而是先判断key是否为null，如果为null，直接返回0，不为null的时候才会调用hashCode函数，	



## HashMap类

### 2.4.1 Hash表[散列表（哈希表）](http://data.biancheng.net/view/107.html)

Hash表也称为**散列表**，它是通过把关键码映射到表中的一个位置来访问记录，以此来加快查找的速度。这个**映射函数**称为**散列表**，只需要o(1)的时间级去访问元素。

1. 为什么需要散列函数

散列函数可以帮助人们迅速的确定key和value的映射关系。

2. 多个key通过散列函数得到相同的值？

* 开放地址法：当遇到冲突了，在通过另一种函数计算一遍，得到相应的映射关系。
  * 线性探测
  * 二次探测， 二次探测是过程是x+1,x+4,x+9,以此类推。二次探测的步数是原始位置相隔的步数的平方。
  * 再哈希法

* 链地址法：对key通过hash之后落在同一个地址上的值，做一个链表。遇到冲突就添加链表的结点。

> 对于开放地址发，可能还会遇到冲突，所以需要良好的散列函数，分布的越均匀越好。
>
> 对于链地址发，虽然不会造成二次冲突，但是如果一次冲突很多，那么会造成子数组或者子链表很长，查找的效率就会变低。

### 2.4.2 什么是HashMap

HashMap是一个利用Hash表原理来存储元素的集合。遇到冲突时，HashMap是采用链地址发来解决，在jdk7中，HashMap是有数组加链表构成的。但是在jdk8中，HashMap是由数组+链表+红黑树构成的，新增了红黑树作为底层数据结构，结构跟复杂，效率更高。

### 2.4.3 HashMap的定义

HashMap是一个散列表，它存储的内容是键值对（key-value）映射，而且key和value都可以为null。

该类常规操作实现了Cloneable和Serializable接口，除此之外，该类还实现了Map接口。Map接口定义了一组键值对映射通用的操作。Map中的key不要求有序，不允许重复，value不要求有序，但可以重复。

Map中的方法有很多，HashMap不想全部都实现，所以jdk还提供了一个抽象类AbstractMap（这个类中只有一个抽象方法```public abstract Set<Entry<K,V>> entrySet();```），这个类提供了Map接口的骨架实现，以最大限度的减少实现此接口需要的工作。所以HashMap继承了抽象类AbstractMap，这样就可以不用全部实现Map接口中的方法，想实现那个实现那个。

HashMap即继承了AbstractMap类又实现了Map接口，这多此一举，是一个失误。

### 2.4.4 字段属性

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
```

默认HashMap集合初始化容量为16（**必须是2的倍数**）。*为什么是2的倍数呢*？因为后面使用table的容量取余来判断key-value的存储位置的时候做了一个优化，把取余操作换成与操作，直接与上容量-1，这样的结果和取余一样，而却还快，所以要求是2的倍数。

*可是为什么使用位运算来定义16，为什么不直接写呢？*

这里主要是位运算的性能好，位运算直接操作内存，不需要进行进制转换，计算机可是以二进制的形式做数据存储啊。

```java
static final int MAXIMUM_CAPACITY = 1 << 30;
```

集合的最大容量，如果通过带参数构造器指定的最大容量超过这个值，那么默认还是使用这个值。

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

当构造函数中未指定时默认使用的填充因子。

hashMap这种数据结构会产生两种问题：1. 如果空间利用率高，那么经过的哈希算法计算存储位置的时候，会发现很多存储位置已经有数据了（哈希冲突）。2. 如果为了避免发生哈希冲突，增大数组容量，就会导致空间利用率不高。所以此时我们就需要填充因子了。

填充因子表示hash表中元素填满的程度。

> 加载因子 = 填入表中的元素个数 / 散列表的长度

```java
static final int TREEIFY_THRESHOLD = 8;
static final int UNTREEIFY_THRESHOLD = 6;
static final int MIN_TREEIFY_CAPACITY = 64;
```

当集合中的容量大于这个值时，表中的桶才能进行树形化，否则桶中元素太多时会扩容。

> 注意：后面这三个属性是jdk8新增的，主要用来进行红黑树和链表的相互转化。

```java
transient Node<K,V>[] table;
```

这就是HashMap结构中数组+链表+红黑树中的数组。它的默认长度是DEFAULT_INITIAL_CAPACITY16，它的长度始终是2的整数幂。

```java
final float loadFactor;
```

装载因子，用来衡量HashMap满的程度。这个值可以大于一。

```java
int threshold;
```

是当前已经占用数组长度的最大值。计算公式：capacity*loadFactor。超过这个数目就需要扩容（resize），扩容后的HashMap容量是之前的两倍。

**注意：**如果table的数组还没有被分配，那么这个threshold的值是初始容量的值，自定义的初始容量，或者默认的16初始容量。

### 2.4.5 构造函数

1. 默认无参构造函数

```java
//构造一个具有默认初始容量 (16) 和默认负载因子 (0.75) 的空HashMap 。
public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
    }
```

2. 指定初始容量的构造函数

```java
//有参构造函数有两个，一个是只指定初始化容量，另一个初始化容量和负载因子都指定，其实执行就一个。
//其中有执行下面这个方法，这个方法的作用是返回一个大于cap的最小的2的整数幂，比如如果cap是5，那么返回的就是8。这个方法的作用就是把5先变为7，然后再加一。
static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;//这几行或的运算就是吧第一位1后面的全变为1，然后再加1就得到最小的2的整数幂
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```

### 2.4.6 确定Hash桶数组索引位置

HashMap中的哈希算法：

```java
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

这个哈希算法算是一个“扰动函数”，它利用key自己的hashCode方法返回的32位int型的数来获取散列的值。但这时候问题就来了，这样就算我的散列值分布再松散，要是只取最后几位的话，碰撞也会很严重。更要命的是如果散列本身做得不好，分布上成等差数列的漏洞，恰好使最后几个低位呈现规律性重复，就无比蛋疼。所以HashMap中的hash方法利用hashCode值的高十六位与低十六位进行异或运算，使得低十六位更具有随机性，同时特保持了高十六位的特征。

但是如果直接用hashCode获得的值来当散列后的值很不现实（32位，40亿个数），所以HashMap利用对数组长度取模运算`(table.length - 1) & hash`作来进行散列值的获取。这也就解释了为什么HashMap中table的长度必须是2的n次方，这是HashMap在速度上的优化，只有这样我们通过table.length-1与hash值进行与运算才会得到hash对数组长度取模运算的值。

### 2.4.7 添加元素

1. 首先通过hash函数获取经过扰动的hash值
2. 然后调用putVal函数，进入真正的添加元素步骤
3. 如果table为null，或者table的长度为0，则调用**resize方法对table属性进行初始化**
4. 如果table中经过hash的模运算找到的位置中没有元素，那么直接创建node放入这个位置
5. 如果发生了冲突，经过模运算找到的位置中放置了元素
   1. 如果这个位置上的结点key和要插入元素的key相同（首先判断hash值是否相同，相同的话，如果key的地址相同或者key的equals方法返回相同，那么就代表这插入的节点和现在找到的结点是一个Node），替换value
   2. 如果table这个位置上是红黑树，调用**putTreeVal方法向红黑树中添加元素**
   3. 如果这个位置是链表，遍历链表的同时记录binCount
      1. 如果在遍历的过程中，遇到和插入key相同的node，那么替换这个node的value
      2. 如果遍历完链表之后，没有找到key相同的node，那么在链表末尾添加元素，同时，如果添加一个元素之后，链表的长度达到了8，进入treeifBin方法

在添加元素的过程中，遇到相同key，替换value的操作实际上是通过一个标记的node e来延迟完成的，等到所有判断都判断完了，如果node e不为null，那么使用value替换e的value

putVal方法的最后，如果table的size大于了threshold，**调用resize方法进行扩容**

```JAVA
//hash(key)就是上面讲的hash方法，对其进行了第一步和第二步处理
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
```

```java
/*
hash 索引的位置
onlyIfAbsent TRUE 表示不要更改现有的值
evict false 表示table处于创建模式
*/
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
    //如果table为null或者长度为0，则对他进行初始化，resize方法本来是用来扩容的，由于初始化没有实际分配空间，这里用这个方法进行空间分配。
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
    //如果位置上没有值，那么直接放到这个位置上
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
    //如果位置上有值，
        else {
            Node<K,V> e; K k;
            //如果结点key已经有值了，直接用新的值覆盖原来的值。
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            //因为TreeNode是Node的子类，所以table可以直接存储TreeNode
            //此处用于判断该结点存储的是否是红黑树
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            //此处用于判断该结点存储的是链表
            else {
                //binCount用于判断链表的长度是否超等于转化为红黑树的临界长度
                for (int binCount = 0; ; ++binCount) {
                    //此处判断的是链表中没有key的映射
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    //链表中存在key的映射
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            //当e！=null的时候，就说明链表或者table中存在key的映射
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
    //用作修改和快速失败校验
        ++modCount;
    //超过最大容量进行扩容
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

添加元素的两个主要函数。put函数底层使用的是putVal函数，这两个函数返回的是key所对的以前的值，如果没有，则为 null。

### 2.4.8 扩容机制

扩容函数会在第一次向HashMap中添加元素或者当加入元素之后，size大于了ThresHold这两种情况下发生。

扩容是由数组+链表+红黑树的扩容操作构成，向HashMap中插入元素时，如果HashMap集合的元素已经大于承载容量的threshold（capacity*loadFactor）,这里的threshold不是数组的最大长度。

首先经过一系列的判断来计算newCapacity和newThreshold

1. 如果旧容量不为0，并且旧容量的2倍小于最大容量，那么将新容量和新扩容阈值都变为原来的两倍
2. 如果旧容量为0，并且旧的扩容阈值不为零（在带有初始容量的构造函数构造hashMap时，将table的初始容量赋值到扩容阈值上），新的容量等于旧的扩容阈值
3. 如果旧容量和扩容阈值都为0，两个都赋值为默认的，16和16*0.74
4. 在第二种情况下，新的扩容阈值为0，那么最后再做一个判断，将新的容量乘上负载因子赋值为新的扩容阈值

然后使用新的容量创建一个新的table，同时赋值给旧的table

最后进行扩容逻辑：

1. 如果旧的table为null，说明初次调用，创建完table之后就返回，表示初始化完成

2. 如果旧的table不为null，这就牵扯上数据的移动，需要遍历旧的table进行数据移动操作

   1. 如果旧table j位置上的node没有下一个结点，那么直接使用hash模上新的容量进行数据移动

   2. 如果旧table j位置上的node是一个红河树结点，**那么调用头结点的split方法**

   3. 如果旧table j位置上的node是一个链表，执行链表的数据移动操作

      在同一个来表上的结点在扩容后的位置只有两种可能，一种是还在原来的位置，另一种是在原来的index加上旧容量大小的位置，因此就可以使用两个链表，

      1. 如果链表上的结点的hash与上旧容量为1，放入第一个链表
      2. 如果与上旧容量为0，放入第二个链表

      最后将这两个链表放到新table的对应位置即可

**如果旧table 的 j位置上是红河树，那么会调用头结点的split方法：**

* 由于TreeNode继承自LinkedHashMap.Entry，然后LinkedHashMap.Entry继承自HashMap的Node，因此TreeNode结点是有next指针的，所以可以使用遍历链表的方式遍历红黑树
* 红黑树的操作和链表的操作相似，都是首先使用两个链表将树中的元素分为两组
* 如果分开的链表中的结点个数小于6个，那么**调用红黑树结点的untreeify将红黑树转化为链表**
* 否则，如果另一个链表不为null（如果另一个链表为null，那么这个链表就是红黑树），**那么调用红黑树结点的treeify方法将链表转化为红黑树**

untreeify方法的实现比较简单：

* 遍历整个红黑树，构造一个链表，然后返回



```java
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
    //if分支走到这儿就说明oldCap==0，那么table中没有东西，说明hashTable只执行了构造函数，没有向里面添加元素，所以此时再判断oldThr是否等于零，如果不等于零，使用的是带有初始化容量的构造函数，这两个构造函数把oldThr赋值为initialCapacity，所以在此时，newCap就可以等于oldThr。
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
    //如果走的是第三个默认构造函数，threshold没有初始化也为零，那么就使用默认值对他们进行初始化。
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
    //计算新的阈值，只有在第二个if分支的时候，阈值为零，也就是初始化的时候阈值等于初始容量的时候，这是我再把阈值设置为初始容量乘上负载因子，或者第一个分支的时候计算newThr左移1位导致溢出。
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
    //以上的代码是为了找到newCapacity和newThreshold，创建newTable。
    //如果oldTab等于null，则直接返回newTable。
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;//使原来table中元素为null，便于垃圾回收
                    if (e.next == null)
               //原来的这个结点没有下一个引用（不是链表），则可以直接把它赋值到新的节点上。
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    //原来的这个结点有下一个引用，表明他是一个链表，在这种情况下，链表上的结点在新的table中散列的位置有两种情况，如果在对newCapacity取余之后，最高位是0，那么还在原来的位置上，如果取余之后最高位是1，那么新的位置就是原来的位置加上原来的长度。因此效率会提高，不用都算新的位置。
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                           //这个if语句是为了把链表上的结点分成两个链表，分别对应新位置和旧位置。
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        //把新粉的两个链表放到对应的位置上。
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

```java
//红黑树转回链表的阈值
static final int UNTREEIFY_THRESHOLD = 6;

 /** 这个方法在HashMap进行扩容时会调用到:  ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
  * @param map 代表要扩容的HashMap
  * @param tab 代表新创建的数组，用来存放旧数组迁移的数据
  * @param index 代表旧数组的索引
  * @param bit 代表旧数组的长度，需要配合使用来做按位与运算
  */
  final void split(HashMap<K,V> map, Node<K,V>[] tab, int index, int bit) {
            //做个赋值，因为这里是((TreeNode<K,V>)e)这个对象调用split()方法，所以this就是指(TreeNode<K,V>)e对象，所以才能类型对应赋值
            TreeNode<K,V> b = this;
            //设置低位首节点和低位尾节点
            TreeNode<K,V> loHead = null, loTail = null;
            //设置高位首节点和高位尾节点
            TreeNode<K,V> hiHead = null, hiTail = null;
            //定义两个变量lc和hc，初始值为0，后面比较要用，他们的大小决定了红黑树是否要转回链表
            int lc = 0, hc = 0;
            //这个for循环就是对从e节点开始对整个红黑树做遍历，如果对这循环赋值略有不懂，可以参考这篇模仿的博客@1
            for (TreeNode<K,V> e = b, next; e != null; e = next) {
                //取e的下一节点赋值给next遍历
                next = (TreeNode<K,V>)e.next;
                //取好e的下一节点后，把它赋值为空，方便GC回收
                e.next = null;
                //以下的操作就是做个按位与运算，按照结果拉出两条链表，具体的操作可以参考这篇博客@2
                if ((e.hash & bit) == 0) {
                    if ((e.prev = loTail) == null)
                        loHead = e;
                    else
                        loTail.next = e;
                    loTail = e;
                    //做个计数，看下拉出低位链表下会有几个元素
                    ++lc;
                }
                else {
                    if ((e.prev = hiTail) == null)
                        hiHead = e;
                    else
                        hiTail.next = e;
                    hiTail = e;
                    //做个计数，看下拉出高位链表下会有几个元素
                    ++hc;
                }
            }
            
            //如果低位链表首节点不为null，说明有这个链表存在
            if (loHead != null) {
                //如果链表下的元素小于等于6
                if (lc <= UNTREEIFY_THRESHOLD)
                    //那就从红黑树转链表了，低位链表，迁移到新数组中下标不变，还是等于原数组到下标
                    tab[index] = loHead.untreeify(map);
                else {
                    //低位链表，迁移到新数组中下标不变，还是等于原数组到下标，把低位链表整个拉到这个下标下，做个赋值
                    tab[index] = loHead;
                    //如果高位首节点不为空，说明原来的红黑树已经被拆分成两个链表了
                    if (hiHead != null)
                        //那么就需要构建新的红黑树了
                        loHead.treeify(tab);
                }
            }
            //如果高位链表首节点不为null，说明有这个链表存在
            if (hiHead != null) {
                 //如果链表下的元素小于等于6
                if (hc <= UNTREEIFY_THRESHOLD)
                    //那就从红黑树转链表了，高位链表，迁移到新数组中的下标=【旧数组+旧数组长度】
                    tab[index + bit] = hiHead.untreeify(map);
                else {
                    //高位链表，迁移到新数组中的下标=【旧数组+旧数组长度】，把高位链表整个拉到这个新下标下，做赋值
                    tab[index + bit] = hiHead;
                    //如果低位首节点不为空，说明原来的红黑树已经被拆分成两个链表了
                    if (loHead != null)
                        //那么就需要构建新的红黑树了
                        hiHead.treeify(tab);
                }
            }
        }

```

### 2.4.9 删除元素

```java
//底层调用removeNode()方法，并且返回被删除结点的value
public V remove(Object key) {
        Node<K,V> e;
        return (e = removeNode(hash(key), key, null, false, true)) == null ?
            null : e.value;
    }

    /**
     * Implements Map.remove and related methods
     *
     * @param hash hash for key
     * @param key the key
     * @param value the value to match if matchValue, else ignored
     * @param matchValue 如果是true的话，当且仅当value参数==key对应的value的时候删除结点。
     * @param movable if false do not move other nodes while removing
     * @return the node, or null if none
     */
    final Node<K,V> removeNode(int hash, Object key, Object value,
                               boolean matchValue, boolean movable) {
        Node<K,V>[] tab; Node<K,V> p; int n, index;
        //如果table中存在这样一个key对应的node。
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (p = tab[index = (n - 1) & hash]) != null) {
            Node<K,V> node = null, e; K k; V v;
            //接下来判断这个node是一个结点，还是多个，如果这个结点和key对应不上，那么就证明这个结点是红黑树或者链表。第一个if是这个结点就一个的情况。
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                node = p;
            //结点是红黑树或者链表
            else if ((e = p.next) != null) {
                //结点是红黑树
                if (p instanceof TreeNode)
                    node = ((TreeNode<K,V>)p).getTreeNode(hash, key);
                //结点是链表
                else {
                    do {
                        if (e.hash == hash &&
                            ((k = e.key) == key ||
                             (key != null && key.equals(k)))) {
                            node = e;
                            break;
                        }
                        p = e;
                    } while ((e = e.next) != null);
                }
            }
            //找到node之后，删除结点，此处还得需要matchValue来进行判断。
            if (node != null && (!matchValue || (v = node.value) == value ||
                                 (value != null && value.equals(v)))) {
                if (node instanceof TreeNode)
                    ((TreeNode<K,V>)node).removeTreeNode(this, tab, movable);
                else if (node == p)
                    tab[index] = node.next;
                else
                    p.next = node.next;
                ++modCount;
                --size;
                afterNodeRemoval(node);
                return node;
            }
        }
        return null;
    }
```

### 2.4.10 查找元素

```java
//containsKey方法，底层调用的是getNode方法。
public boolean containsKey(Object key) {
        return getNode(hash(key), key) != null;
    }

//get方法，底层调用的是getNode方法。
public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }

    /**
     * Implements Map.get and related methods
     *
     * @param hash hash for key
     * @param key the key
     * @return the node, or null if none
     */
    final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        //和removeNode方法的第一步一样，都是先判断key对应的结点是否存在。
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            //接下来判断第一个结点的key是否等于给定的key
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            //如果不等于，那么就沿着红黑树或者链表往后找。
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }
```

```java
public boolean containsValue(Object value) {
        Node<K,V>[] tab; V v;
        if ((tab = table) != null && size > 0) {
            //遍历桶，table中的每一个node
            for (int i = 0; i < tab.length; ++i) {
                //遍历每一个node，node可能是红黑树或者链表。
                for (Node<K,V> e = tab[i]; e != null; e = e.next) {
                    if ((v = e.value) == value ||
                        (value != null && value.equals(v)))
                        return true;
                }
            }
        }
        return false;
    }
```

### 2.4.11 遍历元素

1. 通过keySet方法和values方法获取key的集合视图和value的集合视图。
2. 通过获取keySet然后遍历key获取对应的value
3. 得到Entry集合，然后遍历Entry。
4. 通过HashMap中Entry的迭代器。

Map.Entry我猜测它的作用是为了让我们能得到类似HashMap中Node<K,V>[]这样的私有成员键值对。HashMap并没有把内部类Node<K,V>公有，所以我们只能通过Map.Entry这个接口去获取类似HashMap中的Node这样的键值对。然后Hashmap还给我没提供了entrySet方法用于直接的获取Map.Entry集合，其实就是把许多Node键值对的集合视图提供给我们，有点像（适配器模式），虽然说我们外部拿到的是一个set，但是这个set（Set<Map.Entry<K,V>>）的各种方法，包括Map.Entry的各种方法，都是对内部的Node进行操作，Set中并没有存储Node，它只是一个视图，用于对Node惊醒操作。

## LinkedHashMap

```java
public class LinkedHashMap<K,V>
    extends HashMap<K,V>
    implements Map<K,V>
```

它继承了HashMap类，HashMap中的部分方法有空实现，需要在LinkedHashMap中进行重写。

### 2.5.1 LinkedHashMap 定义

LinkedHashMap是基于HashMap实现的一种集合，具有Hashmap的所有特点，除了HashMap是无序的，LinkedHashMap是有序的。**因为LinkedHashMap在HashMap的基础上单独维护了一个具有数据的双向链表，该链表保证了元素迭代的顺序。**

所以可以这样说，LinkedHashMap = HashMap + LinkedList。

### 2.5.2 字段属性

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
        Entry<K,V> before, after;
        Entry(int hash, K key, V value, Node<K,V> next) {
            super(hash, key, value, next);
        }
    }
```

LinkedHashMap的每一个元素都是一个Entry，Entry继承自HashMap中的静态内部类Node，他们都是Map接口中Map.Entry接口的实现。相对于Node，Entry多了before和after结构。这两个结构是用来维护LinkedHashMap插入Entry的先后顺序的。

```java
//用来指向双向链表的头结点
transient LinkedHashMap.Entry<K,V> head;
//用来指向双向链表的尾结点
transient LinkedHashMap.Entry<K,V> tail;
//用来指定LinkedHashMap的迭代顺序
//TRUE表示按照访问的顺序，会把访问过得元素放到链表后面，放置顺序是访问的顺序。
//false表示按照第一次插入的顺序遍历，之后如果在放入相同key，那么就不会改变顺序。
final boolean accessOrder;
```

### 2.5.3 构造方法

1. 无参构造（accessOrder默认为false）
2. 指定初始容量（accessOrder默认为false）
3. 指定初始容量和加载因子（accessOrder默认为false）
4. 指定初始容量、负载因子和迭代规则
5. 指定构造指定集合中的元素。（accessOrder默认为false）

### 2.5.4 添加元素

LinkedHashMap中的put方法直接调用父类HashMap的put方法。只是LinkedHashMap重写了了HashMap的put方法中调用的四个空方法。`newNode`、`putTreeVal`、`afterNodeAccess`和`afterNodeInsertion`。

1. newNode（hash，key，value，null）方法

```java
Node<K,V> newNode(int hash, K key, V value, Node<K,V> e) {
        LinkedHashMap.Entry<K,V> p =
            new LinkedHashMap.Entry<K,V>(hash, key, value, e);
        linkNodeLast(p);
        return p;
    }

private void linkNodeLast(LinkedHashMap.Entry<K,V> p) {
    //使用临时变量last记录尾结点
        LinkedHashMap.Entry<K,V> last = tail;
    //将尾结点设置为当前插入的结点
        tail = p;
    //如果之前的尾结点为null（还没有插入元素）
        if (last == null)
            //头结点也为当前插入的结点
            head = p;
        else {
            //原来的来链表不为空，那么将当前结点的上结点指向原始尾结点
            p.before = last;
            last.after = p;
        }
    }
```

2. putTreeVal方法

```java
TreeNode<K,V> newTreeNode(int hash, K key, V value, Node<K,V> next) {
        TreeNode<K,V> p = new TreeNode<K,V>(hash, key, value, next);
        linkNodeLast(p);
        return p;
    }
```

上面这两个方法主要改变的是将新添加的元素放置到链表的尾端，并维护链表结点之间的关系。

对于`afterNodeAccess`方法，在putVal方法中，当添加数据键值对的key存在时，会对value进行替换，然后调用`afterNodeAccess`方法。

3. afterNodeAccess方法

```java
//在accessord==TRUE的时候，并且当前插入的结点不等于尾结点，或者对接点进行了访问，此方法都会执行。
//此方法主要用于把e结点放置到双向链表的末尾。
void afterNodeAccess(Node<K,V> e) { // move node to last
        LinkedHashMap.Entry<K,V> last;
        if (accessOrder && (last = tail) != e) {
            LinkedHashMap.Entry<K,V> p =
                (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
            p.after = null;
            if (b == null)
                head = a;
            else
                b.after = a;
            if (a != null)
                a.before = b;
            else
                last = b;
            if (last == null)
                head = p;
            else {
                p.before = last;
                last.after = p;
            }
            tail = p;
            ++modCount;
        }
    }
```

4. afterNodeInsertion方法

```java
void afterNodeInsertion(boolean evict) { // possibly remove eldest
    LinkedHashMap.Entry<K,V> first;
    if (evict && (first = head) != null && removeEldestEntry(first)) {
        K key = first.key;
        removeNode(hash(key), key, null, false, true);
    }
}
protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
        return false;
    }
```

LinkedHashMap中的afterNodeInsertion这个方法并不会执行什么实质性的东西，因为removeEldestEntry方法永远返回的是false，那么为什么要有这个方法呢？

这个方法的存在其实是为了实现LRU（最近最少使用）Cache时，重写的一个方法。当元素大于指定的个个数时，会删除链表的首元素。

### 2.5.5 删除元素

删除元素也是调用的HashMap中的remove方法，但是LinkedHashMap重写了removeNode中调用的afterNodeRemoval方法，

```java
void afterNodeRemoval(Node<K,V> e) { // unlink
    LinkedHashMap.Entry<K,V> p =
        (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
    p.before = p.after = null;
    if (b == null)
        head = a;
    else
        b.after = a;
    if (a == null)
        tail = b;
    else
        a.before = b;
}
```

此方法用来在删除某个结点之后维护双向链表中结点的顺序。

### 2.5.6 查找元素

重写了Hashmap的get方法。

```java
public V get(Object key) {
    Node<K,V> e;
    if ((e = getNode(hash(key), key)) == null)
        return null;
    if (accessOrder)
        afterNodeAccess(e);
    return e.value;
}
```

因为如果accessOrder为true，那么在访问某个结点之后需要把这个结点放到链表末尾。

### 2.5.7 遍历元素

### 2.5.8 迭代器

![image-20211011093749121](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211011093749121.png)

![image-20211011093807572](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211011093807572.png)

LinkedHashMap和HashMap中的Iterator的继承体系如图所示。全部都有一种抽象类Iterator。

## TreeMap类

```java
public class TreeMap<K,V>
    extends AbstractMap<K,V>
    implements NavigableMap<K,V>, Cloneable, java.io.Serializable
```

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211014075054026.png" alt="image-20211014075054026" style="zoom:80%;" />

### 2.6.1 TreeMap的定义

TreeMap是由红黑树实现的有序的key-value集合。

TreeMap首先继承AbstractMap抽象类，表示它具有散列表的性质，也就是由key-value组成。

其次TreeMap实现了NavigableMap接口，该接口支持一系列获取指定集合的导航方法，比如获取小于指定key值的方法。

### 2.6.2 字段定义

1. Comparator

```java
/**
 * The comparator used to maintain order in this tree map, or
 * null if it uses the natural ordering of its keys.
 * 用于维护TreeMap集合中的顺序，如果为null，则按照key的自然顺序排序（字典顺序）
 * @serial
 */
private final Comparator<? super K> comparator;
```

2. Entry

```java
private transient Entry<K,V> root;
```

```java
//TreeMap中的一个静态内部类，实现了Map.Entry接口
static final class Entry<K,V> implements Map.Entry<K,V>
```

```java
K key;
V value;
Entry<K,V> left;
Entry<K,V> right;
Entry<K,V> parent;
boolean color = BLACK;
```

以上是内部类中的字段，由此可见，TreeMap中的结点是红黑树。

3. Size

```java
/**
 * The number of entries in the tree
 * 树中结点的个数
 */
private transient int size = 0;
```

4. modCount

首先介绍一下Fail-Fast快速失败机制：**它是java集合中的一种错误检测机制，当多个线程（单个线程也是可以的）,在结构上对集合进行改变时，就有可能会产生fail-fast机制。**

modCount字段在ArrayList、LinkedList、HashMap等线程不安全的集合中都有此字段，用来实现Fail-Fast机制，如果在迭代这些集合的过程中，有其他线程修改了这些集合，就会抛出ConcurrentModificationException异常。

5. 红黑树常量

```java
private static final boolean RED   = false;
private static final boolean BLACK = true;
```

用于标记结点是红色还是黑色。

### 2.6.3 构造函数

1. 无参构造函数，比较器为null，按照key的自然顺序排序

2. 带比较器的构造函数，指定自己的比较器

3. 构造包含指定map集合元素

4. 带SortedMap的构造函数，使用SortMap所使用的比较器。

   SortMap是NavigableMap的父接口。

### 2.6.4 添加元素

如果初始化TreeMap构造函数时，没有传递comparator类，是不允许插入key==null的键值对的，相反，如果实现了Comparator，则可以传入key==null的键值对。	由一下源码可知，如果初始化时没有传入Comparator比较器，那么会使用key类自己的compareTo方法去比较，因此key==null是不允许的，如果传入了比较器，那么使用比较器的compare方法，在方法内部可以实现null值的比较。

注意：自然排序指的是元素自己实现了Comparable接口，实现其中的ComparaTo方法。自定义排序指的是实现一个Comparator类，实现其中的compare方法。

```java
/**
 * Compares two keys using the correct comparison method for this TreeMap.
 */
@SuppressWarnings("unchecked")
final int compare(Object k1, Object k2) {
    return comparator==null ? ((Comparable<? super K>)k1).compareTo((K)k2)
        : comparator.compare((K)k1, (K)k2);
}
```

```java
public V put(K key, V value) {
    Entry<K,V> t = root;
    if (t == null) {
        compare(key, key); // type (and possibly null) check
		//这个比较是为了确定key值实现了comparable接口或者Comparator接口。
        root = new Entry<>(key, value, null);
        size = 1;
        modCount++;
        return null;
    }
    int cmp;
    Entry<K,V> parent;
    // split comparator and comparable paths
    //分两种情况，比较器为null和比较器不为null，因为这是两种处理情况。
    Comparator<? super K> cpr = comparator;
    if (cpr != null) {
        do {
            parent = t;
            cmp = cpr.compare(key, t.key);
            if (cmp < 0)
                t = t.left;
            else if (cmp > 0)
                t = t.right;
            else
                return t.setValue(value);
        } while (t != null);
    }
    else {
        if (key == null)
            throw new NullPointerException();
        @SuppressWarnings("unchecked")
            Comparable<? super K> k = (Comparable<? super K>) key;
        do {
            parent = t;
            cmp = k.compareTo(t.key);
            if (cmp < 0)
                t = t.left;
            else if (cmp > 0)
                t = t.right;
            else
                return t.setValue(value);
        } while (t != null);
        //这种循环方法很好，可以在结束的时候直接找到父节点。
    }
    Entry<K,V> e = new Entry<>(key, value, parent);
    if (cmp < 0)
        parent.left = e;
    else
        parent.right = e;
    //在插入过后执行红黑树的修复。
    fixAfterInsertion(e);
    size++;
    modCount++;
    return null;
}
```

### 2.6.5 删除元素

```java
public V remove(Object key) {
    //首先根据key值获取元素Entry，在getEntry函数当中又分为有比较器和没有比较器两种情况进行元素比较判断key对应的元素是否存在。如果不存在则函数返回null，但是函数返回null也有可能说明存在key元素，只不过对应的value为null。
    Entry<K,V> p = getEntry(key);
    if (p == null)
        return null;

    V oldValue = p.value;
    deleteEntry(p);
    return oldValue;
}
```

```java
//真正执行删除的函数。
private void deleteEntry(Entry<K,V> p) {
    modCount++;
    size--;

    // If strictly internal, copy successor's element to p and then make p
    // point to successor.
    //如果要删除的结点有两个子节点，那么寻找它的中序后继节点，也就是右子树最左下角的结点。
    if (p.left != null && p.right != null) {
        Entry<K,V> s = successor(p);
        p.key = s.key;
        p.value = s.value;
        p = s;
    } // p has 2 children
	//1、 如果原来的p有两个子节点，那么此时的p指的是原来p的中序后继节点，在这种情况下，p的左子树一定		  为null，因为我查找原来p的中序后继节点的时候，找的就是p的右子树的左子树的左子树。。。，一直			到null，所以这种情况下，可以代替要删除的p的只能是p的右子树。
    //2、 如果原来的p没有两个子节点，那么此时的p还是原来的p，在这种情况下，p的左子树和右子树一定有		      null，两个都为null或者其中一个为null。
    // Start fixup at replacement node, if it exists.
    Entry<K,V> replacement = (p.left != null ? p.left : p.right);
	
    //如果replacement不为null，使用replacement去替换要删除的p
    if (replacement != null) {
        // Link replacement to parent
        // p的父节点赋值给replacement的父节点
        replacement.parent = p.parent;
        // p的父节点的子节点赋值为replacement
        if (p.parent == null)
            root = replacement;
        else if (p == p.parent.left)
            p.parent.left  = replacement;
        else
            p.parent.right = replacement;

        // Null out links so they are OK to use by fixAfterDeletion.
        p.left = p.right = p.parent = null;

        // Fix replacement
        // 如果p是黑色的结点，删除p之后才会破坏红黑树的平衡，如果p原来是红色的结点，删除之后根本不会破坏红黑树的平衡。
        if (p.color == BLACK)
            fixAfterDeletion(replacement);
    // 当replacement为null的时候，证明要删除的p结点没有子节点
    // 1、如果p正好有没有父节点，那么p就是根节点。
    } else if (p.parent == null) { // return if we are the only node.
        root = null;
    // 直接删除p
    } else { //  No children. Use self as phantom replacement and unlink.
        if (p.color == BLACK)
            fixAfterDeletion(p);

        if (p.parent != null) {
            if (p == p.parent.left)
                p.parent.left = null;
            else if (p == p.parent.right)
                p.parent.right = null;
            p.parent = null;
        }
    }
}
```

删除结点总结为一下几种情况:

1. 根据key没有找到结点，直接返回null即可
2. 根据key找到到结点，又分为三种情况：

* 待删除结点没有子节点，直接删除。
* 待删除结点只有一个子节点，用这个子节点代替待删除结点，然后删除待删除结点。
* 待删除结点有两个子节点，找到该结点的中序后继结点，然后使用这个结点代替待删除结点，最后重复1,2删除这个中序后继结点。

### 2.6.6 查找元素

### 2.6.7 遍历元素

常规操作，获取Entryset的iterator。

# Java原子类

原子操作是指一个或者多个操作要么全部成功，要么全部失败。多线程并发操作同一变量时，一般是通过加锁的方式来保证操作的原子性，最常用的就是使用synchronized同步锁。使用加锁的方式，在高并发场景下，线程会被频繁的挂起和唤醒，这是比较消耗机器性能的。

## 3.1 原子变量操作类AtomicLong

JUC包中包含了三个基本类型原子操作类，AtomicBoolean为Boolean类型的原子类；AtomicInteger为Integer类型的原子类；AtomicLong为Long类型原子类。他们三个的原理类似。AtomicLong主要通过Unsafe类的CAS原子更新方法实现原子递增或者原子递减。

AtomicLong的实现并不复杂，所有的操作都是针对内部的value字段。AtomicLong的几个原子更新方法的原理是一样的。最终都会调用到compareAndSwapLong()来保证value值更新的原子性。

CAS机制实现原子操作，相比起加锁的方式可以获得更好的并发性能，但是当多个线程同时竞争一个AtomicLong对象仍然会存在性能瓶颈，可以使用jdk1.8新增的高性能LongAdder类来替换AtomicLong类获得更好的并发性能。

## 3.2 高性能原子操作类LongAdder

jdk1.8中，新增了4个高性能原子类。这四个类使用了分段锁的思想，将不同的线程映射到不同的数据字段上去更新，讲这些字段的值相加就能得到最终的值。

* LongAccumulator：Long类型的聚合器，
* DoubleAccumulator：Double类型的聚合器
* LongAdder：Long类型的累加器，是LongAccumulator的特例，只能用来计算加法，从0开始计算
* DoubleAdder：Double类型的累加器，是DoubleAccumulator的特例，只能用来计算加法，从0开始计算。

### 3.2.1 LongAdder介绍

LongAdder继承自Striped64。Striped64是一个抽象类，用来实现累加功能，是实现高并发累加的工具类。在Striped64内部定义了三个变量，LongAdder的真实值就是将base的值与cells数组元素值累加。

AtomicLong使用内部的Value保存着实际的Long值，所有线程的读写操作都是针对一个变量进行。也就是说，在高并发环境下，N个线程会同时竞争一个热点value变量的读写。

但是LongAdder的基本思路是分散并发冲突时的热点。在没有线程竞争的情况下，要将累加的数累加到一个热点base基础值上；当有线程竞争时，将热点分散到cells数组中，不同线程映射到不同数组位置上，每个线程对对应位置中的元素进行CAS递增或者递减操作，这样就从一个热点分散成了多个热点，发生并发冲突的概率就会小很多。LongAdder类内部有三个重要的成员：基础值base、数组cells和锁标识cellsBusy。LongAdder的所有操作都是基于这三个变量。**这三个变量都是继承父类Striped64**

```java
/**
 * Table of cells. When non-null, size is a power of 2.
 * 计数数组，数组长度为2的整数幂。在有线程竞争时，每个线程会映射到对应的Cell[i]上进行CAS更新操作。
 */
transient volatile Cell[] cells;

/**
 * Base value, used mainly when there is no contention, but also as
 * a fallback during table initialization races. Updated via CAS.
 * 在没有线程竞争时，执行递增、递减操作会直接CAS更新该变量。
 */
transient volatile long base;

/**
 * Spinlock (locked via CAS) used when resizing and/or creating Cells.
 * 锁标识，在cells初始化或者扩容时使用，保证cells变量的线程安全。0代表锁空闲，1代表锁被占用
 */
transient volatile int cellsBusy;
```

LongAdder中只有一个默认构造函数。

在父类Striped64中，定义了LongAdder类中地段的内存地址偏移量。在static代码块中使用UNsafe实例化base和cellsBusy字段的内存地址偏移量，用于后续的CAS更新操作。其中threadLocalRandomProbe字段可以看成是线程的hash值，在计算线程映射cells多以位置时使用。

```java
// Unsafe mechanics
private static final sun.misc.Unsafe UNSAFE;
private static final long BASE;
private static final long CELLSBUSY;
private static final long PROBE;
static {
    try {
        UNSAFE = sun.misc.Unsafe.getUnsafe();
        Class<?> sk = Striped64.class;
        BASE = UNSAFE.objectFieldOffset
            (sk.getDeclaredField("base"));
        CELLSBUSY = UNSAFE.objectFieldOffset
            (sk.getDeclaredField("cellsBusy"));
        Class<?> tk = Thread.class;
        PROBE = UNSAFE.objectFieldOffset
            (tk.getDeclaredField("threadLocalRandomProbe"));
    } catch (Exception e) {
        throw new Error(e);
    }
}
```

数组cells的元素类型是Cell，这是Striped64类中定义的一个静态内部类，Cell定义了一个volatile类型的value字段，对cells中元素的读写操作实际上都是对Cell中value字段进行的。

```java
/**
 * Padded variant of AtomicLong supporting only raw accesses plus CAS.
 *
 * JVM intrinsics note: It would be possible to use a release-only
 * form of CAS here, if it were provided.
 */
@sun.misc.Contended static final class Cell {
    volatile long value;
    Cell(long x) { value = x; }
    // CAS更新value的值，更新成功返回TRUE
    final boolean cas(long cmp, long val) {
        return UNSAFE.compareAndSwapLong(this, valueOffset, cmp, val);
    }

    // Unsafe mechanics，用于CAS操作
    private static final sun.misc.Unsafe UNSAFE;
    private static final long valueOffset;
    static {
        try {
            // 初始化value字段内存地址偏移量
            UNSAFE = sun.misc.Unsafe.getUnsafe();
            Class<?> ak = Cell.class;
            valueOffset = UNSAFE.objectFieldOffset
                (ak.getDeclaredField("value"));
        } catch (Exception e) {
            throw new Error(e);
        }
    }
}
```

### 3.2.2 LongAdder 源码解析

1. add方法：更新数值LongAdder类中的方法。

LongAdder类中的更新数字的方法还有decrement和increment。这两个都是通过add方法实现的。

```java
public void add(long x) {
    Cell[] as; long b, v; int m; Cell a;
    if ((as = cells) != null || !casBase(b = base, b + x)) {
        boolean uncontended = true;
        if (as == null || (m = as.length - 1) < 0 ||//cells未初始化
            (a = as[getProbe() & m]) == null ||// 线程对应的cell没有初始化
            !(uncontended = a.cas(v = a.value, v + x))) // 对cell的CAS操作发生冲突
            longAccumulate(x, null, uncontended);
    }
}
```

add方法的主要逻辑：

1. 无线程竞争时（从创建到现在都没有线程竞争），将值累加到base变量上，如果可以一直增加成功，那么就会一直在base上增加。

`(as = cells) != null || !casBase(b = base, b + x)`

2. 如果有线程竞争或者之前有了，将值累加到cells数组中。

有两种情况都要将值累加到cells数组中：

* 如果cells不为null，说明之前发生过并发冲突，直接将值累加到cells数组中。
* 如果casBase方法失败，说明当前线程和其他线程发生了并发冲突，需要将值累加到cells数组中。

一共有三种情况会进入longAccumulate方法：

1. cells未初始化
2. 对应索引位置未初始化
3. 或者对应索引位置的cellCAS更新失败

LongAccumulate方法是LongAdder最核心的方法，这个方法在服了Striped64中实现。

```java
/**
 * x为要累加的值
 * 第二个参数是一个函数式接口，用于实现自己的操作
 * 第三个参数的意思是未发生竞争，为false表示发生了竞争
 */
final void longAccumulate(long x, LongBinaryOperator fn,
                          boolean wasUncontended) {
    int h;
    if ((h = getProbe()) == 0) {
        ThreadLocalRandom.current(); // force initialization
        h = getProbe();
        wasUncontended = true;
    }
    boolean collide = false;                // True if last slot nonempty
    for (;;) {
        Cell[] as; Cell a; int n; long v;
        // cells已经初始化完毕
        if ((as = cells) != null && (n = as.length) > 0) {
            // 如果线程的索引位置的cell还未初始化
            if ((a = as[(n - 1) & h]) == null) {
                if (cellsBusy == 0) {       // Try to attach new Cell
                    Cell r = new Cell(x);   // Optimistically create
                    if (cellsBusy == 0 && casCellsBusy()) {
                        boolean created = false;
                        try {               // Recheck under lock
                            Cell[] rs; int m, j;
                            if ((rs = cells) != null &&
                                (m = rs.length) > 0 &&
                                rs[j = (m - 1) & h] == null) {
                                rs[j] = r;
                                created = true;
                            }
                        } finally {
                            cellsBusy = 0;
                        }
                        if (created)
                            break;
                        continue;           // Slot is now non-empty
                    }
                }
                collide = false;
            }
            
            // 线程所对应的cell初始化成功，但是我在对这个cell进行CAS的时候失败了
            // 接着重新计算线程的hash值然后重试，最后一步是计算线程hash值
            else if (!wasUncontended)       // CAS already known to fail
                wasUncontended = true;      // Continue after rehash
            // 更新线程对应的cell，成功退出，失败继续
            else if (a.cas(v = a.value, ((fn == null) ? v + x :
                                         fn.applyAsLong(v, x))))
                break;
            // cells的大小已经达到最大容量或者as指针过期，说明其他线程已经抢先完成扩容
            else if (n >= NCPU || cells != as)
                collide = false;            // At max size or stale
            // 线程执行到这里，说明当前线程具备扩容条件，下次自旋开始扩容，正式扩容之前会再次尝试一次CAS
            else if (!collide)
                collide = true;
            // 开始扩容
            else if (cellsBusy == 0 && casCellsBusy()) {
                try {
                    if (cells == as) {      // Expand table unless stale
                        Cell[] rs = new Cell[n << 1];
                        for (int i = 0; i < n; ++i)
                            rs[i] = as[i];
                        cells = rs;
                    }
                } finally {
                    cellsBusy = 0;
                }
                collide = false;
                continue;                   // Retry with expanded table
            }
            // 重新计算线程hash
            h = advanceProbe(h);
        }
        
        // cells还没有初始化 并且加锁成功，这个if还是不太明白
        else if (cellsBusy == 0 && cells == as && casCellsBusy()) {
            // cells初始化标识，存在的意义：就是为了判断初始化是否成功没有别的意思
            boolean init = false; // 尤其是这里。
            try {                           // Initialize table
                if (cells == as) {
                    Cell[] rs = new Cell[2];// 首次初始化容量为2
                    rs[h & 1] = new Cell(x);// 
                    cells = rs;
                    init = true;
                }
            } finally {
                cellsBusy = 0;
            }
            // 如果初始化成功，说明值已经加上，直接退出。如果init为false，说明有其他线程已经抢先完成了初始化操作，这一点很不明白，两个线程不可能同时进入上面的if子句啊。
            if (init)
                break;
        }
        // 尝试将值累加到base上，如果成功直接退出，如果失败进入自旋。
        else if (casBase(v = base, ((fn == null) ? v + x :
                                    fn.applyAsLong(v, x))))
            break;                          // Fall back on using base
    }
}
```

2. sum方法： 获取当前值

sum方法用于获取当前值，也就是累加后的最终值，使用base加上每个cell中的值。

```java
public long sum() {
    Cell[] as = cells; Cell a;
    long sum = base;
    if (as != null) {
        for (int i = 0; i < as.length; ++i) {
            if ((a = as[i]) != null)
                sum += a.value;
        }
    }
    return sum;
}
```

可以看到sum方法中未使用任何锁，在调用这个方法的时候可能有其他线程正在执行add操作，所以在高并发场景中，这个方法只能得到一个近似值，如果想要得到绝对准确的值，还是要加全局锁。这也就是LongAdder不能完全代替AtomicLong的原因之一。

# java并发锁

java早期提供synchronized关键字实现同步机制。可以用它修饰一个方法或者代码块。当时用synchronized修饰代码时，并不需要显示的执行加锁和解锁的过程，所以他也被称为隐式锁。

JUC中的锁分为两类：重入锁和读写锁。

重入锁：ReentranLock类，它是一种可重入的独占锁，具有与使用synchronized相同的一些基本行为和语义，但是它拥有更广泛的API，功能更强大。

读写锁：读写锁的实现有两个，分别为ReentrantReadWriteLock和StampedLock。

## 4.1 为什么引入JUC锁

主要是因为synchronized同步锁存在以下问题：

1. synchronized同步锁提供了一种排他式的同步机制，当多个线程同时竞争锁资源时，同时只能有一个线程持有锁，当一个线程获得了锁，其他线程就会被阻塞，只有等占有锁的线程释放锁后，才能重新竞争锁。

* 当使用synchronized同步锁，线程会在三种情况下释放锁：
  * 线程执行完同步代码块或者方法，
  * 线程执行时发生异常，此时jvm会让线程自动释放锁
  * 在同步代码块或者方法中，锁对象执行了wait方法，线程释放锁

2. 在读多写少的场景中，效率低下。

## 4.2 独占锁ReentrantLock原理

### 4.2.1 ReentrantLock简介

ReentrantLock实现了Lock接口，在ReentrantLock中有非公平锁NonfairSync和公平锁FairSync的实现，Nonfairync和FairSync都继承自抽象类Sync。Sync类是ReentrantLock的内部抽象类，继承自抽象类AbstractQueuedSycnhronizer（简称AQS）。JUC中绝大部分的同步工具都是基于AQS构建的。

ReentrantLock类是唯一实现了Lock接口的类。Lock接口定义了一套锁实现的标准规范，定义活动锁和释放锁的一系列方法，所以Lock锁可以提供比synchronized更广泛的锁操作，可以更灵活的控制锁的粒度。

```java
public interface Lock {

    /**
     * 阻塞式获取锁，如果锁被其他线程获得，线程就会一直等待（阻塞）直到成功获得锁
     */
    void lock();

    /**
     * 获取锁，可相应中断，若锁诶其他线程获取，在等待锁的过程中，可以被其他线程中断（可以响应中断）
     */
    void lockInterruptibly() throws InterruptedException;

    /**
     * （非阻塞的）尝试获取锁
     * 如果锁是空闲状态，获取成功，如果锁被其他线程获得，放弃锁的获取
     */
    boolean tryLock();

    /**
     *  尝试获取锁，限定获取锁的等地时间，可相应中断
     * 如果锁空闲，获取成功
     * 如果在规定等地时间没有获取到锁，线程就会放弃锁的获取，并且可以响应中断
     */
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;

    /**
     * 释放锁
     */
    void unlock();

    /**
     * 
     */
    Condition newCondition();
}
```

ReentrantLock中提供了两个构造器：其中一个构造器可以指定锁的类型——公平锁或者非公平锁，默认构造器使用非公平锁。

```java
public ReentrantLock() {
    sync = new NonfairSync();
}

public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

公平锁：多个线程按照申请锁的顺序，按照先来后到的原则获得锁

非公平锁：多个线程获取锁的顺序并不是按照申请锁的顺序，允许“插队”，有可能出现后申请的线程比先申请的线程优先获取锁的情况。

需要注意的是，一般情况下使用公平锁的程序在多线程访问时，在线程调度上面开销比较大，所以总吞吐量比较低。

ReentrantLock的使用比较简单，代码如下：

```java
class X {
   private final ReentrantLock lock = new ReentrantLock();
   // ...

   public void m() {
     lock.lock();  // block until condition holds
     try {
       // ... method body
     } finally {
       lock.unlock()
     }
   }
 }
```

### 4.2.2 AQS同步队列

重入锁ReentrantLock的底层是使用AbstractQueuedSynchronizer实现的。AbstractQueuedSynchronizer是一个抽象同步类，简称AQS。JUC中绝大部分同步工具都是基于AQS构建的。

AQS提供了一套通用的机制来管理同步状态、阻塞唤醒线程、管理等待队列等。除了JUC中的锁，JUC中的其他同步工具如CountDownLatch、CyclicBarrier等，也都是通过内部类实现了AQS的API，来实现个子的同步功能。

