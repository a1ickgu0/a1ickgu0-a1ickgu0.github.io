---
layout: post
title: Snort 初探
description: 最近在看 DPI 的技术时，了解到开源的 Snort 软件，这个由个人开发，之后被 Cisco 收购的开源 NIPS/NIDS 真是强大，特别是 Rule 定义的灵活性，太无敌了。
category: blog
---
# Snort 初探

## 概述

Snort 是免费 Network Intrusion Prevention System(NIPS) 及 Network Intrusion Detection System (NIDS) 软件，其具有对数据流量分析和对网络数据包进行协议分析处理的能力，通过灵活可定制的规则库(Rule)，可对处理的报文内容进行搜索和匹配，能够检测出各种攻击，并进行实时预警。

软件采用[双授权:](https://www.snort.org/license)
* 源代码及社区版本规则库使用 GPLv2 授权
* 专用规则库(Proprietary Snort Rules)使用 "Non-Commercial Use License"授权，只允许个人使用，不可用作商业


## 架构

![](http://7xjudy.com1.z0.glb.clouddn.com/2016-12-08-14809953928618.jpg)


* Packet Decoder，从接口收帧，作报文粗解析(Ethernet/WIFI/IP/UDP/TCP/GRE等)，最深解析到 IP 协议字段的首部，将报文送到 Preprocessor
* Preprocessor，使用 Plugin 解析报文，主要 Plugin 如 HTTP/FTP/SSH 等等 preprocessor plugin，使用规则(preprocessor rule)做初解析及执行行为(alert/log/pass/drop/sdrop/rject 等)，分片报文在这个阶段进行重组
* Detetction Engine，Snort 的核心模块，将从 preprocessor 送来的报文进行更精确的规则(snort rule)匹配，包括：应用、流方向、报文模式匹配等，后执行行为
* Logging and Alerting System，对于规则匹配中需触发 Log 及告警的流进行处理，写入本地文件 ``/var/log/snort`` 目录
* Output Modules，将 Log 及告警通过 Unix Socket, WindowsPopup, SNMP Trap 等方式提交到日志服务器或数据库


## 规则

### Decoder 与 Preprocessor 规则

Decoder 阶段使用 ``snort.conf`` 文件中 ``config disbale_decode_alerts`` 参数配置告警行为，通过 `` config enable_decode_drops`` 参数的优先级高于 rule 中的 drop 行为优先级。

Decoder 与 Preprocessor 的 Rule 通过配置文件保存在源代码的 ``preproc_rules/`` 目录，对应于 ``decoder.rules`` 与 ``preprocessor.rules`` 文件， Decoder 与 Preprocessor 的 Rule 的启用需要在 snort.conf 中增加相应的配置:
ZZ
```

    var PREPROC_RULE_PATH /path/to/preproc_rules
    ...
    include $PREPROC_RULE_PATH/preprocessor.rules
    include $PREPROC_RULE_PATH/decoder.rules
```

Decoder 与 Preprocessor 的 Rule 支持以下行为:

* alert，发送告警信息
* log，记录 Log 信息
* pass，通过，正常转发
* drop，丢弃报文，并记录
* sdrop，丢弃报文，不记录
* reject，丢弃报文，并记录，对于 TCP 报文，回应 RESET，对于 UDP 回应 ICMP Port unreachable

Rule 的样例 "对于 Ether-Type 为 0x0800 但 IPv4 首部中协议版本号不为 v4 的报文进行告警" 描述如下:

```
alert ( msg: "DECODE_NOT_IPV4_DGRAM"; sid: 1; gid: 116; rev: 1; \
        metadata: rule-type decode ; classtype:protocol-command-decode;)
```

Snort 支持多种类似的 Decoder 与 Preprocessor 处理，详细信息如下:

![](http://7xjudy.com1.z0.glb.clouddn.com/2016-12-08-14810266487430.jpg)

基本上常见的应用协议 Preprocessor 都支持。


### Detection 规则


Snort Detection 的 Rule 规则由 Rule Header 与 Options 两个部分构成

![](http://7xjudy.com1.z0.glb.clouddn.com/2016-12-08-14810187352895.jpg)


* Rule Header，描述规则流的行为及基本特征，描述串为固定格式信息 `` [行为][协议][IP地址][端口号][流方向][IP地址][端口号] `` 七类信息
* Rule Opitons，描述规则流的精确匹配信息，包括：HTTP 首部、Cookie、UA、特定偏移的字串，等等，这个是 Snort 最强大的部分，也是 Snort 的核心部分

下面的例子: 从 ``$BAD_NETK`` 网络任意源端口到 ``$HOME`` 网络的 80 端口 且有效载荷中包含十六进制为 0x47 0x45 0x54（即 "GET"）字符串的报文。

![](http://7xjudy.com1.z0.glb.clouddn.com/2016-12-08-14810069729839.jpg)

### Rule Header

![](http://7xjudy.com1.z0.glb.clouddn.com/2016-12-08-14810318232134.jpg)

Detection 阶段的 Rule Action 比 Decoder/Preprocessor 阶段增加了 Activate / Dynamic 行为， Activate 定义流规则同时指定规则 ID (activates:ID)，Dynmaic 规则通过指定的规则 ID 进行激活(activated_by:ID)，这两个行为能够组合出更灵活的 Snort Rule 规则.

Rule 样例 "检测到非 $HOME_NET 对 IMAP(TCP 80端口) 的 buffer overflow 攻击后，记录后续的 50 个报文首部信息" 如下:

```
activate tcp !$HOME_NET any -> $HOME_NET 143 (flags:PA; \
    content:"|E8C0FFFFFF|/bin"; activates:1; \
    msg:"IMAP buffer overflow!";)
dynamic tcp !$HOME_NET any -> $HOME_NET 143 (activated_by:1; count:50;)
```

通过 Activate/Dynamic 组合，在识别出流量后，可收集后续关联流量，以供进一步的流量分析处理。

### Rule Options

规则 Options 是 Snort 的 Detetction Engine 最核心的技术，通过丰富的 Options 提供易用、灵活且强大的规则定义机制，规则的 Options 为 Rule 中的 ``(;)`` 定义的部分，通过 ``;`` 定义多个 option，每个 option 内的关键字使用 ``:`` 标识。

包括四个分别为 ``flags``, ``content``, ``activates`` 与 ``msg`` 关键字的 Option 样例:

```
activate tcp !$HOME_NET any -> $HOME_NET 143 (flags:PA;
    content:"|E8C0FFFFFF|/bin"; activates:1; \
    msg:"IMAP buffer overflow!";)
```

Snort 的 Rule Options 主要有以下四类:

* general，提供 Rule 的基本信息，并不对流量产生任何行为。
* payload，对数据流的有效载荷进行查找，可进行多数据关联查找。
* non-payload，非数据载荷查找。
* post-detection，对检测出的特定的规则进行关联触发。

#### 通用选项(General)

##### 关键字

| 关键字 | 格式 | 说明 |
| --- | --- | --- |
| msg | msg:”message text" | 用于 alert/log 引擎进行信息告警/记录及报文记录 |
| reference | reference:<id system="">, <id>; [reference:<id system="">, <id>;]</id></id></id></id> | 外部信息引用，包括 URL/CVE 等等 |
| gid | <div>gid:<generator id="">;</generator></div> | General ID，区分 Snort 事件分类，ID 大于 100 是给特定 Preprocessor 与 decoder |
| sid | sid:<snort rules="" id="">;</snort> | Snort Rule ID，每个 Rule 的唯一标识 |
| rev | rev:<revision integer="">;</revision> | sid 所对应 rule 的版本号 |
| classtype | classtype:<class name="">;</class> | Rule 分类，Snort 已做了 4 个优先级 34 种分类 |
| priority | priority:<priority integer="">;</priority> | Rule 优先级 |
| metadata | metadata:key1 value1, key2 value2; | Key-Value 格式存储的 Rule 扩展信息 |

##### 样例

1. msg /reference 关键字

```
alert tcp any any -> any 7070 (msg:"IDS411/dos-realaudio"; \
    flags:AP; content:"|fff4 fffd 06|"; reference:arachnids,IDS411;)

alert tcp any any -> any 21 (msg:"IDS287/ftp-wuftp260-venglin-linux"; \
    flags:AP; content:"|31c031db 31c9b046 cd80 31c031db|"; \
    reference:arachnids,IDS287; reference:bugtraq,1387; \
    reference:cve,CAN-2000-1574;)
```

2. gid/sid/rev 关键字

```
alert tcp any any -> any 80 (content:"BOB"; gid:1000001; sid:1; rev:1;)
```

3. classtype 关键字

```
alert tcp any any -> any 25 (msg:"SMTP expn root"; flags:A+; \
    content:"expn root"; nocase; classtype:attempted-recon;)
```

4. priority 关键字

```
alert tcp any any -> any 80 (msg:"WEB-MISC phf attempt"; flags:A+; \
    content:"/cgi-bin/phf"; priority:10;)
alert tcp any any -> any 80 (msg:"EXPLOIT ntpdx overflow"; \
    dsize:>128; classtype:attempted-admin; priority:10 )
```

5. metadata 关键字

```
alert tcp any any -> any 80 (msg:"Shared Library Rule Example"; \
    metadata:engine shared; metadata:soid 3|12345;)

alert tcp any any -> any 80 (msg:"Shared Library Rule Example"; \
    metadata:engine shared, soid 3|12345;)

alert tcp any any -> any 80 (msg:"HTTP Service Rule Example"; \
    metadata:service http;)
```

#### 载荷选项(Payload)
##### 关键字
Snort 的 Payload 选项的关键字(Key)非常丰富，主要分为三大类

* 查找关键字：对数据流进行内容查找，包括: content, protected_content 、byte_[test/jump] 及 isdata
* 协议关键字：Preprocessor 解析协议相关的选项查找
* 修饰关键字：修饰查找行为的关键字，主要包括：nocase, rawbytes, depth, offset, distance, within, http_[cookie/header/method/uri] 等等

具体的参看下图
![Payload Rule](http://7xjudy.com1.z0.glb.clouddn.com/2016-12-08-Payload Rule.jpg)


##### 样例

载荷查找样例:

```

# 匹配 HTTP Cookie 中包括 ABC 与 EFG 的流
alert tcp any any -> any 80 (content:"ABC"; content:"EFG"; http_cookie;)

# 匹配 TCP 报文 ABC 之后 10 个字节内存在 EFG 的流
alert tcp any any -> any any (content:"ABC"; content:"EFG"; within:10;)

# 匹配 HTTP 报文的 URI 满足 foo.php?id=<数值> 的流
alert tcp any any -> any 80 (content:"/foo.php?id="; pcre:"/\/foo.php?id=[0-9]{1,10}/iU";)

# 匹配 HTTP 报文首部包含 User-Agent 的流
alert tcp any any -> any 80 (content:"User-Agent:"; http_header; nocase;)

```


#### 非载荷选项(Non-Payload)
##### 关键字

Non-Payload 的关键字用于匹配 IP 层、传输层协议的关键字段，主要包括：

* fragoffset，匹配 IP 报文中分片 offset 字段 `` fragoffset:[!|<|>]<number>;``
* ttl，匹配 IP 首部的 TTL 字段，支持比较 ``ttl:[<, >, =, <=, >=]<number>;``，支持数值范围 `` ttl:[<number>]-[<number>]; ``
* tos，匹配 IP 首部 tos 字段 ``tos:[!]<number>;``
* id，匹配 IP ID 字段，``id:<number>;``
* ipopts，匹配 IP 首部的选项，``ipopts:<rr|eol|nop|ts|sec|esec|lsrr|lsrre|ssrr|satid|any>;``
* fragbits，匹配 IP 首部的分片标识，支持使用 ``+/*/!`` 进行逻辑组合 ``fragbits:[+*!]<[MDR]>;``
* dsize，匹配/测试 报文的载荷长度，``dsize:min<>max;`` 与 `` dsize:[<|>]<number>; ``
* flags，匹配 TCP 的 Flag 字段，支持使用 ``+/*/!`` 进行逻辑组合，`` flags:[!|*|+]<FSRPAUCE0>[,<FSRPAUCE>]; ``
* flowbits，匹配/设置 TCP 的 Flag 字段，用于跟踪 TCP 协议 Sesion 状态
```
flowbits:[set|setx|unset|toggle|isset|isnotset|noalert|reset][, <bits/bats>][, <GROUP_NAME>];
bits ::= bit[|bits]
bats ::= bit[&bats]
```
* seq，匹配 TCP 的序列号，``seq:<number>;``
* ack，匹配 TCP 的 ACK 序列号，``ack:<number>;``
* windows，匹配 TCP 的窗口大小，``window:[!]<number>;``
* itype，匹配 ICMP 的 type 字段，``itype:min<>max;`` 与 ``itype:[<|>]<number>;``
* icode，匹配 ICMP 的 code 字段，``icode:min<>max;`` 与 ``icode:[<|>]<number>;``
* icmp_id，匹配 ICMP 的 ID 值，``icmp_id:<number>;``
* icmp_seq，匹配 ICMP 的序列号，``icmp_seq:<number>;``
* rpc，匹配 SUNRPC 请求信息，``rpc:<application number>, [<version number>|*], [<procedure number>|*]>;``
* ip_proto，匹配 IP 的协议字段，``ip_proto:[!|>|<] <name or number>;``
* sameip，匹配源 IP 与目的 IP 相同的流
* stream_reassemable，对 TCP 流的重组流进行设置，``stream_reassemble:<enable|disable>, <server|client|both>[, noalert][, fastpath];``
* stream_size，匹配指定长度的 TCP 流，``stream_size:<server|client|both|either>, <operator>, <number>;``

##### 样例

非载荷查找样例:

```
# 记录 IGMP 报文
alert ip any any -> any any (ip_proto:igmp;)

# 记录源 IP 与目的 IP 相同报文
alert ip any any -> any any (sameip;)

# 记录客户端发出的小于 6 Byte 的 TCP 流
alert tcp any any -> any any (stream_size:client,<,6;)

```

####  检测后处理选项(Post-Detection)

##### 关键字

* logto，将规则对应的信息记录到指定的 LOG 文件中，``logto:"filename";``
* session，将 TCP Session 中的用户数据报文进行记录，``session:[printable|binary|all];``
* resp，通过发送响应结束对 Session 的请求
* react，向对应对的客户送发送 WEB 页面应答后关闭连接
* tag，匹配规则的流触发后续报文进行记录，可指定针对流的源[或/且]目的主机进行后续记录，``tag:host, <count>, <metric>, <direction>;`` 与 ``tag:session[, <count>, <metric>][, exclusive];``
* activates 与 activated_by，用于关联两个 Rule，第1个 Rule 使用 ``activates:id`` 设定 ID，第2个 Rule 使用 ``activated_by:id`` 激活
* count，修饰 activated_by，用于指定激活规则的报文个数
* replace，修订先前 content 匹配的内容，要求 replace 的长度与 content 一致，``replace:"<string>";``
* detection_filter，事件在特定的发生频率之后被激活，``detection_filter: track <by_src|by_dst>, count <c>, seconds <s>;``

##### 样例

检测后处理样例:

```
# 记录 TELNET 中所有可见字符内容
log tcp any any <> any 23 (session:printable;)

# 丢弃 1 分钟内登录失败超过 30 次的 SSH 请求
drop tcp 10.1.2.100 any > 10.1.1.100 22 ( \
    msg:"SSH Brute Force Attempt";
    flow:established,to_server; \
    content:"SSH"; nocase; offset:0; depth:4; \
    detection_filter:track by_src, count 30, seconds 60; \
    sid:1000001; rev:1;)
```

【End]
