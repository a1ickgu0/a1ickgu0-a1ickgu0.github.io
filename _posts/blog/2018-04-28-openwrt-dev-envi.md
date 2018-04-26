---
layout: post
title: OpenWRT 开发环境
description: 一篇旧文整理，两年前 OpenWRT 的学习/应用过程的记录，今年某个项目遇到 buildroot，甚是感慨。
category: blog
---

# OpenWRT 开发环境


## Vagrant 环境安装

1. 下载 [vagrant](https://www.vagrantup.com/docs/) 安装，[OSX 版本](https://releases.hashicorp.com/vagrant/1.8.5/vagrant_1.8.5.dmg)，[Windows 版本](https://releases.hashicorp.com/vagrant/1.8.5/vagrant_1.8.5.msi) 下载
2. 下载 [VirtualBox](https://www.virtualbox.org/) 安装
3. 在 [Hashicorp](https://atlas.hashicorp.com/boxes/search) 库中查找 Vagrant Box 文件


* vagrant 默认使用 ``Vargentfile`` 作为虚拟机的配置文件，如何指定每个虚机的配置文件名？
* How to build a box ?

### openwrt-15.05-x86

#### 安装 Vagrant Box

```
Hostname:openwrtx86 AlickGuo$ vagrant init ntrepid8/openwrt-15.05-x86; vagrant up --provider virtualbox
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'ntrepid8/openwrt-15.05-x86' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'ntrepid8/openwrt-15.05-x86'
    default: URL: https://atlas.hashicorp.com/ntrepid8/openwrt-15.05-x86
==> default: Adding box 'ntrepid8/openwrt-15.05-x86' (v201602.12.0) for provider: virtualbox
    default: Downloading: https://atlas.hashicorp.com/ntrepid8/boxes/openwrt-15.05-x86/versions/201602.12.0/providers/virtualbox.box
==> default: Successfully added box 'ntrepid8/openwrt-15.05-x86' (v201602.12.0) for 'virtualbox'!
==> default: Importing base box 'ntrepid8/openwrt-15.05-x86'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ntrepid8/openwrt-15.05-x86' is up to date...
==> default: Setting the name of the VM: openwrtx86_default_1473392829430_14390
==> default: Fixed port collision for 22 => 2222. Now on port 2200.
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2200 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2200
    default: SSH username: vagrant
    default: SSH auth method: private key
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: No guest additions were detected on the base box for this VM! Guest
    default: additions are required for forwarded ports, shared folders, host only
    default: networking, and more. If SSH fails on this machine, please install
    default: the guest additions and repackage the box to continue.
    default:
    default: This is not an error message; everything may continue to work properly,
    default: in which case you may ignore this message.
==> default: Rsyncing folder: /Users/AlickGuo/Documents/vagrant/openwrtx86/ => /vagrant
==> default:   - Exclude: [".vagrant/", ".DS_Store"]
==> default: Running provisioner: shell...
    default: Running: inline script
==> default:   _______                     ________        __
==> default:  |       |.-----.-----.-----.|  |  |  |.----.|  |_
==> default:  |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
==> default:  |_______||   __|_____|__|__||________||__|  |____|
==> default:           |__| W I R E L E S S   F R E E D O M
==> default:  -----------------------------------------------------
==> default:  CHAOS CALMER (Chaos Calmer, r48666)
==> default:  -----------------------------------------------------
==> default:   * 1 1/2 oz Gin            Shake with a glassful
==> default:   * 1/4 oz Triple Sec       of broken ice and pour
==> default:   * 3/4 oz Lime Juice       unstrained into a goblet.
==> default:   * 1 1/2 oz Orange Juice
==> default:   * 1 tsp. Grenadine Syrup
==> default:  -----------------------------------------------------

==> default: Machine 'default' has a post `vagrant up` message. This is a message
==> default: from the creator of the Vagrantfile, and not from Vagrant itself:
==> default:
==> default: You may need to run the following command to set the user:group on /vagrant before use:
==> default:     $ sudo chown vagrant:vagrant -R /vagrant

```

#### 登录 OpenWRT

```
Hostname:openwrtx86 AlickGuo$ vagrant ssh


BusyBox v1.23.2 (2016-01-27 21:04:08 EST) built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 CHAOS CALMER (Chaos Calmer, r48666)
 -----------------------------------------------------
  * 1 1/2 oz Gin            Shake with a glassful
  * 1/4 oz Triple Sec       of broken ice and pour
  * 3/4 oz Lime Juice       unstrained into a goblet.
  * 1 1/2 oz Orange Juice
  * 1 tsp. Grenadine Syrup
 -----------------------------------------------------
vagrant@OpenWrt:~$ ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:AB:76:EC  
          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:feab:76ec/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:945 errors:0 dropped:0 overruns:0 frame:0
          TX packets:634 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:102967 (100.5 KiB)  TX bytes:109606 (107.0 KiB)
          Interrupt:10 Base address:0xd020

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:16 errors:0 dropped:0 overruns:0 frame:0
          TX packets:16 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:1621 (1.5 KiB)  TX bytes:1621 (1.5 KiB)

vagrant@OpenWrt:~$
```

### 安装 Ubuntu 12.04

```
Hostname:vagrant AlickGuo$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'hashicorp/precise64' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'hashicorp/precise64'
    default: URL: https://atlas.hashicorp.com/hashicorp/precise64
==> default: Adding box 'hashicorp/precise64' (v1.1.0) for provider: virtualbox
    default: Downloading: https://atlas.hashicorp.com/hashicorp/boxes/precise64/versions/1.1.0/providers/virtualbox.box
==> default: Successfully added box 'hashicorp/precise64' (v1.1.0) for 'virtualbox'!
==> default: Importing base box 'hashicorp/precise64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'hashicorp/precise64' is up to date...
==> default: Setting the name of the VM: vagrant_default_1473392522842_90718
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default:
    default: Guest Additions Version: 4.2.0
    default: VirtualBox Version: 5.0
==> default: Mounting shared folders...
    default: /vagrant => /Users/AlickGuo/Documents/vagrant
```


## 更新安装指定包

```
./scripts/feeds update customfeed
./scripts/feeds install -p quagga
```

## 关键的编译命令

```
sudo apt-get update
sudo apt-get install git-core build-essential libssl-dev libncurses5-dev unzip gawk
```

```
git clone https://github.com/openwrt/openwrt.git
```

```
cd openwrt
./scripts/feeds update -a
./scripts/feeds install -a
```

```
make menuconfig (most likely you would like to use this)
make defconfig
make prereq
```

```
# 编译组件, package/[组件名]/compile，组件见 feeds/pacakges/ 下的各目录
make package/openvswitch/compile
# 安装组件
make package/openvswitch/install
# 生成 opkg package 归档， 所有 .ipk 文件放在 bin/[arch]/packages 目录
package/index
```

```
alick@ubuntu:~/project/openwrt/bin/ipq806x/packages$ ls *switch*.* -lsh
 68K -rw-r--r-- 1 alick alick  62K Sep  8 22:09 kmod-openvswitch_4.4.14+2.5.0-22d4614ddf83988a3771fb379ea029e663b4455a_ipq806x.ipk
4.0K -rw-r--r-- 1 alick alick  957 Sep  8 22:09 openvswitch_2.5.0-22d4614ddf83988a3771fb379ea029e663b4455a_ipq806x.ipk
752K -rw-r--r-- 1 alick alick 745K Sep  8 22:09 openvswitch-base_2.5.0-22d4614ddf83988a3771fb379ea029e663b4455a_ipq806x.ipk
4.0K -rw-r--r-- 1 alick alick 3.8K Sep  8 22:09 openvswitch-ovs-appctl_2.5.0-22d4614ddf83988a3771fb379ea029e663b4455a_ipq806x.ipk
 12K -rw-r--r-- 1 alick alick  11K Sep  8 22:09 openvswitch-ovsdb-client_2.5.0-22d4614ddf83988a3771fb379ea029e663b4455a_ipq806x.ipk
4.0K -rw-r--r-- 1 alick alick 3.7K Sep  8 22:09 openvswitch-ovs-dpctl_2.5.0-22d4614ddf83988a3771fb379ea029e663b4455a_ipq806x.ipk
 28K -rw-r--r-- 1 alick alick  25K Sep  8 22:09 openvswitch-ovs-ofctl_2.5.0-22d4614ddf83988a3771fb379ea029e663b4455a_ipq806x.ipk
 20K -rw-r--r-- 1 alick alick  18K Sep  8 22:09 openvswitch-ovs-vsctl_2.5.0-22d4614ddf83988a3771fb379ea029e663b4455a_ipq806x.ipk
```

### 编译打包

```
ie@NE-DEV-X86:~/openwrt/openwrt-15.05$ make
 make[1] world
 make[2] target/compile
 make[3] -C target/linux compile
 make[2] package/cleanup
 make[2] package/compile
 make[3] -C package/libs/toolchain compile
 make[3] -C package/libs/libnl-tiny compile
 make[3] -C package/libs/libjson-c compile
 make[3] -C package/utils/lua compile
 make[3] -C package/libs/libubox compile
 make[3] -C package/system/ubus compile
 make[3] -C package/system/uci compile
 make[3] -C package/network/config/netifd compile
 make[3] -C package/system/opkg host-compile
 make[3] -C package/system/ubox compile
 make[3] -C package/libs/lzo compile
 make[3] -C package/libs/zlib compile
 make[3] -C package/libs/ncurses host-compile
 make[3] -C package/libs/ncurses compile
 make[3] -C package/libs/ncurses compile
 make[3] -C package/utils/util-linux compile
 make[3] -C package/utils/ubi-utils compile
 make[3] -C package/system/procd compile
 make[3] -C package/system/usign host-compile
 make[3] -C package/utils/jsonfilter compile
 make[3] -C package/system/usign compile
 make[3] -C package/base-files compile
 make[3] -C package/boot/grub2 host-compile
 make[3] -C package/boot/grub2 compile
 make[3] -C package/devel/binutils compile
 make[3] -C package/libs/libreadline compile
 make[3] -C package/devel/gdb compile
 make[3] -C package/devel/trace-cmd compile
 make[3] -C package/devel/valgrind compile
 make[3] -C feeds/packages/net/bridge-utils compile
 make[3] -C feeds/packages/devel/diffutils compile
 make[3] -C feeds/packages/net/ethtool compile
 make[3] -C package/libs/gettext compile
 make[3] -C package/libs/libiconv compile
 make[3] -C package/libs/libtool compile
 make[3] -C feeds/packages/devel/gcc compile
 make[3] -C feeds/packages/libs/libpam compile
 make[3] -C package/libs/ocf-crypto-headers compile
 make[3] -C package/libs/openssl compile
 make[3] -C feeds/packages/net/openssh compile
 make[3] -C feeds/packages/net/openssh compile
 make[3] -C feeds/packages/net/openssh compile
 make[3] -C feeds/packages/net/openssh compile
 make[3] -C feeds/packages/net/openssh compile
 make[3] -C feeds/packages/net/openssh compile
 make[3] -C feeds/packages/net/openssh compile
 make[3] -C feeds/packages/net/openssh compile
 make[3] -C feeds/packages/net/sshtunnel compile
 make[3] -C feeds/packages/admin/sudo host-compile
 make[3] -C feeds/packages/admin/sudo compile
 make[3] -C package/firmware/linux-firmware compile
 make[3] -C package/kernel/linux compile
 make[3] -C package/libs/libpcap compile
 make[3] -C package/libs/uclibc++ compile
 make[3] -C package/network/utils/iptables compile
 make[3] -C package/network/config/firewall compile
 make[3] -C package/network/ipv6/odhcp6c compile
 make[3] -C package/network/services/dnsmasq compile
 make[3] -C package/network/services/dropbear compile
 make[3] -C package/network/services/odhcpd compile
 make[3] -C package/network/utils/linux-atm compile
 make[3] -C package/network/utils/resolveip compile
 make[3] -C package/network/services/ppp compile
 make[3] -C package/network/utils/iperf compile
 make[3] -C package/libs/sysfsutils compile
 make[3] -C package/network/utils/iputils compile
 make[3] -C package/network/utils/tcpdump compile
 make[3] -C package/system/fstools compile
 make[3] -C package/system/mtd compile
 make[3] -C package/system/opkg compile
 make[3] -C package/utils/busybox compile
 make[2] package/install
 make[3] package/preconfig
 make[2] target/install
 make[3] -C target/linux install
 make[2] package/index
```

只编译软件包:

```
make package/compile
make package/install
make package/index
```

只打包 rom 文件:

```
make target/install
```

### 编译 Bin 文件

```
ruijie@NE-DEV-X86:~/openwrt/openwrt-15.05/build_dir/target-i386_i486_uClibc-0.9.33.2/OpenWrt-ImageBuilder-x86-generic.Linux-x86_64$ make image
```

### 编译所有的 Package

```
./scripts/feeds update
./scripts/feeds install -d m install -a
```

### 编译过程忽略错误

```
make V=99 IGNORE_ERRORS=m
```

### 文件 Bin 升级

烧写

```
dd if=/home/username/Downloads/openwrt-brcm2708-bcm2708-sdcard-vfat-ext4.img of=/dev/sdX bs=2M conv=fsync
```

```
cd /tmp
wget http://downloads.openwrt.org/snapshots/trunk/ar71xx/openwrt-ar71xx-generic-mw4530r-v1-squashfs-sysupgrade.bin
sysupgrade -v openwrt-ar71xx-generic-mw4530r-v1-squashfs-sysupgrade.bin
```

### 清理

* `` make clean `` ，删除此次编译的 Bin 文件（ /bin 与 /build_dir ）
* `` make dirclean ``，删除目录 /bin 、 /build_dir 、/staging_dir、 /toolchain (=the cross-compile tools) 与 /logs，基本上 'Dirclean' 删除了目标平台的所有文件
* `` make distclean ``，彻底删除，包括 .config、feeds 与 packages 等等，一招打回解放前

### rootfs 路径

```
./package/base-files/files/etc/hosts
./staging_dir/target-i386_i486_uClibc-0.9.33.2/root-x86/etc/hosts
```

### MAC创建虚拟盘

```hdiutil attach OpenWrt.sparseimage```


## 参考资料

* [How to build a single package](https://wiki.openwrt.org/doc/howtobuild/single.package)
* [OpenWrt Build System Usage](https://wiki.openwrt.org/doc/howto/build)
* [upgrade openwrt kernel and reinstall all packages manual
Raw](https://gist.github.com/jiananlu/9258032)
