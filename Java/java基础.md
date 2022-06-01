![image-20210926182033884](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20210926182033884.png)

## 一、abstract抽象类

* 抽象类不能被final关键字修饰。
* 抽象类不能被实例化。
* 抽象类可以没有抽象方法，但是具有抽象方法的类一定是抽象类。如果一个子类继承一个抽象类，子类没有实现父类的所有抽象方法，那么子类也要定义为抽象类，否则的话编译会出错的。
* **抽象类可以有非抽象的构造方法**，不能有抽象的构造方法（可能是因为抽象类可以具有属性，在抽象类中定义构造方法可以使的子类不必做重复的初始化工作）。

## abstract抽象方法

* 抽象方法不能被private修饰
* 抽象方法不能被static修饰

## 二、instanceof

* **如果 obj 不为 null 并且 (T) obj 不抛 ClassCastException 异常则该表达式值为 true ，否则值为 false 。**当用null来判断时，结果总是false。
* instanceof只能用来判断引用数据类型。
* instanceof可以用来判断这个类是否为判断类的实例对象、实现类或直接、间接的继承类。

## 三、数据类型自动提升

两个操作数进行加减乘除运算：

①所有的byte,short,char型的值将被提升为int型；

②如果有一个操作数是long型，计算结果是long型；

③如果有一个操作数是float型，计算结果是float型；

④如果有一个操作数是double型，计算结果是double型；

而声明为final的变量会被JVM优化，第6行相当于 b6 = 10，例如下图，最后的返回语句相当于return 5；

![image-20220224082906772](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220224082906772.png)

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

## String为什么声明为final

主要是为了安全性和效率

1. 为了线程安全

   因为字符串是不可变的，所以**是多线程安全的**，同一个字符串实例可以被多个线程共享。这样便不用因为线程安全问题而使用同步。字符串自己便是线程安全的。

2. 为了实现字符串池

3. 为了实现String可以创建HashCode不可变性

   因为字符串是不可变的，所以在它创建的时候**HashCode**就被缓存了，不需要重新计算。这就使得字符串很适合作为Map中的键，字符串的处理速度要快过其它的键对象。这就是HashMap中的键往往都使用字符串。

String类中的char[] 为什么为private final，因为为了要实现字符串常量池，如它不是private final的，那么就可以随意的更改它所执行的字符串，如果此时这个字符串在字符串常量池中，同时还有其他引用指向这个字符串，那么那个引用指向的字符串会被更改，因此需要把char[]数组声明为private final

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

虚拟机规范严格规定了有且只有六种情况必须立即对类进行“初始化”：

1. 使用new关键字实例化对象的时候、读取或设置一个类的静态字段的时候，已经调用一个类的静态方法的时候。
2. 使用java.lang.reflect包的方法对类进行反射调用的时候，如果类没有初始化，则需要先触发其初始化。
3. 当初始化一个类的时候，如果发现其父类没有被初始化就会先初始化它的父类。
4. 当虚拟机启动的时候，用户需要指定一个要执行的主类（就是包含main()方法的那个类），虚拟机会先初始化这个类；
5. 使用Jdk1.7动态语言支持的时候的一些情况。
5. 当一个接口中定义了JDK8新加入的默认方法（被default关键字修饰的接口方法）时，如果这个接口的实现类发生了初始化，那该接口要在其之前被初始化

除了这六种之外，其他的所有引用类的方式都不会触发初始化，称为被动引用。下面是被动引用的三个例子：

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

public，default，protected，private

![image-20220302130232288](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220302130232288.png)

## 十七、java中的上下文类型推断

JVM编译器可以通过上下文推断出参数的类型，比如初始化String数组的时候，可以```String[] str = {"aaa","bbb"}```这样写，因为有类型推断，可以把后面传入的东西的类型推断为String，但是如果这条语句的声明和赋值分开就会报错。

还有例如：

```java
ArrayList<String> list = new ArrayList<>();
```

这句话的后面的泛型括号可以不用写类型，因为可以根据上下文推断出类型。

![image-20220224092215559](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220224092215559.png)

如上所示用HashMap为参数的函数调用，这个函数调用可以不写泛型类型，因为可以通过函数的形参推断出泛型类型

java8中的类型推断升级了。

## 18 boolean到低占多大内存

在Java中定义的八种基本数据类型中，除了其它七种类型都有明确的内存占用字节数外，就boolean类型没有给出具体的占用字节数，因为对虚拟机来说根本就不存在 boolean 这个类型，boolean类型在编译后会使用其他数据类型来表示，那boolean类型究竟占用多少个字节？网上答案基本有以下几种：
**1、1个bit**
理由是boolean类型的值只有true和false两种逻辑值，在编译后会使用1和0来表示，这两个数在内存中只需要1位（bit）即可存储，位是计算机最小的存储单位。
**2、1个字节**
理由是虽然编译后1和0只需占用1位空间，但计算机处理数据的最小单位是1个字节，1个字节等于8位，实际存储的空间是：用1个字节的最低位存储，其他7位用0填补，如果值是true的话则存储的二进制为：0000 0001，如果是false的话则存储的二进制为：0000 0000。
**3、4个字节**
理由来源是《Java虚拟机规范》一书中的描述：“虽然定义了boolean这种数据类型，但是只对它提供了非常有限的支持。在Java虚拟机中没有任何供boolean值专用的字节码指令，Java语言表达式所操作的boolean值，在编译之后都使用Java虚拟机中的int数据类型来代替，而boolean数组将会被编码成Java虚拟机的byte数组，每个元素boolean元素占8位”。这样我们可以得出**boolean类型占了单独使用是4个字节，在数组中又是1个字节。**

显然第三条是更准确的说法，那虚拟机为什么要用int来代替boolean呢？为什么不用byte或short，这样不是更节省内存空间吗。大多数人都会很自然的这样去想，我同样也有这个疑问，经过查阅资料发现，使用int的原因是，对于当下32位的处理器（CPU）来说，一次处理数据是32位（这里不是指的是32/64位系统，而是指CPU硬件层面），具有高效存取的特点。
原文链接：https://blog.csdn.net/qq_36763419/article/details/91392297

## 19 java自动类型转换

byte、short、int、char 有自己使用自己，没有向上转为最近的。char两个字节

## 20 long和float的类型转换问题

21 double = 两个整型相除

## 21 +=默认强制转换

![image-20211118155555606](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211118155555606.png)

## java Void类型

Void类型不可以被继承和实例化，因为源码中这个类是final修饰的，同时构造函数私有化。

```java
public final class Void {

    /**
     * The {@code Class} object representing the pseudo-type corresponding to
     * the keyword {@code void}.
     */
    @SuppressWarnings("unchecked")
    public static final Class<Void> TYPE = (Class<Void>) Class.getPrimitiveClass("void");

    /*
     * The Void class cannot be instantiated.
     */
    private Void() {}
}
```

在泛型出来之前，Void类型一般被使用在反射中，用于获取返回值为void的方法：

```java
public class Test {
  public void print(String v) {}
  public static void main(String args[]){
    for(Method method : Test.class.getMethods()) {
      if(method.getReturnType().equals(Void.TYPE)) {
        System.out.println(method.getName());
      }
    }
  }
}
```

当出现了泛型之后，又有一种新的用法，当时用泛型函数时，不需要返回结果或者某个对象不需要值的时候，可以使用Void类型来占位，例如`Future<Void>和Map<T,Void>`



## java动态绑定：属性和方法

[好文](https://blog.csdn.net/sted_zxz/article/details/77980124)

在编译阶段就可以确定调用调用那个方法的方式，称为静态绑定机制。Java的方法中只有final，static，private和构造方法是静态绑定的，换句话说就是不能重写的方法是静态绑定的，如果方法可以被重写，那么就是动态绑定的

在程序运行的过程中，通过动态动态绑定主要就是用在可以被重写的方法上，动态绑定方法实现的**主要原理是方法表。**

**运行时动态绑定只针对的是对象的方法，对象的属性在运行时不会动态绑定，静态类型定义的是什么，在调用属性的时候就会调用那个类型的属性**

那么在这种情况下，我们如何动态调用属性呢?可以选择使用get set方法来实现动态调用属性

## java菱形继承

由于Java8 中接口可以有默认方法，因此如果一个类实现了两个接口，这两个接口中有方法签名一样的默认方法，那么这时就会出现菱形继承问题，通俗点来讲，就是通过这个实现类，jvm不知道到低要执行那个方法。

java8中解决这种冲突的办法：

* 类中方法优先，类中或者父类中声明的方法优先级高于接口中的默认方法
* 如果第一条没办法判断出来应该调用那个方法，那么子接口的方法优先级更高，优先选择子接口的默认方法
* 如果最后还是没有办法判断，那么继承了多个接口的类必须通过 **显示覆盖 和 期望调用**的方式

```java
public class Solution {
    public static void main(String[] args) throws IOException {
    }
    class D implements B,C {
        @Override
        public void say() {
            // 这种方式就是显示覆盖和期望调用
            B.super.say();
        }
    }
    interface A {
        default void say(){
            System.out.println("a");
        }
    }
    interface B extends A {
        @Override
        default void say(){
            System.out.println("b");
        }
    }
    interface C extends A {
        @Override
        default void say(){
            System.out.println("c");
        }
    }
}
```



## java foreach增强for[循环原理](http://www.hollischuang.com/archives/1776)

见jvm

## java 泛型

见jvm

## java泛型 协变逆变

泛型中的通配符：

* ？ 表示不确定的java类型
* T 表示一个具体的java类型
* K V分别表示java键值中的Key Value
* E （element）代表Element

？ 和 T的区别：

* ？ 和 T都可以表示不确定的类型，但是我们可以对T进行操作，不能对？ 进行操作，例如可以T t = ，但是不能 ？ t = 
* T是一个确定的类型，常常用于泛型类和泛型方·法的定义，？ 是一个不确定的类型，通常用于泛型方法的调用代码和形参，不能用于定义类和泛型方法
* 类型参数（T）可以**多重限定父类型而通配符不行**，也就是说T extends A & B合法，？extends A & B不合法(？是可以限定父类型的，只限定一个)
* 通配符可以使用超类限定，而类型参数不行，就是说 ？ super Object合法，而T super Object不合法



Class\<?>和Class\<T>的区别：

* ? 表示的是一个不确定的Java类型，因此通常用于声明时的限制，声明一个Class对象，但是不知道类型
* T 表示一个确定的类型，在实例化的时候，T需要替换为具体类

### 协变：

Java的泛型是不可变的，换句话说就是如果<>中的类型不一致，编译是不会通过的。

例如，我现在有一个方法中接收的参数是List A ，我想要实现只要是A的子类都可以以List的形式传递到这个函数中，但是由于java的泛型不支持自动向上转型（协变），因此，如果使用一个List B ，b是A的子类，作为实参传递给那个方法，那么编译就会报错。因此，此时我们需要通过无界通配符 ？ 然后配合 extends关键字实现泛型的向上转型。

因此有时需要实现协变，也就是在两个类型之间建立某个类型的向上转型关系，这时就需要使用通配符？了

```java
public class GenericsAndCovariance {
    public static void main(String[] args) {
        List<? extends Fruit> flist = new ArrayList<Apple>();
        flist.add(new Apple());  // 编译错误
        flist.add(new Fruit());  // 编译错误
        flist.add(new Object());  // 编译错误
    }
}
```

flist的类型是<? extends Fruit>, extends指出了泛型的上界Fruit，<? extends T>称为**子类通配符**，意味着某个继承自Fruit的具体类型。使用通配符可以将ArrayList\<Apple>向上转型，也就实现了协变。

这个和将String[]的引用，赋值给一个Object[]类型的引用一样，都实现了协变

但是经过这种协变之后，向列表中添加元素的操作被拒绝了，原因在于，List<? extends Fruit>类型的引用可以向Fruit的任意一个子类列表，显然这时候如果向里面添加和子类不符合的元素是非法的。但是编译器不知道List<? extends Fruit>所持有的具体的类型是什么，所以一旦执行了这种向上转型，这个类型本身就失去了传递任何对象的能力。

和数组一样，如果把Apple[]向上转型为Fruit[]，然而往里面添加Fruit和Orange等对象都是不合法的，会在运行时抛出ArrayStoreException异常。泛型把类型检查移到了编译器，协变过程丢掉了类型信息，编译器拒绝所有不安全的操作。

**<? extends T>表示存储类型都是T或者T的子类，但是获取元素的时候只能使用T或者T的父类，会失去存储对象的能力**

### 逆变

因为协变的话，规定了泛型的元素可以是T的子类，但是我们无法向泛型集合中添加元素，因为集合中是T的子类，并且这个拥有子类型的集合中肯定只有子类行一种，但是我们不知道是哪一种，所以无法实现向里面添加子类行。

但是如果想要实现能够添加元素的功能，我们可以使用逆变，就是使用一个超类通配符来实现。因为很显然，超类通配符的那个集合会指向一个T的父类型的集合，那么我们就可以肆无忌惮的往里面添加T类型的子类，不管这个父类型的集合是什么 ，用来实现生产者。

```java
public class SuperTypeWildcards {
    static void writeTo(List<? super Apple> apples) {
        apples.add(new Apple());
        apples.add(new Jonathan());
        apples.add(new Fruit());  // 编译错误
    }
}
```

使用关键字super指出泛型的下界Apple，<? super T>称为超类通配符，代表一个具体类型，而这个类型是T的父类。这样编译器就知道向其中添加Apple或者Apple的子类型是安全的了，但是既然Apple是下界，那么就可以知道向这样的List中添加Fruit是不安全的。

**下限通配符<? super T>表示所存储的类型为T及其父类，但是添加元素类型只能为T及其子类，而获取元素所使用的类型只能是Object，因为Object为所有类的基类，**

### PECS

那么什么时候使用extends，什么时候使用super呢——producer-extends，consumer-super（PECS）

说的直白一点，从数据流来看，extends是限制数据来源（生产者），而super是限制数据流入的（消费者）

例如**java.util.Collections的copy方法**：

```java
// Collections.java
public static <T> void copy(List<? super T> dest, List<? extends T> src) {
    int srcSize = src.size();
    if (srcSize > dest.size())
        throw new IndexOutOfBoundsException("Source does not fit in dest");

    if (srcSize < COPY_THRESHOLD ||
        (src instanceof RandomAccess && dest instanceof RandomAccess)) {
        for (int i=0; i<srcSize; i++)
            dest.set(i, src.get(i));
    } else {
        ListIterator<? super T> di=dest.listIterator();
        ListIterator<? extends T> si=src.listIterator();
        for (int i=0; i<srcSize; i++) {
            di.next();
            di.set(si.next());
        }
    }
}
```

**copy方法限制了拷贝源src必须是T或者是它的子类，而拷贝目的地dest必须是T或者是它的父类，这样就保证了类型的合法性。**



## JAVA的序列化框架、transition关键字

序列化：将Java对象转化为字节数组

反序列化：将字节数组转化为Java对象



## java中的strictfp

一旦使用了strictfp来声明一个类，接口或者方法时，那么所声明的范围内Java的编译器以及运行环境会完全依照浮点规范IEEE-754来执行，因此如果想让浮点运算更加精确，而且不会因为不同硬件平台所执行的结果不一致的话，那就可以使用strictfp关键字，这个关键字所声明的范围内所有浮点运算都是精确的。

可以将一个类，接口，以及方法声明为stricfp，但是不允许对接口中的方法以及构造函数声明strictfp关键字，

## Java 反射原理

反射机制的优点：

* 让我们在运行时有了分析操作类的能力，可以让代码更灵活，为各种框架提供开箱即用的功能提供了便利

缺点：

* 安全问题，例如可以无视泛型参数的安全检查（泛型参数的安全检查发生在编译时）。另外，反射的性能也要差点，不过，对于框架来说实际影响不是很大。

例如可以通过反射跳过编译器的类型检查，然后向其中添加不符合类型约定的类型

```java
public class Test {
    public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        List<Integer> list = new ArrayList<>();
        Class<List> clazz = (Class<List>) list.getClass();
        Method add = clazz.getMethod("add", Object.class);
        add.invoke(list, "liu");
        System.out.println(list.get(0));

    }
}
```

java 注解





## mysql为什么要加载驱动，同时为什么可以不用加载

首先，我们会使用Class.forname(com.mysql.cj.jdbc.Driver)加载类com.mysql.cj.jdbc.Driver，然后在执行这个类加载的过程中，最重要的步骤就是执行这个类的静态代码块：

```java
public class Driver extends NonRegisteringDriver implements java.sql.Driver {
    //
    // Register ourselves with the DriverManager
    //
    static {
        try {
            java.sql.DriverManager.registerDriver(new Driver());
        } catch (SQLException E) {
            throw new RuntimeException("Can't register driver!");
        }
    }

    /**
     * Construct a new driver and register it with DriverManager
     * 
     * @throws SQLException
     *             if a database error occurs.
     */
    public Driver() throws SQLException {
        // Required for Class.forName().newInstance()
    }
}
```

以上使用class.forname的方式也可以使用new Driver的方式来实现，但是我们为什么不使用new的方式来实现相同的功能呢？

主要的原因就是解耦，因为如果使用new的方式，就把加载数据库驱动的过程以代码的方式耦合到自己的代码当中，会对这个具体的类产生依赖，但是如果我们使用反射的方式，只需要在配置文件中，更改相应的驱动和url即可。

但是我们会发现，当把Class.forName 去掉之后，driver还是可以注册到driverManager中的，这到底是为什么？

因为在JDBC4.0以后新增了新特性：JDBC4.0不在需要显示调用class.forName注册驱动，DriverManager初始化中会通过ServiceLoader类，在我们的classPath中jar（数据库驱动包）中查找，使用META-INFO\services\java.sql.Driver文本中的类名称去注册，也就是说，在启动的时候，通过jar包下面的java.sql.Driver里面的文本内容，帮你把驱动给加载了。

## a+=b和a = a+b的区别

这两个操作之间存在类型转换的问题

a = a+b在进行a+b的操作的时候，如果a和b类型不匹配并且能够进行操作，那么a+b会进行隐式类型转换，如果转换后的类型比a的类型大，那么编译器就会报错

![image-20220307090527551](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220307090527551.png)

相反的，+=在java中是一个运算符，在进行运算的时候会自动将a的类型转换，不会出现赋值类型不匹配的问题。

## a++和++a jvm实现的不同

a++如果要赋值给一个变量b，会首先将局部变量表中的变量加载到操作数栈上，然后在将局部变量表中的a执行iinc操作，最后再弹出操作数栈上的a赋值给b

++a首先将局部变量表中的a执行incr操作，直接加一，然后再放入操作数栈上，最后pop赋值给局部变量表中表示b变量的槽位

## serialVersionUID的作用

如果一个类实现了java.io.Serializable接口，但是没有显示的设置serialVersionUID，那么我们在序列化这个类之后，如果对这个类中的字段做了改变，那么再次反序列化的时候，程序就会报错，InvalidClassException，类型不匹配。

报这个错的主要原因就是当我们更改已经序列化好的对象对应的类后，如果在没有显示设置serialVersionUID的情况下，类的serialVersionUID会发生改变，因为此时系统会自动生成serialVersionUID，当我们改变类的时候，自动生成的serialVersionUID也会改变。

如果我们显示声明了serialVersionUID，那么当我们更改类之后，再次反序列化，不会报错。

serialVersionUID是为了兼容不同的版本而设定的。

serialVersionUID如何生成的？

利用JDK bin目录下的serialver.exe工具生成，对于Student.class，执行命令：serialver Student，就可以生成

或者使用代码来生成，使用代码生成的主要原理就是更具类文件中的类的类名，属性名，属性修饰符，继承的接口，属性类型，名称，方法，静态代码块等等...这些都考虑进去了，都写到一个`DataOutputStream`中，然后再做`hash运算。

```java
ObjectStreamClass c = ObjectStreamClass.lookup(Student.class);
long serialID = c.getSerialVersionUID();
System.out.println(serialID);
```



