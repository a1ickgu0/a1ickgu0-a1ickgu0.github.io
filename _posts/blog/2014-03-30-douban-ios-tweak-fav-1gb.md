---
layout: post
title: 豆瓣fm突破100MB红心限制
description: 豆瓣fm只能够在机器上缓存100MB的容量，这么丁点的空间高质的音乐放不了几首，深受此事困扰，因此花了点时间了解一下，把机器上缓存的上限调整到了1G，这下足够了。
category: blog
--- 


### 线索

查找豆瓣fm的目录, 在应用中清空红心缓存后对douban的目录进行两次du操作比较目录的变化.

    iPhone:/var/mobile/Applications/0DD6B324-371F-4139-874B-96FF68D60809 root# du
    

旧数据

    2744    ./Library/Caches/com.douban.DoubanRadio/fsCachedData
    5600    ./Library/Caches/com.douban.DoubanRadio
    1312    ./Library/Caches/com.hackemist.SDWebImageCache.default
    6952    ./Library/Caches
    8   ./Library/Cookies
    0   ./Library/DOUStatLib/log/important
    8   ./Library/DOUStatLib/log/normal
    8   ./Library/DOUStatLib/log
    8   ./Library/DOUStatLib
    1488    ./Library/DoubanFM3/OfflineLibrary/CoverStorage
    16  ./Library/DoubanFM3/OfflineLibrary/FMDynamicData
    184 ./Library/DoubanFM3/OfflineLibrary/FMStaticData
    51376   ./Library/DoubanFM3/OfflineLibrary/MusicStorage
    0   ./Library/DoubanFM3/OfflineLibrary/tmp
    53072   ./Library/DoubanFM3/OfflineLibrary
    53088   ./Library/DoubanFM3
    

新数据

    2968    ./Library/Caches/com.douban.DoubanRadio/fsCachedData
    6600    ./Library/Caches/com.douban.DoubanRadio
    1312    ./Library/Caches/com.hackemist.SDWebImageCache.default
    7952    ./Library/Caches
    8   ./Library/Cookies
    0   ./Library/DOUStatLib/log/important
    8   ./Library/DOUStatLib/log/normal
    8   ./Library/DOUStatLib/log
    8   ./Library/DOUStatLib
    1688    ./Library/DoubanFM3/OfflineLibrary/CoverStorage
    32  ./Library/DoubanFM3/OfflineLibrary/FMDynamicData
    368 ./Library/DoubanFM3/OfflineLibrary/FMStaticData
    104160  ./Library/DoubanFM3/OfflineLibrary/MusicStorage
    0   ./Library/DoubanFM3/OfflineLibrary/tmp
    106256  ./Library/DoubanFM3/OfflineLibrary
    106272  ./Library/DoubanFM3
    

从差异文件中可以明显的看出MusicStorage目录为102MB正好略大于100MB，相当符合红心目录100MB的限制.

    iPhone:/var/mobile/Applications/0DD6B324-371F-4139-874B-96FF68D60809/Library/DoubanFM3/OfflineLibrary/MusicStorage root# ls 
    p1002089.mp4  p1388715.mp4  p1395419.mp4     p1508831.mp4     p1635044.mp4     p1828818_1v.mp4  p34901.mp4   p412416_1v.mp4  p471527_1v.mp4  p552455.mp4  p606247.mp4     p966143.mp4
    p1024291.mp4  p1392710.mp4  p14314_1v.mp4    p15413.mp4       p1753407_1v.mp4  p191729.mp4      p34902.mp4   p425687.mp4     p507939_1v.mp4  p571575.mp4  p629285_1v.mp4  p990310_1v.mp4
    p1028442.mp4  p1394561.mp4  p1486348.mp4     p1563412.mp4     p1795637_1v.mp4  p34721_1v.mp4    p355201.mp4  p425703.mp4     p508620_1v.mp4  p583689.mp4  p723752.mp4
    p1383779.mp4  p1394599.mp4  p1504320_1v.mp4  p1611938_1v.mp4  p182277_1v.mp4   p348242.mp4      p355901.mp4  p425704.mp4     p529072_1v.mp4  p583690.mp4  p73559.mp4
    iPhone:/var/mobile/Applications/0DD6B324-371F-4139-874B-96FF68D60809/Library/DoubanFM3/OfflineLibrary/MusicStorage root# du
    104160  .
    iPhone:/var/mobile/Applications/0DD6B324-371F-4139-874B-96FF68D60809/Library/DoubanFM3/OfflineLibrary/MusicStorage root#     
    

找到了目录名字，接下来是要查找豆瓣fm获取存储文件空间以及限制100MB的方式，在进行此步之前，心里有以下猜测

##### 获取存储文件缓存的方法：

*   根据下载记录的文件长度.
*   直接根据MusicStorage目录的大小.

##### 记录最大可缓存的方法

*   在bin里头硬编码，一般来说有推敲的软件都不会采用这个机制。
*   根据.plist文件中的限制.

为进一步分析上述猜测(1),采用了以下方法

1.  在后台Kill fm，通过 ls -lhs * 查看当前所有文件的状态。
2.  重启fm，再次 ls -lsh * 查看当前所有文件的状态。

看到以下文件的时间戳与大小有变化:

    32K -rw-r--r-- 1 mobile mobile  32K Mar 29 14:46 googleanalytics-v2.sql-shm
    32K -rw-r--r-- 1 mobile mobile  32K Mar 29 14:46 googleanalytics-v3.sql-shm
    2.0M -rw-r--r-- 1 mobile mobile 2.0M Mar 29 14:46 googleanalytics-v3.sql-wal
    

同时tmp目录下多了两个文件

    2.5M -rw-r--r-- 1 mobile mobile 2.5M Mar 29 14:46 douas-2e57c5ff448bb2c2b78f13ecaa0b166d671777041e64f6a50746ff15fc1b5dd4.tmp
    2.6M -rw-r--r-- 1 mobile mobile 2.6M Mar 29 14:46 douas-ee4ee2399d99f15a6c65c3681dc5e8145cb00c0357b0057e07a2633243c1694a.tmp
    

Easy的先处理，vim看一下两个tmp文件，不是文本文件，由于是放在tmp目录下的，基本不大可能与要查找信息相关，因此先不离这两个文件，接下来google一下googleanalytics，这个Google所提供的用户WEB访问分析工具，与要处理的事没有啥关系，至此，基本上可以知道fm重新引导不需要更新文件，因此fm不可能将当前已下载的文件长度回写到plist中，基本上可以确定fm是根据目录下的文件实时计算的，接下来进行实验，这个目录有没有机会破突100MB，同时了解一下fm获取文件的方式.

在MusicStorage生成1个无用的空文件以及COPY一个正常的mp4文件，看看fm中所显示的缓存大小，结果加了N外文件后，fm始终只显示100.00MB的缓存，再查看一下目录发现刚才创建的mp4文件被删除了(TBD:搞不清楚fm是如何识别需要删除的文件).

基先前查看ls * -lsh的结果上查看所有plist文件

    $ more emptry_list_detail | grep plist
    8.0K -rw-r--r--  1 mobile mobile 3.2K Mar 29 14:10 iTunesMetadata.plist
    8.0K -rw-r--r--  1 mobile mobile 4.5K Mar 29 14:10 DoubanFM.plist
    8.0K -rw-r--r--  1 mobile mobile  246 Mar 29 14:10 Features.plist
    8.0K -rw-r--r--  1 mobile mobile 1.7K Mar 29 14:10 Info.plist
    8.0K -rw-r--r--  1 mobile mobile  150 Mar 29 14:10 ResourceRules.plist
    

重点怀疑ResouceRule.plist，打开看了一，果然有个weight的key为100，这个有没有可能是100MB，先改个试试看，将100改为1000之后，没有任何效果，看来事情没有这么好处理，进行到这里，基本上明确几个内容：

1.  红心的缓存数据是从文件系统中获取的，fm有处理100MB的边界问题，即可以超过100MB，但仅限于最后一首歌文件长度跨边界的情况，想多下歌是不可能的。
2.  100MB的数字控制并不是简单地存储在plist中。

目前看来基本上不过bin文件只改plist等方法是行不通的，接下来就要依赖拆fm文件来进行进一下的探纠了。

#### 程序的脱壳分析

关于程序如何脱壳，[iOSRE][1] 有相应的方法，这里不就再累述.

    DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib /var/mobile/Applications/0DD6B324-371F-4139-874B-96FF68D60809/DoubanRadio.app/DoubanRadio
    

脱好壳的文件通过scp下载到PC后，接下来就是用class-dump工具进行拆包获取头class定义

    class-dump --arch armv7s DoubanRadio.decrypted > head/head.h
    

根据一般的经验，先查找到max/size等关键字，很快以下接口引起注意：

    @interface DOUFMMusicLibrary 
    - (unsigned long long)maxSizeOfAChannel;
    @end
    
    @interface DOULibrarySyncManager
    - (unsigned long long)maxSizeOfAChannel;  
    @end
    
    @interface DOURadioStation 
    - (unsigned long long)maxSizeForOfflineChannel; 
    @end
    
    @interface DOUFMLibraryDataManager
    - (unsigned long long)totalSizesOfSongs;
    - (unsigned long long)_totalSizeInChannelID:(id)arg1; 
    - (unsigned long long)totalSizeOfAudioFilesInChannel:(id)arg1;
    @end
    

接下来就是制作Tweak验证接口，Bingo 手感很不错，很Easy就明确了，把所有maxSize改大到1G，同时将totalSize计算得到结果进行除10后，在fm里头进行‘同步’，原来最多只能同步40来首歌，现在可以把收藏的140多首红心都同步下来了，至此基本的目的达成了。

余下有还有几个问题要处理，待所有问题完毕之后，提交thebigboss free share，了解theos的同学也可以自己操刀处理之。 1. ‘管理离线数据’里头的‘每个兆赫容量不超过100MB’需要改为‘每个兆赫容量不超过1GB’。 2. ‘管理离线数据’里头的‘清空全部缓存(xxx.xxMB)’由于先前的除10操作，这显示的比实际小10倍，需要修订为正确值。

 [1]: http://iosre.com/t/dumpdecrypted-app/22



