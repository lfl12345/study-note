## gcc

gcc编译可执行程序四个步骤：预处理、编译、汇编、链接

![image-20211223163412694](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211223163412694.png)

![image-20211223163546095](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211223163546095.png)

### gcc和头文件

正常情况下，如果源文件和同文件在一个目录下，直接使用gcc编译源文件即可，如果源文件和头文件不在一个目录下，必须使用-I命令指定头文件的目录。

![image-20211223164950756](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211223164950756.png)

![image-20211223171045766](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211223171045766.png)

## 静态库和动态库

### 静态库的制作以及使用

![image-20211223182407875](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211223182407875.png)

## 数据段合并

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211223195638417.png" alt="image-20211223195638417" style="zoom:50%;" />

## gdb调试

[安装](https://blog.csdn.net/weixin_46048542/article/details/120288361)

[错误解决](https://blog.csdn.net/youjian124/article/details/75204860)

vscode[配置调试](https://blog.csdn.net/ziranqiuzhi/article/details/112141260)

## 系统调用

man page中给出的系统调用实际上是真正的系统调用封装了一层。

![image-20211224090147196](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211224090147196.png)

### 系统调用和库函数对比

预读入、换输出机制  

库函数会有缓冲，系统调用没有缓冲。到内核区域之后内核会有4096的缓冲

## 进程间通信

文件、管道、信号、共享内存、消息队列、套接字、命名管道等，随着计算机的蓬勃发展，一些方法由于自身设计缺陷被淘汰或者弃用。现今常用的进程间通信方式有：

* 管道（使用最简单）
* 信号（开销最小）
* 共享映射区（无血缘关系）
* 本地套接字（最稳定）
* 个人觉得还有消息队列

### 管道

前提因为父子文件共享文件描述符

必须作用在有血缘关系的进程之间

linux中文件类型有七种，加上未知八种，七种普通文件、目录、软连接是真正占用磁盘空间的。字符设备、块设备、管道、套接字文件这些都是伪文件，不会占用磁盘空间，这些只是占用内存。

**管道的原理: 管道实为内核使用环形队列机制，借助内核缓冲区(4k)实现。**

### 存储映射

存储映射I/O（memory-mapped IO）是一个磁盘上的文件与存储空间的缓冲区相映射。于是当从缓冲区读取数据就相当于读取文件，同样的向缓冲区写数据就相当于将数据写入文件。这样就可以在不使用read和write的情况下，使用地址（指针）来完成IO操作。