---
layout: post
title: 小米Mini路由器刷公版 OpenWrt 
description: 小米路由本身是基于 OpenWrt 开发的，虽然有提供 SSH 版本，但开放性依旧不足，想要玩得爽，还是得刷成公版的 OpenWrt，刷机的过程也很简单。
category: blog
--- 


小米路由本身是基于 OpenWrt 开发的，虽然有提供 SSH 版本，但开放性依旧不足，想要玩得爽，还是得刷成公版的 OpenWrt，刷机的过程也很简单：

###刷 SSH 版本

1. 下载小米的 [ssh](https://d.miwifi.com/rom/ssh) 版本，命名为 miwifi_ssh.bin 保存到 U 盘根目录，同时记录页面所显示的 SSH 密码

2.  将小米路由下电后，再把 U 盘插入小米路由

3. 长按 REST 键，上电开机，直到 LED 灯橙色闪烁时放开，此时小米路由开始刷机，待 LED 由橙灯变为蓝灯后，即可使用 SSH 密码进行登录


> 注：刷机前确保小米路由当前运行的是原生的小米系统，如果不是的话，需要下载 miwifi.bin 先刷新系统，之后再刷 SSH 版本



###SSH 登录刷 OpenWrt

在终端下使用 scp 将下载好的 OpenWrt 固件写到路由器的 /tmp 目录

```

scp openwrt-15.05.1-ramips-mt7620-xiaomi-miwifi-mini-squashfs-sysupgrade.bin root@192.168.31.1:/tmp

```



通过 ssh 登录到小米路由开始刷新 OpenWrt 固件

```

ssh root@192.168.31.1

root@XiaoQiang:/tmp# mtd -r write openwrt-15.05.1-ramips-mt7620-xiaomi-miwifi-mini-squashfs-sysupgrade.bin OS1

Unlocking OS1 ...



Writing from openwrt-15.05.1-ramips-mt7620-xiaomi-miwifi-mini-squashfs-sysupgrade.bin to OS1 ...     

Rebooting ...

```

刚刷新完 OpenWrt 小米路由的 LED 可能是红色的，同时可能看不到 OpenWrt 的 SSID，这时不要慌，使用小米的 LAN 接入，用浏览器访问 192.168.1.1 对 OpenWrt 的  WIFI 进行配置即可，LED 灯红色，很可能是 OpenWrt 最新版本没有对 LED 进行正确的设置，不要理会即可。 

至此 OpenWrt 公版已安装完毕，可以放手折腾安装各种软件包，比如 shadowsocks ……

[End]


