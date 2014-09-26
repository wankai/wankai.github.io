---
layout: post
title: "Rust模块化机制"
date: 2014-09-25
---

## Rust模块化机制

许多人觉得Rust的模块机制很难理解，是因为误会了它的作用。Rust模块仅仅是个命名空间，不是分发、链接的基本单位。真正用于分发和链接的叫crate，所以我们应该用namespace的概念去理解Rust的模块化机制
