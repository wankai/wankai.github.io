---
layout: post
title: gRPC回调机制
---

# gRPC的回调机制

一个高性能的RPC框架离不开epoll机制，而epoll必须和回调相结合才能实现一个完全非阻塞的RPC

gRPC的回调函数定义如下

``` c
typedef void grpc_iomgr_cb_func(void* arg, int success);
```
