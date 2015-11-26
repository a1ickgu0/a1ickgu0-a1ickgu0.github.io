---
layout: post
title: [译文] Cydia API 中文手册
description: Cydia Store 为开发者(vendor,直译为供应商，本文称为开发者)提供基于HTTP协议的API，用于获取软件购买以及授权信息，开发者可以通过Cydia API在应用程序中查询软件包的付费状态信息，进行软件防盗版校验。
category: blog
--- 


## 概要

Cydia Store 为开发者(vendor,直译为供应商，本文称为开发者)提供基于HTTP协议的API，用于获取软件购买以及授权信息，开发者可以通过Cydia API在应用程序中查询软件包的付费状态信息，进行软件防盗版校验。

## 请求协议

### 关键字

开发者可以通过以下请求接口向Cydia请求获取产品的付费信息，信息将通过HTTP应答反馈

    http://cydia.saurik.com/api/check
    

发启信息获取请求时需要包括以下信息:

*   api(建议):请求所使用的API的版本号，如果未提供的话则为缺省版本号"store-0.9"。
*   device:设备唯一标识信息(UDID)或者自定义的hash值，采用小写的16进制描述。
*   mode:“local”或"recursive"，一般用local即可，recursive为server-server的请求模型。
*   nonce:扰动值，需要保证使用的唯一性，用于匹配单次的请求与应答，一般来说可以直接使用Unix time，当然也可以使用随机值进行扰动，前提是保证随机值不重复。
*   product或package:需要获取的软件名字。package是cydia源服务器(如thebigboss)所定义的名字，而product为cydia库所定义的名字。
*   timestamp:请求发启的unix时间，这个时间必须与cydia服务器的unix时间保持在3分钟之内。
*   vendor:请求发启者的名字。
*   version(有条件的建议):所请求软件的版本信息.如果使用product进行发启请求则**不允许**包括version字段，其它情况下可填可不填。
*   host(可选):发启请求的设备IP地址，只在用户设备终端直接发启请求的情况下使用。
*   hash(可选):UDID的校验信息，当前只支持MD5校验。
*   prefix(可选):UDID进行Hash之前所添加的前缀。
*   signature:将请求数据以及开发者密钥(vendor's secret key)或服务器密钥(Server's secret key)信息使用HMAC-SHA1算法计算得到的签名信息(hash key)。

注 1. 如果有使用hash以及prefix关键字，则hash必须是由prefix加上device关键字所对应的UDID进行生成。 注 2. 如果请求数据中有包括可选字段，那么可选字段的数据需要被一同签名。

### 生成签名

signature(签名)关键字的内容是通过HMAC-SHA1算法对除了signature以外的所有请求字符串进行签名。查询字符串与数据字符采用*application/x-www-form-urlencoded*的content type，同时数据字符串**必须采用大写**的16进制格式，同时空格需要用"+"替代。对于查询字符串则无大小写限制。

还需要说明的是数据字段必须依照其首字母的字典序进行排列，如果字段名一样则按值(value)的字典序排列，但对于查询请求则可以依照任意序排列。

### 样例

以下以UDID为*048108573c7ed8f52126a912d1517a6c40a48858*的设备查询名为*com.widgco.wmark*的package授权信息:

    api=store-0.9&device=048108573c7ed8f52126a912d1517a6c40a
    48858&host=32.174.245.141&mode=local&nonce=1234585489&pa
    ckage=com.widgco.wmark&timestamp=1234585489&vendor=docho
    st&version=0.9
    

此时就可以使用开者密钥对上述采用HMAC-SHA1算法进行签名，假定开发者的密钥为*abcdef0123456789abcdef0123456789*，上述信息采用URL-safe base-64编码进行HMAC签名后为F0AKwxM_oG5b9eExVprTWblO5V4，因此完整的API请求信息如下:

    http://cydia.saurik.com/api/check?nonce=1234585489&vendo
    r=dochost&mode=local&package=com.widgco.wmark&host=32.17
    4.245.141&api=store-0.9&version=0.9&device=048108573c7ed
    8f52126a912d1517a6c40a48858&timestamp=1234585489&signatu
    re=F0AKwxM_oG5b9eExVprTWblO5V4
    

## 应答协议

### 关键字

服务器接收到合法请求之后采用application/x-wwww-form-urlencoded类型进行回送应答，应答中将包括以下关键字信息:

*   memssage(可选):付费服务商的描述信息。
*   nonce:服务应所发出的nonce，用于匹配请求。
*   payment:购买者的付款描述信息。
*   provider:付费平台的描述信息，如:Amazon, PayPal。
*   state:付费的状态信息，具体见下文的状态值章节。
*   signature:采用开发者密钥或服务器密匙对应答的数据进行签名后信息。

对于mode为recursive类型的请求，Cydia服务器会将请求再发回开发者的服务器以获取

如果请求信息在服务器上检索后未能找到信息，则服务器的应答中只会包括nonce与signature两个关键字段。

### 状态值

服务器所回应的状态值包括以下几种类型

*   error:查询付费服务操作失败. 
*   pending:查询到的付费状态为正在进行中。
*   failed:查询到的付费状态为未完成。
*   completed:查询到的付费状态为已完成。
*   reversed:查询到的付费状态为已撤销。

### 错误信息

如果服务器返回的state为error，那么message字段中会指定error的具体原因，以下是常见的信息(非完整信息):

*   missing vendor: 请求未包含开发者(vendor)信息.
*   unknown vendor: 请请求中所指示的开发者信息非法。
*   unsupport api: 请求的API不支持。

如果vendor信息是正常的，服务器所反馈的错误信息则会采用HAC-SHA1进行签: - missing nonce: 请求中缺少nonce字段。 - missing timestamp: 请求中缺少timestamp字段。 - missing product or package: 请求中缺少product或package字段。 - invalid product: 请求的product或package不存在。 - missing device: 请求中缺少device字段。 - invalid mode: 请求中缺少mode或者mode的信息不是local也不是recursive。 - missing signature: 请求中缺少签名字段。 - invalid signature: 请求中的签名字段信息不正确。

### 样例

如果请求的开发者密钥信息、开发者信息、package以及timestamp信息正确的话，服务器会通过appliction/x-www-form-urlencoded的格式进行回复以下信息:

    nonce=1234585489&status=Success&provider=Amazon&state=co
    mpleted&signature=ZZCicZZZd61fKzh5y7n_FksRv68&payment=11
    

如果UDID对应的设备并没有找到相应的付费信息，服务器则只回复nonce与signature两个字段:

    nonce=1234585489&signature=gGcuiEC2qsyD0cIn-iu7fCHNdjU
    

如果服务器查询出现错误，服务器则只回复signature与error信息:

    signature=RFtOfaT7KNwDyolZq151ESh8zKg&error=invalid+signature
    

## 实现方案

![enter image description here][1]

*   iPhone: 运行应用软件的手机/平板设备，通过WIFI或是移动网络与开发者服务器通讯。
*   Vendor Server:开发者的代理服务器，代理iPhone与Cydia服务器的请求操作。
*   Cydia Server:Cydia API服务器，即指cydia.saurik.com。

由于开发者Secret Key信息是开发者重要的私秘信息，因此不应该被直接编码到应用软件中，因此应用软件对Cydia的所有数据请求都需要经过开发者的服务器进行代理.

## 参考

### vendor id 与 secret key

*   Vendor ID: 开发者名，即cydia包显示的作者。
*   Secret Key: 开发者密钥，如:23490sd29509040sdk9838xc0sd0dsfs。

上述信息在cydia的管理后台的"API Credentials"页面下可以查找到。

### Unix Time

Unix时间或被称为POSIX时间，是Unix或类Unix系统所使用的时间表达方式，从UTC/GMT 1970年1月1日0时0分0秒起至当前的总秒数，不包括闰秒。

具体的可以参看以下链接: - Unix time: http://en.wikipedia.org/wiki/Unix_time

### HMAC-SHA1

HMAC-SHA1的全称是Hash-based Message Authentication code using SHA-1，是基于SHA1构造Hash Key的算法，HMAC将密钥与消息数据混合，使用Hash对混合的结果进行Hash计算，将Hash值现密匙混合，之后再进行一次Hash计算，得到1个长度为160bit的Hash串。

通过HMAC-SHA1对数据进行签名后，在接收端可以对信息进行计算以确认数据是否在通讯过程中被篡改。

具体的可以参看以下链接:

*   RFC 2104: http://tools.ietf.org/html/rfc2104
*   Wikipedia: http://en.wikipedia.org/wiki/Hash-based_message_authentication_code
*   SHA-1: http://en.wikipedia.org/wiki/SHA-1

* * *

[End] （转载本站文章请注明作者和出处 Alick – alick.sinaapp.com ，请勿用于任何商业用途）

 [1]: http://alick-wordpress.stor.sinaapp.com/uploads/2014/07/Snip20140711_4.png



