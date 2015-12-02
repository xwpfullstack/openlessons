[TOC]

# 什么是libevent 

Libevent是一个轻量级的开源高性能网络库，使用者众多，研究者更甚。

## Libevent简介

上来当然要先夸奖啦，Libevent 有几个显著的亮点：

    1.事件驱动（event-driven），高性能;
    2.轻量级，专注于网络，不如ACE那么臃肿庞大；
    3.源代码相当精炼、易读；
    4.跨平台，支持Windows、Linux、*BSD和Mac Os；
    5.支持多种I/O多路复用技术， epoll、poll、dev/poll、select和kqueue等；
    6.支持I/O，定时器和信号等事件；
    7.注册事件优先级；

Libevent已经被广泛的应用，作为底层的网络库；比如memcached、Vomit、Nylon、Netchat等等。

学习libevent有助于提升程序设计功力，除了网络程序设计方面外，Libevent的代码里有很多有用的设计技巧和基础数据结构，比如信息隐藏、函数指针、c语言的多态支持、链表和堆等等，都有助于提升自身的程序功力。程序设计不止要了解框架，很多细节之处恰恰也是事关整个系统成败的关键。只对libevent本身的框架大概了解，那或许仅仅是一知半解，不深入代码分析，就难以了解其设计的精巧之处，也就难以为自己所用。


# libevent核心原理
## Reactor模式优点

Reactor模式是编写高性能网络服务器的必备技术之一，它具有如下的优点：

1. 响应快，不必为单个同步时间所阻塞，虽然Reactor本身依然是同步的
2. 编程相对简单，可以最大程度的避免复杂的多线程及同步问题，并且避免了多线程/进程的切换开销
3. 可扩展性，可以方便的通过增加Reactor实例个数来充分利用CPU资源
4. 可复用性，reactor框架本身与具体事件处理逻辑无关，具有很高的复用性

## Reactor模式原理

普通函数调用：

1. 程序调用某函数函数执行
2. 程序等待函数处理
3. 函数将结果和控制权返回给程序
4. 程序继续处理。

Reactor释义“反应堆”，是一种事件驱动机制。和普通函数调用的不同之处在于：

 应用程序不是主动的调用某个API完成处理，而是恰恰相反，Reactor逆置了事件处理流程，应用程序需要提供相应的接口并注册到Reactor上，如果相应的事件发生，Reactor将主动调用应用程序注册的接口，这些接口又称为“回调函数”。使用Libevent也是想Libevent框架注册相应的事件和回调函数；当这些事件发生时，Libevent会调用这些回调函数处理相应的事件（I/O读写、定时和信号）。

1. 事件分类，I/O读写、定时和信号
2. 应用程序实现回调函数
3. 应用程序注册事件发生时的回调函数到框架
4. 如果事件发生，框架主动调用对应的回调函数相应事件

# libevnet核心数据结构
# libevnet常用函数详解

# 如何使用libevent
# libevent下载和安装

官网：
    http://libevent.org

github：
    https://github.com/libevent/libevent.git

## 代码剖析参考
    http://www.wangafu.net/~nickm/libevent-book/Ref0_meta.html
    http://wenku.baidu.com/link?url=sf6GlGvIc0wjRfKhTk-3mo0nbXkgf0bw7d_tQm-wnr3oLUmT84Bp4jl47G3Ed_gmH_Uzho6YwIh6xdyU1cpqxrufhp9CA1qJlfkhteXY0V7

