# JUC和Synchronized对比

Synchronized存在以下问题：

* Synchronized不能控制阻塞，不能灵活控制锁的释放（无法控制阻塞时长，以及线程不可中断）

  使用Synchronized同步锁，线程会在一下三种情况释放锁：

  1. 线程完成同步代码块，方法，释放锁
  2. 线程执行时发生异常，此时JVM会让线程自动释放锁
  3. 在同步代码块/方法中，锁对象执行了wait方法，线程释放锁

* 在读多写少的场景中，效率低下，因为读线程本来是可以共享资源的，但是在使用的互斥锁之后，读线程去读共享资源也要先获取锁，

Lock锁解决的Synchronized问题的方式：

* 可以使用tryLock(long time, TimeUnit unit)接口以及lockInterruptibly接口实现控制阻塞时长以及实现线程在等待锁的过程中可释放
* 使用ReentryReadWriteLock可以解决Synchronized在读多写少场景下的效率低下问题

# Lock接口

Lock接口和相关实现类可以用来实现锁功能，它提供了synchronized关键字类似的同步功能，只是在使用时需要显式地获取和释放锁。

虽然它去少了隐式获取释放锁的便捷性，但是却拥有了锁获取和释放的可操作性性，可中断性以及超时获取锁等多种synchronized关键字不具备的同步特性。

Lock的使用方式：

```java
Lock lock = new ReentrantLock();
lock.lock();
try{
}finally{
	lock.unlock();
}
```

在finally块中释放锁，目的是保证在获取到锁之后，最终能够被释放。

获取锁的过程不要写在try中，因为如果在获取锁时发生了异常，异常抛出的同时也会造成锁的无故释放。

Lock接口相比于synchronized的特性：

* 尝试非阻塞的获取锁
* 能被中断的获取锁
* 超时获取锁

Lock接口API详情：

```java
public interface Lock {
    // 阻塞获取锁，调用该方法当前线程将会获取锁，当锁获得后，从该方法返回，如果锁被其他线程获取，线程就会一直等待直到成功获取锁
    void lock();
    // 可中断地获取锁，和lock方法的不同之处在于该方法会相应中断，即在锁的获取中可以中断当前线程
    void lockInterruptibly() throws InterruptedException;
    // 尝试非阻塞获取锁，调用该方法后立即返回，如果能够获取则返回true，否则返回false
    boolean tryLock();
    // 超时获取锁，当前线程在一下三种情况下会返回：
    //	1.当前线程在超时时间内获取到锁
    //  2.当前线程在超时时间内被中断
    //  3.超时时间结束，返回false
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    // 释放锁
    void unlock();
    // 获取等待通知组件，该组件和当前的锁绑定，当前线程之后获得了锁，产能调用该组件的wait方法，而调用后当前线程释放锁
    Condition newCondition();
}
```

# AbstractOwnableSynchronizer

用来设置或者获取独占锁的拥有者线程

# 队列同步器AQS

AbstractQueuedSynchronizer提供了一个框架来实现依赖于先进先出等待队列的阻塞锁和相关同步器（信号量，events等），是用来构建锁和其他同步组件的基础框架。他使用一个int成员变量表示状态，并通过内置的FIFO队列来完成组员获取线程的排队工作。

AQS提供了一套通用的机制来管理同步状态、阻塞/唤醒线程、管理等待队列等。

子类应该被定义为非公共静态内部类，用于实现其封闭类的同步属性。

子类主要实现这样一些方法（在AQS中默认抛出UnsupportedOperationException）：

* tryAcquire
* tryRelease
* tryAcquireShared
* tryReleaseShared
* isHeldExclusively

此类支持默认独占模式和共享模式中的一种或两种。不同模式下等待的线程公用一个FIFO队列。

## 同步队列中的结点Node

```java
static final class Node {
    /** Marker to indicate a node is waiting in shared mode */
    static final Node SHARED = new Node();
    /** Marker to indicate a node is waiting in exclusive mode */
    static final Node EXCLUSIVE = null;

    /** waitStatus value to indicate thread has cancelled */
    static final int CANCELLED =  1;
    /** waitStatus value to indicate successor's thread needs unparking */
    static final int SIGNAL    = -1;
    /** waitStatus value to indicate thread is waiting on condition */
    static final int CONDITION = -2;
    /**
     * waitStatus value to indicate the next acquireShared should
     * unconditionally propagate
     */
    static final int PROPAGATE = -3;

    volatile int waitStatus;

    volatile Node prev;

    volatile Node next;

    volatile Thread thread;

    Node nextWaiter;

    final boolean isShared() {
        return nextWaiter == SHARED;
    }

    final Node predecessor() throws NullPointerException {
        Node p = prev;
        if (p == null)
            throw new NullPointerException();
        else
            return p;
    }

    // 用于初始化head结点以及SHARED结点的构造函数
    Node() {
    }

    Node(Thread thread, Node mode) {     // Used by addWaiter
        this.nextWaiter = mode;
        this.thread = thread;
    }

    Node(Thread thread, int waitStatus) { // Used by Condition
        this.waitStatus = waitStatus;
        this.thread = thread;
    }
}
```

## 重要属性

```java
// 等待队列的头部，延迟初始化。除了初始化外，只能通过setHead方法进行修改。注意：如果head存在，它的waitStatus保证不会被cancelled
private transient volatile Node head;
// 等待队列的尾部，延迟初始化。仅通过方法enq修改以添加新的等待结点
private transient volatile Node tail;
// 同步器的状态
private volatile int state;
```

## 同步队列

AQS依赖内部的同步队列（一个FIFO双向队列）来完成同步状态的管理，当前线程获取同步状态失败时，同步器会将当前线程以及等待状态等信息构造成一个结点（Node）并将其加入同步队列，同时会阻塞当前线程，当同步状态释放时，会把首结点中的线程唤醒，使其再次尝试获取同步状态。

同步队列中的结点（Node）用来保存获取同步状态失败的线程引用，等待状态，以及前驱和后继节点，节点的属性如下：

* waitStatus：等待状态。
  * Cancelled，值为1，由于在等待队列中等待的线程等待超时或者被中断，需要从同步队列中取消等待，界定进入该状态将不会变化
  * SIGNAL，值为-1，后继结点的线程处于等待状态，而当前结点的线程如果释放了同步状态或者被取消了，将会通知后继结点，使后继结点的线程得以运行
  * CONDITION，值为-2，结点在等待队列中，结点线程等待在Condition上，当其他线程对Condition调用了signal方法后，该结点会从等待队列中转移到同步队列上，加入到对同步状态的获取中
  * PROPAGATE，值为-3，表示下一次共享式同步状态获取将无条件被传播下去
  * INITIAL，值为0，初始状态

## 独占式同步状态获取与释放

acquire-->tryAcquire(模板方法，需要子类实现)-->addWaiter(Node.EXCLUSIVE)-->enq-->acquireQueued-->tryAcquire

### acquire方法的执行步骤：

首先调用子类实现的tryAcquire方法线程安全的获取同步状态，子类中实现的这个方法必须是线程安全地获取同步状态。

```java
public final void acquire(int arg) {
  	// 调用子类实现的tryAcquire方法，此方法必须保证线程安全的获取同步状态
    if (!tryAcquire(arg) &&
        // 如果同步状态获取失败，构造同步结点并通过addWaiter方法将该结点加入到同步队列尾部，最后调用acquireQueued方法，使得该结点以“死循环”的方式获取同步状态，如果再次获取同步状态没有获取到，则阻塞结点中的线程，而被阻塞的线程的唤醒主要依靠前驱结点的出队或者阻塞线程被中断来实现
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        // 如果线程是被中断唤醒的，那么执行中断方法。
        selfInterrupt();
}
```

* 如果成功，则直接返回，线程获取到同步状态。

* 如果失败：

  * 通过addWaiter向同步队列尾部添加结点
    * 如果尾结点不为null，同时使用CAS**快速**添加尾结点成功，直接返回添加的结点
    * 如果尾结点为null，或者CAS添加失败，调用**循环添加尾结点**enq方法，创建头结点和尾结点并循环使用CAS添加结点，结点添加成功后，返回添加的结点
    
    ```java
    private Node addWaiter(Node mode) {
        Node node = new Node(Thread.currentThread(), mode);
        // Try the fast path of enq; backup to full enq on failure
        Node pred = tail;
        if (pred != null) {
            node.prev = pred;
            // 底层调用unsafe类的CAS原子操作Object
            if (compareAndSetTail(pred, node)) {
                pred.next = node;
                return node;
            }
        }
        // 如果tail为null，则创建；如果CAS操作失败，则死循环设置
        enq(node);
        return node;
    }
    private final boolean compareAndSetTail(Node expect, Node update) {
        return unsafe.compareAndSwapObject(this, tailOffset, expect, update);
    }
    // 通过我们传入的字段在对象（thisObject）中的偏移量（offset）来获取字段的地址（对象首地址+字段在对象中的偏移量），然后调用CompareAndSwap方法比较字段的地址是否和我们期望的地址（expect）相等，如果相等则使用我们新传入的新地址（update）更新字段的地址
    public final native boolean compareAndSwapObject(Object thisObject, long offset, Object expect, Object update);
    // 通过死循环来保证结点的正确添加
    private Node enq(final Node node) {
        for (;;) {
            Node t = tail;
            // 必须保证头结点指向的是一个空的结点，也就是头结点指向的一个是占位的结点dummy
            if (t == null) { // Must initialize
                if (compareAndSetHead(new Node()))
                    tail = head;
            } else {
                // 将node的prev设置为dummy
                node.prev = t;
                // 将tail指向node
                if (compareAndSetTail(t, node)) {
                    // 此时的t是原来的dummy，将t的next指向现在的尾结点，dummy
                    t.next = node;
                    return t;
                }
                // 将node的prev设置为dummy以及把dummy的next设置为node这两个操作为什么要分开？我认为如果这两个操作在一起，如果在CAS之前，那么就会出现并发错误，例如已经将dummy的next设置为node1以及将node1的prev设置为dummy，但是此时线程上下文切换，如果CASnode2 成功，链表就断开了；至于为什么不把这两个操作都放到CAS之后，我感觉可能是想要提升CAS之后操作的效率问题。
            }
        }
    }
    ```
    
  * 通过addWaiter方法向同步队列尾部添加完结点之后，结点被返回，
    
  * 同时acquireQueued方法被调用，此方法不会检查结点中的线程是否是被中断唤醒的。
    
    * 如果当前线程在同步队列中的node的前驱结点为头结点head同时调用tryAcquire方法成功获取同步状态，那么调用setHead方法（因为此时调用setHead方法的线程只可能有一个，因此不需要做线程的同步）将当前结点设置为头结点head，返回中断状态
    * 如果上述条件不成立，那么会调用shouldParkAfterFailedAcquire方法检查当前结点是否可以被阻塞（前驱结点是否设置好）
      * 如果前驱结点的状态为SIGNAL，当前线程可以被放心阻塞，返回true
      * 如果是Cancelled，先前扫描链表把cancelled状态的结点从同步队列中移除，知道当前结点的前驱结点的状态小于0，返回false
      * 如果前驱结点的状态为默认状态或propagate，那么使用CAS设置结点状态为SIGNAL，然后返回false
    * 如果shouldParkAfterFailedAcquire结果返回false，说明当前结点还不能被阻塞，前驱结点的状态还没有准备好，那么就会在acquireQueued方法中循环，尝试获取锁，如果还是没有获取到，再次调用shouldParkAfterFailedAcquire判断是否需要阻塞（如果是因为前驱结点的waitStatus是0或者propa，那么这一次调用一定可以返回true，因为如果前驱结点的waitStatus值为0或者propagate，会CAS设置结点状态为SIGNAL）
    * 如果检查结果返回true，那么当前结点可以被阻塞，调用parkAndCheckInterrupt方法阻塞并检查线程是否被中断，返回线程是否被中断（true or false）
      * 如果线程被中断，那么parkAndCheckInterrupt方法返回true，同时acquireQueued中的中断状态被设置为true
      * 如果线程被unpark唤醒，那么不会设置中断状态，线程继续在acquireQueued方法中循环获取同步状态。
    * 如果子类实现的tryAcquire方法在acquireQueued中执行时抛出了异常，或者acquireQueued，那么会执行finally中的cancelAcquire方法
      * **cancelAcquire方法的执行还没有搞懂**[cancelAcquire源码详解](https://www.jianshu.com/p/01f2046aab64)

* 如果acquireQueued返回true，表示线程中间被中断唤醒，那么调用selfInterrupt方法手动再次中断线程

当进入同步队列之后，节点就进入一个自旋过程，每个结点（线程）都在自省地观察，当条件满足，获取到了同步状态，就可以从这个自旋过程中退出，否则依旧留在这个自旋过程中（并会阻塞结点的线程）。

```java
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            final Node p = node.predecessor();
            // 如果当前结点的前驱结点是head并且调用子类的tryAcquire成功获取同步状态，那么
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
// 不断找安全点，如果已在安全点，返回true，否则找到安全点，并返回false
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    int ws = pred.waitStatus;
    if (ws == Node.SIGNAL)
        /*
		当前驱结点的waitStatus值为SIGNAL的时候，后继结点的线程处于等待状态，而当前结点的线程如果释放了同步状态或者被取消，将会通知后继结点，使后继结点继续运行，因此此时如果park当前结点，前驱结点已经准备好了，所以可以park当前结点
        */
        return true;
    if (ws > 0) {
        /*
        如果前驱结点的状态大于0，也就是为Cancelled，则需要取消该结点，因此使用循环将前驱结点移出队列
        */
        do {
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else {
        /*
        此时waitStatus一定是0或者Propagate，表明
             * waitStatus must be 0 or PROPAGATE.  Indicate that we
             * need a signal, but don't park yet.  Caller will need to
             * retry to make sure it cannot acquire before parking.
             */
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}
private final boolean parkAndCheckInterrupt() {
    LockSupport.park(this);
    return Thread.interrupted();
}
```

### cancelAcquire的操作

cancelAcquire需要对目前的node做两种操作：

* 清理状态
  * node不再关联任何线程，
  * node的waitStatus设置为cancelled

```java
private void cancelAcquire(Node node) {
    // 如果结点为null，直接忽略
    if (node == null)
        return;
    node.thread = null;
    // 跳过取消的前驱结点
    Node pred = node.prev;
    while (pred.waitStatus > 0)
        node.prev = pred = pred.prev;
    // predNext is the apparent node to unsplice. CASes below will
    // fail if not, in which case, we lost race vs another cancel
    // or signal, so no further action is necessary.
    Node predNext = pred.next;

    // Can use unconditional write instead of CAS here.
    // After this atomic step, other Nodes can skip past us.
    // Before, we are free of interference from other threads.
    node.waitStatus = Node.CANCELLED;

    // If we are the tail, remove ourselves.
    if (node == tail && compareAndSetTail(node, pred)) {
        compareAndSetNext(pred, predNext, null);
    } else {
        // If successor needs signal, try to set pred's next-link
        // so it will get one. Otherwise wake it up to propagate.
        int ws;
        if (pred != head &&
            ((ws = pred.waitStatus) == Node.SIGNAL ||
             (ws <= 0 && compareAndSetWaitStatus(pred, ws, Node.SIGNAL))) &&
            pred.thread != null) {
            Node next = node.next;
            if (next != null && next.waitStatus <= 0)
                compareAndSetNext(pred, predNext, next);
        } else {
            unparkSuccessor(node);
        }

        node.next = node; // help GC
    }
}
```



### release方法的执行步骤：

执行释放锁的方法release后，会唤醒其后继节点（进而使后继节点重新尝试获取同步状态）

首先调用子类实现的tryRelease方法尝试释放同步状态

* 如果成功，调用unparkSuccessor方法唤醒后继结点
  * 首先改变当前结点的状态为0（如果不为0的话）
  * 然后判断当前结点的后继结点是否为null或者状态为取消结点，寻找到第一个正常的结点
  * 如果找到一个正常状态的后继节点，使用unpark唤醒
* 如果失败，返回false

```java
public final boolean release(int arg) {
    if (tryRelease(arg)) {
        Node h = head;
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}
private void unparkSuccessor(Node node) {
    /*
         * If status is negative (i.e., possibly needing signal) try
         * to clear in anticipation of signalling.  It is OK if this
         * fails or if status is changed by waiting thread.
         */
    int ws = node.waitStatus;
    if (ws < 0)
        compareAndSetWaitStatus(node, ws, 0);

    /*
	如果当前结点的后继结点为null或者是取消的，那么让后继结点出队列
    */
    Node s = node.next;
    if (s == null || s.waitStatus > 0) {
        s = null;
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    if (s != null)
        LockSupport.unpark(s.thread);
}
```



## 共享式同步状态获取与释放

acquireShared-->tryAcquireShared-->doAcquireShared-->addWaiter(Node)-->

### acquireShared方法的执行步骤

* 首先调用子类实现的模板方法tryAcquireShared尝试获取共享锁
  * 如果返回值小于0 ，表示获取共享锁失败，进入doAcquireShared方法自循环获取共享锁
  * 如果返回值大于等于0，表示获取共享锁成功，直接返回
* 在doAcquireShared方法中，会调用addWaiter方法将共享结点加入同步队列当中
  * 如果加入当前线程构造的结点是头结点的下一个结点，并且调用tryAcquireShared方法成功获取共享锁，那么调用setHeadAndPropagate[方法设置头结点](https://blog.csdn.net/weixin_36586120/article/details/108642253)

```java
public final void acquireShared(int arg) {
    if (tryAcquireShared(arg) < 0)
        doAcquireShared(arg);
}
private void doAcquireShared(int arg) {
    // addWaiter方法 放到这里了
    final Node node = addWaiter(Node.SHARED);
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            final Node p = node.predecessor();
            if (p == head) {
                int r = tryAcquireShared(arg);
                if (r >= 0) {
                    // 设置头结点的方式不一样
                    setHeadAndPropagate(node, r);
                    p.next = null; // help GC
                    // 自己中断自己的方法放到这里，和独占式同步其实一样
                    if (interrupted)
                        selfInterrupt();
                    failed = false;
                    return;
                }
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
private void setHeadAndPropagate(Node node, int propagate) {
    Node h = head; // Record old head for check below
    setHead(node);
    /*
    当propagate大于零，也就是共享的同步状态还有空间，
	*/
    if (propagate > 0 || h == null || h.waitStatus < 0 ||
        (h = head) == null || h.waitStatus < 0) {
        Node s = node.next;
        if (s == null || s.isShared())
            doReleaseShared();
    }
}
```

### releaseShared方法的执行步骤

```java
public final boolean releaseShared(int arg) {
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}
```



```java
private void doReleaseShared() {
    /*
         * Ensure that a release propagates, even if there are other
         * in-progress acquires/releases.  This proceeds in the usual
         * way of trying to unparkSuccessor of head if it needs
         * signal. But if it does not, status is set to PROPAGATE to
         * ensure that upon release, propagation continues.
         * Additionally, we must loop in case a new node is added
         * while we are doing this. Also, unlike other uses of
         * unparkSuccessor, we need to know if CAS to reset status
         * fails, if so rechecking.
         */
    for (;;) {
        Node h = head;
        if (h != null && h != tail) {
            int ws = h.waitStatus;
            if (ws == Node.SIGNAL) {
                if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))
                    continue;            // loop to recheck cases
                unparkSuccessor(h);
            }
            else if (ws == 0 &&
                     !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
                continue;                // loop on failed CAS
        }
        if (h == head)                   // loop if head changed
            break;
    }
}
```



## 独占式超时获取同步状态

### tryAcquireNanos方法的执行步骤

可以响应中断

首先调用子类实现的tryAcquire方法

* 如果成功获取同步状态，返回true
* 如果失败，进入doAcquireNanos方法，线程进入自旋状态

进入doAcquireNanos方法的第一件事是将线程代表的结点加入队列

* 然后如果当前结点的前驱为头结点尝试获取同步状态，获取成功，设置头结点并返回true
* 获取失败
  * 如果没有超时，重新计算时间间隔，然后使当前线程等待新的时间。
  * 如果已经到时间了，方法从LockSupport的parkNanos方法返回
  * 如果剩余时间小于1000纳秒（spinForTimeoutThreshold），不阻塞线程，直接快速自旋

```java
public final boolean tryAcquireNanos(int arg, long nanosTimeout)
    throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    return tryAcquire(arg) ||
        doAcquireNanos(arg, nanosTimeout);
}

private boolean doAcquireNanos(int arg, long nanosTimeout)
    throws InterruptedException {
    if (nanosTimeout <= 0L)
        return false;
    final long deadline = System.nanoTime() + nanosTimeout;
    final Node node = addWaiter(Node.EXCLUSIVE);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return true;
            }
            nanosTimeout = deadline - System.nanoTime();
            if (nanosTimeout <= 0L)
                return false;
            if (shouldParkAfterFailedAcquire(p, node) &&
                nanosTimeout > spinForTimeoutThreshold)
                LockSupport.parkNanos(this, nanosTimeout);
            if (Thread.interrupted())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```



# ReentrantLock

ReentrantLock是唯一实现了Lock接口的外部类，而不是内部类

ReentrantLock在实现tryAcquire的时候考虑了占有锁的线程再次获取锁的场景，如果获取锁的线程再次尝试获取锁，会返回true，不会阻塞线程

## 两个构造方法

```java
/**
 * 使用非公平锁策略
 */
public ReentrantLock() {
    sync = new NonfairSync();
}

/**
 * 可以通过参数指定创建的是公平锁还是非公平锁。
 */
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

## 内部实现的同步器

### 抽象同步器Sync 继承 AQS

```java
abstract static class Sync extends AbstractQueuedSynchronizer {
    private static final long serialVersionUID = -5179523762034025860L;
	// 由ReentrantLock中抽象同步器的子类FairSync和NonfairSync来实现
    abstract void lock();

    // 实现非公平的tryAcquire方法获取锁状态，但是tryAcquire方法的实现在子类
    final boolean nonfairTryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            if (compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        // 判断当前线程是否获已经获取锁，然后实现线程的可重入。
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0) // overflow
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
	// 实现tryRelease方法
    protected final boolean tryRelease(int releases) {
        int c = getState() - releases;
        if (Thread.currentThread() != getExclusiveOwnerThread())
            throw new IllegalMonitorStateException();
        boolean free = false;
        if (c == 0) {
            free = true;
            setExclusiveOwnerThread(null);
        }
        setState(c);
        return free;
    }

    protected final boolean isHeldExclusively() {
        return getExclusiveOwnerThread() == Thread.currentThread();
    }

    final ConditionObject newCondition() {
        return new ConditionObject();
    }

    // Methods relayed from outer class
    final Thread getOwner() {
        return getState() == 0 ? null : getExclusiveOwnerThread();
    }

    final int getHoldCount() {
        return isHeldExclusively() ? getState() : 0;
    }

    final boolean isLocked() {
        return getState() != 0;
    }

    /**
     * Reconstitutes the instance from a stream (that is, deserializes it).
     */
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        s.defaultReadObject();
        setState(0); // reset to unlocked state
    }
}
```

## 实现可重入原理

如果要实现可重入必须解决两个问题：

* 线程再次获取锁，锁需要去识别获取锁的线程是否为当前占据锁的线程，如果是则再次获取成功
* 锁的最终释放，

如何实现可重入：

当发现同步状态不为0时，也就是说现在已经有线程获取了同步状态，此时通过判断获取了同步状态的线程是否是当前正在尝试获取同步状态的线程来进行锁重进入的设置

成功获取锁的线程再次获取锁，只是增加了同步状态的值，这也就要求ReentrantLock在释放同步状态时减少同步状态值。

如果锁被获取了n次，那么前（n-1）此tryRelease方法必须返回false，而只有同步状态完全释放了，才能返回true。

## 公平同步器和非公平同步器

```java
// 非公平同步器的实现，通过继承内部的抽象同步器Sync
static final class NonfairSync extends Sync {
    private static final long serialVersionUID = 7316153563782823691L;

    // 非公平锁实现线程上锁的操作，首先直接使用CAS获取同步状态，如果获取同步状态成功，设置锁拥有线程为当前线程，如果CAS快速获取状态失败，那么调用AQS的acquire方法再次尝试获取。
    // 在acquire方法中，会首先调用tryAcquire方法，此方法直接调用的是Sync类中实现的方法，在这个方法中如果判断同步状态为0，还会使用CAS快速获取同步状态，同时这个方法还实现了可重入
    final void lock() {
        if (compareAndSetState(0, 1))
            setExclusiveOwnerThread(Thread.currentThread());
        else
            acquire(1);
    }

    protected final boolean tryAcquire(int acquires) {
        return nonfairTryAcquire(acquires);
    }
}


// 公平锁的同步器
static final class FairSync extends Sync {
    private static final long serialVersionUID = -3000897897090466540L;

    final void lock() {
        acquire(1);
    }

    // 公平的方式获取同步状态，除非线程已经获取锁或者同步队列中没有等待的线程，否则要将线程加入同步队列，然后再排队进行锁的获取。
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            if (!hasQueuedPredecessors() &&
                compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
}
```



```java
// 公平锁和非公平锁释放锁的代码
protected final boolean tryRelease(int releases) {
    int c = getState() - releases;
    if (Thread.currentThread() != getExclusiveOwnerThread())
        throw new IllegalMonitorStateException();
    boolean free = false;
    if (c == 0) {
        free = true;
        setExclusiveOwnerThread(null);
    }
    setState(c);
    return free;
}
```



## 公平与非公平获取锁的区别

公平性与否是针对于**获取锁而言的**，对于释放锁，这两种类型的锁没有区别。如果一个锁是公平的，那么锁的获取顺序就应该符合请求的绝对时间顺序，也就是FIFO。

```java
// 非公平锁的lock方法
final void lock() {
    if (compareAndSetState(0, 1))
        setExclusiveOwnerThread(Thread.currentThread());
    else
        acquire(1);
}
// 公平锁的lock方法
final void lock() {
    acquire(1);
}
```

对于非公平锁，只要CAS获取同步状态成功，那么就成功获取到了锁，但是公平锁不是这样的。

公平锁的tryAcquire方法与非公平锁的比较，多了一个hasQueuedPredecessors的判断,此方法会判断同步队列中当前结点是否有前驱结点，如果有，该方法返回true，则表示有线程比当前线程更早地请求获取锁。

```java
public final boolean hasQueuedPredecessors() {
    // The correctness of this depends on head being initialized
    // before tail and on head.next being accurate if the current
    // thread is first in queue.
    Node t = tail; // Read fields in reverse initialization order
    Node h = head;
    Node s;
    return h != t &&
        ((s = h.next) == null || s.thread != Thread.currentThread());
}
```

## 优缺点：

公平锁保证了锁的获取按照FIFO原则，代价是进行了大量的线程切换

非公平锁虽然可能造成线程饥饿，但是极少的线程切换，保证了其更大的吞吐量



# ReentrantReadWriteLock

ReentrantReadWriteLock实现了**ReadWriteLock**接口

```java
public interface ReadWriteLock {
    // 返回读锁
    Lock readLock();
    // 返回写锁
    Lock writeLock();
}
```

在读多于写的情况下，读写锁能够提供比排他锁更好的并发性和吞吐量。它提供的特性如下：

* 公平性选择，支持非公平（默认）和公平的锁获取方式
* 重进入，该锁支持重进入，以读写线程为例：读线程在获取了读锁之后，能够再次获取读锁。而写线程在回去了写锁之后能再次获取写锁，同时也可以获取读锁。
* 锁降级：遵循获取写锁，获取读锁再释放写锁的次序，写锁能够降级成为读锁。

## 读写状态的设计

读写锁自定义的同步状态需要在同步状态（一个整型变量）上维护多个读线程和一个写线程的状态，使得该状态的设计成为读写锁实现的关键。

读写锁将同步状态变量切分成了两个部分，高十六位表示读，低十六位表示写

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220210095259123.png" alt="image-20220210095259123" style="zoom:80%;" />

## 内部抽象同步器Sync

Sync类继承AQS实现了如下核心模板函数：

* tryAcquire
* release
* tryAcquireShared
* tryReleaseShared

### Sync重要属性：

AQS中维护了一个state状态变量，正常来说，维护读状态和写状态需要两个变量，但是AQS只提供了一个state变量，如果要在一个int变量中维护两种状态，那么就需要使用高地位切割实现state状态变量维护两种状态，即高16位表示读状态，第16位表示写状态。

```java
// 读状态转变要向右移动的位数，因为高16位为读状态，所以要向右移动16位
static final int SHARED_SHIFT   = 16;
// 读锁计数基本单位
static final int SHARED_UNIT    = (1 << SHARED_SHIFT);
// 读锁、写锁可重入的最大数量
static final int MAX_COUNT      = (1 << SHARED_SHIFT) - 1;
// 获取写线程重入数量的掩码，因为写状态在低16位，因此将1向左移动16位再减1，就会获得一个高16位为0，低16位为1的掩码。
static final int EXCLUSIVE_MASK = (1 << SHARED_SHIFT) - 1;

/** 将state向右移动16位，表示读线程的数量  */
static int sharedCount(int c)    { return c >>> SHARED_SHIFT; }
/** 获取写锁重入数量  */
static int exclusiveCount(int c) { return c & EXCLUSIVE_MASK; }
```

### Sync内部类—HoldCounter和ThreadLocalHoldCount

HoldCounter是每个读线程重入数的计数器

ThreadLocalHoldCounter用来记录每一个读线程的重入个数。

为什么要设计这两个内部类？

* 首先我们可以通过AQS中的state变量的高16位获取所有读线程的重入数
* 但是在线程释放共享读锁的时候，我们还需要知道每一个线程的读重入数，这样才能正确的释放线程的共享锁
* 因此我们需要使用ThreadLocal来记录每个线程自有的读重入数

```java
static final class HoldCounter {
    int count = 0;
    // Use id, not reference, to avoid garbage retention
    final long tid = getThreadId(Thread.currentThread());
}

/**
 * ThreadLocal subclass. Easiest to explicitly define for sake
 * of deserialization mechanics.
 */
static final class ThreadLocalHoldCounter
    extends ThreadLocal<HoldCounter> {
    public HoldCounter initialValue() {
        return new HoldCounter();
    }
}
```

## 写锁的获取与释放

写锁是一个支持重进入的**排他锁**。

* 如果当前线程已经获取了写锁，则增加写状态。
* 如果当前线程在获取写锁时，读锁已经被获取（读状态不为0）或者该线程不是已经获取写锁的线程，则当线程进入等待状态。

### 获取源码：

```java
// WriteLock 的lock接口实现 方法入口
public void lock() {
    sync.acquire(1);
}
// AQS中排他锁的方法总入口
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
// ReentrantReadWriteLock 中WriteLock的父类Sync中tryAcquire的实现
protected final boolean tryAcquire(int acquires) {
    Thread current = Thread.currentThread();
    int c = getState();
    int w = exclusiveCount(c);
    if (c != 0) {
        // C同步状态不为0，同时w写状态为0，表示当前是共享状态，有线程在读，那么写线程不能获取共享状态，方法返回false；或者当w不为0时，当前线程不是已经获取了写锁的线程，方法直接返回false
        if (w == 0 || current != getExclusiveOwnerThread())
            return false;
        // 如果写锁的获取超过了最大的写锁获取数量，报错
        if (w + exclusiveCount(acquires) > MAX_COUNT)
            throw new Error("Maximum lock count exceeded");
        // 当程序运行到这，表示当前线程是重复获取写锁，那么直接设置同步状态并且返回true
        setState(c + acquires);
        return true;
    }
    // 程序运行到这表示同步状态为0，既没有写线程，也没有读线程。如果当前写线程应该被阻塞（当锁的实现是公平锁时，返回是否有前驱结点，当是非公平锁时，直接返回false）或者CAS设置状态没有成功，返回false
    if (writerShouldBlock() ||
        !compareAndSetState(c, c + acquires))
        return false;
    setExclusiveOwnerThread(current);
    return true;
}
// 返回写锁的数量
static int exclusiveCount(int c) { return c & EXCLUSIVE_MASK; }
// 公平锁写锁的实现，返回是否有前驱结点
final boolean writerShouldBlock() {
    return hasQueuedPredecessors();
}
// 非公平写锁的实现，返回false
final boolean writerShouldBlock() {
    return false; // writers can always barge
}

public boolean tryLock( ) {
    return sync.tryWriteLock();
}
final boolean tryWriteLock() {
    Thread current = Thread.currentThread();
    int c = getState();
    if (c != 0) {
        int w = exclusiveCount(c);
        if (w == 0 || current != getExclusiveOwnerThread())
            return false;
        if (w == MAX_COUNT)
            throw new Error("Maximum lock count exceeded");
    }
    if (!compareAndSetState(c, c + 1))
        return false;
    setExclusiveOwnerThread(current);
    return true;
}
```

### 释放源码：

```java
public void unlock() {
    sync.release(1);
}        
// 因为是重入锁，所以每次减少写锁的状态，如果减为0，并设置当前拥有线程为null，返回true，如果没有则返回false
protected final boolean tryRelease(int releases) {
    if (!isHeldExclusively())
        throw new IllegalMonitorStateException();
    int nextc = getState() - releases;
    boolean free = exclusiveCount(nextc) == 0;
    if (free)
        setExclusiveOwnerThread(null);
    setState(nextc);
    return free;
}
```

## 读锁的获取与释放

### 获取源码：

```java
// ReadLock
public void lock() {
    sync.acquireShared(1);
}
// AQS中
public final void acquireShared(int arg) {
    if (tryAcquireShared(arg) < 0)
        doAcquireShared(arg);
}
protected final int tryAcquireShared(int unused) {
    Thread current = Thread.currentThread();
    int c = getState();
    // 写线程不为0并且占有资源的不是当前线程，返回-1，表示失败
    if (exclusiveCount(c) != 0 &&
        getExclusiveOwnerThread() != current)
        return -1;
    // 获取读锁数量
    int r = sharedCount(c);
    // 判断读锁是否应该被阻塞，用于公平锁和非公平锁的判断，读锁数量是否大于最大值，通过CAS是否设置读锁成功，如果这些判断都成立，那么会进入这个if分支
    if (!readerShouldBlock() &&
        r < MAX_COUNT &&
        compareAndSetState(c, c + SHARED_UNIT)) {
        if (r == 0) {
            // 读锁数量为0，设置第一个读线程为当前线程，读线程占用资源数为1
            firstReader = current;
            firstReaderHoldCount = 1;
        } else if (firstReader == current) {
            // 如果读锁数量不为0但是当前线程是第一个读线程，读线程占用数加1
            firstReaderHoldCount++;
        } else {
            // 读锁数量不为0切当前线程不是第一个读线程，获取当前的计数器
            HoldCounter rh = cachedHoldCounter;
            // 如果当前计数器为null，或者计数器不是当前线程的，那么获取当前线程ThreadLocal的计数器
            if (rh == null || rh.tid != getThreadId(current))
                cachedHoldCounter = rh = readHolds.get();
            else if (rh.count == 0)
                // 如果计数器存在并且是当前线程的，但是计数器的值为0，设置当前线程计数器
                readHolds.set(rh);
            // 当前线程计数器加1
            rh.count++;
        }
        return 1;
    }
    // 如果第2步失败，执行这个方法
    return fullTryAcquireShared(current);
}
// 返回获取读锁的线程数
static int sharedCount(int c)    { return c >>> SHARED_SHIFT; }
// 读锁非公平锁实现的readerShouldBlock方法，此方法的实现可以避免写线程饥饿
final boolean readerShouldBlock() {
    return apparentlyFirstQueuedIsExclusive();
}
// 如果等待队列的第一个线程是写线程，那么返回true
final boolean apparentlyFirstQueuedIsExclusive() {
    Node h, s;
    return (h = head) != null &&
        (s = h.next)  != null &&
        !s.isShared()         &&
        s.thread != null;
}
// 解决tryAcquireShared方法没有解决的三个问题：如果线程应该被阻塞；如果读线程超过限制；如果CAS失败；如果出现以上三个问题，读线程将在此方法循环。
final int fullTryAcquireShared(Thread current) {
    HoldCounter rh = null;
    for (;;) {
        int c = getState();
        if (exclusiveCount(c) != 0) {
            if (getExclusiveOwnerThread() != current)
                return -1;
            // else we hold the exclusive lock; blocking here
            // would cause deadlock.
        } else if (readerShouldBlock()) {
            // Make sure we're not acquiring read lock reentrantly
            if (firstReader == current) {
                // assert firstReaderHoldCount > 0;
            } else {
                if (rh == null) {
                    rh = cachedHoldCounter;
                    if (rh == null || rh.tid != getThreadId(current)) {
                        rh = readHolds.get();
                        if (rh.count == 0)
                            readHolds.remove();
                    }
                }
                if (rh.count == 0)
                    return -1;
            }
        }
        if (sharedCount(c) == MAX_COUNT)
            throw new Error("Maximum lock count exceeded");
        if (compareAndSetState(c, c + SHARED_UNIT)) {
            if (sharedCount(c) == 0) {
                firstReader = current;
                firstReaderHoldCount = 1;
            } else if (firstReader == current) {
                firstReaderHoldCount++;
            } else {
                if (rh == null)
                    rh = cachedHoldCounter;
                if (rh == null || rh.tid != getThreadId(current))
                    rh = readHolds.get();
                else if (rh.count == 0)
                    readHolds.set(rh);
                rh.count++;
                cachedHoldCounter = rh; // cache for release
            }
            return 1;
        }
    }
}
```

### 释放源码：

```java
protected final boolean tryReleaseShared(int unused) {
    Thread current = Thread.currentThread();
    if (firstReader == current) {
        // assert firstReaderHoldCount > 0;
        if (firstReaderHoldCount == 1)
            firstReader = null;
        else
            firstReaderHoldCount--;
    } else {
        HoldCounter rh = cachedHoldCounter;
        if (rh == null || rh.tid != getThreadId(current))
            rh = readHolds.get();
        int count = rh.count;
        if (count <= 1) {
            readHolds.remove();
            if (count <= 0)
                throw unmatchedUnlockException();
        }
        --rh.count;
    }
    for (;;) {
        int c = getState();
        int nextc = c - SHARED_UNIT;
        if (compareAndSetState(c, nextc))
            return nextc == 0;
    }
}
```

## 锁降级

锁降级指的是写锁降级成为读锁。如果当前线程拥有写锁，然后将其释放，最后再获取读锁，这种过程不能称为锁降级。锁降级指的是把持住写锁，在获取到读锁，然后释放写锁的过程。



# LockSupport工具

| 方法                                    | 用处                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| park()                                  | 阻塞当前线程，直到unpark或者被中断                           |
| park(Object blocker)                    | 阻塞当前线程，直到unpark或者被中断，blocker表示线程要等待的对象 |
| parkNanos(long nanos)                   | 阻塞当前线程，阻塞时长不超过nanos秒                          |
| parkNanos(Object blocker, long nanos)   |                                                              |
| parkUtil(long deadline)                 | 阻塞当前线程，直到deadline时间（从1970年开始到deadline时间的毫秒数） |
| parkUtil(Object blocker, long deadline) |                                                              |
| setBlocker(Thread t, Object blocker)    | 使用blocker设置t线程的parkBlocker字段，用于线程监控和分析工具定位线程阻塞原因 |
| unpark(Thread thread)                   | 唤醒thread线程                                               |

类中的的方法全部是静态方法，这些方法提供了最基本的线程阻塞和唤醒功能。LockSupport称为构建同步组件的基础工具。

## 原理

HotSpot里park/unpark的实现，每个java线程都有一个**Parker实例**，Parker类是这样定义：

```C++
class Parker : public os::PlatformParker {  
private:  
 volatile int _counter ;  // 用来记录许可
 ...  
public:  
 void park(bool isAbsolute, jlong time);  
 void unpark();  
 ...  
}  
class PlatformParker : public CHeapObj<mtInternal> {  
 protected:  
   pthread_mutex_t _mutex [1] ;  
   pthread_cond_t  _cond  [1] ;  
   ...  
}  
```

Parker类实际上用Posix的mutex，condition来实现的，在Parker类里的_counter字段，就是用来记录所谓的许可的。

当调用park时，

* 先尝试直接能否拿到许可，即\_counter>0时，如果成功，则把\_counter设置为0，并返回。
* 如果不成功，则构造一个ThreadBlockInVM ,然后检查_counter是不是>0，如果是，则把\_counter设置为0，unlock mutex并返回
* 否则，再判断等待的时间，然后调用pthread_cond_wait函数等待，如果等待返回，则把_counter设置为0，unlock mutex并返回。

> 注意：如果线程被中断，那么只要中断标记还在，也就是中断标记为true，那么再次调用park方法会直接返回，不会阻塞线程，因为在park方法内部，判断了线程是否被中断，如果线程的中断状态被设置，那么不会阻塞线程，直接返回

当调用unpark时：

* 则简单多了，直接设置_counter为1，再unlock mutext返回。如果_counter之前的值是0，**则还要调用pthread_cond_signal唤醒在park中等待的线程**

```java
// 使给定线程的许可证可用。如果线程在park上被阻塞，那么它将解除阻塞。否则，它的下一次park保证不会阻塞。如果给定线程尚未启动，则不保证此操作有任何效果。
public static void unpark(Thread thread) {
    if (thread != null)
        UNSAFE.unpark(thread);
}
```

```java
// 除非许可可用，否则禁用当前线程以进行线程调度，如果许可可用，则方法立即返回，否则，当前先后才能处于休眠状态，知道发生一下三种情况：1.其他线程调用此线程unpark，2.其他线程中断当前线程，3.无缘无故的调用返回
public static void park(Object blocker) {
    Thread t = Thread.currentThread();
    setBlocker(t, blocker);
    UNSAFE.park(false, 0L);
    setBlocker(t, null);
}
public static void park() {
    UNSAFE.park(false, 0L);
}
// 和park方法大致相同，只不过是多了一个超时时间（单位是纳秒），如果阻塞时间到了，则立即返回
public static void parkNanos(Object blocker, long nanos) {
    if (nanos > 0) {
        Thread t = Thread.currentThread();
        setBlocker(t, blocker);
        UNSAFE.park(false, nanos);
        setBlocker(t, null);
    }
}
public static void parkNanos(long nanos) {
    if (nanos > 0)
        UNSAFE.park(false, nanos);
}
// 一直到deadline时间（从1970年开始到deadline时间的好描述）
public static void parkUntil(Object blocker, long deadline) {
    Thread t = Thread.currentThread();
    setBlocker(t, blocker);
    UNSAFE.park(true, deadline);
    setBlocker(t, null);
}
public static void parkUntil(long deadline) {
    UNSAFE.park(true, deadline);
}
```



# Condition接口

Condition接口提供了类似于Object的监视器方法，与Lock配合可以实现等待/通知模式（依赖于Lock对象）。

Object监视器方法和Condition接口的对比：

* object的wait支持等待队列个数1个，condition支持多个等待队列
* 当前线程释放锁并进入等待状态，在等待状态中wait不支持不响应中断，condition支持不响应中断
* wait不支持等待到未来的某个时间点，condition支持等待到未来的某个时间点。

Condition中的方法：

```java
public interface Condition {
    /*
    当前线程进入等待状态直到被通知（signal）或中断
    当前线程将进入运行状态切从await返回的情况，包括：
    1.其他线程调用该condition的signal方法
    2.其他线程调用interrupt方法中断线程
    当线程从await方法返回，就说明线程已经获取了condition对象所对应的锁
    */
    void await() throws InterruptedException;
    // 当前线程进入等待直到被通知，对中断不敏感
    void awaitUninterruptibly();
    /*
    当前线程进入等待状态直到被通知，中断或者超时
    返回值表示剩余的时间，如果在超时之前被唤醒，那么返回的是nanosTimeout-实际消耗时间，
    如果返回值是0，或者负数，那么可以认定已经超时了
    */
    long awaitNanos(long nanosTimeout) throws InterruptedException;
    boolean await(long time, TimeUnit unit) throws InterruptedException;
    // 当前线程进入等待状态直到被通知，中断或者到某个时间，如果没有到指定时间就被通知，方法返回true
    // 否则方法返回false
    boolean awaitUntil(Date deadline) throws InterruptedException;
    // 必须先获取condition关联的锁，然后唤醒等待在condition上的线程
    void signal();
    void signalAll();
}
```

# AQS中Condition的实现—ConditionObject

## 等待队列

等待队列是一个FIFO队列，在队列中的每一个结点都包含了一个线程的引用，该线程就是在Condition对象上等待的线程。如果一个线程调用Condition.await方法，那么该线程将会释放锁，构造成结点加入等待队列并进入等待状态。这个节点的定义复用了同步器中结点的定义，也就是同步队列和等待队列中结点类型都是同步器中的静态内部类AbstractQueuedSynchronizer.Node 

一个Condition包含一个等待队列，Condition拥有首结点和尾结点（firstWaiter和lastWaiter）。

```java
public class ConditionObject implements Condition, java.io.Serializable {
    private static final long serialVersionUID = 1173984872572414699L;
    /** First node of condition queue. */
    private transient Node firstWaiter;
    /** Last node of condition queue. */
    private transient Node lastWaiter;

    /**
     * Creates a new {@code ConditionObject} instance.
     */
    public ConditionObject() { }
}
```

**相比于同步器，要进入等待状态的尾结点的更新不用使用CAS更新，因为调用await方法的线程必定是获取了锁的线程。**

在Object的监视器模型上，一个对象拥有一个同步队列和一个等待队列，而并发包中的Lock（更准确来说是同步器）拥有一个同步队列和多个等待队列。

## await方法

**如果从队列（同步队列和等待队列）的角度看await方法，当调用await方法时，相当于同步队列的首节点（获取了锁的结点）移动到Condition的等待队列中。**

await方法的整体逻辑：

* 在Condition中维护着一个等待队列，每当执行await方法，都会根据当前线程创建一个结点，并加入到等待队列尾部
* 加入到等待队列尾部之后，释放锁，并唤醒阻塞在锁的AQS队列中的一个线程
* 然后，将自己阻塞
* 在被别的线程唤醒后，将刚刚这个结点放到AQS队列，接下来就是同步队列的事情了，例如在同步队列中循环抢锁
* 紧接着就会尝试抢锁，就是在AQS同步队列中所做的事情，抢不到就阻塞，抢到了就继续执行

```java
public final void await() throws InterruptedException {
    // await是响应线程中断的函数。
    if (Thread.interrupted())
        throw new InterruptedException();
    // 创建一个新的结点，把当前线程加入等待队列
    Node node = addConditionWaiter();
    // 释放当前线程的同步状态，释放锁，同时唤醒同步队列中的一个线程
    int savedState = fullyRelease(node);
    int interruptMode = 0;
    while (!isOnSyncQueue(node)) {
        LockSupport.park(this);
        if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
            break;
    }
    if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
        interruptMode = REINTERRUPT;
    if (node.nextWaiter != null) // clean up if cancelled
        unlinkCancelledWaiters();
    if (interruptMode != 0)
        reportInterruptAfterWait(interruptMode);
}
// 把当前线程加入等待队列
private Node addConditionWaiter() {
    Node t = lastWaiter;
    // 如果等待队列的尾结点被取消了，那么把它移除等待队列
    if (t != null && t.waitStatus != Node.CONDITION) {
        unlinkCancelledWaiters();
        t = lastWaiter;
    }
    // 创建等待结点
    Node node = new Node(Thread.currentThread(), Node.CONDITION);
    if (t == null)
        firstWaiter = node;
    else
        t.nextWaiter = node;
    lastWaiter = node;
    return node;
}
// 移除等待队列中已经被取消的结点
private void unlinkCancelledWaiters() {
    Node t = firstWaiter;
    Node trail = null;
    while (t != null) {
        Node next = t.nextWaiter;
        if (t.waitStatus != Node.CONDITION) {
            t.nextWaiter = null;
            if (trail == null)
                firstWaiter = next;
            else
                trail.nextWaiter = next;
            if (next == null)
                lastWaiter = trail;
        }
        else
            trail = t;
        t = next;
    }
}
// 释放同步状态，释放锁
final int fullyRelease(Node node) {
    boolean failed = true;
    try {
        int savedState = getState();
        if (release(savedState)) {
            failed = false;
            return savedState;
        } else {
            throw new IllegalMonitorStateException();
        }
    } finally {
        if (failed)
            node.waitStatus = Node.CANCELLED;
    }
}
final boolean isOnSyncQueue(Node node) {
    if (node.waitStatus == Node.CONDITION || node.prev == null)
        return false;
    if (node.next != null) // If has successor, it must be on queue
        return true;
    /*
         * node.prev can be non-null, but not yet on queue because
         * the CAS to place it on queue can fail. So we have to
         * traverse from tail to make sure it actually made it.  It
         * will always be near the tail in calls to this method, and
         * unless the CAS failed (which is unlikely), it will be
         * there, so we hardly ever traverse much.
         */
    return findNodeFromTail(node);
}
private boolean findNodeFromTail(Node node) {
    Node t = tail;
    for (;;) {
        if (t == node)
            return true;
        if (t == null)
            return false;
        t = t.prev;
    }
}
```

## signal方法

```java
// 调用该方法的前置条件是线程必须获取了锁
public final void signal() {
    // 检查线程是否获取了锁
    if (!isHeldExclusively())
        throw new IllegalMonitorStateException();
    // 获取等待队列的首结点
    Node first = firstWaiter;
    if (first != null)
        doSignal(first);
}
private void doSignal(Node first) {
    do {
        if ( (firstWaiter = first.nextWaiter) == null)
            lastWaiter = null;
        first.nextWaiter = null;
    } while (!transferForSignal(first) &&
             (first = firstWaiter) != null);
}
final boolean transferForSignal(Node node) {
    /*
         * If cannot change waitStatus, the node has been cancelled.
         */
    if (!compareAndSetWaitStatus(node, Node.CONDITION, 0))
        return false;

    /*
         * Splice onto queue and try to set waitStatus of predecessor to
         * indicate that thread is (probably) waiting. If cancelled or
         * attempt to set waitStatus fails, wake up to resync (in which
         * case the waitStatus can be transiently and harmlessly wrong).
         */
    Node p = enq(node);
    int ws = p.waitStatus;
    if (ws > 0 || !compareAndSetWaitStatus(p, ws, Node.SIGNAL))
        LockSupport.unpark(node.thread);
    return true;
}
```




