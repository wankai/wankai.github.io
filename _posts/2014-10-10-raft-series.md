---
layout: post
title: "raft串讲"
---

## Raft串讲

<h2 id="preface">前言</h2>

本文不识图精确地描述Raft协议，而是提供一种不同的视角帮助大家加深对Raft协议的理解。建议读者将此文与论文对照着看。

<p><strong><a href="#introduction">1. 背景介绍</a></strong></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="#internet-and-ha">1.1. 互联网服务与高可用</a></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="#single-device">1.2. 单机系统</a></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="#replication">1.3. 多机系统</a></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="#enter-raft">1.4. Raft出场</a></p>
<p><strong><a href="#raft-basics">2. 基本协议</a></strong></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="#rsm">2.1. 复制状态机</a></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="#leader-election">2.2. 选举</a></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="log-replication">2.3. 日志备份</a></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <a href="safety">2.3. 正确性</a></p>
<p><strong>3. 集群变更</strong></p>
<p><strong>4. 日志紧缩</strong></p>
<p><strong>5. 客户端交互</strong></p>
<p><strong>6. 参考资料</strong></p>

<h3 id="introduction">1. 背景介绍</h3>

本章介绍理解Raft协议所需的背景知识，重点关注高可用的概念和方法。本文中“高可用”和“容错”两个词多数情况下可互换。

<h4 id="internet-and-ha">1.1 互联网服务与高可用</h4>

近十来年，互联网急速发展，行业内催生了诸多处理海量业务的大系统。在设计这些系统时，通常要借助分布式一致性协议达到高可用。

高可用对互联网业务非常重要。可用性直接关系经济利益，如果baidu.com有两个小时访问不了，百度就会损失500万营收。可用性关乎产品信誉，QQ在初期有不少竞争者，就是靠不掉线赢得了市场。可用性关乎工程师幸福指数，如果服务老出问题，工程师就没办法好好睡觉，休假旅游期间也是提心吊胆。

可用性 = (无故障运行时间 ) / (无故障运行时间 + 故障修复时间) * 100%，通常用几个9来表示，下表是一年内各种可用性对应的故障修复时间

<img src="/images/availability.png" with="389px" height="168px"/>

增强服务可用性有很多方法，具体情况要结合业务特点考虑，我们粗略的看单机系统和多机系统两大类。

<h4 id="single-device">1.2 单机系统</h4>

互联网业务是不允许单点服务的，但是单机服务是分布式服务的基础。以Redis为例，它是一个非常典型的单机系统。Redis的数据都在内存中，为了持久化和可靠性，每一条更新操作都会先写入日志，然后再修改内存中的数据。我们把Redis服务抽象成状态机:

<img src="/images/single-state-machine.png" width="380px" height="307px"/>

单机系统的故障恢复时间就是日志replay的耗时。系统运行越久，日志会越长，恢复耗时也越长，可用性随之降低。我们通常用snapshot方法解决日志过长导致可用性降低的问题，Redis正是如此。

<h4 id="replication">1.3 多机系统</h4>

无论采用什么方法，单机系统的可用性在互联网业务中都是不可接受的。就算程序写的再怎么好，服务恢复再怎么快，机器一挂就全完了，恢复时间可能长达数小时。

程序员一般采用主从切换机制解决可用性问题，有一台单机服务作为主，处理请求时异步地将日志增量发送给从服务，一旦检测出主服务不可用，就切换至从服务，从变主。

但是异步备份的主从切换机制有个很微妙的问题：当主服务宕掉时，最近的操作可能还没来得及发送给从服务，此时从变主会带来数据不一致，某些业务会因此招致风险。那我们用同步复制怎么样?？对不起，更糟糕了，主从任何一个服务宕掉复制操作都无法进行， 所以同步复制对提高可用性毫无裨益。主从切换问题总结如下：

1. 异步复制，能提高可用性，但是可能导致数据不一致
2. 同步复制，数据是一致的，但是根本不提高可用性

一致性和可用性，鱼和熊掌不可得兼吗？未必，轮到分布式一致性协议出场了。本文的主角Raft协议正是分布式一致性协议的一种，它能让我们手握熊掌吃烤鱼。

<h4 id="enter-raft">1.4 Raft出场</h4>

Raft将同步复制和异步复制结合起来，化解了一致性和可用性的矛盾。

Raft集群由N个服务组成，他们运行着相同的Raft协议，部署在不同的机器上，互相通信。主服务每次复制日志时，要求超过半数的机器（包括自己）是同步复制，其余的同步异步皆可。举个例子，如果集群有5台机器，主服务要把操作日志复制到剩下4台机器上，要求4台中至少2台是同步复制，其余的随便。从Raft的复制策略可以看出，它要求至少一半以上的服务是存活的，即容错(N-1)/2。当主服务宕掉时，剩下的服务会自动选出新的主，并且保证数据是一致的。

到此，大家应该知道了Raft存在的意义。至于Raft是怎么在容错(N-1)/2的情况下兼顾可用性和一致性，那正是本文的核心内容。可以说弄懂Raft协议是颇费精力的，请跟我来。

<h3 id="raft-basics">2. 基本协议</h3>

Raft协议包括很多方面，我们先看最基础的协议。后面几节会阐述Raft的其他方面，可能会对基础协议做一些修改，但是理解基础协议是根本。本节先从复制状态机的角度俯瞰Raft集群的行为，然后分别阐述协议的三大核心，即选举、日志复制和正确性。

<h4 id="rsm">2.1 复制状态机</h4>

前面说过，单机系统可以看成是一个状态机。Raft集群则可以看成多个状态机的集合，并且每个状态机的状态变迁过程是完全一样的，用理论术语叫复制状态机（Replicated State Machine）。

<img src="/images/replicated-statemachine.png" width="390px" height="256px" />

**复制状态机**提供了解决容错问题的全景视角。每个服务都是一个状态机，服务运行的过程即是状态的变迁，操作日志决定了变迁序列。Raft就是要保证

1. 每个服务的日志都是一样的;
2. 日志中的操作作用于状态机的顺序也是一样的;
3. 应答客户端前，要保证当前操作及前面的操作都已经作用于状态机。

<h4 id="leader-election">2.2 选举</h4>

Raft的选举机制保证同一时刻最多只有一个能commit操作的leader。5台机器的集群中是有可能存在3个状态为leader的服务的，但是只有一个是真正的leader，另外两个称为过期的leader。

"同一时刻最多只有一个能commit操作的leader"，我们来证明这个性质:

<strong>推论1：同一个term最多只有一个leader</strong>

```
假设有两个leader有相同的term，为L1, L2
那么必定有一个server，在选举时，既投给了L1又投给了L2
与“一个server对一个term只能投一次票”的机制矛盾

所以推论得证
```

<strong>推论2：如果集群中存在一个leader，为L，那么集群中过半的term >= term(L)</strong>

```
假设超过半的term < term(L)
因为L被过半数的服务投过票，所以这些服务的term在投给L后立即设置为term(L)
所以在当时，至少过半的term = term(L)
又因为Raft的term只增不减
所以，有过半的term >= term(L)

得证
```

<strong>推论3：如果某一时刻，leader L能commit，那么此时有过半的term = term(L)

```
根据提交机制，可得出有过半数服务的term <= term(L)
又根据推论2，得出过半的term >= term(L)
所以有过半的term，满足term(L) <= term <= term(L)，即term = term(L)

得证
```

<strong>定理： 任意时刻只有一个能commit的leader</strong>

```
假设某一时刻，有两个leader可以commit操作，分别为L1、 L2
根据推论1，term(L1) != term(L2)
L1能commit，根据推论2，有过半的term，满足term = term(L1)
L2能commit，根据推论2，有过半的term，满足term = term(L2)
所以必定存在一个S，满足 term(S) = term(L1) = term(L2)，即term(L1) = term(L2)
与term(L1) != term(L2)矛盾

命题得证。
```

<strong>总结</strong>

Raft集群中最多只有一个有效leader，当leader挂掉时会产生一个新leader，Raft保证这个新leader继承了上一代leader的全部意志。好比一个人的灵魂不死，当肉身死去时，赶紧转移到一个新肉身，于是就容错了。

<h4 id="log-replication">2.3 日志复制</h4>

leader不断的将自己的日志增量复制给follower，命令follower与自己保持一致。

<h4 id="safety">2.4 正确性</h4>
