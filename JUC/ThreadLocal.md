# ThreadLocal

ThreadLocal类用来提供线程内部的局部变量，这种变量在多线程环境下访问（通过get和set方法访问）时，能保持各个线程的变量相对独立于其他线程内的变量。ThreadLocal实例通常来说都是private static类型的，用于关联线程的线程上下文。

ThreadLocal的作用是：提供线程内局部变量，不同线程之间不会相互干扰，这种变量在线程的生命周期内起作用，减少同一个线程内多个函数或组件之间一些公共变量传递的复杂度。

总结：

* 线程并发：在多线程并发场景下
* 传递数据：我们可以通过ThreadLocal在同一线程，不同组件中传递公共变量
* 线程隔离：每个线程的变量都是独立的，不会互相影响。

## 源码分析

```java
public class ThreadLocal<T> {
    // 哈希映射，每个ThreadLocal独一份，final变量，这是一个自定义的哈希码，它消除了在相同线程使用连续构造的ThreadLocals的常见情况下的冲突，同时在不常见的情况下保持良好行为
    private final int threadLocalHashCode = nextHashCode();
    // 下一个要给出的哈希码，原子更新，初始值为0。由于是static变量，类中只有一份，因此每次创建一个ThreadLocal对象，它的ThreadLocalHashCode都不一样
    private static AtomicInteger nextHashCode = new AtomicInteger();
    // 连续生成哈希码之间的差值
    private static final int HASH_INCREMENT = 0x61c88647;
    // 返回下一个hash值
    private static int nextHashCode() {
        return nextHashCode.getAndAdd(HASH_INCREMENT);
    }
    // 返回此线程局部变量的当前线程的初始值。如果程序员希望线程局部变量具有除null以外的初始值，则必须将ThreadLocal子类化，并重写此方法，通常使用匿名内部类。
    protected T initialValue() {
        return null;
    }
    // 创建一个ThreadLocal，初始值是supplier提供的
    public static <S> ThreadLocal<S> withInitial(Supplier<? extends S> supplier) {
        return new SuppliedThreadLocal<>(supplier);
    }
    // 无参构造函数
    public ThreadLocal() {
    }
}
```

使用匿名内部类的方式重写了initialvalue方法，解决SimpleDateFormat线程不安全的问题

![image-20220208091614282](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220208091614282.png)

### ThreadLocal的get方法

1. 获取当前线程
2. 获取线程的字段threadLocals
3. 如果ThreadLocals为null，那么调用setInitialvalue方法设置初始值返回
4. 如果threadLocals不为null，获取ThreadLocal对象对应的Entry
   1. 如果通过HashThreadLocal对象对应的Entry不为null，且key值等于ThreadLocal对象，直接返回
   2. 否则，有可能发生了hash冲突，此时调用getEntryAfterMiss方法寻找Entry
   3. 在寻找的过程中如果遇到key等于ThreadLocal直接返回，
   4. 如果遇到key为null的，调用expungeStaleEntry方法，删除过期的Entry
      1. expungeStaleEntry方法中，不仅删除过期的Entry，还重新散列其他发生了hash冲突的Entry
   5. 否则table的坐标加一继续判断
5. 如果getEntryAfterMiss找到，则返回，未找到返回null

```java
// 返回此线程局部变量的当前线程副本中的值。如果变量没有当前线程的值，则首先将其初始化为调用 initialValue 方法返回的值
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}
// Thread类中的字段
ThreadLocal.ThreadLocalMap threadLocals = null;
// Thread类中的方法
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}
// ThreadLocalMap中的方法，
private Entry getEntry(ThreadLocal<?> key) {
    int i = key.threadLocalHashCode & (table.length - 1);
    Entry e = table[i];
    if (e != null && e.get() == key)
        return e;
    else
        return getEntryAfterMiss(key, i, e);
}
// ThreadLocalMap中的方法，由于可能出现hash冲突，所以如果第一次没有找到Entry，那么就使用再散列的方式去接着寻找ThreadLocal对应的Entry，同时清除table中key为null的Entry
private Entry getEntryAfterMiss(ThreadLocal<?> key, int i, Entry e) {
    Entry[] tab = table;
    int len = tab.length;

    while (e != null) {
        ThreadLocal<?> k = e.get();
        if (k == key)
            return e;
        if (k == null)
            expungeStaleEntry(i);
        else
            // 如果存在Hash冲突，再散列是通过index加一实现的
            i = nextIndex(i, len);
        e = tab[i];
    }
    return null;
}
//删除entry的key为null的entry，因为remove函数其实就是将key的值设置为null，又或者由于GC使得key为弱引用的ThreadLocal被回收了这样key也为null。然后再调用这个函数真正删除entry。 通过重新散列位于 staleSlot 和下一个空槽之间的任何可能冲突的条目来删除过时的条目
private int expungeStaleEntry(int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;

    // expunge entry at staleSlot
    tab[staleSlot].value = null;
    tab[staleSlot] = null;
    size--;

    // Rehash until we encounter null
    Entry e;
    int i;
    for (i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {
        ThreadLocal<?> k = e.get();
        if (k == null) {
            e.value = null;
            tab[i] = null;
            size--;
        } else {
            // 再散列ThreadLocal
            int h = k.threadLocalHashCode & (len - 1);
            if (h != i) {
                tab[i] = null;

                // Unlike Knuth 6.4 Algorithm R, we must scan until
                // null because multiple entries could have been stale.
                while (tab[h] != null)
                    h = nextIndex(h, len);
                tab[h] = e;
            }
        }
    }
    return i;
}

private T setInitialValue() {
    T value = initialValue();
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
    return value;
}
```

### ThreadLocal的set方法

* 如果当前ThreadLocal的threadLocals变量不为null
  * 如果通过hash算法以及再散列找到课key为ThreadLocal的Entry，那么直接设置value
  * 如果在寻找的过程中遇到key为null的Entry
    * 调用replaceStaleEntry方法清除前面和后面的过期的Entry
  * 如果寻找完之后都没有找到key为ThreadLocal的Entry，那么直接设置value
  * 同时通过一次清除操作之后如果容量大于阈值，那么扩容
    * 以2倍进行扩容再散列
* 如果当前ThreadLocal的threadLocals变量为null，创键threadLocals属性并设置value

```java
// 将此线程局部变量的当前线程副本设置为指定值。大多数子类不需要重写此方法，仅依靠 initialValue 方法来设置线程局部变量的值
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}
// ThreadLocalMap的方法
private void set(ThreadLocal<?> key, Object value) {
    Entry[] tab = table;
    int len = tab.length;
    int i = key.threadLocalHashCode & (len-1);

    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
        ThreadLocal<?> k = e.get();

        if (k == key) {
            e.value = value;
            return;
        }

        if (k == null) {
            replaceStaleEntry(key, value, i);
            return;
        }
    }

    tab[i] = new Entry(key, value);
    int sz = ++size;
    if (!cleanSomeSlots(i, sz) && sz >= threshold)
        rehash();
}
// 
private void replaceStaleEntry(ThreadLocal<?> key, Object value, int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;
    Entry e;
    // 从当前的slot向前寻找第一个Entry不为null并且key还等于null的slot
    int slotToExpunge = staleSlot;
    for (int i = prevIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = prevIndex(i, len))
        if (e.get() == null)
            slotToExpunge = i;

    // Find either the key or trailing null slot of run, whichever
    // occurs first
    for (int i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {
        ThreadLocal<?> k = e.get();

        // If we find key, then we need to swap it
        // with the stale entry to maintain hash table order.
        // The newly stale slot, or any other stale slot
        // encountered above it, can then be sent to expungeStaleEntry
        // to remove or rehash all of the other entries in run.
        if (k == key) {
            e.value = value;

            tab[i] = tab[staleSlot];
            tab[staleSlot] = e;

            // Start expunge at preceding stale entry if it exists
            if (slotToExpunge == staleSlot)
                slotToExpunge = i;
            cleanSomeSlots(expungeStaleEntry(slotToExpunge), len);
            return;
        }

        // If we didn't find stale entry on backward scan, the
        // first stale entry seen while scanning for key is the
        // first still present in the run.
        if (k == null && slotToExpunge == staleSlot)
            slotToExpunge = i;
    }

    // If key not found, put new entry in stale slot
    tab[staleSlot].value = null;
    tab[staleSlot] = new Entry(key, value);

    // If there are any other stale entries in run, expunge them
    if (slotToExpunge != staleSlot)
        cleanSomeSlots(expungeStaleEntry(slotToExpunge), len);
}
// 创建一个存储当前ThreadLocal对象的ThreadLocalMap
void createMap(Thread t, T firstValue) {
    // this 代表的是当前的ThreadLocal对象
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```

## 为什么要使用弱引用

首先，无论Entry的key使用强引用还是弱引用都无法避免内存泄漏，因为只要Entry在，那么value的内存就不会释放，因此要避免内存泄漏的方法有两种：

* 使用完ThreadLocal，使用remove方法删除对用的Entry
* 使用完ThreadLocal，其线程页随之结束

也就是说，只要记得在使用完ThreadLocal及时的调用remove，无论key是强引用还是弱引用都不会有问题。那么为什么key要用弱引用呢？

事实上，在ThreadLocalMap中的set/getEntry方法中，会对key为null（也即是ThreadLocal为null）进行判断，如果为null的话，那么是会对value置为null的。

这就意味着使用完ThreadLocal，CurrentThread依然运行的前提下，就算忘记调用remove方法，**弱引用比强引用可以多一层保障**：弱引用的ThreadLocal会被回收，对应的value在下一次ThreadLocalMap调用set,get,remove中的任一方法的时候会被清除，从而避免内存泄漏。

## hash冲突的解决

首先定义了一个AtomicInteger类型，每次获取当前值并加上HASH_INCREMENT，`HASH_INCREMENT = 0x61c88647`,这个值跟斐波那契数列（黄金分割数）有关，其主要目的就是为了让哈希码能均匀的分布在2的n次方的数组里, 也就是Entry[] table中，这样做可以**尽量避免hash冲突。**

 其次如果发生了hash冲突，ThreadLocalMap使用`线性探测法`来解决哈希冲突的。