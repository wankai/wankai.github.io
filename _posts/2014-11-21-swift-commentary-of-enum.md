---
layout: post
title: "Swift枚举"
---

# Swift枚举类型

## 概论

用过C/C++/Java的人都知道枚举类型，但是Swift的枚举类型比他们都要强大。原来在C/Java中需要struct/class的情景，在Swift中可能更适合用枚举。

Swift枚举也被称为Sum Type或者Tagged Unions，实际上是一个二层的类型层次，通过模式匹配做down-casting。

## 典型应用

商家仓库存储了很多商品，有些是一维码，有些是二维码

```swift
enum BarCode {
    case UPCA(Int, Int, Int, Int)
    case QRCode(String)
}

// 枚举名BarCode可以起命名空间的作用
var productCode = BarCode.UPCA(8, 85909, 51226, 3)

// 当编译器可以推断类型时，命名空间可省略
productCode = Barcode.QRCode("ABCDEFGHIJKLMNOP")

// 用pattern match提取出封装的值
switch productCode {
    case .UPCA(let system, let manufacturer, let product, let check):
        println("UPC-A: \(system),\(manufacturer,\(product),\(check).")
    case .QRCode(let productCode):
        println("QR code: \(productCode)")
}
```

## 朴素应用
枚举当然也可以完成C/C++那种很弱的功能。

假设我们有一个指南针，可以指向东南西北，建模如下

```swift
enum CompassPoint {
    case North
    case South
    case East
    case West
}
```
North ... West可以看成是单例的类。比如North，表明类型是North，值也是North。用法同典型应用。

## 带值应用
在朴素应用中，North ... West的值就是自己，而不是和C/Java那样的数值。有时候我们希望枚举是一个值

可以是数值

```swift
enum CompassPoint : Int {
    case North = 1
    case South
    case East
    case West
}

// 返回类型是 CompassPoint?，因为并不是每个Int都有效
let compassPoint = CompassPoint(rawValue: 1)

println("\(compassPoint.rawValue)")
```

Raw Value还可以是String, Character, Float, Double类型的。枚举的每个值都不能重，否则编译器报错。


## 总结

* Swift枚举就是Tagged Unions

* 枚举是值拷贝的

* 枚举不能在继承树中

* 枚举不能带stored properties

* 枚举的空间应该是以最大值为准的（未证实）

* 枚举可以带Raw Value，但必须是基本类型，并且构造时返回的是Optional Type

* 枚举名可以作为命名空间。当编译器可以类型推断时，命名空间可省略

* pattern match时，let和var如果全部一致，可以提到枚举名前面
