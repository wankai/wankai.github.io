---
layout: post
title: "Rust模块化机制"
date: 2014-09-25
---

## Rust模块化机制

刚开始学习Rust时，我觉得它的模块机制难以理解。后来我明白是自己想多了，Rust模块其实就是命名空间，仅此而已。它的crate倒更像是其他语言中的模块。

这一步走通，接下来就顺畅了，我们来看看Rust模块机制的设计理念。


### 编译
Rust编译器只接受一个.rs文件作为输入，并且只生成一个crate。

<img src="/images/rustc.png" width="450px" height="140px" />

有两种crate，可执行文件和库，源文件中有main函数则生成可执行文件，没有则生成库。

```
fn main() {
    println!("hello, rust");
}
```
