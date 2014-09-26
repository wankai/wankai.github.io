---
layout: post
title: "Rust模块化机制"
date: 2014-09-25
---

## Rust模块化机制

许多人觉得Rust的模块机制很难理解，是因为误会了它的作用，把Module错当成了分发和链接的基本单位。Rust的模块仅仅是个命名空间，分发链接的基本单位叫crate。

其实明白了这一点后，Rust Module System就很容易理解了。
