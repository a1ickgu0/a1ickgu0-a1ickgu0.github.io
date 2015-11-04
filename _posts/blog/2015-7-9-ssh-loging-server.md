---
layout: post
title: 使用 ssh key 登录服务器
description: 使用 SSH key 的方式登录 Linux 服务器比每次输入密码录登更加安全以及高效，设置了 SSH key 登录的服务器可以关闭密码登录以避免被暴力破解，接下来就说明一下如何配置使用 SSH Key 进行服务器登录。
category: blog
---

使用 SSH key 的方式登录 Linux 服务器比每次输入密码录登更加安全以及高效，设置了 SSH key 登录的服务器可以关闭密码登录以避免被暴力破解，接下来就说明一下如何配置使用 SSH Key 进行服务器登录。

### 第一步 - 创建 RSA 密钥

SSH Key登录需要生成用于登录的公/私钥对，在终端下输入以下命令：

    ssh-keygen -t rsa 
    

### 第二步 - 存储密钥

在输入第一步命令之后会有以下提示信息：

    Enter file in which to save the key (/home/demo/.ssh/id_rsa):
    

`/home/demo/.ssh/id_rs` 为生成的密钥文件名及路径，你可以根据实际情况更改密钥的文件名，如 `/home/demo/.ssh/alickguo_srv`，确定文件名之后，出现提示输入校验密码(passphrase)，这个是用于在使用密钥时进行使用权限校验，以避免私钥泄漏导致服务器权限泄漏。

整个密钥生成的过程大致如下：

    ssh-keygen -t rsa
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/demo/.ssh/id_rsa): 
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /home/demo/.ssh/id_rsa.
    Your public key has been saved in /home/demo/.ssh/id_rsa.pub.
    The key fingerprint is:
    4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 demo@a
    The key's randomart image is:
    +--[ RSA 2048]----+
    |          .oo.   |
    |         .  o.E  |
    |        + .  o   |
    |     . = = .     |
    |      = S = .    |
    |     o + = +     |
    |      . o + o .  |
    |           . o   |
    |                 |
    +-----------------+
    

此时公钥将存储在 `/home/demo/.ssh/id_rsa.pub` 文件中，私钥存储在 `/home/demo/.ssh/id_rsa` 文件中。

### 第三步 - 拷贝公钥

在密钥对生成之后，通过以下命令进行公钥拷贝

    ssh-copy-id user@123.45.56.78
    

或者使用

    cat ~/.ssh/id_rsa.pub | ssh user@123.45.56.78 "mkdir -p ~/.ssh && cat >>  ~/.ssh/authorized_keys"
    

这个过程会需要输入 `passphrase` 校验对密钥的访问权限以及服务器的 user 帐号对应的密码以进行公钥文件传输，至此就可以使用

ssh user@123.45.56.78

直接登录服务器而不需要输入密码。

### 第四步 - 关闭密码登录

在设置了 SSH key 之后，可以关闭帐户的密码访问权限以避免被暴力破解，操作需要先 SSH 登录到服务器修订ssh配置文件：

    sudo vi /etc/ssh/sshd_config
    

找到 PermitRootLogin 字段，修改为以下值，以关闭密码登录

    PermitRootLogin without-password
    

最后重启 SSH 守护进程

    reload ssh
    

至此服务器仅能够使用 SSH Key 的方式访问，即方便又安全。

