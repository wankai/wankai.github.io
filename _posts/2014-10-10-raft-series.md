---
layout: post
title: "raft串讲"
---

## Raft串讲

Raft是一种分布式一致性协议，对于开发大规模分布式系统至关重要。 

<p><a href="#introduction">1. 背景介绍</a></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="#internet-and-ha">1.1. 互联网服务与高可用</a></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="#single-device">1.2. 单机系统</a></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="#replication">1.3. 备份容错</a></p>
<p>2. 基本协议</p>
<p>&nbsp;&nbsp;&nbsp;2.1. 选举</p>
<p>&nbsp;&nbsp;&nbsp;2.2. 日志备份</p>
<p>&nbsp;&nbsp;&nbsp;2.3. 正确性</p>
<p>3. 集群变更</p>
<p>4. 日志紧缩</p>
<p>5. 客户端交互</p>

<h3 id="introduction">1. 背景介绍</h3>

本章介绍理解Raft协议所需的背景知识，重点关注高可用的概念和方法。本文中“高可用”和“容错”两个词多数情况下可互换。

<h4 id="internet-and-ha">1.1 互联网服务与高可用</h4>

近十来年，互联网急速发展，行业内催生了诸多处理海量业务的大系统。在设计这些系统时，通常要借助分布式一致性协议达到高可用。

高可用对互联网业务非常重要。可用性直接关系经济利益，如果baidu.com有两个小时访问不了，百度就会损失500万人民币。可用性关乎产品信誉，QQ在初期有不少竞争者，就是靠不掉线赢得了市场。可用性关乎工程师幸福指数，如果服务老出问题，工程师就没办法好好睡觉，休假旅游期间也是提心吊胆。

可用性 = (无故障运行时间 ) / (无故障运行时间 + 故障修复时间) * 100%，通常用几个9来表示，下表是一年内各种可用性对应的故障修复时间

<img src="/images/availability.png" with="389px" height="168px"/>

<h4 id="single-device">1.2 单机系统</h4>

<h4 id="replication">1.3 备份容错</h4>
