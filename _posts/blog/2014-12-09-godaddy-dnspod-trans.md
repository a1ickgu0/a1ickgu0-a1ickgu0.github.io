---
layout: post
title: 迁移Godaddy域名解析至DNSPod
description: 因为众所周知的原因，Godaddy 的域名解析经常出现一些状况，因此需要将 Godaddy 上买购买的域名迁到天朝来。
category: blog
--- 

<img src="https://farm7.staticflickr.com/6222/6300694811_552f8fa96f.jpg" width="500" height="374" alt="Oh shit" />

域名先前是在狗爹买的，自然使用的是它所提供的NameServer服务，由于众所周知的原因，不被天朝所监管的服务用起来总是三不五时需要问候一下方教授，深受其苦。

外事不决问谷狗，谷狗正解是将域名依旧留在狗爹，而将NameSever迁移到[DNSPod][1]，具体的操作见[DNSPod的指导][2]，简要描述如下:

*   DNSPod注册帐户。
*   在Godaddy上将NameServer指到DSNPod的服务器。
*   在DNSPod的域名管理后台将原先Godaddy上配置的域名解析规则重新配置到DNSPod上。

 [1]: https://www.dnspod.cn/
 [2]: https://support.dnspod.cn/Kb/showarticle/tsid/42/



