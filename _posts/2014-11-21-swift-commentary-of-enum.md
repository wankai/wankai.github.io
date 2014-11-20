---
layout: post
title: "Swift枚举"
---

# Swift枚举类型

## 概论

用过C/C++/Java的人都知道枚举类型，但是Swift的枚举类型比他们都要强大。原来在C/Java中需要struct/class的情景，在Swift中可能更适合用枚举。

Swift枚举也被称为Sum Type或者Tag Union，实际上是一个二层的类型层次，通过模式匹配做down-casting。

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
