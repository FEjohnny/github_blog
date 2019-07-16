---
title: http状态码和首部字段详解
date: 2018/5/22 21:06:42
tags: [javascript, http]
---

&emsp;&emsp;http：HyperText Transfer Protocol 超文本传输协议

&emsp;&emsp;诞生背景：1990年问世

### tcp/ip按层次分别分为4层：

    应用层：决定向用户提供应用服务时通信的活动。http也处于该层。
    传输层：对上层的应用层，提供处于网络链接中两台计算机之间的数据传输。有tcp（传输控制协议）和udp（用户数据报协议）。
    网络层：用来处理在网络上流动的数据包，该层规定了通过怎样的路径（传输路线）到达对方计算机，并把数据包传送给对方，与对方计算机之间通过多太计算机或网络设备进行传输时，网络层所起的作用就是在众多的选项内选择一条传输路线。
    链路层：又名数据链路层/网络接口层，用来处理链接网络的硬件部分，硬件上的范畴均在链路层的作用范围之内。

<!--more-->

### 返回结果的状态码：

    1XX：接收的请求正在处理。

    2XX：成功状态码
    204 no content ：请求成功，当没有资源可返回。
    206 partial content ：返回请求。

    3XX：重定向状态码
    301 moved permanently：永久重定向（资源被重新分配了URI）
    302 found：临时性重定向（资源的URI被临时定位到了其他位置）。
    303 see other：与302相似，但303明确表示客户端应该采用GET方法去获取资源。
    304 not modified ：该状态码表示客户端发送附带条件请求时，服务器端允许请求访问资源，但因发生请求未满足条件的情况后，直接返回304（服务器端资源未改变，可直接使用客户端未过期的缓存），304状态码返回时，不包含任何响应主体部分。304虽划分在3XX类别中，但是和重定向没有关系。
    307 temporary redirect ：临时重定向，与302类似，但不会强制改变请求方法。（每个浏览器处理可能不同）

    4XX：客户端错误，服务器无法处理请求
    400 bad request ：错误的请求，请求报文中存在语法错误。客户端会像对待200一样对待该状态码。
    401 unauthorized ：该状态码表明发送的请求需要有通过http认证（basic认证/digest认证）的认证信息。
    403 forbidden ：请求资源的访问被服务器拒绝。
    404 not found ：服务器上没有请求的资源。（也可能是服务端拒绝请求且不想说明理由时使用）

    5XX：服务端错误
    500 internet server error ：服务端在执行请求时发生了错误，可能是存在bug或者临时故障。
    502 错误网关，服务器作为网关或代理，从上游服务器收到无效响应。
    503 服务器超时。
    504 service unavailable ： 表明服务端暂时处于超负荷或正在进行停机维护，现在无法处理请求。

### http首部：

    1.在请求中，http报文由方法、url、http版本、http首部字段等构成。
    2.在响应报文中，http报文由http版本、状态码（数字和原因短语）、http首部字段3部分构成。

#### 一、通用首部字段（General Header Fields)，请求报文和响应报文都会使用的首部。

    Cathe-Control：控制缓存的行为。
    Connection：逐跳首部、连接的管理。
    Date：创建报文的日期时间。
    Pragma：指令报文。
    Trailer：报文末端的首部一览。
    Transfer-Encoding：制定报文主体的传输编码方法。
    Upgrade：升级为其他协议。
    via：代理服务器的相关信息。
    Warming：错误通知。

#### 二、请求首部字段（Request Header Fields)，从客户端向服务器发送请求报文时是使用的首部。补充了请求的附加内容、客户端信息、响应内容相关优先级等信息。

    Accept：用户代理（客户端）可处理的媒体类型。
    Accept-Charset：优先的字符集。
    Accept-Encoding：优先的编码内容。
    Accept-Language：优先的语言（自然语言）。
    Authorization：web认证信息。
    Except：期待服务器的特定行为。
    Form：用户的电子邮件地址。
    Host：请求资源所在的服务器。
    If-Match：比较实体标记（ETag）。
    If-Modified-Since：比较资源的更新时间。
    If-None-Match：笔记实体标记（与If-Match相反）。
    If-Range：资源未更新时发送实体Byte的范围请求。
    If-Unmodified-Since：比较资源的更新时间（与If-Modified-Since相反）。
    Max-Forwards：最大传输逐跳数。
    Proxy-Authorization：代理服务器要求客户端的认证信息。
    Range：实体的字节范围请求。
    Referer：对请求中URL的原始获取方。
    TE：传输编码的优先级。
    User-Agent：HTTP客户端程序的信息。
    Cookie：cookie信息。
    x-request-with：XMLHttpRequest请求头用于在服务器端判断request来自Ajax请求还是传统请求。

#### 三、响应首部字段（Response Header Fields)，从服务器向客户端返回响应报文时使用的首部。补充了响应的附加内容，也会要求客户端附加额外的内容信息。

    Accept-Ranges：是否接受字节范围请求。
    Age：推算资源创建经过时间。
    ETag：资源的匹配信息。
    Location：令客户端重定向至指定URI。
    Proxy-Authenticate：代理服务器对客户端的认证信息。
    Retry-After：对再次发起请求的时机要求。
    Server：http服务器的安装信息。
    Vary：代理服务器缓存的管理信息。
    WWW-Authenticate：服务器对客户端的认证信息。
    Set-Kookie：设置客户端cookie信息。

#### 四、实体首部字段（Entity Header Fields)，针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更新时间等实体有关信息。

    Allow：资源可支持的http方法。
    Content-Encoding：实体主体使用的编码方法。
    Content-Language：实体主体的自然语言。
    Content-Length：实体主体的大小（单位：字节）
    Content-Location：替代对于资源的URI。
    Content-MD5：实体主体的报文摘要。
    Content-Range：实体主体的位置范围。
    Content-Type：实体主体的媒体类型。
    Content-Dispositon：作为对下载文件的一个标识字段。
    Expires：设置实体主体的过期的日期时间。http1.0的东西
    Last-Modified：资源的最后修改日期。

#### HTTP首部字段将定义成缓存代理和非缓存代理的行为，分成2种类型：
&emsp;&emsp;1.End-to-end Header：端到端首部，分在此类别中的首付会转发请求/响应对应的最终接受目标，且必须保存在由缓存生成的响应中，另外规定它必须被转发。
&emsp;&emsp;2.Hop-by-hop Header：逐跳首部，分在此类别中的首部只对单词转发有效，会因通过缓存或代理而不再转发。
&emsp;&emsp;http/1.1中的逐跳首部字段，除了8个首部字段之外，其他所有字段都属于端到端首部：Connect、Keep-Alive、Proxy-Authenticate、Proxy-Authoriaztion、Trailer、TE、Transfer-Encoding、Upgrade。

Catch-Control指令可选值：

一、缓存请求指令

    1.no-cache：强制向源服务器再次验证
    2.no-store：不缓存请求或响应的任何内容
    3.max-age：响应的最大age值
    4.min-fresh：期望在指定时间内的响应依然有效
    5.no-transfer：代理不可更改媒体类型
    6.only-if-cached：从缓存资源获取
    7.cache-extension：新指令标记（token）

二、缓存响应指令

    1.public：可向任意方提供响应的缓存
    2.private：只能向特定用户返回响应
    3.no-cathe：缓存钱必须先确认其有效性
    4.no-store：不缓存请求或响应的任何内容
    5.no-transform：代理不可更改媒体类型
    6.must-revalidate：可缓存但必须再向资源服务器进行确认
    7.proxy-revalidate：要求中间缓存服务器对缓存的响应有效性再进行确认
    8.max-age=（秒）：响应的最大Age值
    9.s-maxage=（秒）：公共缓存服务器响应的最大age值
    10.cache-extension：新指令标记（token）
