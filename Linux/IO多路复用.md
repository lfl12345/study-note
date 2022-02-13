不使用select如何实现判断accept返回的fd中有数据？

bitmap在用户态只能使用4个宏去操作

**select**系统调用的缺点（阻塞函数）

* 只有1024个bitmap，有限
* rset不可重用
* 用户态到内核态还是有不小的开销
* select返回的时候，说明至少有一个fd处于活跃，但是我还得遍历一遍寻找哪一个处于活跃，没有告诉我们到低哪一个或者那几个

select系统调用相对于用户使用的优点：

* 内核帮助我们监听fd

**poll**对比select：

* poll中那个结构体的组织方式？应该是链表所以可以多余1024个
* poll中的event可以重用，只用在用户态重置revent就可以
* 但是poll还是有用户态到内核态的开销
* 并且还要使用O(n)的复杂度去判断哪一个fd处于活跃状态

**epoll**

1. epoll_create
2. epoll_ctl
3. epoll_wait

redis、Nginx和java nio都是使用epoll实现的

多路复用借助硬件的DMA

问题：

SSD和机械硬盘区别，为什么SSD快

那些DB对SSD做了优化，

Database对SSD做了优化的原理

## select函数

select第一个参数是要监听的所有文件描述符的最大值加一。在select函数内部，要做一个循环，循环的上限是传入的第一个参数。

第二个第三个第四个参数的类型都是fd_set类型的指针，这三个参数都是传入传出参数。fd_set结构体仅包含一个整型数组，该数组的每一位标记一个文件描述符。fd_set能包含的文件描述符数量由FD_SETSIZE指定，这就限制了select函数能同时处理的文件描述符的总量。

最后一个参数是超时时长

fd_set的本质是一个位图，

select返回值为监听的所有文件描述符中发生事件的总个数

缺点：

* 当文件描述符很离散的时候，程序会有不必要的花销
* 文件描述符集合的个数有限，所以监听是有上限的

优点：

* select可以跨平台，epoll只能在Linux中使用

## poll函数

poll没有什么实际的作用。当我监听的文件描述符数量不多并且又比较散乱，那select效率不高。发现这个问题之后人们就想改

```c
int poll(struct pollfd *fds, nfds_t nfds, int timeout);
struct pollfd {
    int   fd;         /* file descriptor 待监听的文件描述符*/
    short events;     /* requested events 表示待监听的文件描述符对应的监听事件*/
    short revents;    /* returned events 串进去的参数和传出来的分开了*/
};
```

fds：所要监听的文件描述符数组。

优点：

* 自带数组结构，
* 可以将监听事件集合和返回事件集合分离
* 可以拓展监听上限，可以超出1024的上限

缺点：

* 不能跨平台，只能在Unix平台下实现
* 无法直接定位满足监听事件的文件描述符，必须通过循环轮训。

## epoll函数

突破1024文件描述符限制

![image-20211222112045037](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211222112045037.png)

查看计算机所能打开的文件描述符的个数限制

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211222112209045.png" alt="image-20211222112209045" style="zoom:50%;" />

当前用户下的进程，默认打开文件描述符个数。缺省为1024

## epoll事件模型

ET模式（Edge Triggered）：边缘触发模式，只有数据到来才触发，不管缓冲区是否有数据。

LT模式（Level Triggered）：水平触发模式——默认采用的方式。只要有数据都会触发。

setsockopt用法https://blog.csdn.net/A493203176/article/details/70053137