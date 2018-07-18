---
layout: post
title: 10 个常见软件架构模式
description: 这个是之前在 Jobbole 上的翻译文章，记录之。
category: blog
---

这个是先前在 Jobbole 上的翻译文章 [10 个常用的软件架构模式](http://blog.jobbole.com/113953/) ，转到 Blog 上记录之。


你是否曾经思考过如何设计大型的企业级系统？在决定启动软件开发之前，首要的是选择恰当的架构来指引系统的功能及质量属性设计。因此在将软件架构应用于设计之前，必需要了解常用的架构模式。

![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233646527354.jpg)


## 什么是架构模式?

Wikipedia 的解释：

> 在软件架构中，架构模式是对特定环境下常见问题的通用且可重用的解决方案。架构模式与软件设计模式很相似，但架构模式的层次更高，且外延更大。

这篇文章将简述常见的 10 种架构模式的概念、用法以及其优缺点。

1. 分层模式(Layered pattern)
2. 客户端／服务器模式(Client-server pattern)
3. 主／从模式(Master-slave pattern)
4. 管道／过滤器模式(Pipe-filter pattern)
5. 代理模式(Broker pattern)
6. 对等模式(Peer-to-peer pattern)
7. 事件总线模式(Event-bus pattern)
8. 模型／视图／控制器(MVC)模式(Model-view-controller pattern)
9. 黑板模式(Blackboard pattern)
10. 解析器模式(Interpreter pattern)

## 1. 分层模式

分层模式(Layered pattern)于对结构化设计的软件进行层次拆解，每个层次为独立的抽象，为其上层抽象提供服务。

系统通常被拆分为以下四个层次：

* 表示层（也称为 UI 层）
* 应用层（也称为服务层）
* 业务逻辑层（也称为领域层）
* 数据访问层（也称为持久化层）

### 使用场景

* 通用桌面应用程序
* 电子商务 Web 应用

![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233648925198.jpg)

## 2. 客户端／服务器模式

客户端／服务器模式(Client-server pattern)由两个部分构成：一个服务器与多个客户端。服务器组件同时为多个客户端组件提供服务。客户端向服务器发启服务请求，服务器将相应服务信息回应给客户端。此外，服务器持续监听来自客户端的请求。


### 使用场景

* 电子邮件、文件共享及银行业务等在线应用


![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233717833733.jpg)

### 3. 主／从模式

主／从模式(Master-slave pattern)由两个部分构成：主设备与从设备。主服务组件将作业分发给多个从设备组件，并根据这些从设备反馈的结果，计算生成最终结果。

### 使用场景

* 数据库复制，主数据库被认定为权威数据源，各从数据库与主数据保持同步
* 在计算机系统中通过总线互连的各设备（包括主设备与从设备）

![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233718238936.jpg)


## 4. 管道／过滤器模式

管道／过滤器模式(Pipe-filter pattern)用于构造用于生成及处理数据流的系统。每个处理过程都封装在``过滤器（filter）``组件之中，要处理的数据通过 ``管道(pips)`` 进行投递。管道同时用于作为 ``过滤器（filter）`` 间的缓冲及同步。

### 使用场景

* 编译器，一系列的过滤器用于词法分析、语法分析、语义分析及代码生成
* 生物信息学的工作流

![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233718865512.jpg)


## 5. 代理模式

代理模式（Broker pattern）用于在结构化系统中对组件解耦。系统内各组件间采用远过程调用（remote service invocations）的方式交互。**代理（Broker）**组件充当组件间通讯的协调角色。

提供服务的组件将其能力（服务以及特性）发布给代理，客户端均向代理请求服务，由代理将请求重定向到先前已发布过对应服务的组件进行处理。

### 使用场景

* 消息中间件软件：Apache ActiveMQ，Apache Kafka，RabbitMQ 与 JBoss 等等

![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233719444041.jpg)

## 6. 对等模式

对等模式(Peer-to-peer pattern)中的组件称之为对等体（peer），对等体既作为向其他对等体请求服务的客户端，同时也做为响应其他对等体请求的服务端。对等体可以在运行过程中动态地改变其角色，即，既可以单独做为客户端或服务端运行，又可同时作为客户端与服务端运行。

### 使用场景

* 网络文件共享：[Gnutella](https://en.wikipedia.org/wiki/Gnutella) 与 [G2](https://en.wikipedia.org/wiki/Gnutella2))
* 流媒体协议：[P2PTV](https://en.wikipedia.org/wiki/P2PTV) 与 [PDTP](https://en.wikipedia.org/wiki/Peer_Distributed_Transfer_Protocol).
* 流媒体应用： [Spotify](https://en.wikipedia.org/wiki/Spotify).


![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233720492236.jpg)

## 7. 事件总线模式

事件总线模式（Event-bus pattern）应用于事件处理，主要由四个组件构成：**事件源（event source），事件侦听者（event listener），通道（Channel）以及总线（event bus）**。 事件源将消息发布到总线的特定通道，侦听者订阅相应的通道，事件源所发布的消息经通道通告给订阅通道的侦听者。

### 使用场景

* Android 开发
* 通告（Notification）服务

![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233720911021.jpg)


## 8. 模型／视图／控制器(MVC)模式

模型／视图／控制器模式（Model-view-controller pattern，简称 MVC 模式）将交互式应用程序拆分为三个部分：

1. **模型（model）** - 包含核心功能及数据
2. **视图（view）** - 呈现信息给用户（通过有多个视图）
3. **控制器（controller）** - 处理用户的输入操作

MVC 模式通过将内部信息表示、用户信息呈现以及用户操作接收分开的方式解耦组件，实现高效代码重用。

### 使用场景


* 主流开发语言所构建的互联网网页应用架构
* [Django](https://en.wikipedia.org/wiki/Django_%28web_framework%29) 与 [Rails](https://en.wikipedia.org/wiki/Ruby_on_Rails) 等网页应用开发框架

![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233721635183.jpg)

## 9. 黑板模式

黑板模式（Blackboard pattern）适用于 ``无预知确定解决策略`` 的问题，主要由三个组件构成：

* **黑板（blackboard）** - 用于存储解空间对象的结构化全局内存
* **知识（knowledge）**源 - 能自表意的专用模块
* **控制（control）**组件 - 选择、配置与执行的模块


所有的组件均能访问黑板，组件可将新生成的数据对象写入黑板，也可以通过模式匹配从黑板中获取知识源所生成的特定数据。

### 使用场景

* 语音识别
* 车辆识别和追踪
* 蛋白质的结构鉴定
* 声纳信号解析

![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233722237447.jpg)

## 10. 解析器模式

解析器模式（Interpreter pattern）用于设计语言的解析程序，主要用于指定评估程序代码行，即解析出特定语言的语句与表达式，其核心思想是为语言的每个符号定义相应的类。

### 使用场景

* SQL 等数据库查询语言
* 通讯协议描述语言

![](http://7xjudy.com1.z0.glb.clouddn.com/2018-04-25-15233722490294.jpg)

## 架构模式综述


下表格总结了各架构模式的优缺点。

![](http://jbcdn2.b0.upaiyun.com/2018/03/525d773e6ca3b2bebc40eb22baac20cf.png)

希望这篇文章对你所帮助

感谢阅读





[原文](https://towardsdatascience.com/10-common-software-architectural-patterns-in-a-nutshell-a0b47a1e9013)



