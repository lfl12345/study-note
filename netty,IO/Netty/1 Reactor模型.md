# Reactor模型

Reactor模型（反应器模型）是一种基于IO多路复用策略、处理一个或多个客户端并发请求的事件设计模式。

一般情况下，IO多路复用策略是需要使用事件分发器的。对于事件分发器的作用，简单来说就是将那些读写事件源分发给各自读写事件的处理者。涉及事件分发器的两种模式分别称为Reactor模型和Proactor模型。

Reactor模型是基于同步IO的，而Proactor模型是与异步IO相关的。Netty线程模型就是通过Reactor模型，并基于IO多路复用策略设计的。

## Reactor模型和Proactor模型

无论是Reator还是Proactor模型，两者都是一种基于事件驱动的，高性能的IO设计模型。两者的区别就是Reactor模型是基于同步IO的，而Proactor模型是与异步IO相关的。

### Reactor模型

在普通的程序调用函数方式中，应用程序会根据处理流程主动调用并执行函数，然后程序会等待函数执行完成后的返回结果，函数会在执行完毕后将控制权回交给程序。

至于Reactor模型则恰恰相反，它反置了整个处理流程。应用程序不是主动的调用某个函数完成处理，而是提供了调用函数对应的接口并注册到Reactor上，并将触发调用的操作定义为事件。当系统或用户操作触发事件时，Reactor将主动调用应用程序注册的对应接口并执行函数，因此这些接口也称为“回调函数”。

当客户端提交一个或者多个并发服务请求时，服务端的处理程序会使用IO多路复用策略，同步派发这些请求至相关的请求处理程序。

Reactor模型是处理并发IO比较常见的一种模型，其核心思想就是将所有要处理的IO事件注册到一个中心IO多路复用器上，同时主线程阻塞在多路复用器上。如果有IO事件到来或是准备就绪，IO多路复用器就会返回，并将相应IO事件分发到对应的处理器中进行处理。

### Proactor模型

虽然Reactor模型相对简单高效，但是在处理异步IO请求时就会显得很不适应。因此，Proactor模型诞生了，专门用于异步IO请求。

在Proactor模型下，应用程序初始化一个异步读写操作，然后注册相应的事件处理器。此时，事件处理器并不会关注读写就绪事件，而是只关注读写操作完成事件（这也正是区别于Reactor模型的关键）。然后，在事件分发器等待读写操作完成事件的过程中，操作系统会通过调用内核线程完成读写操作，并将读写的内容放入应用缓冲区中（异步IO请求均是由操作系统负责将数据读写到应用缓冲区中的）。在事件分发器捕获到读写完成事件后，激活应用程序注册的事件处理器，并由事件处理器直接从缓冲区中读写数据（此时就不是实际的读写操作了，实际的读写操作已经有系统内核完成了）。

### Reactor和Proactor对比

相同点：

* 基于IO多路复用策略实现的。
* 实现了一个被动的事件分发模型，服务等待请求事件到来，再通过不受间断的同步处理事件，进而完成操作处理。

区别：

* Reactor基于同步IO，而Proactor与异步IO相关。
* 实现了一个主动的事件分发模型，并支持多个任务并发执行，对耗时长的任务有特别优势（各个任务之间互不影响）

## Reactor线程模型

在Reactor模型中，主要包括Handle，Synchronous Event Demultiplexer，Event Handler，Concrete Event Handler和Initiation Dispatcher五大角色。

### Handler(资源描述符)

Handle本质上是操作系统范畴中一种用于描述资源的标识，例如：文件描述符，Socket描述符和事件描述符等。在Reactor模型中，Handle主要用于表示事件发生的源头。

### Synchronous Event Demultiplexer（同步事件分离器）

Synchronous Event Demultiplexer本质上是一个系统调用，主要用于等待一个或者多个事件的发生。它是同步的，被调用的时候会被阻塞，直到有事件产生为止。在Linux中，Synchronous Event Demultiplexer一般就是指IO多路复用机制，例如select，poll，epoll。在Java NIO范畴中，Synchronous Event Demultiplexer指的就是selector选择器，对应的就是select()方法。

### Event Handler（事件处理器）

Event Handler是由多个回调方法构成的，这些回调方法构成了针对某个事件的反馈机制。例如ChannelHandler中对应的都是一个个特定事件的回调方法。

### Concrete Event Handler（具体事件处理器）

concrete Event Handler是Event Handler的具体实现，它实现了Event Handler所提供的各种回调方法，进而实现了特定的业务逻辑。

### Initiation Dispatcher（初始分发器）

Initiation Dispatcher定义了一组规范，用于控制事件的调度方式，同时汉提供了针对Event Handler的注册，删除等机制。Initiation Dispatcher是整个Event Handler的核心，其通过Synchronous Event Demultiplexer来等待事件的发生、事件到来时负责分离出每一件事件，进而调用Event Handler中特定回调方法来处理这些事件。在Reator模型中，Initiation Dispatcher实际上就扮演者Reactor的角色。

首先初始化分发器启动时，会把所有相关的事件处理器对应的具体实现注册到初始化分发器中，然后初始化分发器通过同步时间分离器等待事件的发生，事件到来时再根据事件的类型调用事件处理器的回调。事件处理器拥有事件描述符的引用，初始化分发器在事件注册完成后会执行自己的内部循环。最后，当客户端的连接请求到来时，select()方法会返回事件的集合，有初始化分发器遍历集合负责获取每一个具体的事件，再根据事件类型调用Event Handler的回调方法进行具体操作。

基于Reactor模型的主要线程模型主要有Reactor单线程模型、Reactor多线程模型和主从Reactor多线程模型三种，这也是Netty构建Netty线程模型的核心基础。

### Reactor单线程模型

所有的IO操作都由一个NIO线程处理。

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20220418204732825.png" alt="image-20220418204732825" style="zoom: 33%;" />

Reactor单线程模型主要适用于低负载、低并发、小数据量的应用场景，对于高负载、高并发、大数据量的应用场景是明显力不从心的。为了解决Reactor单线程模型的缺点，产生了Reactor多线程模型。

### Reactor多线程模型

Reactor多线程模型是指在Reactor单线程模型的基础上，增加了一个工作者（Worker）线程池，该工作者线程池负责处理从Reactor线程中移交出来的非IO操作，最大程度地提高Reactor线程的IO响应处理能力，尽量避免由于一些耗时的业务操作占用资源，进而延迟后续IO请求的处理。

Reactor多线程相比于Reactor单线程模型，在业务逻辑上主要做了一下改进：

* 将Handler处理器的执行放入线程池，由多线程进行业务处理。
* Reactor线程对象可以仍是单线程，但是如果服务器为双核CPU，为了充分利用系统资源，可以将Reactor对象拆分为两个线程

Reactor多线程优点：

* 提高Reactor线程对象的IO响应处理能力，避免由于一些耗时的业务操作占用资源，进而延迟后续IO请求操作的处理
* 复用已有的线程，避免在处理多个请求时，由于创建新线程以及销毁旧线程过程中产生的资源开销。
* 避免了由于创建线程而延迟任务的执行，提高了线程的相应性。

但是多线程Reactor模型还有缺陷，就是所有的IO操作（select）仍然是由一个Reactor来完成的，于是又改进了Reactor多线程模型，提出了主从Reactor多线程模型。

### 主从Reactor多线程模型

由一组线程池接收请求，一组线程池处理IO

主从Reactor多线程模型在Reactor多线程模型的基础上，将Reactor分成boss和worker两部分。

其中boss负责监听服务端的套接字并接受新连接，然后将建立的套接字分给worker，而worker负责解析已经连接的套接字，然后将具体的业务操作转发给工作者线程池来完成。实际上，worker的数量可以和服务器的CPU数量相同。

主要改进：

* 服务器用于接收客户端连接的不再是一个单独的线程，而是一个独立的线程池。



# Netty线程模型

Netty线程模型就是基于IO多路复用策略而实现的一个Reactor线程模型的经典通信架构。

* Netty服务器端在启动的时候会配置一个ChannelPipeline，在ChannelPipeline中包含一个ChannelHandler链，所有的IO事件发生时都会触发Channelhandler中的事件方法，这个事件会在ChannelPipeline中的ChannelHandler链里进行传递，此时Netty的事件处理器（Event Handler）就是ChannelHandler。
* Netty服务器端从bossGroup事件循环池（NioEventLoopGroup）中获取一个NioEventLoop来实现服务器端程序绑定本地端口的操作，将对应的ServerSocketChannel注册到该NioEventLoop中的Selector上，并注册ACCEPT事件为ServerSocketChannel所感兴趣的事件。此时，在Netty的bossGroup事件循环池（NioEventLoopGroup）中获取的NioEventLoop就相当于mainReactor，NioEventLoop中的Selector就相当于同步事件分离器（Synchronous Event Demultiplexer）
* NioEventLoop启动事件循环来监听客户端的连接请求。每当有客户端向服务器端发起连接请求时，NioEventLoop的时间循环监听到该ACCEPT事件，Netty负责接收这个连接并通过accept()方法得到与这个客户端的连接（SocketChannel）。此时Netty会触发ChannelHandler中的ChannelRead事件，该事件会在ChannelPipeline中的ChannelHandler链中执行并传递。ServerBootStrapAcceptor中的readChannel()方法负责将客户端的连接SocketChannel注册到workerGroup（NioEventLoopGroup）中某个NioEventLoop的Selector上，并注册READ事件为客户端的连接（SocketChannel）所感兴趣的事件，接下来服务器和客户端之间就可以通信了。









