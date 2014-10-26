---
layout: post
title: "thrift服务分析"
---

## Thrift服务分析


本文基于thrift的java库

thrift是一个设计良好的服务框架，它将网络服务从底层到高层抽象出TTransport、TProtocol、TProcessor等对象，然后再用抽象的TServer将前三者组装起来。所以thrift的任何一个环节都是可定制化的。在此，我们着重看TServer及其具体类。

### TServer & TAbstractNonblockingServer

TServer要求我们将其所需的TTransport、TProtocol、TProcessor对象打包成AbstractServerArgs传入其构造函数。具体的组装行为由TServer的具体类通过serve()方法实现。
TAbstractNonBlockingServer是所有非阻塞服务的基类，同时它本身继承自TServer。

### TSimpleServer
这是最简单的TServer，具有以下特点：

* 单连接
* 短连接
* 阻塞读写

### TThreadPoolServer
这是基于线程池的TServer，具有以下特点

* 并发连接。最大并发数与线程池中线程数量相同，当只有一个线程时，退化为TSimpleServer
* 短连接
* 阻塞读写

### TNonblockingServer

这是最简单的AsbtractNonBlockingServer，所有的读写都是非阻塞的。

* 单线程，事件驱动。
* 支持长连接

### THsHaServer

把TNonblockingServer的业务处理移到单独的线程中去了，防止业务处理阻塞网络IO

### TThreadedSelectServer

在THsHaServer的基础上，将连接监听单独放在一个线程。
