![image-20210926182033884](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20210926182033884.png)

## 一、abstract抽象类

* 抽象类不能被final关键字修饰。
* 抽象类不能被实例化。
* 抽象类可以没有抽象方法，但是具有抽象方法的类一定是抽象类。如果一个子类继承一个抽象类，子类没有实现父类的所有抽象方法，那么子类也要定义为抽象类，否则的话编译会出错的。
* 抽象类可以有非抽象的构造方法，不能有抽象的构造方法（可能是因为抽象类可以具有属性，在抽象类中定义构造方法可以使的子类不必做重复的初始化工作）。

## 二、instanceof

* **如果 obj 不为 null 并且 (T) obj 不抛 ClassCastException 异常则该表达式值为 true ，否则值为 false 。**当用null来判断时，结果总是false。
* instanceof只能用来判断引用数据类型。
* instanceof可以用来判断这个类是否为判断类的实例对象、实现类或直接、间接的继承类。

## 三、数据类型自动提升

①所有的byte,short,char型的值将被提升为int型；

②如果有一个操作数是long型，计算结果是long型；

③如果有一个操作数是float型，计算结果是float型；

④如果有一个操作数是double型，计算结果是double型；

而声明为final的变量会被JVM优化，第6行相当于 b6 = 10

## 四、深入理解final关键字

### 1.基本用法

> 在Java中，final关键字可以用来修饰类、方法和变量（包括成员变量和局部变量）

#### 1.1 final修饰类

```java
	当final修饰类的时候，表明这个类不能被继承。final类中的成员变量可以根据需求设置为final，但是final类中的成员方法默认为final方法。
```

#### 1.2 final修饰方法

> 注：类的private方法会隐式地被指定为final方法。

```java
	“使用final方法的原因有两个。第一个原因是把方法锁定，以防任何继承类修改它的含义；第二个原因是效率。在早期的Java实现版本中，会将final方法转为内嵌调用。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升。在最近的Java版本中，不需要使用final方法进行这些优化了。“
```

#### 1.3 final修饰变量

```java
	对于一个final变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象,但是指向的对象中的值可以更改。
```



## 五、8种基本类型所占字节数

* byte 一个字节、int 四个字节、long 八个字节、char两个字节、float 四个字节、double 八个字节、boolean一个字节。

| Primitive Type（原生类型） | Memory Required(bytes) |
| -------------------------- | ---------------------- |
| byte, boolean              | 1 byte                 |
| short, char                | 2 bytes                |
| int, float                 | 4 bytes                |
| long, double               | 8 bytes                |

## 六、String的两种创建方法

![image-20210918095745891](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20210918095745891.png)

![image-20210918095800746](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20210918095800746.png)

## 七、对类进行初始化的时机

虚拟机规范严格规定了有且只有五种情况必须立即对类进行“初始化”：

1. 使用new关键字实例化对象的时候、读取或设置一个类的静态字段的时候，已经调用一个类的静态方法的时候。

2. 使用java.lang.reflect包的方法对类进行反射调用的时候，如果类没有初始化，则需要先触发其初始化。

3. 当初始化一个类的时候，如果发现其父类没有被初始化就会先初始化它的父类。

4. 当虚拟机启动的时候，用户需要指定一个要执行的主类（就是包含main()方法的那个类），虚拟机会先初始化这个类；

5. 使用Jdk1.7动态语言支持的时候的一些情况。

除了这五种之外，其他的所有引用类的方式都不会触发初始化，称为被动引用。下面是被动引用的三个例子：

1. 通过子类引用父类的的静态字段，不会导致子类初始化。

2. 通过数组定义来引用类，不会触发此类的初始化。

```java
public class NotInitialization { 
  public static void main(String[] args) { 
​    SuperClass[] sca = new SuperClass[10]; 
  }  
}
```

3. 常量在编译阶段会存入调用类的常量池中，本质上没有直接引用到定义常量的类，因此不会触发定义常量的类的初始化。

```java
public class ConstClass { 
  static { 
​    System.out.println("ConstClass init!"); 
  } 
  public static final int value = 123; 
} 
public class NotInitialization{ 
  public static void main(String[] args) { 
​    int x = ConstClass.value; 
  } 
} 
```

## 八、volatile关键字

```java
被volatile修饰的共享变量具有一下两个特征：不同线程对其可见，禁止指令重新排序
```



1. **保证内存的可见性**

   ![image-20210918222223699](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20210918222223699.png)

> 内存可见性，即线程A对volatile变量的修改，其他线程获取的volatile变量都是最新的。
>
> 如上图所示，所有线程的共享变量都存储在主内存中，每一个线程都有一个独有的工作内存，每个线程不直接操作在主内存中的变量，而是将主内存上变量的副本放进自己的工作内存中，只操作工作内存中的数据。当修改完毕后，再把修改后的结果放回到主内存中。每个线程都只操作自己工作内存中的变量，无法直接访问对方工作内存中的变量，线程间变量值的传递需要通过主内存来完成。
>
> 上述的Java内存模型在单线程的环境下不会出现问题，但在多线程的环境下可能会出现脏数据，例如：如果有AB两个线程同时拿到变量i，进行递增操作。A线程将变量i放到自己的工作内存中，然后做+1操作，然而此时，线程A还没有将修改后的值刷回到主内存中，而此时线程B也从主内存中拿到修改前的变量i，也进行了一遍+1的操作。最后A和B线程将各自的结果分别刷回到主内存中，看到的结果就是变量i只进行了一遍+1的操作，而实际上A和B进行了两次累加的操作，于是就出现了错误。究其原因，是因为线程B读取到了变量i的脏数据的缘故。
>
> 此时如果对变量i加上volatile关键字修饰的话，它可以保证当A线程对变量i值做了变动之后，会立即刷回到主内存中，而其它线程读取到该变量的值也作废，强迫重新从主内存中读取该变量的值，这样在任何时刻，AB线程总是会看到变量i的同一个值。

2. **保证指令的执行顺序，禁止指令重新排序**

> 指令的执行顺序并不一定会像我们编写的顺序那样执行，为了保证执行上的效率，JVM（包括CPU）可能会对指令进行重排序。比方说下面的代码：
>
> ```java
> int i = 1;
> int j = 2;
> ```
>
> 上述的两条赋值语句在同一个线程之中，根据程序上的次序，“int i = 1;”的操作要先行发生于“int j = 2;”，但是“int j = 2;”的代码完全可能会被处理器先执行。JVM会保证在单线程的情况下，重排序后的执行结果会和重排序之前的结果一致。但是在多线程的场景下就不一定了。最典型的例子就是双重检查加锁版的单例实现，代码如下所示：
>
> ```java
> public class Singleton {
> 
>     private volatile static Singleton instance;
> 
>     private Singleton() {}
> 
>     public static Singleton getInstance() {
>         if (instance == null) {
>             synchronized (Singleton.class) {
>                 if (instance == null) {
>                     instance = new Singleton();
>                 }
>             }
>         }
>         return instance;
>     }
> }
> ```
>
> 由上可以看到，instance变量被volatile关键字所修饰，但是如果去掉该关键字，就不能保证该代码执行的正确性。这是因为“instance = new Singleton();”这行代码并不是原子操作，其在JVM中被分为如下三个阶段执行：
>
> 1. 为instance分配内存
> 2. 初始化instance
> 3. 将instance变量指向分配的内存空间
>
> 由于JVM可能存在重排序，上述的二三步骤没有依赖的关系，可能会出现先执行第三步，后执行第二步的情况。也就是说可能会出现instance变量还没初始化完成，其他线程就已经判断了该变量值不为null，结果返回了一个没有初始化完成的半成品的情况。而加上volatile关键字修饰后，可以保证instance变量的操作不会被JVM所重排序，每个线程都是按照上述一二三的步骤顺序的执行，这样就不会出现问题。

3. **volatile不保证原子性**

[参考连接：]（https://www.html.cn/qa/other/20717.html）

​				   https://www.techug.com/post/java-volatile-keyword.html

## 九、| & || &&

| :检测ture;不具备短路功能，会检查每一个条件，表达式中只要一个ture 就整体返回true
|| :检测true;具备短路功能，一遇到true,就返回true;
&:检测false;同理上；
&&：检测false;同理上；

## 十、同步方法

## 十一、对象成员占用内存

## 十二、TreeSet

## 十三、equals和hashCode和==

* 首先==运算符就是用来判断两个变量指向的是不是同一个地址空间或者**两个基本类型数据是否相等**。

* Object的equals方法内部使用的就是==运算符。

  ```java
  //Object类中的equals方法
  public boolean equals(Object obj) {
          return (this == obj);
  }
  ```

* 如果重写了Object的equals方法，那么一般就是根据对象的内容来判断对象是否相等（业务逻辑上的相等）。
* hashCode方法用来返回对象的散列表值，如果重写了equals方法，那么尽量重写hashCode方法。因为Object的hashCode方法是为不同对象（指向地址不同）产生不同的hash值。
* 如果根据equals(Object)方法两个对象相等，则对这两个对象中的每一个调用hashCode方法必须产生相同的整数结果。

## 十四、Integer的==

* 由一下代码分析可知，由于IntegerCache内部类的存在（此处使用的是享元模式），他会保存 表示-128 和 127数值的Integer对象，所以在使用直接赋值数值以及使用Integer的valueOf方法初始化一个表示 -128 和 127Integer对象时，相同数值的Integer对象，内存地址一样。但是在使用new时，每次都会创建一个新的对象，所以内存地址不一样。

```java
/**
构造一个新分配的Integer对象，表示指定的int值。
参数：
value – 要由Integer对象表示的值
*/
public Integer(int value) {
        this.value = value;
    }
```

```java
/**
返回表示指定int值的Integer实例。 如果不需要新的Integer实例，则通常应优先使用此方法而不是构造函数Integer(int) ，因为此方法通过缓存频繁请求的值可能会产生明显更好的空间和时间性能。 此方法将始终缓存 -128 到 127（含）范围内的值，并且可能缓存此范围之外的其他值。
参数：
i - 一个int数值。
返回：
表示i的Integer实例。
*/
public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
   }
```

```java
/**
缓存以支持 JLS 要求的 -128 和 127（含）之间值的自动装箱的对象标识语义。 缓存在第一次使用时初始化。 缓存的大小可以由-XX:AutoBoxCacheMax=<size>选项控制。 在VM初始化过程中，java.lang.Integer.IntegerCache.high属性可能会被设置并保存在sun.misc.VM类的私有系统属性中。
*/
private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];
```

```java
public class test {
    public static void main(String[] args) {
        Integer a = 1;
        Integer b = 1;
        System.out.println(a==b);//true
        System.out.println(a.equals(b));//true
        System.out.println(a.hashCode() == b.hashCode());//true
        
        Integer c = new Integer(1);
        Integer d = new Integer(1);
        System.out.println(c == d);//false
        System.out.println(c.equals(d));//true
        System.out.println(a.hashCode() == b.hashCode());//true
        
        Integer e = Integer.valueOf(1);
        Integer f = Integer.valueOf(1);
        System.out.println(e == f);//true
        System.out.println(e.equals(f));//true
        System.out.println(e.hashCode() == f.hashCode());//true
    }
}
```

## 十五、数字与字符串相加

1）不论有什么运算，小括号的优先级都是最高的，先计算小括号中的运算，得到x+y +""+25+y

2）任何字符与字符串相加都是字符串，但是是有顺序的，字符串前面的按原来的格式相加，字符串后面的都按字符串相加，得到25+“”+25+5

3）上面的结果按字符串相加得到25255

## 十六、访问限定修饰符

## 十七、java中的上下文类型推断

JVM编译器可以通过上下文推断出参数的类型，比如初始化String数组的时候，可以```String[] str = new String[]{"aaa","bbb"}```这样写，因为有类型推断，可以把后面传入的类型推断为String，但是如果这条语句的声明和赋值分开就会报错。

java8中的类型推断升级了。

## 18 boolean到低占多大内存

在Java中定义的八种基本数据类型中，除了其它七种类型都有明确的内存占用字节数外，就boolean类型没有给出具体的占用字节数，因为对虚拟机来说根本就不存在 boolean 这个类型，boolean类型在编译后会使用其他数据类型来表示，那boolean类型究竟占用多少个字节？网上答案基本有以下几种：
**1、1个bit**
理由是boolean类型的值只有true和false两种逻辑值，在编译后会使用1和0来表示，这两个数在内存中只需要1位（bit）即可存储，位是计算机最小的存储单位。
**2、1个字节**
理由是虽然编译后1和0只需占用1位空间，但计算机处理数据的最小单位是1个字节，1个字节等于8位，实际存储的空间是：用1个字节的最低位存储，其他7位用0填补，如果值是true的话则存储的二进制为：0000 0001，如果是false的话则存储的二进制为：0000 0000。
**3、4个字节**
理由来源是《Java虚拟机规范》一书中的描述：“虽然定义了boolean这种数据类型，但是只对它提供了非常有限的支持。在Java虚拟机中没有任何供boolean值专用的字节码指令，Java语言表达式所操作的boolean值，在编译之后都使用Java虚拟机中的int数据类型来代替，而boolean数组将会被编码成Java虚拟机的byte数组，每个元素boolean元素占8位”。这样我们可以得出boolean类型占了单独使用是4个字节，在数组中又是1个字节。

显然第三条是更准确的说法，那虚拟机为什么要用int来代替boolean呢？为什么不用byte或short，这样不是更节省内存空间吗。大多数人都会很自然的这样去想，我同样也有这个疑问，经过查阅资料发现，使用int的原因是，对于当下32位的处理器（CPU）来说，一次处理数据是32位（这里不是指的是32/64位系统，而是指CPU硬件层面），具有高效存取的特点。
原文链接：https://blog.csdn.net/qq_36763419/article/details/91392297

## 19 java自动类型转换

byte、short、int、char 有自己使用自己，没有向上转为最近的。char两个字节

## 20 long和float的类型转换问题

21 double = 两个整型相除

## 21 +=默认强制转换

![image-20211118155555606](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211118155555606.png)

## java Void类型

## java动态绑定：属性和方法

## java菱形继承

## java foreach增强for[循环原理](http://www.hollischuang.com/archives/1776)

## java 泛型擦除

## java泛型协变逆变

## JAVA的序列化框架、transition关键字

## java中的ThreadLocal
