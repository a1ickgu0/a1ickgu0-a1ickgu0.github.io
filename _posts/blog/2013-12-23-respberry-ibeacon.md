---
layout: post
title: 基于Raspberry实现iBeacon基站－译文+实践
description: Apple的iBeacon使用低功耗蓝牙(BLE)实现近场通讯，相对于NFC而言，iBeacon有更远的传输距离，Apple在其Appstroe已经推出iBeacon服务，对此非常之好奇，偶然看一篇文章，手上亦正好有Raspberry B 版本，因此学习&实践如下。
category: blog
--- 


Apple的iBeacon使用低功耗蓝牙(**B**luetooth **L**ow **E**nergy)实现近场通讯，相对于NFC而言，iBeacon有更远的传输距离，Apple在其Appstroe已经推出iBeacon服务，对此非常之好奇，偶然看一篇文章，手上亦正好有Raspberry B 版本，因此学习&实践如下。

![Raspberry][1]

除了Raspberry Pi之外，还需要的是支持4.0协议的蓝牙适配器，Raspberry Pi所能够兼容的蓝牙设备可以在[the Embedded Linux Wiki][2] 查询，我得使用的是采用CSR8510A10芯片的[奥睿科（ORICO） BTA-406-WH ][3]适配器，能不能用，看缘份了。

-

### 1\.下载蓝牙协议栈以及开发包

通过ssh登录到Raspberry的shell下，执行下面的命令下载相关的开发包。

    sudo apt-get install libusb-dev libdbus-1-dev libglib2.0-dev libudev-dev libical-dev libreadline-dev
    

### 2\.安装BlueZ工具

在shell下执行以下命令进行BlueZ的下载与安装：

    sudo wget www.kernel.org/pub/linux/bluetooth/bluez-5.11.tar.xz
    sudo unxz bluez-5.11.tar.xz
    sudo tar xvf bluez-5.11.tar
    cd bluez-5.11
    sudo ./configure --disable-systemd
    sudo make
    sudo make install
    

如果源代码无法在Raspberry Pi上通过wget下载，直接在PC上下载好通过SFTP的方式进行上传也是一样的，make是直接在Raspberry Pi上进行编译，整个的过程比较久，大概需要20分钟左右，需要有耐心等待，如果没有make工具，执行以下操作进行安装：

    sudo apt-get install build-essential
    

安装好后将适配器插入Raspberry Pi的USB接口后重启，之后通过lsusb可以看到HCI(Host Controller Interface) Mode的设备就说明蓝牙适配器被正确地识别到了，我的设备上lsusb所看到的信息：

    lsusb
    Bus 001 Device 002: ID 0424:9512 Standard Microsystems Corp. 
    Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    Bus 001 Device 003: ID 0424:ec00 Standard Microsystems Corp. 
    Bus 001 Device 004: ID 148f:5370 Ralink Technology, Corp. RT5370 Wireless Adapter
    Bus 001 Device 005: ID 0a12:0001 Cambridge Silicon Radio, Ltd Bluetooth Dongle (HCI mode)
    

查看蓝牙设备是否正常识别：

    hciconfig
    hci0:   Type: BR/EDR  Bus: USB
        BD Address: 00:1A:7D:DA:71:0A  ACL MTU: 310:10  SCO MTU: 64:8
        DOWN 
        RX bytes:547 acl:0 sco:0 events:27 errors:0
        TX bytes:384 acl:0 sco:0 commands:27 errors:0
    

打开蓝牙进行扫描，可以正常检测到我的手机以及本子(--为人肉匿去的MAC地址信息)：

    sudo hciconfig hci0 up
    hcitool scan
    Scanning ...
        B8:8D:12:--:--:--   Alick's MBP
        D8:96:95:--:--:--   Alick's iPhone
    

打开BLE广播公告，从而使得蓝牙能够被识别为iBeacon设备

    sudo hciconfig hci0 leadv 3
    

按步骤3测试完成后可以用以下命令关闭iBeacon公告

    sudo hciconfig hci0 noleadv
    

### 3\.解析iBeacon协议

iBeacond协议数据中需要UUID信息，可以通过[这里][4]获取一个唯一的UUID以备后续协议封装使用，比如我所生成的UUID

    e1b48630-68c4-11e3-949a-0800200c9a66
    

使用hcitool工具发送iBeacon信息，信息中包括UUID、Major 与 Minor Code（见下面字串口[]内部分，发送时去除[]），之后1 Byte为 Measured Power，表示的是基站与接收器相距1米时的接收信号参考值，RSSI：Received Signal Strength Indicator），接收器根据该参考RSSI与接收信号的强度来推断发送模块与接收器的距离。

具体使用时将UUID替换成前一步所获取到的UUID：

    sudo hcitool -i hci0 cmd 0x08 0x0008 1E 02 01 1A 1A FF 4C 00 02 15 [ E1 B4 86 30 68 C4 11 E3 94 0A 08 00 20 0C 9A 66 ] [ 00 00 ] [ 00 00 ] C5 00
    

这样手机上的iBeacon客户端就可能正确地发现Raspberry基站设备，测试使用的iOS客户端为[ETL Beacon][5]软件，免费软件。

![ibeaconshow][6]

CSR8510 A10为前面所述的USB蓝牙适配器芯片型号，看来此次人品还行，CSR芯片在Raspberry上实现免驱运行，括号内的信号强度，数值越大，信号越弱，你可以尝试着拿在手机在四处走动，观察手机与Raspberry位置的变化与数值变化的关系，这样有有比较具体的感性认识。

### 4\.未完待继的内容

通过前文的方法，达到使用Raspberry模拟iBeacon基站的目的，后续需要基于本文中的一些没有深入涉及的问题淡开去，包括Apple iBeacon的基本原理以及Linux下蓝牙的工作原理以及实现模型，这些内容是我当前也还没有来得及深入学习的，因此后文的产出需要一些时日，最终的目标是能够通过Raspberry实现一个基本具备实用价值的iBeacon基站。

* * *

本文主要章节翻译致 [原文 Build your OWN Apple iBeacon with a Raspberry Pi -- DIY Bluetooth LE zone tracking][7] ，部分为我的个人资料查找以及实践。

 [1]: http://regmedia.co.uk/2013/08/25/piglow_14.jpg
 [2]: http://elinux.org/RPi_USB_Bluetooth_adapters
 [3]: http://item.jd.com/977906.html
 [4]: http://www.famkruithof.net/uuid/uuidgen
 [5]: https://itunes.apple.com/us/app/etl-beacon/id774743416?mt=8
 [6]: http://alick-wordpress.stor.sinaapp.com/uploads/2013/12/ETL_FW_CONFIG.jpg
 [7]: http://www.theregister.co.uk/2013/11/29/feature_diy_apple_ibeacons/

