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

``` greeting.rs
fn main() {
    println!("hello, rust");
}
```
运行`rustc greeting.rs`会生成同名的可执行文件greeting

``` greeting.rs
pub fn hello() {
    println!("hello, rust");
}
```
运行 `rustc greeting.rs --crate-type=lib` 会生成同名的库libgreeting.rlib。函数前面多了一个关键词pub，如果不加的话编译器会告警，说“你这个库啥public的东西都没有，让别人用毛啊”。恩，Rust编译器还是很贴心的。

### 引入命名空间
Rust的模块就是命名空间，用关键词`mod`表示。它的作用是把一个crate的代码划分成可管理的部分。每一个crate都有一个顶层的匿名根命名空间, 根空间下面的命名空间可以任意嵌套，这样构成一个树形的空间结构。

假设我们接到一个项目，制作一个包含各国语言日常用语的程序库。

* 首先，这个库应该按语种名组织，中俄日美等都给他们划分出一个模块
* 日常用语有很多，如果我们想把代码组织得清晰一点，还可以按照使用场景再划分一次，比如有用于问候的（“你好”、“吃了么”），有用语离别的（“再见”，“天下谁人不识君”）

根据以上两点分析，我们代码库组织如下，以中英两种语言为例

```
// greeting.rs
pub mod english {
    pub mod greetings {
        fn hello() {
            println!("Hello!")
        }
        fn guies() {
           println!("Hey, guies!")
        }
    }
    pub mod farewells {
        fn goodbye() {
            println!("Goodbye!")
        }
        fn see_you() {
            println!("See you!")
        }
    }
}

pub mod chinese {
    pub mod greetings {
        fn hello() {
            println!("你好!")
        }
        fn eat() {
            println!("吃了么?")
        }
    }
    pub mod farewells {
        fn goodbye() {
            println!("再见!")
        }
        fn everyone_will_know_you() {
            println("天下谁人不识君!")
        }
    }
}

```

运行 `rustc phrases.rs --crate-type=lib` 得到一个程序库，其模块树形图如下

<img src="/images/rust-mod-tree.png" width="400px" height="210px"/>

* phrases::chinese::greetings::everyone_will_know_you()                -> 天下谁人不识君
* use phrases::chinese; chinese::greetings::everyone_will_know_you()  -> 天下谁人不识君
* use phrases::chinese::greetings; greetings::everyone_will_know_you() -> 天下谁人不识君


### 打散命名空间

### 提升命名空间

