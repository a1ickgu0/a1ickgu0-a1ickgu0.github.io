---
layout: post
title: Shadowsocks 客户端配置
description: 拿梯子爬墙上 google & github 是码农提高效率的必备技能，shadowsock 是一个很好用的样子工具，但使用一定要低调。
category: blog
---


## MAC OSX 客户端

OSX客户端[下载地址][1]，需要配置的内容很少

#### 第一步，添加服务器

"服务器" -> "打开服务器设定" 中添加服务器信息，主要包括以下内容

*   地址，填写服务器的域名（或IP地址）以及SS账号的端口号，比如 8039。
*   加密，选择服务器所使用的加密算法，通常采用 aes-256-cfb 算法。
*   密码，填写账号对应的密码。
*   备注，填写用于记忆的服务器名字，比如：新加坡节点。

![server_setting][2]

#### 第二步，客户端配置

![global_setting][3]

**重要**，在“服务器”中钩选刚才所添加的服务器，比如“新加波节点”，选择 “Shadowsocks 打开” 之后即可以科学上网了。

默认情况下，客户端使用的是“自动代理模式”，自动模式下，只有必要的国外网站才会走代理服务器，其他服务器则不代理，这种方式上网流量按需代理，使用效果最好。

有些时候，我们可能需要让所有上网流量都走代理（比如：在星巴克、机场等等不可依赖的地方上网），此时勾选“全局模式”，所有上网流量都通过加密代理服务器中转，可以有效避免上网数据被WIFI嗅探。

## iOS 客户端

iOS客户端有两个版本，App Store 与 Cydia 版本，下载地址见以下链接：

*   [Cydia 版本][4]
*   [App Store版本][5]

App Store 版本使用起来有很多限制，基本上只能用其自带的客户端，而手机上诸如 Twitter、Facebook等客户端软件，使用起来极不方便，因此对于这个软件就不说了，重点说一下 Cydia 版本。

Cydia 软件包括两个部分，直接在 cydia 上搜索 shaodowsocks 安装

![cydia][6]

*   配置客户端，Shadowsocks ：shadowsocks 的基本配置，内容与 MAC OSX 上的类似。

![s1][7]

*   增强插件，Shadowsocks Per-App Proxy：iOS 上的增强配置，包括禁用 SPDY 协议，让 facebook, twitter 等官方客户可以使用代理，插件更为强大之处在于可以指定哪些应用软件使用代理，一般情况下：Facebook、Twitter、Mail、Inbox以及Safari等等常用外网的客户设置为打开代理，其他客户端则不使用代理。

![s2][8]

使用 Per-App Proxy 插件可以完美地解决一些包流量客户端，使用代理之后流量统计可能出现错误，被收费的问题，比如使用“虾米音乐”的流量包月服务，只要在插件中关闭“虾米”代理，以确保使用shadowsocks代理时，“虾米” 能够正常使用流量包月服务。

 [1]: https://github.com/shadowsocks/shadowsocks-windows/releases
 [2]: http://i3.tietuku.com/973e03a328592a09.png
 [3]: http://i3.tietuku.com/b554da459336de4e.png
 [4]: https://github.com/linusyang/MobileShadowSocks/releases
 [5]: https://itunes.apple.com/us/app/shadowsocks/id665729974?mt=8
 [6]: http://i3.tietuku.com/b53af86a3b5b347et.jpg
 [7]: http://i3.tietuku.com/805affd1c85bdcf3t.jpg
 [8]: http://i3.tietuku.com/9dd71b56b37b4c28t.jpg

