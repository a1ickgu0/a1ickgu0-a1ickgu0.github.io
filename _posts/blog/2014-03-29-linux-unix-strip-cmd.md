---
layout: post
title:  Linux & Unix strip 命令
description: 最近在工作中正好用到strip命令，只是简单删除指定的T符号，对strip工具没有完全的了解，闲时将strip命令整理了一下，不同的发行版本的strip的option实际不尽相关，但主要的option基本相近。
category: blog
--- 


最近在工作中正好用到strip命令，只是简单删除指定的T符号，对strip工具没有完全的了解，闲时将strip命令整理了一下，不同的发行版本的strip的option实际不尽相关，但主要的option基本相近.

#### 作用

删除object文件中的符号信息

#### 版本

    alick@SRV:~/tmp$strip -V
    GNU strip (GNU Binutils for Ubuntu) 2.22
    Copyright 2011 Free Software Foundation, Inc.
    This program is free software; you may redistribute it under the terms of
    the GNU General Public License version 3 or (at your option) any later version.
    This program has absolutely no warranty.
    

#### 语法

    strip [option] object-file [...]
    

#### 说明

strip 是GNU提供用的用于删除object文件中的符号的工具。通过删除object文件中的不需要的符号信息可以达到减少object文件大小以方便文件的发布。它有助于提高逆向工程(reverse-engineer)object文件的难度。

strip 可以处理单个object文件，也可以处理*.a、*.so等静态与动态链接库，strip处理文件时缺省直接操作目标文件而不会生成新的文件。

#### option选项说明

*   -F *fbname*,--targe=*bfdname*:将目标文件指定为fbname格式.
*   --help:显示帮助信息.
*   --info:显示strip命令所能够支持的文件格式.
*   -I *bfdname*,--ipnput-targe=*bfdname*:将源文指定为*bfdname*对应的文件格式.
*   -O *bfdname*,--output-target=*bfdname*:指定strip的输出文件为*bfdname*格式.
*   -R *sectioname*,--rever-section=*sectioname*:
*   -s --strip-all: 删除所有符号.
*   -g,-S,-d,--strip-debug:只删除所有debug符号.
*   --strip-unneeded:删除object中不被进程所引用的无用符号.
*   -K *symbolname*,--keep-symbol=*symbolname*:保留特定的符号,可以通过多个-K参数保留多个符号.
*   -N *symbolname*,--strip-symbol=*symbolname*:删除指定的符号,可以通过多个-N参数删除多个符号,此个选项不能够与除-K之外的其他选项一起用.
*   o *file*:将strip的执行结果保存在*file*文件中，而不是采用缺省覆盖原文件的方式。
*   -P,--preserve-dates:保持文件的访问(access)与修订(modification)时间(date)不变. 
*   -D,--enable-deterministicarchives: 固化模式，在拷贝或写归档索引时，UID、GID以及时间戳采用全0.
*   -w,--wildcared:支持采用通配符以及正则表达式进行符号匹配，例如 -w -K !foo -Kfo*，保留所有以fo打头的符号，但不保留foo. 
*   -x,--discard-all:删除所有非全局符号.
*   -X,--discard-locals:删除编译器生成的所有本地符（符号中以L或.打头的）.
*   --only-keep-debug:删除所有不能被--strip-debug所删除的符号.
*   V,--version:显示strip的版本信息.
*   -v,--verbose:显示所有被修订过的object.

#### 应用实例

删除libjemalloc.so中的全局符号(T) malloc以及free

    strip -N malloc -N free libjemalloc.so

