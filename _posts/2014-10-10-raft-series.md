---
layout: post
title: "raft串讲"
---

## Raft串讲

Raft是一种分布式一致性协议，对于开发大规模分布式系统至关重要。 

<p><a href="#introduction">1. 背景介绍</a></p>
<p>&nbsp;&nbsp;&nbsp; 1.1. 互联网服务与高可用</p>
<p>&nbsp;&nbsp;&nbsp;1.2. 单机系统</p>
<p>&nbsp;&nbsp;&nbsp;1.3. 备份容错</p>
<p>2. 基本协议</p>
<p>&nbsp;&nbsp;&nbsp;2.1. 选举</p>
<p>&nbsp;&nbsp;&nbsp;2.2. 日志备份</p>
<p>&nbsp;&nbsp;&nbsp;2.3. 正确性</p>
<p>3. 集群变更</p>
<p>4. 日志紧缩</p>
<p>5. 客户端交互</p>

<h3 id="introduction">1. 背景介绍</h3>
<h4 id="internet-and-ha">互联网服务与高可用</h4>
