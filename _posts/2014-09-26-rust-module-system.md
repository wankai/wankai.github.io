---
layout: post
title: "Rust模块化机制"
date: 2014-09-25
---

## Rust模块化机制

刚开始学习Rust时，我觉得它的模块机制难以理解。后来我明白是自己想多了，Rust模块其实就是命名空间，仅此而已。它的crate倒更像是其他语言中的模块。

这一步走通，接下来就顺畅了，我们来看看Rust模块机制的设计理念。


### 编译
Rust编译器只接受一个.rs文件作为输入，并且只生成一个crate。这一点要牢记。

生成的crate分两种，源文件中有main函数会生成可执行文件，无main函数则生成库。

``` hello.rs
fn main() {
    println!("hello, rust");
}
```
运行`rustc hello.rs`会生成同名的可执行文件hello

``` hello.rs
pub fn hello() {
    println!("hello, rust");
}
```
运行 `rustc hello.rs --crate-type=lib` 会生成同名的库libhello.rlib。函数前面多了一个关键词pub，如果不加的话编译器会告警，说“你这个库啥public的东西都没有，让别人用毛啊”。恩，Rust编译器还是很贴心的。

### 引入命名空间
Rust的模块就是命名空间，用关键词`mod`表示

``` greeting.rs
pub mod greeting {
    pub mod english {
        pub fn println("hello, english world")
    }
    pub mod chinese {
        pub fn println("hello, chinese world")
    }
}
```

### 打散命名空间

### 提升命名空间

### 提升
