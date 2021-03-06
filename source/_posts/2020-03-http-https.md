---
title: 了解 HTTP HTTPS TCP TLS/SSL
tags:
  - http
  - https
  - ssl
  - tls
categories:
  - - Computer
    - Communication
date: 2020-03-24 10:32:50
updated: 2021-01-23 16:53:21
---


## OSI模型

![OSI模型](/images/OSI模型层.png)
OSI 是用于描述网络通信的理论模型。 简单来说， 所有功能都被映射到七个层上。 最底层是最接近物理通信链路的层， 后面的层以次建立在其它层之上， 提供更高级别的抽象。 最顶层就是应用层， 携带应用数据。

OSI 模型的优点：

- 清晰的划分概念， 高层的协议不必担心在底层实现的功能。
- 不同层次的协议可以加入通信或者从通信中删除， 一种底层协议可以服务于多种上层协议。

## TCP

- [参考1](https://segmentfault.com/a/1190000022410446)
- [参考2](https://hit-alibaba.github.io/interview/basic/network/TCP.html)

### TCP 的特性

1. TCP 提供一种面向连接的、可靠的字节流服务
2. 在一个 TCP 连接中，仅有两方进行彼此通信。广播和多播不能用于 TCP
3. TCP 使用校验和，确认和重传机制来保证可靠传输
4. TCP 给数据分节进行排序，并使用累积确认保证数据的顺序不变和非重复
5. TCP 使用滑动窗口机制来实现流量控制，通过动态改变窗口的大小进行拥塞控制

>注意： TCP 并不能保证数据一定会被对方接收到，因为这是不可能的。TCP 能够做到的是，如果有可能，就把数据递送到接收方，否则就（通过放弃重传并且中断连接这一手段）通知用户。因此准确说 TCP 也不是 100% 可靠的协议，它所能提供的是数据的可靠递送或故障的可靠通知。

### TCP 控制位

在详细了解 TCP 的握手、挥手过程前， 先来了解下 TCP 报文首部中控制位 (Control Bits) 的6个具体标志位，它们会频繁的出现在握手和挥手的过程中。

1. URG(Urgent Bit): 值为1时，紧急指针生效。
2. ACK (Acknowledgment Bit)：值为 1 时，确认序号生效
3. PSH (Push Bit)：接收方应尽快将这个报文段交给应用层
4. RST (Reset Bit)：发送端遇到问题，想要重建连接
5. SYN (Synchronize Bit)：同步序号，用于发起一个连接
6. FIN (Finish Bit)：发送端要求关闭连接

### TCP 三次握手

所谓三次握手(Three-way Handshake)，是指建立一个 TCP 连接时，需要客户端和服务器总共发送3个包。

![三次握手](/images/3way-handshake.png)

- 第一次握手 (SYN=1, seq=x): 
  客户端发送一个 TCP 的 SYN 标志位为1的包，指明客户端打算连接的服务器的端口
  设置序列号(seq))字段为初始序号(ISN) X。
  发送完毕后，客户端进入 SYN_SEND 状态。

- 第二次握手 (SYN=1, ACK=1, seq=y, ack=x+1):
  服务器发回确认包应答， SYN 标志位和 ACK 标志位均为1（ACK 为1表示对客户端做出应答；SYN 为1表示这是一个发起连接）;
  同时将确认序号(ack)设置为客户的 ISN 加1，即 x+1（代表服务器期望下次收到的序列号seq）。 
  服务器端选择自己初始序号(ISN) y，放到 seq 里;
  发送完毕后，服务器端进入 SYN_RECEIVD 状态。

- 第三次握手 (ACK=1，seq=x+1，ack=y+1):
  客户端向服务器发送确认包，ACK 标志位为1（向客户端做出应答）；
  设置 seq 为 x+1，ack 为 y + 1；
  发送完毕后，客户端进入 ESTABLISH 状态；
  当服务器接收到这个包时，也进入 ESTABLISH 状态，至此 TCP 握手结束。


为什么非要三次握手才能建立连接呢？简单拆解三次握手所得出的结论：

1. 第一次握手服务端能得出结论：客户端的发送能力、服务端的接收能力是正常的。
2. 第二次握手客户端能得出结论：服务端的接收、发送能力，客户端的接收、发送能力是正常的；不过此时服务器并不能确认客户端的接收能力是否正常。
3. 第三次握手服务端就能得出结论：客户端的接收、发送能力正常，服务器自己的发送、接收能力也正常。


简单来说只有当客户端、服务器的接收、发送能力都正常时，才能建立稳定的连接。

#### TCP 三次握手的漏洞

在第一次握手之后，服务器接收到客户端的报文，需要分配并初始化连接变量和资源，并向客户端发送 SYN+ACK 报文段。 这建立了一个 **半开连接(half-open connection)** ，服务器会在一段时间内（一分钟）保持连接等待客户端的响应。而**半开连接**会消耗服务器资源。

在 SYN 洪泛攻击中，攻击者发送大量的 SYN 报文段，但不进行第三次握手，这会导致服务器大量资源的消耗而无法进行正常服务。

解决方案： SYN Cookies； 其原理是不再建立半开连接，而是根据客户端发送的 SYN 报文里的一些重要信息: 源、IP、端口等生成一个 cookie， 使用该 cookie 作为 seq 返回给客户端，在第三次握手时根据报文信息计算 cookie，与返回的 ack 进行对比， 如果相同则建立连接，否则拒绝。

### TCP 四次挥手

TCP 是全双工通信，因此在关闭时需要在两个方向都要进行关闭。

![四次挥手](/images/4way-handshake.png)

- 第一次挥手(FIN=1, seq=x):
  客户端发送一个 FIN 标志位置为1的包，请求关闭连接，客户端停止发送数据，但是仍然可以接受数据；
  根据上一个包的 ack 设置 seq；
  发送完毕后，客户端进入 FIN_WAIT_1 状态；

- 第二次挥手(ACK=1，ack=x+1，seq=y):
  服务器收到 FIN 报文后，发送确认报文，设置 ACK=1， ack=x+1，并带上自己的 seq=y；
  服务器进入 CLOSE-WAIT 状态；
  客户端收到服务器的 ACK 报文段后随即进入 FIN-WAIT-2 状态，此时还能收到来自服务器的数据，直到收到 FIN 报文段。

- 第三次挥手(FIN=1，seq=z):
  如果服务端准备断开，和客户端的第一次挥手一样，向服务器发送 FIN 报文，请求关闭；
  发送完毕后，服务器端进入 LAST_ACK 状态

- 第四次挥手(ACK=1，ack=z+1):
  客户端接收到来自服务器端的关闭请求，发送一个确认包，并进入 TIME_WAIT状态，等待可能出现的要求重传的 ACK 包。
  服务器端接收到这个确认包之后，关闭连接，进入 CLOSED 状态。
  客户端等待了某个固定时间（两个最大段生命周期，2MSL，2 Maximum Segment Lifetime）之后，没有收到服务器端的 ACK ，认为服务器端已经正常关闭连接，于是自己也关闭连接，进入 CLOSED 状态。

## HTTP

![OSI对比TCP/IP](/images/osi.jfif)

http 处于 通信模型的应用层， 基于 TCP/IP ， 因为 TCP 协议能保证数据不丢失是一种可靠的协议， 而 UDP 则是不可靠的协议。
TCP/IP 协议你一定听过，TCP/IP 我们一般称之为协议簇，什么意思呢？就是 TCP/IP 协议簇中不仅仅只有 TCP 协议和 IP 协议，它是一系列网络通信协议的统称。而其中最核心的两个协议就是 TCP / IP 协议，其他的还有 UDP、ICMP、ARP 等等，共同构成了一个复杂但有层次的协议栈。

IP 协议的全称是 Internet Protocol 的缩写，它主要解决的是通信双方寻址的问题。IP 协议使用 IP 地址 来标识互联网上的每一台计算机。

DNS 的全称是域名系统（Domain Name System，缩写：DNS），它作为将域名和 IP 地址相互映射的一个分布式数据库，能够使人更方便地访问互联网。

### HTTP 的影响因素

影响一个 http 请求的主要因素有两个： 带宽、 延迟。 带宽收到客户端环境因素影响， 所以主要优化的方向是延迟， 而影响延迟的主要有下面三点。

1. 浏览器阻塞（HOL blocking）， 浏览器 http 请求是有并发限制的， 正常同个域名下是 4 个， 根据浏览器不同限制也不一样。
2. DNS 查询 IP 也需要时间， 一般浏览器都会有 DNS 缓存来优化查询时间。
3. 建立连接 ， HTTP 传输层使用的是 TCP ， 需要经过三次握手之后才能建立连接， 但是这些连接无法被复用， 导致每次请求都要重新建立连接。

### HTTP 1.0 与 1.1 的区别

[参考文章](https://mp.weixin.qq.com/s/GICbiyJpINrHZ41u_4zT-A?)

两者主要区别体现在一下几点：

| | http 1.0 | http 1.1 |
| -- | -- | -- |
| 缓存处理 | header 中以 If-Modified-Since、 Expires 做判断标准 | 引入了更多的缓存策略如 etag，If-None-Match |
| 状态码 | | 新增了 206（只请求资源的某个部分，对应 header 中的 range）； 新增了 409（conflict） 等错误状态码 |
| Host 头处理 | 认为每台服务器都绑定唯一一个 IP 所以请求头中没有带 host 头 | 随着虚拟机的发展，一个物理机可能对应多个虚拟主机，他们公用同一个 IP ， 请求头中必须带上 host |
| 长连接	| 每次请求都要创建 TCP 连接	| 支持长连接， 在一个 TCP 连接上可以传送多个 HTTP 请求和响应（对应 keep-alive）， 减少连接的建立及关闭消耗|

### HTTP 和 HTTPS 的区别

- HTTPS 的表示层中使用了 TLS 协议， 传输的内容都经过加密。
- HTTPS 的默认端口号为 443 ， HTTP 的则是 80。

### SPDY HTTP 1.X 的优化

SPDY 方案优化了 HTTP 1.x 的请求延时， 具体如下：

1. 降低延时， SPDY 采用多路复用多个请求 stream 共享一个 tcp 连接的方式， 解决了浏览器阻塞问题。
2. 请求优先级， SPDY 允许给每个 request 设置优先级， 优先相应重要的请求。
3. header 压缩， 选择合适的算法可以减少包的大小。
4. 基于 HTTPS 的加密传输协议
5. 服务端推送， 采用了SPDY的网页，例如我的网页有一个style.css的请求，在客户端收到style.css数据的同时，服务端会将style.js的文件推送给客户端，当客户端再次尝试获取style.js时就可以直接从缓存中获取到，不用再发请求了。

SPDY位于HTTP之下，TCP和SSL之上，这样可以轻松兼容老版本的HTTP协议(将HTTP1.x的内容封装成一种新的frame格式)，同时可以使用已有的SSL功能。

### HTTP2.0: SPDY 的升级版

HTTP2.0可以说是SPDY的升级版（其实原本也是基于SPDY设计的），但是，HTTP2.0 跟 SPDY 仍有不同的地方，如下：
1. HTTP2.0 支持明文 HTTP 传输，而 SPDY 强制使用 HTTPS。
2. HTTP2.0 消息头的压缩算法采用 HPACK 而非 SPDY 采用的 DEFLATE

### HTTP2.0和 HTTP1.X 相比的新特性

- HTTP2.0 采用二进制格式， HTTP1.x 的解析是基于文本。
- HTTP2.0 采用多路复用， 多个请求可并行在一个连接上执行， 而 HTTP 1.1 中的长连接一个连接每次只能处理一个请求，无法并行。
- HTTP2.0 有 header 压缩
- HTTP2.0 有服务端推送

## TLS/SSL

SSL 是由 Netscape 公司开发的协议， TLS1.0 基于 SSL3 进行了一些修改并改了协议名为 TLS 。
TLS 握手是启动 HTTPS 通信的过程， 在握手过程中， 通信双方交换消息以相互验证，并确认它们索要使用的加密算法及会话密钥。

### TLS 握手详解

![TLS 握手过程](/images/tls.png)

1. 客户端通过发送 "client hello" 消息向服务器发起握手请求，该消息包含了客户端所支持的 TLS 版本和密码组合以供服务器进行选择，还有一个"client random"随机字符串。
2. 服务器发送"server hello"消息对客户端进行回应，该消息包含了数字证书，服务器选择的密码组合和"server random"随机字符串。
3. 客户端对服务器发来的证书进行验证，确保对方的合法身份。该过程中会检查数字签名、验证证书链、证书有效期、证书的撤回状态。
4. 客户端向服务器发送另一个随机字符串"premaster secret (预主密钥)"，这个字符串是经过服务器的公钥加密过的，只有对应的私钥才能解密。
5. 使用私钥：服务器使用私钥解密"premaster secret"。
6. 生成共享密钥：客户端和服务器均使用 client random，server random 和 premaster secret，并通过相同的算法生成相同的共享密钥 KEY。
7. 客户端就绪：客户端发送经过共享密钥 KEY加密过的"finished"信号。
8. 服务器就绪：服务器发送经过共享密钥 KEY加密过的"finished"信号。
9. 达成安全通信：握手完成，双方使用对称加密进行安全通信。

#### 数字证书(digital certificate)
在非对称加密通信过程中，服务器需要将公钥发送给客户端，在这一过程中，公钥很可能会被第三方拦截并替换，然后这个第三方就可以冒充服务器与客户端进行通信，这就是传说中的“中间人攻击”(man in the middle attack)。

解决此问题的方法是通过受信任的第三方交换公钥，具体做法就是服务器不直接向客户端发送公钥，而是要求受信任的第三方，也就是证书认证机构 (Certificate Authority, 简称 CA)将公钥合并到数字证书中，然后服务器会把公钥连同证书一起发送给客户端，私钥则由服务器自己保存以确保安全。

#### 数字签名 (digital signature)
这个概念很好理解，其实跟人的手写签名类似，是为了确保数据发送者的合法身份，也可以确保数据内容未遭到篡改，保证数据完整性。与手写签名不同的是，数字签名会随着文本数据的变化而变化。具体到数字证书的应用场景，数字签名的生成和验证流程如下：
1. 服务器对证书内容进行信息摘要计算 (常用算法有 SHA-256等)，得到摘要信息，再用私钥把摘要信息加密，就得到了数字签名
2. 服务器把数字证书连同数字签名一起发送给客户端
3. 客户端用公钥解密数字签名，得到摘要信息
4. 客户端用相同的信息摘要算法重新计算证书摘要信息，然后对这两个摘要信息进行比对，如果相同，则说明证书未被篡改，否则证书验证失败

#### 证书链 (certificate chain)

证书链，也称为证书路径，是用于认证实体合法身份的证书列表，具体到 HTTPS 通信中，就是为了验证服务器的合法身份。之所以使用证书链，是为了保证根证书 (root CA certificate)的安全，中间层可以看做根证书的代理，起到了缓冲的作用。

证书链从根证书开始，并且证书链中的每一级证书所标识的实体都要为其下一级证书签名，而根证书自身则由证书颁发机构签名。客户端在验证证书链时，必须对链中所有证书的数字签名进行验证，直到达到根证书为止。

## HTTPS

[参考](https://segmentfault.com/a/1190000021494676#)
HTTPS 代替 HTTP 的最大原因就是安全性，HTTP 在数据传输过程中采用的是明文传输，而 HTTPS 采用了混合加密：对称加密和非对称加密两种算法。

### 对称加密
对称加密，顾名思义就是加密和解密都是使用同一个密钥，常见的对称加密算法有 DES、3DES 和 AES 等，其优缺点如下：
- 优点： 算法公开、计算量小、加密速度快、加密效率高，适合加密比较大的数据。
- 缺点：
  1. 交易双方需要使用相同的密钥，也就无法避免密钥的传输，而密钥在传输过程中无法保证不被截获，因此对称加密的安全性得不到保证。

  2. 每对用户每次使用对称加密算法时，都需要使用其他人不知道的唯一密钥，这会使得发收信双方所拥有的钥匙数量急剧增长，密钥管理成为双方的负担。对称加密算法在分布式网络系统上使用较为困难，主要是因为密钥管理困难，使用成本较高。

### 非对称加密

非对称加密算法需要公钥、私钥两个不同的密钥来进行加密和解密。如果使用公钥对数据进行加密，只有对应的私钥才能解密；同样，如果使用私钥加密，只有对应的公钥才能解密；常见的非对称加密的算法是 RSA，非对称加密的优缺点如下：
- 优点： 算法公开，加密和解密使用不同的钥匙，私钥不需要通过网络进行传输，安全性很高。
- 缺点： 计算量比较大，加密和解密速度相比对称加密慢很多。

### HTTPS 通信过程

![HTTPS通信过程](/images/https.png)

HTTPS 通信过程分为证书验证、数据传输两个阶段。具体过程如下：

1. 客户端向服务器发送支持的SSL/TSL的协议版本号，以及客户端支持的加密方法，和一个客户端生成的随机数R1
2. 服务器确认协议版本和加密方法，向客户端发送一个由服务器生成的随机数R2，以及数字证书CA
3. 客户端验证证书是否有效，有效则从证书中取出公钥，生成一个随机数R3，然后用公钥加密这个随机数，发给服务器
4. 服务器用私钥解密，获取发来的随机数
5. 客户端和服务器根据约定好的加密方法，使用前面生成的三个随机数，生成对话密钥，用来加密接下来的整个对话过程





<!-- ## HTTP 面试解答

> 应用层 => 影响 http 因素 => http 1.0 & 1.1 & 2.0 各种优化及区别 => HTTPS

HTTP 协议基于 TCP/IP 处于通信模型的应用层， 影响一个 http 请求的主要因素有两个： 带宽和延迟。 
而其中 HTTP 协议的主要优化在于延迟， 影响延迟的也有三个主要因素：
1. 浏览器阻塞（http 请求的并发限制）
2. DNS 域名解析
3. TCP 连接

在 1.1 版本中通过长连接复用 TCP 连接， 在 2.0 中通过多路复用 TCP 可以并行处理 http 请求优化延迟。
1.1 中还引入了 etag 、 If-None-Match 的缓存策略， 新增了 206 、409 等状态码， 请求头中必带 host 字段。

HTTPS 在通信模型的表示层应用了 TLS/SSL 协议， 对传输内容进行了加密。

对于 DNS 解析的优化， 浏览器本身就带有 dns 缓存数据， 对于需要预解析的域名可以使用 dns-prefetch 。

### HTTPS 握手过程

1. 客户端向服务器发送支持的SSL/TSL的协议版本号，以及客户端支持的加密方法，和一个客户端生成的随机数
2. 服务器确认协议版本和加密方法，向客户端发送一个由服务器生成的随机数，以及数字证书
3. 客户端验证证书是否有效，有效则从证书中取出公钥，生成一个随机数，然后用公钥加密这个随机数，发给服务器
4. 服务器用私钥解密，获取发来的随机数
5. 客户端和服务器根据约定好的加密方法，使用前面生成的三个随机数，生成对话密钥，用来加密接下来的整个对话过程 -->