---
layout: post
title: gRPC回调机制
---

# gRPC的回调机制

一个高性能的RPC框架离不开epoll机制，而epoll必须和回调相结合才能实现一个完全非阻塞的RPC

回调函数分两种

* 普通回调。大部分回调函数属于这类, 比如epoll发现有新数据可读时执行的就是普通回调。
* 超时回调。任何一个网络框架都要支持超时机制，超时回调是可以在中途撤销的，这是和普通回调的一个重要区别。

gRPC的回调函数定义如下：

``` c
typedef void grpc_iomgr_cb_func(void* arg, int success);
```
arg是外带参数，这是回调函数的通用设计。第二个参数success正式为超时撤销机制设计的，超时回调通过success判断自己是不是已经被撤销。
