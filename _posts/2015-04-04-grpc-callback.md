---
layout: post
title: gRPC回调机制
---

# gRPC的回调机制

一个高性能的RPC框架离不开epoll机制，而epoll必须和回调相结合才能实现一个完全非阻塞的RPC

## 回调函数定义

回调函数分两种

* 普通回调。大部分回调函数属于这类, 比如epoll发现有新数据可读时执行的就是普通回调。
* 超时回调。任何一个网络框架都要支持超时机制，超时回调是可以在中途撤销的，这是和普通回调的一个重要区别。

gRPC的回调函数定义如下：

``` c
typedef void grpc_iomgr_cb_func(void* arg, int success);
```
arg是外带参数，这是回调函数的通用设计。第二个参数success正式为超时撤销机制设计的，超时回调通过success判断自己是不是已经被撤销。

## 回调队列

回调机制的核心是一个生产者-消费者队列，里面是回调函数对象。回调对象定义如下

```c
typedef struct delayed_callback {
  grpc_iomgr_cb_func cb;
  void *arg;
  int success;
  struct delayed_callback *next;
} delayed_callback;
```

从next成员变量可以看出，回调对象会形成一个单链表队列。队列的头和尾如下

```c
static delayed_callback *g_cbs_head = NULL;
static delayed_callback *g_cbs_tail = NULL;
```

回调队列是线程安全的，应用线程想往队列添加回调对象，可使用如下接口

```c
// 传入的success一定为1，为普通函数设计
void grpc_iomgr_add_callback(grpc_iomgr_cb_func, void* cb_arg);

// 完整的接口，普通回调和超时回调都可以创建
void grpc_iomgr_add_delayed_callback(grpc_iomgr_cb_func, void* cb_arg, int success);
```

