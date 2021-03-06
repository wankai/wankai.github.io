---
layout: post
title: "Swift类型和数据抽象"
---

# Swift类型和数据抽象

抽象是一种非常根本的编程方法。抽象分两种：数据抽象和操作抽象。本文阐述Swift给数据抽象提供了哪些方法。

### 抽象的概念

抽象是人认识世界的基本方法。世界很大，人脑人小。大脑要认识世界，必须把暂时不想认知的东西剔除出去，把剩下的东西提取出来当做一个整体，然后研究它的性质。我们每天都在抽象：

> 当我们说“猫很萌”时就用了抽象。猫这个概念，就是把实体猫共同的特点提取出来，形成一个词语。几乎所有的概念名词都使用了抽象。

> 当我们看到一张桌子，其实也用了抽象。假设桌子是木质材料，当我们指桌子时，并不会认为它是木头，也不会认为它是一团分子。这是因为我们把这一坨东西看成一个整体了，它有四条腿，能放东西。

编程其实就是在创造世界，抽象是程序员创造世界的一种方法。

# 数据抽象

把若干个甚至无限个数据放在一起，把它们当做整体看待，就是数据抽象。

Swift根据数据应用场景，把数据抽象分成了struct、enum、class、tuple四类。

其中struct、enum、class，我们可以给它们取名字，这在哲学中叫“指称”，如

``` swift
struct Point {
    let x: Int
    let y: Int
}

enum Direction {
    case North
    case South
    case East
    case West
}

class Person {
    let name: String
}
```

上面三个数据类型都使用了抽象和指称。Swift还提供了一种叫做typealias的指称手段，如

```swift

typealias SomePerson = Person	// 现在SomePerson和Person两个名字都指称同一个类型

```

tuple的指称方式只有typealias
