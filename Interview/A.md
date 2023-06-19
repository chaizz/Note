- [什么是网络编程](https://juejin.cn/post/6844904125692379143#heading-1)
- [网络编程中两个主要的问题](https://juejin.cn/post/6844904125692379143#heading-2)
- [网络协议是什么](https://juejin.cn/post/6844904125692379143#heading-3)
- [为什么要对网络协议分层](https://juejin.cn/post/6844904125692379143#heading-4)
- [计算机网络体系结构](https://juejin.cn/post/6844904125692379143#heading-5)
  - [1 TCP / UDP](https://juejin.cn/post/6844904125692379143#heading-6)
    - [1.1 什么是TCP/IP和UDP](https://juejin.cn/post/6844904125692379143#heading-7)
    - [1.2 TCP与UDP区别：](https://juejin.cn/post/6844904125692379143#heading-8)
    - [1.3 TCP和UDP的应用场景：](https://juejin.cn/post/6844904125692379143#heading-9)
    - [1.4 形容一下TCP和UDP](https://juejin.cn/post/6844904125692379143#heading-10)
    - [1.5 运行在TCP 或UDP的应用层协议分析。](https://juejin.cn/post/6844904125692379143#heading-11)
    - [什么是ARP协议 (Address Resolution Protocol)？](https://juejin.cn/post/6844904125692379143#heading-12)
    - [什么是NAT (Network Address Translation, 网络地址转换)？](https://juejin.cn/post/6844904125692379143#heading-13)
    - [从输入址到获得页面的过程?](https://juejin.cn/post/6844904125692379143#heading-14)
    - [1.6 TCP的三次握手](https://juejin.cn/post/6844904125692379143#heading-15)
    - [1.7 TCP的四次挥手](https://juejin.cn/post/6844904125692379143#heading-24)
  - [2 Socket](https://juejin.cn/post/6844904125692379143#heading-31)
    - [1 什么是Socket](https://juejin.cn/post/6844904125692379143#heading-32)
    - [2 socket属于网络的那个层面](https://juejin.cn/post/6844904125692379143#heading-33)
    - [3 Socket通讯的过程](https://juejin.cn/post/6844904125692379143#heading-34)
    - [4 TCP协议Socket代码示例：](https://juejin.cn/post/6844904125692379143#heading-35)
    - [5 UDP协议Socket代码示例：](https://juejin.cn/post/6844904125692379143#heading-36)
    - [6 Socket的常用类](https://juejin.cn/post/6844904125692379143#heading-37)
  - [3. HTTP](https://juejin.cn/post/6844904125692379143#heading-38)
    - [什么是Http协议？](https://juejin.cn/post/6844904125692379143#heading-39)
    - [Socket和http的区别和应用场景](https://juejin.cn/post/6844904125692379143#heading-40)
    - [什么是http的请求体？](https://juejin.cn/post/6844904125692379143#heading-41)
    - [http的响应报文有哪些？](https://juejin.cn/post/6844904125692379143#heading-42)
    - [http和https的区别？](https://juejin.cn/post/6844904125692379143#heading-43)
    - [HTTPS工作原理](https://juejin.cn/post/6844904125692379143#heading-44)
    - [一次完整的HTTP请求所经历几个步骤?](https://juejin.cn/post/6844904125692379143#heading-45)
    - [常用HTTP状态码是怎么分类的，有哪些常见的状态码？](https://juejin.cn/post/6844904125692379143#heading-46)
    - [Http协议中有那些请求方式](https://juejin.cn/post/6844904125692379143#heading-47)
    - [GET方法与POST方法的区别](https://juejin.cn/post/6844904125692379143#heading-48)
    - [http版本的对比](https://juejin.cn/post/6844904125692379143#heading-49)
    - [什么是对称加密与非对称加密](https://juejin.cn/post/6844904125692379143#heading-50)
    - [cookie和session对于HTTP有什么用？](https://juejin.cn/post/6844904125692379143#heading-51)
    - [cookie和session对于HTTP有什么用？](https://juejin.cn/post/6844904125692379143#heading-55)



## 什么是网络编程

- 网络编程的本质是多台计算机之间的数据交换。数据传递本身没有多大的难度，不就是把一个设备中的数据发送给其他设备，然后接受另外一个设备反馈的数据。现在的网络编程基本上都是基于请求/响应方式的，也就是一个设备发送请求数据给另外一个，然后接收另一个设备的反馈。在网络编程中，发起连接程序，也就是发送第一次请求的程序，被称作客户端(Client)，等待其他程序连接的程序被称作服务器(Server)。客户端程序可以在需要的时候启动，而服务器为了能够时刻相应连接，则需要一直启动。
- 例如以打电话为例，首先拨号的人类似于客户端，接听电话的人必须保持电话畅通类似于服务器。连接一旦建立以后，就客户端和服务器端就可以进行数据传递了，而且两者的身份是等价的。在一些程序中，程序既有客户端功能也有服务器端功能，最常见的软件就是QQ、微信这类软件了。

## 网络编程中两个主要的问题

1. 一个是如何准确的定位网络上一台或多台主机，
2. 另一个就是找到主机后如何可靠高效的进行数据传输。

- 在TCP/IP协议中IP层主要负责网络主机的定位，数据传输的路由，由IP地址可以唯一地确定Internet上的一台主机。
- 而TCP层则提供面向应用的可靠（TCP）的或非可靠（UDP）的数据传输机制，这是网络编程的主要对象，一般不需要关心IP层是如何处理数据的。
- 目前较为流行的网络编程模型是客户机/服务器（C/S）结构。即通信双方一方作为服务器等待客户提出请求并予以响应。客户则在需要服务时向服务器提 出申请。服务器一般作为守护进程始终运行，监听网络端口，一旦有客户请求，就会启动一个服务进程来响应该客户，同时自己继续监听服务端口，使后来的客户也 能及时得到服务。

## 网络协议是什么

- 在计算机网络要做到井井有条的交换数据，就必须遵守一些事先约定好的规则，比如交换数据的格式、是否需要发送一个应答信息。这些规则被称为网络协议。

## 为什么要对网络协议分层

- 简化问题难度和复杂度。由于各层之间独立，我们可以分割大问题为小问题。
- 灵活性好。当其中一层的技术变化时，只要层间接口关系保持不变，其他层不受影响。
- 易于实现和维护。
- 促进标准化工作。分开后，每层功能可以相对简单地被描述

## 计算机网络体系结构



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297c7320a555~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- OSI参考模型
  - OSI（Open System Interconnect），即开放式系统互联。一般都叫OSI参考模型，是ISO（国际标准化组织）组织在1985年研究的网络互连模型。ISO为了更好的使网络应用更为普及，推出了OSI参考模型，这样所有的公司都按照统一的标准来指定自己的网络，就可以互通互联了。
  - OSI定义了网络互连的七层框架（物理层、数据链路层、网络层、传输层、会话层、表示层、应用层）。

> ![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297c7324a8f0~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

- **TCP/IP参考模型**
- TCP/IP四层协议（数据链路层、网络层、传输层、应用层）
  1. 应用层 应用层最靠近用户的一层，是为计算机用户提供应用接口，也为用户直接提供各种网络服务。我们常见应用层的网络服务协议有：HTTP，HTTPS，FTP，TELNET等。
  2. 传输层 建立了主机端到端的链接，传输层的作用是为上层协议提供端到端的可靠和透明的数据传输服务，包括处理差错控制和流量控制等问题。该层向高层屏蔽了下层数据通信的细节，使高层用户看到的只是在两个传输实体间的一条主机到主机的、可由用户控制和设定的、可靠的数据通路。我们通常说的，TCP UDP就是在这一层。端口号既是这里的“端”。
  3. 网络层 本层通过IP寻址来建立两个节点之间的连接，为源端的运输层送来的分组，选择合适的路由和交换节点，正确无误地按照地址传送给目的端的运输层。就是通常说的IP层。这一层就是我们经常说的IP协议层。IP协议是Internet的基础。
  4. 数据链路层 通过一些规程或协议来控制这些数据的传输，以保证被传输数据的正确性。实现这些规程或协议的`硬件`和软件加到物理线路，这样就构成了数据链路，

### 1 TCP / UDP

#### 1.1 什么是TCP/IP和UDP

- TCP/IP即传输控制/网络协议，是面向连接的协议，发送数据前要先建立连接(发送方和接收方的成对的两个之间必须建 立连接)，TCP提供可靠的服务，也就是说，通过TCP连接传输的数据不会丢失，没有重复，并且按顺序到达
- UDP它是属于TCP/IP协议族中的一种。是无连接的协议，发送数据前不需要建立连接，是没有可靠性的协议。因为不需要建立连接所以可以在在网络上以任何可能的路径传输，因此能否到达目的地，到达目的地的时间以及内容的正确性都是不能被保证的。

#### 1.2 TCP与UDP区别：

- TCP是面向连接的协议，发送数据前要先建立连接，TCP提供可靠的服务，也就是说，通过TCP连接传输的数据不会丢失，没有重复，并且按顺序到达；
- UDP是无连接的协议，发送数据前不需要建立连接，是没有可靠性；
- TCP通信类似于于要打个电话，接通了，确认身份后，才开始进行通行；
- UDP通信类似于学校广播，靠着广播播报直接进行通信。
- TCP只支持点对点通信，UDP支持一对一、一对多、多对一、多对多；
- TCP是面向字节流的，UDP是面向报文的； 面向字节流是指发送数据时以字节为单位，一个数据包可以拆分成若干组进行发送，而UDP一个报文只能一次发完。
- TCP首部开销（20字节）比UDP首部开销（8字节）要大
- UDP 的主机不需要维持复杂的连接状态表

#### 1.3 TCP和UDP的应用场景：

- 对某些实时性要求比较高的情况使用UDP，比如游戏，媒体通信，实时直播，即使出现传输错误也可以容忍；其它大部分情况下，HTTP都是用TCP，因为要求传输的内容可靠，不出现丢失的情况

#### 1.4 形容一下TCP和UDP

- **TCP通信可看作打电话：**

  李三(拨了个号码)：喂，是王五吗？ 王五：哎，您谁啊？ 李三：我是李三，我想给你说点事儿，你现在方便吗？ 王五：哦，我现在方便，你说吧。 甲：那我说了啊？ 乙：你说吧。 (连接建立了，接下来就是说正事了…)

- **UDP通信可看为学校里的广播：**

  播音室：喂喂喂！全体操场集合

#### 1.5 运行在TCP 或UDP的应用层协议分析。

- 运行在TCP协议上的协议：
  - HTTP（Hypertext Transfer Protocol，超文本传输协议），主要用于普通浏览。
  - HTTPS（HTTP over SSL，安全超文本传输协议）,HTTP协议的安全版本。
  - FTP（File Transfer Protocol，文件传输协议），用于文件传输。
  - POP3（Post Office Protocol, version 3，邮局协议），收邮件用。
  - SMTP（Simple Mail Transfer Protocol，简单邮件传输协议），用来发送电子邮件。
  - TELNET（Teletype over the Network，网络电传），通过一个终端（terminal）登陆到网络。
  - SSH（Secure Shell，用于替代安全性差的TELNET），用于加密安全登陆用。
- 运行在UDP协议上的协议：
  - BOOTP（Boot Protocol，启动协议），应用于无盘设备。
  - NTP（Network Time Protocol，网络时间协议），用于网络同步。
  - DHCP（Dynamic Host Configuration Protocol，动态主机配置协议），动态配置IP地址。
- 运行在TCP和UDP协议上：
  - DNS（Domain Name Service，域名服务），用于完成地址查找，邮件转发等工作。
  - ECHO（Echo Protocol，回绕协议），用于查错及测量应答时间（运行在[TCP](https://link.juejin.cn/?target=http%3A%2F%2Fzh.wikipedia.org%2Fzh-cn%2FTCP)和[UDP](https://link.juejin.cn/?target=http%3A%2F%2Fzh.wikipedia.org%2Fzh-cn%2FUDP)协议上）。
  - SNMP（Simple Network Management Protocol，简单网络管理协议），用于网络信息的收集和网络管理。
  - DHCP（Dynamic Host Configuration Protocol，动态主机配置协议），动态配置IP地址。
  - ARP（Address Resolution Protocol，地址解析协议），用于动态解析以太网硬件的地址。

#### 什么是ARP协议 (Address Resolution Protocol)？

- **ARP协议完成了IP地址与物理地址的映射**。每一个主机都设有一个 ARP 高速缓存，里面有**所在的局域网**上的各主机和路由器的 IP 地址到硬件地址的映射表。当源主机要发送数据包到目的主机时，会先检查自己的ARP高速缓存中有没有目的主机的MAC地址，如果有，就直接将数据包发到这个MAC地址，如果没有，就向**所在的局域网**发起一个ARP请求的广播包（在发送自己的 ARP 请求时，同时会带上自己的 IP 地址到硬件地址的映射），收到请求的主机检查自己的IP地址和目的主机的IP地址是否一致，如果一致，则先保存源主机的映射到自己的ARP缓存，然后给源主机发送一个ARP响应数据包。源主机收到响应数据包之后，先添加目的主机的IP地址与MAC地址的映射，再进行数据传送。如果源主机一直没有收到响应，表示ARP查询失败。
- 如果所要找的主机和源主机不在同一个局域网上，那么就要通过 ARP 找到一个位于本局域网上的某个路由器的硬件地址，然后把分组发送给这个路由器，让这个路由器把分组转发给下一个网络。剩下的工作就由下一个网络来做。

#### 什么是NAT (Network Address Translation, 网络地址转换)？

- 用于解决内网中的主机要和因特网上的主机通信。由NAT路由器将主机的本地IP地址转换为全球IP地址，分为静态转换（转换得到的全球IP地址固定不变）和动态NAT转换。

#### 从输入址到获得页面的过程?

1. 浏览器查询 DNS，获取域名对应的IP地址:具体过程包括浏览器搜索自身的DNS缓存、搜索操作系统的DNS缓存、读取本地的Host文件和向本地DNS服务器进行查询等。对于向本地DNS服务器进行查询，如果要查询的域名包含在本地配置区域资源中，则返回解析结果给客户机，完成域名解析(此解析具有权威性)；如果要查询的域名不由本地DNS服务器区域解析，但该服务器已缓存了此网址映射关系，则调用这个IP地址映射，完成域名解析（此解析不具有权威性）。如果本地域名服务器并未缓存该网址映射关系，那么将根据其设置发起递归查询或者迭代查询；
2. 浏览器获得域名对应的IP地址以后，浏览器向服务器请求建立链接，发起三次握手；
3. TCP/IP链接建立起来后，浏览器向服务器发送HTTP请求；
4. 服务器接收到这个请求，并根据路径参数映射到特定的请求处理器进行处理，并将处理结果及相应的视图返回给浏览器；
5. 浏览器解析并渲染视图，若遇到对js文件、css文件及图片等静态资源的引用，则重复上述步骤并向服务器请求这些资源；
6. 浏览器根据其请求到的资源、数据渲染页面，最终向用户呈现一个完整的页面。

#### 1.6 TCP的三次握手

##### 1.6.1 什么是TCP的三次握手

- 在网络数据传输中，传输层协议TCP是要建立连接的可靠传输，TCP建立连接的过程，我们称为三次握手。

##### 1.6.2 三次握手的具体细节



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297c73467e00~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



1. 第一次握手：Client将SYN置1，随机产生一个初始序列号seq发送给Server，进入SYN_SENT状态；
2. 第二次握手：Server收到Client的SYN=1之后，知道客户端请求建立连接，将自己的SYN置1，ACK置1，产生一个acknowledge number=sequence number+1，并随机产生一个自己的初始序列号，发送给客户端；进入SYN_RCVD状态；
3. 第三次握手：客户端检查acknowledge number是否为序列号+1，ACK是否为1，检查正确之后将自己的ACK置为1，产生一个acknowledge number=服务器发的序列号+1，发送给服务器；进入ESTABLISHED状态；服务器检查ACK为1和acknowledge number为序列号+1之后，也进入ESTABLISHED状态；完成三次握手，连接建立。

- 简单来说就是 ：
  1. 客户端向服务端发送SYN
  2. 服务端返回SYN,ACK
  3. 客户端发送ACK

##### 1.6.3 用现实理解三次握手的具体细节

- 三次握手的目的是建立可靠的通信信道，主要的目的就是双方确认自己与对方的发送与接收机能正常。

1. 第一次握手：客户什么都不能确认；服务器确认了对方发送正常
2. 第二次握手：客户确认了：自己发送、接收正常，对方发送、接收正常；服务器确认 了：自己接收正常，对方发送正常
3. 第三次握手：客户确认了：自己发送、接收正常，对方发送、接收正常；服务器确认 了：自己发送、接收正常，对方发送接收正常 所以三次握手就能确认双发收发功能都正常，缺一不可。

##### 1.6.4 建立连接可以两次握手吗？为什么?

- 不可以。
- 因为可能会出现已失效的连接请求报文段又传到了服务器端。 > client 发出的第一个连接请求报文段并没有丢失，而是在某个网络结点长时间的滞留了，以致延误到连接释放以后的某个时间才到达 server。本来这是一个早已失效的报文段。但 server 收到此失效的连接请求报文段后，就误认为是 client 再次发出的一个新的连接请求。于是就向 client 发出确认报文段，同意建立连接。假设不采用 “三次握手”，那么只要 server 发出确认，新的连接就建立了。由于现在 client 并没有发出建立连接的请求，因此不会理睬 server 的确认，也不会向 server 发送数据。但 server 却以为新的运输连接已经建立，并一直等待 client 发来数据。这样，server 的很多资源就白白浪费掉了。采用 “三次握手” 的办法可以防止上述现象发生。例如刚才那种情况，client 不会向 server 的确认发出确认。server 由于收不到确认，就知道 client 并没有要求建立连接。
- 而且，两次握手无法保证Client正确接收第二次握手的报文（Server无法确认Client是否收到），也无法保证Client和Server之间成功互换初始序列号。

##### 1.6.5 可以采用四次握手吗？为什么？

- 这个肯定可以。三次握手都可以保证连接成功了，何况是四次，但是会降低传输的效率。

##### 1.6.6 第三次握手中，如果客户端的ACK未送达服务器，会怎样？

- Server端：由于Server没有收到ACK确认，因此会每隔 3秒 重发之前的SYN+ACK（默认重发五次，之后自动关闭连接进入CLOSED状态），Client收到后会重新传ACK给Server。
- Client端，会出现两种情况：
  1. 在Server进行超时重发的过程中，如果Client向服务器发送数据，数据头部的ACK是为1的，所以服务器收到数据之后会读取 ACK number，进入 establish 状态
  2. 在Server进入CLOSED状态之后，如果Client向服务器发送数据，服务器会以RST包应答。

##### 1.6.7 如果已经建立了连接，但客户端出现了故障怎么办？

- 服务器每收到一次客户端的请求后都会重新复位一个计时器，时间通常是设置为2小时，若两小时还没有收到客户端的任何数据，服务器就会发送一个探测报文段，以后每隔75秒钟发送一次。若一连发送10个探测报文仍然没反应，服务器就认为客户端出了故障，接着就关闭连接。

##### 1.6.8 初始序列号是什么？

- TCP连接的一方A，随机选择一个32位的序列号（Sequence Number）作为发送数据的初始序列号（Initial Sequence Number，ISN），比如为1000，以该序列号为原点，对要传送的数据进行编号：1001、1002...三次握手时，把这个初始序列号传送给另一方B，以便在传输数据时，B可以确认什么样的数据编号是合法的；同时在进行数据传输时，A还可以确认B收到的每一个字节，如果A收到了B的确认编号（acknowledge number）是2001，就说明编号为1001-2000的数据已经被B成功接受。

#### 1.7 TCP的四次挥手

##### 1.7.1 什么是TCP的四次挥手

- 在网络数据传输中，传输层协议断开连接的过程我们称为四次挥手

##### 1.7.2 四次挥手的具体细节



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297c746f6ee2~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



1. 第一次挥手：Client将FIN置为1，发送一个序列号seq给Server；进入FIN_WAIT_1状态；
2. 第二次挥手：Server收到FIN之后，发送一个ACK=1，acknowledge number=收到的序列号+1；进入CLOSE_WAIT状态。此时客户端已经没有要发送的数据了，但仍可以接受服务器发来的数据。
3. 第三次挥手：Server将FIN置1，发送一个序列号给Client；进入LAST_ACK状态；
4. 第四次挥手：Client收到服务器的FIN后，进入TIME_WAIT状态；接着将ACK置1，发送一个acknowledge number=序列号+1给服务器；服务器收到后，确认acknowledge number后，变为CLOSED状态，不再向客户端发送数据。客户端等待2*MSL（报文段最长寿命）时间后，也进入CLOSED状态。完成四次挥手。

##### 1.7.3 用现实理解三次握手的具体细节TCP的四次挥手

- 四次挥手断开连接是因为要确定数据全部传书完了

1. 客户与服务器交谈结束之后，客户要结束此次会话，就会对服务器说：我要关闭连接了（第一 次挥手）
2. 服务器收到客户的消息后说：好的，你要关闭连接了。（第二次挥手）
3. 然后服务器确定了没有话要和客户说了，服务器就会对客户说，我要关闭连接了。(第三次挥 手)
4. 客户收到服务器要结束连接的消息后说：已收到你要关闭连接的消息。(第四次挥手)，才关闭

##### 1.7.4 为什么不能把服务器发送的ACK和FIN合并起来，变成三次挥手（CLOSE_WAIT状态意义是什么）？

- 因为服务器收到客户端断开连接的请求时，可能还有一些数据没有发完，这时先回复ACK，表示接收到了断开连接的请求。等到数据发完之后再发FIN，断开服务器到客户端的数据传送。

##### 1.7.5 如果第二次挥手时服务器的ACK没有送达客户端，会怎样？

- 客户端没有收到ACK确认，会重新发送FIN请求。

##### 1.7.6 客户端TIME_WAIT状态的意义是什么？

- 第四次挥手时，客户端发送给服务器的ACK有可能丢失，TIME_WAIT状态就是用来重发可能丢失的ACK报文。如果Server没有收到ACK，就会重发FIN，如果Client在2*MSL的时间内收到了FIN，就会重新发送ACK并再次等待2MSL，防止Server没有收到ACK而不断重发FIN。 MSL(Maximum Segment Lifetime)，指一个片段在网络中最大的存活时间，2MSL就是一个发送和一个回复所需的最大时间。如果直到2MSL，Client都没有再次收到FIN，那么Client推断ACK已经被成功接收，则结束TCP连接。

### 2 Socket

#### 1 什么是Socket

- 网络上的两个程序通过一个双向的通讯连接实现数据的交换，这个双向链路的一端称为一个Socket。Socket通常用来实现客户方和服务方的连接。Socket是TCP/IP协议的一个十分流行的编程界面，一个Socket由一个IP地址和一个端口号唯一确定。

- 但是，Socket所支持的协议种类也不光TCP/IP、UDP，因此两者之间是没有必然联系的。在Java环境下，Socket编程主要是指基于TCP/IP协议的网络编程。

- socket连接就是所谓的长连接，客户端和服务器需要互相连接，理论上客户端和服务器端一旦建立起连接将不会主动断掉的，但是有时候网络波动还是有可能的

- Socket偏向于底层。一般很少直接使用Socket来编程，框架底层使用Socket比较多，

  ![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297c768507e4~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

  

#### 2 socket属于网络的那个层面



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297c7c7176ba~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- Socket是应用层与TCP/IP协议族通信的中间软件抽象层，它是一组接口。在设计模式中，Socket其实就是一个[外观模式](https://link.juejin.cn/?target=https%3A%2F%2Fblog.csdn.net%2Fweixin_43122090%2Farticle%2Fdetails%2F104904625)，它把复杂的TCP/IP协议族隐藏在Socket接口后面，对用户来说，一组简单的接口就是全部，让Socket去组织数据，以符合指定的协议。

#### 3 Socket通讯的过程

- 基于TCP：服务器端先初始化Socket，然后与端口绑定(bind)，对端口进行监听(listen)，调用accept阻塞，等待客户端连接。在这时如果有个客户端初始化一个Socket，然后连接服务器(connect)，如果连接成功，这时客户端与服务器端的连接就建立了。客户端发送数据请求，服务器端接收请求并处理请求，然后把回应数据发送给客户端，客户端读取数据，最后关闭连接，一次交互结束。
- 基于UDP：UDP 协议是用户数据报协议的简称，也用于网络数据的传输。虽然 UDP 协议是一种不太可靠的协议，但有时在需要较快地接收数据并且可以忍受较小错误的情况下，UDP 就会表现出更大的优势。我客户端只需要发送，服务端能不能接收的到我不管

#### 4 TCP协议Socket代码示例：

`先运行服务端，在运行客户端`，

1. 服务端：

```java
java复制代码package com.test.io;

import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;

//TCP协议Socket使用BIO进行通行：服务端
public class BIOServer {
    // 在main线程中执行下面这些代码
    public static void main(String[] args) {
        //1单线程服务
        ServerSocket server = null;
        Socket socket = null;
        InputStream in = null;
        OutputStream out = null;
        try {
            server = new ServerSocket(8000);
            System.out.println("服务端启动成功，监听端口为8000，等待客户端连接...");
            while (true){
                socket = server.accept(); //等待客户端连接
                System.out.println("客户连接成功，客户信息为：" + socket.getRemoteSocketAddress());
                in = socket.getInputStream();
                byte[] buffer = new byte[1024];
                int len = 0;
                //读取客户端的数据
                while ((len = in.read(buffer)) > 0) {
                    System.out.println(new String(buffer, 0, len));
                }
                //向客户端写数据
                out = socket.getOutputStream();
                out.write("hello!".getBytes());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

1. 客户端：

```java
java复制代码package com.test.io;

import java.io.IOException;
import java.io.OutputStream;
import java.net.Socket;
import java.util.Scanner;

//TCP协议Socket：客户端
public class Client01 {
    public static void main(String[] args) throws IOException {
        //创建套接字对象socket并封装ip与port
        Socket socket = new Socket("127.0.0.1", 8000);
        //根据创建的socket对象获得一个输出流
        OutputStream outputStream = socket.getOutputStream();
        //控制台输入以IO的形式发送到服务器
        System.out.println("TCP连接成功 \n请输入：");
        while(true){
            byte[] car = new Scanner(System.in).nextLine().getBytes();
            outputStream.write(car);
            System.out.println("TCP协议的Socket发送成功");
            //刷新缓冲区
            outputStream.flush();
        }
    }
}
```

`先运行服务端，在运行客户端`。测试结果发送成功：

![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297c9803dcaf~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

·

![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297ca3ffbda2~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



#### 5 UDP协议Socket代码示例：

```
先运行服务端，在运行客户端
```

1. 服务端：

```reasonml
reasonml复制代码//UDP协议Socket：服务端
public class Server1 {
    public static void main(String[] args) {
        try {
            //DatagramSocket代表声明一个UDP协议的Socket
            DatagramSocket socket = new DatagramSocket(8888);
            //byte数组用于数据存储。
            byte[] car = new byte[1024];
            //DatagramPacket 类用来表示数据报包DatagramPacket
            DatagramPacket packet = new DatagramPacket(car, car.length);
            // //创建DatagramPacket的receive()方法来进行数据的接收,等待接收一个socket请求后才执行后续操作；
            System.out.println("等待UDP协议传输数据");
            socket.receive(packet);
            //packet.getLength返回将要发送或者接收的数据的长度。
            int length = packet.getLength();
            System.out.println("啥东西来了：" + new String(car, 0, length));
            socket.close();
            System.out.println("UDP协议Socket接受成功");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

1. 客户端：

```java
java复制代码//UDP协议Socket：客户端
public class Client1 {
    public static void main(String[] args) {
        try {
            //DatagramSocket代表声明一个UDP协议的Socket
            DatagramSocket socket = new DatagramSocket(2468);
            //字符串存储人Byte数组
            byte[] car = "UDP协议的Socket请求，有可能失败哟".getBytes();
            //InetSocketAddress类主要作用是封装端口
            InetSocketAddress address = new InetSocketAddress("127.0.0.1", 8888);
            //DatagramPacket 类用来表示数据报包DatagramPacket
            DatagramPacket packet = new DatagramPacket(car, car.length, address);
            //send() 方法发送数据包。
            socket.send(packet);
            System.out.println("UDP协议的Socket发送成功");
            socket.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

`先运行服务端，在运行客户端`。测试结果成功发送成功：

![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297ca5df17df~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



#### 6 Socket的常用类

| 类名              | 用于      | 作用                                                         |
| ----------------- | --------- | ------------------------------------------------------------ |
| Socket            | TCP协议   | Socket类同时工作于客户端和服务端，所有方法都是通用的，这个类三个主要作用，校验包信息，发起连接（Client），操作流数据（Client/Server） |
| ServerSocket      | TCP协议   | ServerSocket表示为服务端，主要作用就是绑定并监听一个服务器端口，为每个建立连接的客户端“克隆/映射”一个Socket对象，具体数据操作都是通过这个Socket对象完成的，ServerSocket只关注如何和客户端建立连接 |
| DatagramSocket    | ODP协议   | DatagramSocket 类用于表示发送和接收数据报包的套接字。        |
| DatagramPacket    | ODP协议   | DatagramPacket 类用来表示数据报包，数据报包用来实现无连接包投递服务。 |
| InetAddress       | IP+端口号 | Java提供了InetAddress类来代表互联网协议（IP）地址，InetAddress类没有提供构造器，而是提供了如下两个静态方法来获取InetAddress实例： |
| InetSocketAddress | IP+端口号 | 在使用Socket来连接服务器时最简单的方式就是直接使用IP和端口，但Socket类中并未提供这种方式，而是靠SocketAddress的子类InetSocketAddress来实现 IP 地址 + 端口号的创建，不依赖任何协议。 |

### 3. HTTP

#### 什么是Http协议？

- Http协议是对客户端和服务器端之间数据之间实现可靠性的传输文字、图片、音频、视频等超文本数据的规范，格式简称为“超文本传输协议”

- Http协议属于应用层，及用户访问的第一层就是http

  ![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297caf6776de~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

  

#### Socket和http的区别和应用场景

- Socket连接就是所谓的长连接，理论上客户端和服务器端一旦建立起连接将不会主动断掉；
- Socket适用场景：网络游戏，银行持续交互，直播，在线视屏等。
- http连接就是所谓的短连接，即客户端向服务器端发送一次请求，服务器端响应后连接即会断开等待下次连接
- http适用场景：公司OA服务，互联网服务，电商，办公，网站等等等等

#### 什么是http的请求体？

- HTTP请求体是我们请求数据时先发送给服务器的数据，毕竟我向服务器那数据，先要表明我要什么吧
- HTTP请求体由：请求行 、请求头、请求数据组成的，
- 注意：GIT请求是没有请求体的

1. POST请求

   

2. GIT请求是没有请求体的 ![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297cbc5c7a1c~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp) `发现只有请求行和请求头，少了个请求体`

#### http的响应报文有哪些？

- http的响应报是服务器返回给我们的数据，必须先有请求体再有响应报文

- 响应报文包含三部分 状态行、响应首部字段、响应内容实体实现

  ![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717297cbf073fa2~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

  

#### http和https的区别？

- 其实HTTPS就是从HTTP加上加密处理（一般是SSL安全通信线路）+认证+完整性保护
- 区别：
  1. http需要拿到ca证书，需要钱的
  2. 端口不一样，http是80，https443
  3. http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。
  4. http和https使用的是完全不同的连接方式（http的连接很简单，是无状态的；HTTPS 协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。）

#### HTTPS工作原理

- 一、首先HTTP请求服务端生成证书，客户端对证书的有效期、合法性、域名是否与请求的域名一致、证书的公钥（RSA加密）等进行校验；
- 二、客户端如果校验通过后，就根据证书的公钥的有效， 生成随机数，随机数使用公钥进行加密（RSA加密）；
- 三、消息体产生的后，对它的摘要进行MD5（或者SHA1）算法加密，此时就得到了RSA签名；
- 四、发送给服务端，此时只有服务端（RSA私钥）能解密。
- 五、解密得到的随机数，再用AES加密，作为密钥（此时的密钥只有客户端和服务端知道）。

#### 一次完整的HTTP请求所经历几个步骤?

```
HTTP通信机制是在一次完整的HTTP通信过程中，Web浏览器与Web服务器之间将完成下列7个步骤：
```

1. 建立TCP连接

   怎么建立连接的，看上面的三次捂手

2. Web浏览器向Web服务器发送请求行

   一旦建立了TCP连接，**Web浏览器就会向Web服务器发送请求命令**。例如：GET /sample/hello.jsp HTTP/1.1。

3. Web浏览器发送请求头

   浏览器发送其请求命令之后，还要以头信息的形式向Web服务器发送一些别的信息，**之后浏览器发送了一空白行来通知服务器**，它已经结束了该头信息的发送。

4. Web服务器应答

   客户机向服务器发出请求后，服务器会客户机回送应答， **HTTP/1.1 200 OK ，应答的第一部分是协议的版本号和应答状态码。**

5. Web服务器发送应答头

   正如客户端会随同请求发送关于自身的信息一样，服务器也会随同应答向用户发送关于它自己的数据及被请求的文档。

6. Web服务器向浏览器发送数据

   Web服务器向浏览器发送头信息后，它会发送一个空白行来表示头信息的发送到此为结束，接着，**它就以Content-Type应答头信息所描述的格式发送用户所请求的实际数据**。

7. Web服务器关闭TCP连接

#### 常用HTTP状态码是怎么分类的，有哪些常见的状态码？

- HTTP状态码表示客户端HTTP请求的返回结果、标识服务器处理是否正常、表明请求出现的错误等。
- 状态码的类别：

> | 类别  | 描述                                    |
> | ----- | --------------------------------------- |
> | 1xx： | 指示信息–表示请求已接收，正在处理       |
> | 2xx： | 成功–表示请求已被成功接收、理解、接受   |
> | 3xx： | 重定向–要完成请求必须进行更进一步的操作 |
> | 4xx： | 客户端错误–请求有语法错误或请求无法实现 |
> | 5xx： | 服务器端错误–服务器未能实现合法的请求   |

- 常见的状态码：

> | 状态码 | 描述                                                         |
> | ------ | ------------------------------------------------------------ |
> | 200：  | 请求被正常处理                                               |
> | 204：  | 请求被受理但没有资源可以返回                                 |
> | 206：  | 客户端只是请求资源的一部分，服务器只对请求的部分资源执行GET方法，相应报文中通过Content-Range指定范围的资源。 |
> | 301：  | 永久性重定向                                                 |
> | 302：  | 临时重定向                                                   |
> | 303：  | 与302状态码有相似功能，只是它希望客户端在请求一个URI的时候，能通过GET方法重定向到另一个URI上 |
> | 304：  | 发送附带条件的请求时，条件不满足时返回，与重定向无关         |
> | 307：  | 临时重定向，与302类似，只是强制要求使用POST方法              |
> | 400：  | 请求报文语法有误，服务器无法识别                             |
> | 401：  | 请求需要认证                                                 |
> | 403：  | 请求的对应资源禁止被访问                                     |
> | 404：  | 服务器无法找到对应资源                                       |
> | 500：  | 服务器内部错误                                               |
> | 503：  | 服务器正忙                                                   |

#### Http协议中有那些请求方式

> | 请求方式  | 描述                                                         |
> | --------- | ------------------------------------------------------------ |
> | GET：     | 用于请求访问已经被URI（统一资源标识符）识别的资源，可以通过URL传参给服务器 |
> | POST：    | 用于传输信息给服务器，主要功能与GET方法类似，但一般推荐使用POST方式。 |
> | PUT：     | 传输文件，报文主体中包含文件内容，保存到对应URI位置。        |
> | HEAD：    | 获得报文首部，与GET方法类似，只是不返回报文主体，一般用于验证URI是否有 > 效。 |
> | PATCH：   | 客户端向服务器传送的数据取代指定的文档的内容(部分取代)       |
> | TRACE：   | 回显客户端请求服务器的原始请求报文，用于"回环"诊断           |
> | DELETE：  | 删除文件，与PUT方法相反，删除对应URI位置的文件。             |
> | OPTIONS： | 查询相应URI支持的HTTP方法。                                  |

#### GET方法与POST方法的区别

- **区别一**： get重点在从服务器上获取资源，post重点在向服务器发送数据；
- **区别二**： Get传输的数据量小，因为受URL长度限制，但效率较高； Post可以传输大量数据，所以上传文件时只能用Post方式；
- **区别三**： get是不安全的，因为get请求发送数据是在URL上，是可见的，可能会泄露私密信息，如密码等； post是放在请求头部的，是安全的

#### http版本的对比

- HTTP1.0版本的特性：
  - 早先1.0的HTTP版本，是一种无状态、无连接的应用层协议。
  - HTTP1.0规定浏览器和服务器保持短暂的连接，浏览器的每次请求都需要与服务器建立一个TCP连接，服务器处理完成后立即断开TCP连接（无连接），服务器不跟踪每个客户端也不记录过去的请求（无状态）。
- HTTP1.1版本新特性
  - 默认持久连接节省通信量，只要客户端服务端任意一端没有明确提出断开TCP连接，就一直保持连接，可以发送多次HTTP请求
  - 管线化，客户端可以同时发出多个HTTP请求，而不用一个个等待响应
  - 断点续传原理
- HTTP2.0版本的特性
  - 二进制分帧（采用二进制格式的编码将其封装）
  - 首部压缩（设置了专门的首部压缩设计的HPACK算法。）
  - 流量控制（设置了接收某个数据流的多少字节一些流量控制）
  - 多路复用（可以在共享TCP链接的基础上同时发送请求和响应）
  - 请求优先级（可以通过优化这些帧的交错和传输顺序进一步优化性能）
  - 服务器推送（就是服务器可以对一个客户端请求发送多个响应。服务器向客户端推送资 源无需客户端明确的请求。（重大更新））

#### 什么是对称加密与非对称加密

- 对称密钥加密是指加密和解密使用同一个密钥的方式，这种方式存在的最大问题就是密钥发送问题，即如何安全地将密钥发给对方；
- 而非对称加密是指使用一对非对称密钥，即公钥和私钥，公钥可以随意发布，但私钥只有自己知道。发送密文的一方使用对方的公钥进行加密处理，对方接收到加密信息后，使用自己的私钥进行解密。 由于非对称加密的方式不需要发送用来解密的私钥，所以可以保证安全性；但是和对称加密比起来，非常的慢

#### cookie和session对于HTTP有什么用？

- HTTP协议本身是无法判断用户身份。所以需要cookie或者session

##### 什么是cookie

- cookie是由Web服务器保存在用户浏览器上的文件（key-value格式），可以包含用户相关的信息。客户端向服务器发起请求，就提取浏览器中的用户信息由http发送给服务器

##### 什么是session

- session 是浏览器和服务器会话过程中，服务器会分配的一块储存空间给session。
- 服务器默认为客户浏览器的cookie中设置 sessionid，这个sessionid就和cookie对应，浏览器在向服务器请求过程中传输的cookie 包含 sessionid ，服务器根据传输cookie 中的 sessionid 获取出会话中存储的信息，然后确定会话的身份信息。

##### cookie与session区别

1. cookie数据存放在客户端上，安全性较差，session数据放在服务器上，安全性相对更高
2. 单个cookie保存的数据不能超过4K，session无此限制 信息后，使用自己的私钥进行解密。 由于非对称加密的方式不需要发送用来解密的私钥，所以可以保证安全性；但是和对称加密比起来，非常的慢

#### cookie和session对于HTTP有什么用？

- HTTP协议本身是无法判断用户身份。所以需要cookie或者session

##### 什么是cookie

- cookie是由Web服务器保存在用户浏览器上的文件（key-value格式），可以包含用户相关的信息。客户端向服务器发起请求，就提取浏览器中的用户信息由http发送给服务器

##### 什么是session

- session 是浏览器和服务器会话过程中，服务器会分配的一块储存空间给session。
- 服务器默认为客户浏览器的cookie中设置 sessionid，这个sessionid就和cookie对应，浏览器在向服务器请求过程中传输的cookie 包含 sessionid ，服务器根据传输cookie 中的 sessionid 获取出会话中存储的信息，然后确定会话的身份信息。

##### cookie与session区别

1. cookie数据存放在客户端上，安全性较差，session数据放在服务器上，安全性相对更高
2. 单个cookie保存的数据不能超过4K，session无此限制
3. session一定时间内保存在服务器上，当访问增多，占用服务器性能，考虑到服务器性能方面，应当使用cookie。





---

目录

- [什么是Nginx？](https://juejin.cn/post/6844904125784653837#heading-0)
- [为什么要用Nginx？](https://juejin.cn/post/6844904125784653837#heading-1)
- [为什么Nginx性能这么高？](https://juejin.cn/post/6844904125784653837#heading-2)
- [Nginx怎么处理请求的？](https://juejin.cn/post/6844904125784653837#heading-3)
- [什么是正向代理和反向代理？](https://juejin.cn/post/6844904125784653837#heading-4)
- [使用“反向代理服务器的优点是什么?](https://juejin.cn/post/6844904125784653837#heading-5)
- [Nginx的优缺点？](https://juejin.cn/post/6844904125784653837#heading-6)
- [Nginx应用场景？](https://juejin.cn/post/6844904125784653837#heading-7)
- [Nginx目录结构有哪些？](https://juejin.cn/post/6844904125784653837#heading-8)
- [Nginx配置文件nginx.conf有哪些属性模块?](https://juejin.cn/post/6844904125784653837#heading-9)
- [Nginx静态资源?](https://juejin.cn/post/6844904125784653837#heading-10)
- [如何用Nginx解决前端跨域问题？](https://juejin.cn/post/6844904125784653837#heading-11)
- [Nginx虚拟主机怎么配置?](https://juejin.cn/post/6844904125784653837#heading-12)
  - [基于虚拟主机配置域名](https://juejin.cn/post/6844904125784653837#heading-13)
  - [基于端口的虚拟主机](https://juejin.cn/post/6844904125784653837#heading-14)
- [location的作用是什么？](https://juejin.cn/post/6844904125784653837#heading-15)
  - [location的语法能说出来吗？](https://juejin.cn/post/6844904125784653837#heading-16)
  - [Location正则案例](https://juejin.cn/post/6844904125784653837#heading-17)
- [限流怎么做的？](https://juejin.cn/post/6844904125784653837#heading-18)
  - [1、正常限制访问频率（正常流量）：](https://juejin.cn/post/6844904125784653837#heading-19)
  - [2、突发限制访问频率（突发流量）：](https://juejin.cn/post/6844904125784653837#heading-20)
  - [3、 限制并发连接数](https://juejin.cn/post/6844904125784653837#heading-21)
- [漏桶流算法和令牌桶算法知道？](https://juejin.cn/post/6844904125784653837#heading-22)
  - [漏桶算法](https://juejin.cn/post/6844904125784653837#heading-23)
  - [令牌桶算法](https://juejin.cn/post/6844904125784653837#heading-24)
- [为什么要做动静分离？](https://juejin.cn/post/6844904125784653837#heading-25)
- [Nginx怎么做的动静分离？](https://juejin.cn/post/6844904125784653837#heading-26)
- [Nginx负载均衡的算法怎么实现的?策略有哪些?](https://juejin.cn/post/6844904125784653837#heading-27)
  - [1 轮询(默认)](https://juejin.cn/post/6844904125784653837#heading-28)
  - [2 权重 weight](https://juejin.cn/post/6844904125784653837#heading-29)
  - [3 ip_hash( IP绑定)](https://juejin.cn/post/6844904125784653837#heading-30)
  - [4 fair(第三方插件)](https://juejin.cn/post/6844904125784653837#heading-31)
  - [5、url_hash(第三方插件)](https://juejin.cn/post/6844904125784653837#heading-32)
- [Nginx配置高可用性怎么配置？](https://juejin.cn/post/6844904125784653837#heading-33)
- [Nginx怎么判断别IP不可访问？](https://juejin.cn/post/6844904125784653837#heading-34)
- [怎么限制浏览器访问？](https://juejin.cn/post/6844904125784653837#heading-35)
- [Rewrite全局变量是什么？](https://juejin.cn/post/6844904125784653837#heading-36)



### 什么是Nginx？

- Nginx是一个 轻量级/高性能的反向代理Web服务器，他实现非常高效的反向代理、负载平衡，他可以处理2-3万并发连接数，官方监测能支持5万并发，现在中国使用nginx网站用户有很多，例如：新浪、网易、 腾讯等。

### 为什么要用Nginx？

- 跨平台、配置简单、方向代理、高并发连接：处理2-3万并发连接数，官方监测能支持5万并发，内存消耗小：开启10个nginx才占150M内存 ，nginx处理静态文件好，耗费内存少，
- 而且Nginx内置的健康检查功能：如果有一个服务器宕机，会做一个健康检查，再发送的请求就不会发送到宕机的服务器了。重新将请求提交到其他的节点上。
- 使用Nginx的话还能：
  1. 节省宽带：支持GZIP压缩，可以添加浏览器本地缓存
  2. 稳定性高：宕机的概率非常小
  3. 接收用户请求是异步的

### 为什么Nginx性能这么高？

- 因为他的事件处理机制：异步非阻塞事件处理机制：运用了epoll模型，提供了一个队列，排队解决

### Nginx怎么处理请求的？

- nginx接收一个请求后，首先由listen和server_name指令匹配server模块，再匹配server模块里的location，location就是实际地址

```axapta
axapta复制代码    server {            		    	# 第一个Server区块开始，表示一个独立的虚拟主机站点
        listen       80；      		        # 提供服务的端口，默认80
        server_name  localhost；    		# 提供服务的域名主机名
        location / {            	        # 第一个location区块开始
            root   html；       		# 站点的根目录，相当于Nginx的安装目录
            index  index.html index.htm；    	# 默认的首页文件，多个用空格分开
        }          				# 第一个location区块结果
    }           
```

### 什么是正向代理和反向代理？

1. 正向代理就是一个人发送一个请求直接就到达了目标的服务器
2. 反方代理就是请求统一被Nginx接收，nginx反向代理服务器接收到之后，按照一定的规 则分发给了后端的业务处理服务器进行处理了

### 使用“反向代理服务器的优点是什么?

- 反向代理服务器可以隐藏源服务器的存在和特征。它充当互联网云和web服务器之间的中间层。这对于安全方面来说是很好的，特别是当您使用web托管服务时。

### Nginx的优缺点？

- 优点：
  1. 占内存小，可实现高并发连接，处理响应快
  2. 可实现http服务器、虚拟主机、方向代理、负载均衡
  3. Nginx配置简单
  4. 可以不暴露正式的服务器IP地址
- 缺点： 动态处理差：nginx处理静态文件好,耗费内存少，但是处理动态页面则很鸡肋，现在一般前端用nginx作为反向代理抗住压力，

### Nginx应用场景？

1. http服务器。Nginx是一个http服务可以独立提供http服务。可以做网页静态服务器。
2. 虚拟主机。可以实现在一台服务器虚拟出多个网站，例如个人网站使用的虚拟机。
3. 反向代理，负载均衡。当网站的访问量达到一定程度后，单台服务器不能满足用户的请求时，需要用多台服务器集群可以使用nginx做反向代理。并且多台服务器可以平均分担负载，不会应为某台服务器负载高宕机而某台服务器闲置的情况。
4. nginz 中也可以配置安全管理、比如可以使用Nginx搭建API接口网关,对每个接口服务进行拦截。

### Nginx目录结构有哪些？

```autoit
autoit复制代码[root@localhost ~]# tree /usr/local/nginx
/usr/local/nginx
├── client_body_temp
├── conf                             # Nginx所有配置文件的目录
│   ├── fastcgi.conf                 # fastcgi相关参数的配置文件
│   ├── fastcgi.conf.default         # fastcgi.conf的原始备份文件
│   ├── fastcgi_params               # fastcgi的参数文件
│   ├── fastcgi_params.default       
│   ├── koi-utf
│   ├── koi-win
│   ├── mime.types                   # 媒体类型
│   ├── mime.types.default
│   ├── nginx.conf                   # Nginx主配置文件
│   ├── nginx.conf.default
│   ├── scgi_params                  # scgi相关参数文件
│   ├── scgi_params.default  
│   ├── uwsgi_params                 # uwsgi相关参数文件
│   ├── uwsgi_params.default
│   └── win-utf
├── fastcgi_temp                     # fastcgi临时数据目录
├── html                             # Nginx默认站点目录
│   ├── 50x.html                     # 错误页面优雅替代显示文件，例如当出现502错误时会调用此页面
│   └── index.html                   # 默认的首页文件
├── logs                             # Nginx日志目录
│   ├── access.log                   # 访问日志文件
│   ├── error.log                    # 错误日志文件
│   └── nginx.pid                    # pid文件，Nginx进程启动后，会把所有进程的ID号写到此文件
├── proxy_temp                       # 临时目录
├── sbin                             # Nginx命令目录
│   └── nginx                        # Nginx的启动命令
├── scgi_temp                        # 临时目录
└── uwsgi_temp                       # 临时目录
```

### Nginx配置文件nginx.conf有哪些属性模块?

```nginx
nginx复制代码worker_processes  1；                			# worker进程的数量
events {                              			# 事件区块开始
    worker_connections  1024；          		# 每个worker进程支持的最大连接数
}                               			# 事件区块结束
http {                           			# HTTP区块开始
    include       mime.types；         			# Nginx支持的媒体类型库文件
    default_type  application/octet-stream；            # 默认的媒体类型
    sendfile        on；       				# 开启高效传输模式
    keepalive_timeout  65；       			# 连接超时
    server {            		                # 第一个Server区块开始，表示一个独立的虚拟主机站点
        listen       80；      			        # 提供服务的端口，默认80
        server_name  localhost；    			# 提供服务的域名主机名
        location / {            	        	# 第一个location区块开始
            root   html；       			# 站点的根目录，相当于Nginx的安装目录
            index  index.html index.htm；       	# 默认的首页文件，多个用空格分开
        }          				        # 第一个location区块结果
        error_page   500502503504  /50x.html；          # 出现对应的http状态码时，使用50x.html回应客户
        location = /50x.html {          	        # location区块开始，访问50x.html
            root   html；      		      	        # 指定对应的站点目录为html
        }
    }  
    ......
```

### Nginx静态资源?

- 静态资源访问，就是存放在nginx的html页面，我们可以自己编写

### 如何用Nginx解决前端跨域问题？

- 使用Nginx转发请求。把跨域的接口写成调本域的接口，然后将这些接口转发到真正的请求地址。

### Nginx虚拟主机怎么配置?

- 1、基于域名的虚拟主机，通过域名来区分虚拟主机——应用：外部网站
- 2、基于端口的虚拟主机，通过端口来区分虚拟主机——应用：公司内部网站，外部网站的管理后台
- 3、基于ip的虚拟主机。

#### 基于虚拟主机配置域名

- 需要建立/data/www /data/bbs目录，windows本地hosts添加虚拟机ip地址对应的域名解析；对应域名网站目录下新增index.html文件；

```nginx
nginx复制代码    #当客户端访问www.lijie.com,监听端口号为80,直接跳转到data/www目录下文件
    server {
        listen       80;
        server_name  www.lijie.com;
        location / {
            root   data/www;
            index  index.html index.htm;
        }
    }

    #当客户端访问www.lijie.com,监听端口号为80,直接跳转到data/bbs目录下文件
    server {
        listen       80;
        server_name  bbs.lijie.com;
        location / {
            root   data/bbs;
            index  index.html index.htm;
        }
    }
```

#### 基于端口的虚拟主机

- 使用端口来区分，浏览器使用域名或ip地址:端口号 访问

```nginx
nginx复制代码    #当客户端访问www.lijie.com,监听端口号为8080,直接跳转到data/www目录下文件
    server {
        listen       8080;
        server_name  8080.lijie.com;
        location / {
            root   data/www;
            index  index.html index.htm;
        }
    }
	
    #当客户端访问www.lijie.com,监听端口号为80直接跳转到真实ip服务器地址 127.0.0.1:8080
    server {
        listen       80;
        server_name  www.lijie.com;
        location / {
		proxy_pass http://127.0.0.1:8080;
                index  index.html index.htm;
        }
    }
```

### location的作用是什么？

- location指令的作用是根据用户请求的URI来执行不同的应用，也就是根据用户请求的网站URL进行匹配，匹配成功即进行相关的操作。

#### location的语法能说出来吗？

> 注意：~ 代表自己输入的英文字母
>
> | 匹配符 | 匹配规则                     | 优先级 |
> | ------ | ---------------------------- | ------ |
> | =      | 精确匹配                     | 1      |
> | ^~     | 以某个字符串开头             | 2      |
> | ~      | 区分大小写的正则匹配         | 3      |
> | ~*     | 不区分大小写的正则匹配       | 4      |
> | !~     | 区分大小写不匹配的正则       | 5      |
> | !~*    | 不区分大小写不匹配的正则     | 6      |
> | /      | 通用匹配，任何请求都会匹配到 | 7      |

#### Location正则案例

- 示例：

```crmsh
crmsh复制代码    #优先级1,精确匹配，根路径
    location =/ {
        return 400;
    }

    #优先级2,以某个字符串开头,以av开头的，优先匹配这里，区分大小写
    location ^~ /av {
       root /data/av/;
    }

    #优先级3，区分大小写的正则匹配，匹配/media*****路径
    location ~ /media {
          alias /data/static/;
    }

    #优先级4 ，不区分大小写的正则匹配，所有的****.jpg|gif|png 都走这里
    location ~* .*\.(jpg|gif|png|js|css)$ {
       root  /data/av/;
    }

    #优先7，通用匹配
    location / {
        return 403;
    }
```

### 限流怎么做的？

- Nginx限流就是限制用户请求速度，防止服务器受不了
- 限流有3种
  1. 正常限制访问频率（正常流量）
  2. 突发限制访问频率（突发流量）
  3. 限制并发连接数
- Nginx的限流都是基于漏桶流算法，底下会说道什么是桶铜流

**实现三种限流算法**

##### 1、正常限制访问频率（正常流量）：

- 限制一个用户发送的请求，我Nginx多久接收一个请求。
- Nginx中使用ngx_http_limit_req_module模块来限制的访问频率，限制的原理实质是基于漏桶算法原理来实现的。在nginx.conf配置文件中可以使用limit_req_zone命令及limit_req命令限制单个IP的请求处理频率。

```bash
bash复制代码	#定义限流维度，一个用户一分钟一个请求进来，多余的全部漏掉
	limit_req_zone $binary_remote_addr zone=one:10m rate=1r/m;

	#绑定限流维度
	server{
		
		location/seckill.html{
			limit_req zone=zone;	
			proxy_pass http://lj_seckill;
		}

	}
```

- 1r/s代表1秒一个请求，1r/m一分钟接收一个请求， 如果Nginx这时还有别人的请求没有处理完，Nginx就会拒绝处理该用户请求。

##### 2、突发限制访问频率（突发流量）：

- 限制一个用户发送的请求，我Nginx多久接收一个。
- 上面的配置一定程度可以限制访问频率，但是也存在着一个问题：如果突发流量超出请求被拒绝处理，无法处理活动时候的突发流量，这时候应该如何进一步处理呢？Nginx提供burst参数结合nodelay参数可以解决流量突发的问题，可以设置能处理的超过设置的请求数外能额外处理的请求数。我们可以将之前的例子添加burst参数以及nodelay参数：

```bash
bash复制代码	#定义限流维度，一个用户一分钟一个请求进来，多余的全部漏掉
	limit_req_zone $binary_remote_addr zone=one:10m rate=1r/m;

	#绑定限流维度
	server{
		
		location/seckill.html{
			limit_req zone=zone burst=5 nodelay;
			proxy_pass http://lj_seckill;
		}

	}
```

- 为什么就多了一个 burst=5 nodelay; 呢，多了这个可以代表Nginx对于一个用户的请求会立即处理前五个，多余的就慢慢来落，没有其他用户的请求我就处理你的，有其他的请求的话我Nginx就漏掉不接受你的请求

##### 3、 限制并发连接数

- Nginx中的ngx_http_limit_conn_module模块提供了限制并发连接数的功能，可以使用limit_conn_zone指令以及limit_conn执行进行配置。接下来我们可以通过一个简单的例子来看下：

```nginx
nginx复制代码    http {
	limit_conn_zone $binary_remote_addr zone=myip:10m;
	limit_conn_zone $server_name zone=myServerName:10m;
    }

    server {
        location / {
            limit_conn myip 10;
            limit_conn myServerName 100;
            rewrite / http://www.lijie.net permanent;
        }
    }
```

- 上面配置了单个IP同时并发连接数最多只能10个连接，并且设置了整个虚拟服务器同时最大并发数最多只能100个链接。当然，只有当请求的header被服务器处理后，虚拟服务器的连接数才会计数。刚才有提到过Nginx是基于漏桶算法原理实现的，实际上限流一般都是基于漏桶算法和令牌桶算法实现的。接下来我们来看看两个算法的介绍：

### 漏桶流算法和令牌桶算法知道？

#### 漏桶算法

- 漏桶算法是网络世界中流量整形或速率限制时经常使用的一种算法，它的主要目的是控制数据注入到网络的速率，平滑网络上的突发流量。漏桶算法提供了一种机制，通过它，突发流量可以被整形以便为网络提供一个稳定的流量。也就是我们刚才所讲的情况。漏桶算法提供的机制实际上就是刚才的案例：

  突发流量会进入到一个漏桶，漏桶会按照我们定义的速率依次处理请求，如果水流过大也就是突发流量过大就会直接溢出，则多余的请求会被拒绝。所以漏桶算法能控制数据的传输速率。

  ![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/17172646dbb8b696~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

#### 令牌桶算法

- 令牌桶算法是网络流量整形和速率限制中最常使用的一种算法。典型情况下，令牌桶算法用来控制发送到网络上的数据的数目，并允许突发数据的发送。Google开源项目Guava中的RateLimiter使用的就是令牌桶控制算法。**令牌桶算法的机制如下：存在一个大小固定的令牌桶，会以恒定的速率源源不断产生令牌。如果令牌消耗速率小于生产令牌的速度，令牌就会一直产生直至装满整个令牌桶。**



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/17172646dbc20c88~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



### 为什么要做动静分离？

- Nginx是当下最热的Web容器，网站优化的重要点在于静态化网站，网站静态化的关键点则是是动静分离，动静分离是让动态网站里的动态网页根据一定规则把不变的资源和经常变的资源区分开来，动静资源做好了拆分以后，我们则根据静态资源的特点将其做缓存操作。
- 让静态的资源只走静态资源服务器，动态的走动态的服务器
- Nginx的静态处理能力很强，但是动态处理能力不足，因此，在企业中常用动静分离技术。
- 对于静态资源比如图片，js，css等文件，我们则在反向代理服务器nginx中进行缓存。这样浏览器在请求一个静态资源时，代理服务器nginx就可以直接处理，无需将请求转发给后端服务器tomcat。 若用户请求的动态文件，比如servlet,jsp则转发给Tomcat服务器处理，从而实现动静分离。这也是反向代理服务器的一个重要的作用。

### Nginx怎么做的动静分离？

- 只需要指定路径对应的目录。location/可以使用正则表达式匹配。并指定对应的硬盘中的目录。如下：（操作都是在Linux上）

```awk
awk复制代码        location /image/ {
            root   /usr/local/static/;
            autoindex on;
        }
```

1. 创建目录

   ```awk
   awk
   复制代码mkdir /usr/local/static/image
   ```

2. 进入目录

   ```awk
   awk
   复制代码cd  /usr/local/static/image
   ```

3. 放一张照片上去#

   ```mipsasm
   mipsasm
   复制代码1.jpg
   ```

4. 重启 nginx

   ```ebnf
   ebnf
   复制代码sudo nginx -s reload
   ```

5. 打开浏览器 输入 server_name/image/1.jpg 就可以访问该静态图片了

### Nginx负载均衡的算法怎么实现的?策略有哪些?

- 为了避免服务器崩溃，大家会通过负载均衡的方式来分担服务器压力。将对台服务器组成一个集群，当用户访问时，先访问到一个转发服务器，再由转发服务器将访问分发到压力更小的服务器。
- Nginx负载均衡实现的策略有以下五种：

#### 1 轮询(默认)

- 每个请求按时间顺序逐一分配到不同的后端服务器，如果后端某个服务器宕机，能自动剔除故障系统。

```nginx
nginx复制代码upstream backserver { 
 server 192.168.0.12; 
 server 192.168.0.13; 
} 
```

#### 2 权重 weight

- weight的值越大分配
- 到的访问概率越高，主要用于后端每台服务器性能不均衡的情况下。其次是为在主从的情况下设置不同的权值，达到合理有效的地利用主机资源。

```routeros
routeros复制代码upstream backserver { 
 server 192.168.0.12 weight=2; 
 server 192.168.0.13 weight=8; 
} 
```

- 权重越高，在被访问的概率越大，如上例，分别是20%，80%。

#### 3 ip_hash( IP绑定)

- 每个请求按访问IP的哈希结果分配，使来自同一个IP的访客固定访问一台后端服务器，`并且可以有效解决动态网页存在的session共享问题`

```roboconf
roboconf复制代码upstream backserver { 
 ip_hash; 
 server 192.168.0.12:88; 
 server 192.168.0.13:80; 
} 
```

#### 4 fair(第三方插件)

- 必须安装upstream_fair模块。
- 对比 weight、ip_hash更加智能的负载均衡算法，fair算法可以根据页面大小和加载时间长短智能地进行负载均衡，响应时间短的优先分配。

```abnf
abnf复制代码upstream backserver { 
 server server1; 
 server server2; 
 fair; 
} 
```

- 哪个服务器的响应速度快，就将请求分配到那个服务器上。

#### 5、url_hash(第三方插件)

- 必须安装Nginx的hash软件包
- 按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，可以进一步提高后端缓存服务器的效率。

```nginx
nginx复制代码upstream backserver { 
 server squid1:3128; 
 server squid2:3128; 
 hash $request_uri; 
 hash_method crc32; 
} 
```

### Nginx配置高可用性怎么配置？

- 当上游服务器(真实访问服务器)，一旦出现故障或者是没有及时相应的话，应该直接轮训到下一台服务器，保证服务器的高可用
- Nginx配置代码：

```clean
clean复制代码server {
        listen       80;
        server_name  www.lijie.com;
        location / {
		    ### 指定上游服务器负载均衡服务器
		    proxy_pass http://backServer;
			###nginx与上游服务器(真实访问的服务器)超时时间 后端服务器连接的超时时间_发起握手等候响应超时时间
			proxy_connect_timeout 1s;
			###nginx发送给上游服务器(真实访问的服务器)超时时间
            proxy_send_timeout 1s;
			### nginx接受上游服务器(真实访问的服务器)超时时间
            proxy_read_timeout 1s;
            index  index.html index.htm;
        }
    }
```

### Nginx怎么判断别IP不可访问？

```nginx
nginx复制代码# 如果访问的ip地址为192.168.9.115,则返回403
if  ($remote_addr = 192.168.9.115) {  
     return 403;  
}  
```

### 怎么限制浏览器访问？

```nginx
nginx复制代码## 不允许谷歌浏览器访问 如果是谷歌浏览器返回500
if ($http_user_agent ~ Chrome) {   
    return 500;  
}
```

### Rewrite全局变量是什么？

> | 变量              | 含义                                                         |
> | ----------------- | ------------------------------------------------------------ |
> | $args             | 这个变量等于请求行中的参数，同$query_string                  |
> | $content length   | 请求头中的Content-length字段。                               |
> | $content_type     | 请求头中的Content-Type字段。                                 |
> | $document_root    | 当前请求在root指令中指定的值。                               |
> | $host             | 请求主机头字段，否则为服务器名称。                           |
> | $http_user_agent  | 客户端agent信息                                              |
> | $http_cookie      | 客户端cookie信息                                             |
> | $limit_rate       | 这个变量可以限制连接速率。                                   |
> | $request_method   | 客户端请求的动作，通常为GET或POST。                          |
> | $remote_addr      | 客户端的IP地址。                                             |
> | $remote_port      | 客户端的端口。                                               |
> | $remote_user      | 已经经过Auth Basic Module验证的用户名。                      |
> | $request_filename | 当前请求的文件路径，由root或alias指令与URI请求生成。         |
> | $scheme           | HTTP方法（如http，https）。                                  |
> | $server_protocol  | 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。                   |
> | $server_addr      | 服务器地址，在完成一次系统调用后可以确定这个值。             |
> | $server_name      | 服务器名称。                                                 |
> | $server_port      | 请求到达服务器的端口号。                                     |
> | $request_uri      | 包含请求参数的原始URI，不包含主机名，如”/foo/bar.php?arg=baz”。 |
> | $uri              | 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。 |
> | $document_uri     | 与$uri相同。                                                 |







---

- 

- 目录

- - [数据库基础知识](https://juejin.cn/post/6844904127047139335#heading-0)
    - [为什么要使用数据库](https://juejin.cn/post/6844904127047139335#heading-1)
    - [什么是SQL？](https://juejin.cn/post/6844904127047139335#heading-2)
    - [什么是MySQL?](https://juejin.cn/post/6844904127047139335#heading-3)
    - [MySql, Oracle，Sql Service的区别](https://juejin.cn/post/6844904127047139335#heading-4)
    - [数据库三大范式是什么](https://juejin.cn/post/6844904127047139335#heading-5)
    - [mysql有关权限的表都有哪几个](https://juejin.cn/post/6844904127047139335#heading-6)
    - [MySQL的binlog有有几种录入格式？分别有什么区别？](https://juejin.cn/post/6844904127047139335#heading-7)
    - [数据库经常使用的函数](https://juejin.cn/post/6844904127047139335#heading-8)
  - [数据类型](https://juejin.cn/post/6844904127047139335#heading-9)
    - [mysql有哪些数据类型](https://juejin.cn/post/6844904127047139335#heading-10)
  - [引擎](https://juejin.cn/post/6844904127047139335#heading-11)
    - [MySQL存储引擎MyISAM与InnoDB区别](https://juejin.cn/post/6844904127047139335#heading-12)
    - [MyISAM索引与InnoDB索引的区别？](https://juejin.cn/post/6844904127047139335#heading-13)
    - [InnoDB引擎的4大特性](https://juejin.cn/post/6844904127047139335#heading-14)
    - [存储引擎选择](https://juejin.cn/post/6844904127047139335#heading-15)
  - [索引](https://juejin.cn/post/6844904127047139335#heading-16)
    - [什么是索引？](https://juejin.cn/post/6844904127047139335#heading-17)
    - [索引有哪些优缺点？](https://juejin.cn/post/6844904127047139335#heading-18)
    - [怎么创建索引的，有什么好处，有哪些分类](https://juejin.cn/post/6844904127047139335#heading-19)
    - [简述有哪些索引和作用](https://juejin.cn/post/6844904127047139335#heading-20)
    - [索引使用场景](https://juejin.cn/post/6844904127047139335#heading-21)
    - [主键索引与唯一索引的区别](https://juejin.cn/post/6844904127047139335#heading-22)
    - [索引有哪几种类型？](https://juejin.cn/post/6844904127047139335#heading-23)
    - [索引的数据结构（b树，hash）](https://juejin.cn/post/6844904127047139335#heading-24)
    - [索引的基本原理](https://juejin.cn/post/6844904127047139335#heading-25)
    - [索引算法有哪些？](https://juejin.cn/post/6844904127047139335#heading-26)
    - [索引设计的原则？](https://juejin.cn/post/6844904127047139335#heading-27)
    - [创建索引的原则](https://juejin.cn/post/6844904127047139335#heading-28)
    - [创建索引的三种方式](https://juejin.cn/post/6844904127047139335#heading-29)
    - [如何删除索引](https://juejin.cn/post/6844904127047139335#heading-30)
    - [创建索引时需要注意什么？](https://juejin.cn/post/6844904127047139335#heading-31)
    - [使用索引查询一定能提高查询的性能吗？为什么](https://juejin.cn/post/6844904127047139335#heading-32)
    - [百万级别或以上的数据如何删除](https://juejin.cn/post/6844904127047139335#heading-33)
    - [前缀索引](https://juejin.cn/post/6844904127047139335#heading-34)
    - [什么是最左前缀原则？什么是最左匹配原则](https://juejin.cn/post/6844904127047139335#heading-35)
    - [B树和B+树的区别](https://juejin.cn/post/6844904127047139335#heading-36)
    - [使用B树的好处](https://juejin.cn/post/6844904127047139335#heading-37)
    - [使用B+树的好处](https://juejin.cn/post/6844904127047139335#heading-38)
    - [Hash索引和B+树所有有什么区别或者说优劣呢?](https://juejin.cn/post/6844904127047139335#heading-39)
    - [数据库为什么使用B+树而不是B树](https://juejin.cn/post/6844904127047139335#heading-40)
    - [B+树在满足聚簇索引和覆盖索引的时候不需要回表查询数据，](https://juejin.cn/post/6844904127047139335#heading-41)
    - [什么是聚簇索引？何时使用聚簇索引与非聚簇索引](https://juejin.cn/post/6844904127047139335#heading-42)
    - [非聚簇索引一定会回表查询吗？](https://juejin.cn/post/6844904127047139335#heading-43)
    - [联合索引是什么？为什么需要注意联合索引中的顺序？](https://juejin.cn/post/6844904127047139335#heading-44)
  - [事务](https://juejin.cn/post/6844904127047139335#heading-45)
    - [什么是数据库事务？](https://juejin.cn/post/6844904127047139335#heading-46)
    - [事物的四大特性(ACID)介绍一下?](https://juejin.cn/post/6844904127047139335#heading-47)
    - [什么是脏读？幻读？不可重复读？](https://juejin.cn/post/6844904127047139335#heading-48)
    - [什么是事务的隔离级别？MySQL的默认隔离级别是什么？](https://juejin.cn/post/6844904127047139335#heading-49)
  - [锁](https://juejin.cn/post/6844904127047139335#heading-50)
    - [对MySQL的锁了解吗](https://juejin.cn/post/6844904127047139335#heading-51)
    - [从锁的类别上分MySQL都有哪些锁呢？](https://juejin.cn/post/6844904127047139335#heading-52)
    - [隔离级别与锁的关系](https://juejin.cn/post/6844904127047139335#heading-53)
    - [按照锁的粒度分数据库锁有哪些？锁机制与InnoDB锁算法](https://juejin.cn/post/6844904127047139335#heading-54)
    - [MySQL中InnoDB引擎的行锁是怎么实现的？](https://juejin.cn/post/6844904127047139335#heading-55)
    - [InnoDB存储引擎的锁的算法有三种](https://juejin.cn/post/6844904127047139335#heading-56)
    - [什么是死锁？怎么解决？](https://juejin.cn/post/6844904127047139335#heading-57)
    - [数据库的乐观锁和悲观锁是什么？怎么实现的？](https://juejin.cn/post/6844904127047139335#heading-58)
  - [视图](https://juejin.cn/post/6844904127047139335#heading-59)
    - [为什么要使用视图？什么是视图？](https://juejin.cn/post/6844904127047139335#heading-60)
    - [视图有哪些特点？](https://juejin.cn/post/6844904127047139335#heading-61)
    - [视图的使用场景有哪些？](https://juejin.cn/post/6844904127047139335#heading-62)
    - [视图的优点](https://juejin.cn/post/6844904127047139335#heading-63)
    - [视图的缺点](https://juejin.cn/post/6844904127047139335#heading-64)
    - [什么是游标？](https://juejin.cn/post/6844904127047139335#heading-65)
  - [存储过程与函数](https://juejin.cn/post/6844904127047139335#heading-66)
    - [什么是存储过程？有哪些优缺点？](https://juejin.cn/post/6844904127047139335#heading-67)
  - [触发器](https://juejin.cn/post/6844904127047139335#heading-68)
    - [什么是触发器？触发器的使用场景有哪些？](https://juejin.cn/post/6844904127047139335#heading-69)
    - [MySQL中都有哪些触发器？](https://juejin.cn/post/6844904127047139335#heading-70)
  - [常用SQL语句](https://juejin.cn/post/6844904127047139335#heading-71)
    - [SQL语句主要分为哪几类](https://juejin.cn/post/6844904127047139335#heading-72)
    - [SQL语句的语法顺序：](https://juejin.cn/post/6844904127047139335#heading-73)
    - [超键、候选键、主键、外键分别是什么？](https://juejin.cn/post/6844904127047139335#heading-74)
    - [SQL 约束有哪几种？](https://juejin.cn/post/6844904127047139335#heading-75)
    - [六种关联查询](https://juejin.cn/post/6844904127047139335#heading-76)
      - [表连接面试题](https://juejin.cn/post/6844904127047139335#heading-77)
    - [什么是子查询](https://juejin.cn/post/6844904127047139335#heading-84)
    - [mysql中 in 和 exists 区别](https://juejin.cn/post/6844904127047139335#heading-85)
    - [varchar与char的区别](https://juejin.cn/post/6844904127047139335#heading-86)
    - [varchar(50)中50的涵义](https://juejin.cn/post/6844904127047139335#heading-87)
    - [int(20)中20的涵义](https://juejin.cn/post/6844904127047139335#heading-88)
    - [mysql为什么这么设计](https://juejin.cn/post/6844904127047139335#heading-89)
    - [mysql中int(10)和char(10)以及varchar(10)的区别](https://juejin.cn/post/6844904127047139335#heading-90)
    - [FLOAT和DOUBLE的区别是什么？](https://juejin.cn/post/6844904127047139335#heading-91)
    - [drop、delete与truncate的区别](https://juejin.cn/post/6844904127047139335#heading-92)
    - [UNION与UNION ALL的区别？](https://juejin.cn/post/6844904127047139335#heading-93)
  - [SQL优化](https://juejin.cn/post/6844904127047139335#heading-94)
    - [说出一些数据库优化方面的经验?](https://juejin.cn/post/6844904127047139335#heading-95)
    - [怎么优化SQL查询语句吗](https://juejin.cn/post/6844904127047139335#heading-96)
    - [你怎么知道SQL语句性能是高还是低](https://juejin.cn/post/6844904127047139335#heading-97)
    - [SQL的执行顺序](https://juejin.cn/post/6844904127047139335#heading-98)
    - [如何定位及优化SQL语句的性能问题？创建的索引有没有被使用到?或者说怎么才可以知道这条语句运行很慢的原因？](https://juejin.cn/post/6844904127047139335#heading-99)
    - [SQL的生命周期？](https://juejin.cn/post/6844904127047139335#heading-100)
    - [大表数据查询，怎么优化](https://juejin.cn/post/6844904127047139335#heading-101)
    - [超大分页怎么处理？](https://juejin.cn/post/6844904127047139335#heading-102)
    - [mysql 分页](https://juejin.cn/post/6844904127047139335#heading-103)
    - [慢查询日志](https://juejin.cn/post/6844904127047139335#heading-104)
    - [关心过业务系统里面的sql耗时吗？统计过慢查询吗？对慢查询都怎么优化过？](https://juejin.cn/post/6844904127047139335#heading-105)
    - [为什么要尽量设定一个主键？](https://juejin.cn/post/6844904127047139335#heading-106)
    - [主键使用自增ID还是UUID？](https://juejin.cn/post/6844904127047139335#heading-107)
    - [字段为什么要求定义为not null？](https://juejin.cn/post/6844904127047139335#heading-108)
    - [如果要存储用户的密码散列，应该使用什么字段进行存储？](https://juejin.cn/post/6844904127047139335#heading-109)
    - [如何优化查询过程中的数据访问](https://juejin.cn/post/6844904127047139335#heading-110)
    - [如何优化长难的查询语句](https://juejin.cn/post/6844904127047139335#heading-111)
    - [优化特定类型的查询语句](https://juejin.cn/post/6844904127047139335#heading-112)
    - [优化关联查询](https://juejin.cn/post/6844904127047139335#heading-113)
    - [优化子查询](https://juejin.cn/post/6844904127047139335#heading-114)
    - [优化LIMIT分页](https://juejin.cn/post/6844904127047139335#heading-115)
    - [优化UNION查询](https://juejin.cn/post/6844904127047139335#heading-116)
    - [优化WHERE子句](https://juejin.cn/post/6844904127047139335#heading-117)
    - [SQL语句优化的一些方法](https://juejin.cn/post/6844904127047139335#heading-118)
  - [数据库优化](https://juejin.cn/post/6844904127047139335#heading-119)
    - [为什么要优化](https://juejin.cn/post/6844904127047139335#heading-120)
    - [数据库结构优化](https://juejin.cn/post/6844904127047139335#heading-121)
    - [MySQL数据库cpu飙升到500%的话他怎么处理？](https://juejin.cn/post/6844904127047139335#heading-122)
    - [大表怎么优化？分库分表了是怎么做的？分表分库了有什么问题？有用到中间件么？他们的原理知道么？](https://juejin.cn/post/6844904127047139335#heading-123)
      - [1、垂直分区](https://juejin.cn/post/6844904127047139335#heading-124)
      - [2、垂直分表](https://juejin.cn/post/6844904127047139335#heading-125)
      - [3、水平分区](https://juejin.cn/post/6844904127047139335#heading-126)
      - [4、水平分表：](https://juejin.cn/post/6844904127047139335#heading-127)
      - [数据库分片的两种常见方案：](https://juejin.cn/post/6844904127047139335#heading-128)
      - [分库分表后面临的问题](https://juejin.cn/post/6844904127047139335#heading-129)
    - [MySQL的复制原理以及流程](https://juejin.cn/post/6844904127047139335#heading-130)
      - [主从复制的作用](https://juejin.cn/post/6844904127047139335#heading-131)
      - [MySQL主从复制解决的问题](https://juejin.cn/post/6844904127047139335#heading-132)
      - [MySQL主从复制工作原理](https://juejin.cn/post/6844904127047139335#heading-133)
      - [基本原理流程，3个线程以及之间的关联](https://juejin.cn/post/6844904127047139335#heading-134)
      - [复制过程](https://juejin.cn/post/6844904127047139335#heading-135)
    - [读写分离有哪些解决方案？](https://juejin.cn/post/6844904127047139335#heading-136)
    - [备份计划，mysqldump以及xtranbackup的实现原理](https://juejin.cn/post/6844904127047139335#heading-137)
    - [数据表损坏的修复方式有哪些？](https://juejin.cn/post/6844904127047139335#heading-138)

- 

## 数据库基础知识

### 为什么要使用数据库

- **数据保存在内存**
  - 优点： 存取速度快
  - 缺点： 数据不能永久保存
- **数据保存在文件**
  - 优点： 数据永久保存
  - 缺点：1、速度比内存操作慢，频繁的IO操作。2、查询数据不方便
- **数据保存在数据库**
  - 数据永久保存
  - 使用SQL语句，查询方便效率高。
  - 管理数据方便

### 什么是SQL？

- 结构化查询语言(Structured Query Language)简称SQL，是一种数据库查询语言。

```
作用：用于存取数据、查询、更新和管理关系数据库系统。
```

### 什么是MySQL?

- MySQL是一个关系型数据库管理系统，由瑞典MySQL AB 公司开发，属于 Oracle 旗下产品。MySQL 是最流行的关系型数据库管理系统之一，在 WEB 应用方面，MySQL是最好的 RDBMS (Relational Database Management System，关系数据库管理系统) 应用软件之一。在Java企业级开发中非常常用，因为 MySQL 是开源免费的，并且方便扩展。

### MySql, Oracle，Sql Service的区别

1. Sql Service只能在Windows上使用，而MySql和Oracle可以在其他系统上使用， 而且可以支持数据库不同系统之间的移植
2. MySql开源免费的，Sql Service和Oracle要钱。
3. 我从小到大排序哈，MySql很小，Sql Service居中，Oracle最大
4. Oracle支持大并发量，大访问量，Sql Service还行，而MySql的话压力没这么大，因此现在的MySql的话最好是要使用集群或者缓存来搭配使用
5. Oracle支持多用户不同权限来进行操作，而MySql只要有登录权限就可操作全部数据库
6. 安装所用的空间差别也是很大的，Mysql安装完后才几百M而Oracle有几G左右，且使用的时候Oracle占用特别大的内存空间和其他机器性能。
7. 做分页的话，MySql使用Limit，Sql Service使用top，Oracle使用row
8. Oracle没有自动增长类型，Mysql和Sql Service一般使用自动增长类型

### 数据库三大范式是什么

- 第一范式：每个列都不可以再拆分。
- 第二范式：在第一范式的基础上，非主键列完全依赖于主键，而不能是依赖于主键的一部分。
- 第三范式：在第二范式的基础上，非主键列只依赖于主键，不依赖于其他非主键。

```
在设计数据库结构的时候，要尽量遵守三范式，如果不遵守，必须有足够的理由。比如性能。事实上我们经常会为了性能而妥协数据库的设计。
```

### mysql有关权限的表都有哪几个

```
MySQL服务器通过权限表来控制用户对数据库的访问，权限表存放在mysql数据库里，由mysql\_install\_db脚本初始化。这些权限表分别user，db，table\_priv，columns\_priv和host。下面分别介绍一下这些表的结构和内容：
```

- user权限表：记录允许连接到服务器的用户帐号信息，里面的权限是全局级的。
- db权限表：记录各个帐号在各个数据库上的操作权限。
- table_priv权限表：记录数据表级的操作权限。
- columns_priv权限表：记录数据列级的操作权限。
- host权限表：配合db权限表对给定主机上数据库级操作权限作更细致的控制。这个权限表不受GRANT和REVOKE语句的影响。

### MySQL的binlog有有几种录入格式？分别有什么区别？

```
有三种格式，statement，row和mixed。
```

- statement(语句)模式下，每一条会修改数据的sql都会记录在binlog中。不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。由于sql的执行是有上下文的，因此在保存的时候需要保存相关的信息，同时还有一些使用了函数之类的语句无法被记录复制。
- row级别下，不记录sql语句上下文相关信息，仅保存哪条记录被修改。记录单元为每一行的改动，基本是可以全部记下来但是由于很多操作，会导致大量行的改动(比如alter table)，因此这种模式的文件保存的信息太多，日志量太大。
- mixed，一种折中的方案，普通操作使用statement(语句)记录，当无法使用statement(语句)的时候使用row。

```
此外，新版的MySQL中对row级别也做了一些优化，当表结构发生变化的时候，会记录语句而不是逐行记录。
```

### 数据库经常使用的函数

- count(*/column)：返回行数
- sum(column)： 返回指定列中唯一值的和
- max(column)：返回指定列或表达式中的数值最大值
- min(column)：返回指定列或表达式中的数值最小值
- avg(column)：返回指定列或表达式中的数值平均值
- date（Expression）: 返回指定表达式代表的日期值

......

## 数据类型

### mysql有哪些数据类型

> | 分类             | 类型名称     | 说明                                                         |
> | ---------------- | ------------ | ------------------------------------------------------------ |
> | 整数类型         | tinyInt      | 很小的整数(8位二进制)                                        |
> | 整数类型         | smallint     | 小的整数(16位二进制)                                         |
> | 整数类型         | mediumint    | 中等大小的整数(24位二进制)                                   |
> | 整数类型         | int(integer) | 普通大小的整数(32位二进制)                                   |
> | 小数类型         | float        | 单精度浮点数                                                 |
> | 小数类型         | double       | 双精度浮点数                                                 |
> | 小数类型         | decimal(m,d) | 压缩严格的定点数                                             |
> | 日期类型         | year         | YYYY 1901~2155                                               |
> | 日期类型         | time         | HH:MM:SS -838:59:59~838:59:59                                |
> | 日期类型         | date         | YYYY-MM-DD 1000-01-01~9999-12-3                              |
> | 日期类型         | datetime     | YYYY-MM-DD HH:MM:SS 1000-01-01 00:00:00~ 9999-12-31 23:59:59 |
> | 日期类型         | timestamp    | YYYY-MM-DD HH:MM:SS 19700101 00:00:01 UTC~2038-01-19 03:14:07UTC |
> | 文本、二进制类型 | CHAR(M)      | M为0~255之间的整数                                           |
> | 文本、二进制类型 | VARCHAR(M)   | M为0~65535之间的整数                                         |
> | 文本、二进制类型 | TINYBLOB     | 允许长度0~255字节                                            |
> | 文本、二进制类型 | BLOB         | 允许长度0~65535字节                                          |
> | 文本、二进制类型 | MEDIUMBLOB   | 允许长度0~167772150字节                                      |
> | 文本、二进制类型 | LONGBLOB     | 允许长度0~4294967295字节                                     |
> | 文本、二进制类型 | TINYTEXT     | 允许长度0~255字节                                            |
> | 文本、二进制类型 | TEXT         | 允许长度0~65535字节                                          |
> | 文本、二进制类型 | MEDIUMTEXT   | 允许长度0~167772150字节                                      |
> | 文本、二进制类型 | LONGTEXT     | 允许长度0~4294967295字节                                     |
> | 文本、二进制类型 | VARBINARY(M) | 允许长度0~M个字节的变长字节字符串                            |
> | 文本、二进制类型 | BINARY(M)    | 允许长度0~M个字节的定长字节字符串                            |

- `1、整数类型`，包括TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT，分别表示1字节、2字节、3字节、4字节、8字节整数。任何整数类型都可以加上UNSIGNED属性，表示数据是无符号的，即非负整数。
  `长度`：整数类型可以被指定长度，例如：INT(11)表示长度为11的INT类型。长度在大多数场景是没有意义的，它不会限制值的合法范围，只会影响显示字符的个数，而且需要和UNSIGNED ZEROFILL属性配合使用才有意义。
  `例子`，假定类型设定为INT(5)，属性为UNSIGNED ZEROFILL，如果用户插入的数据为12的话，那么数据库实际存储数据为00012。

- `2、实数类型`，包括FLOAT、DOUBLE、DECIMAL。
  DECIMAL可以用于存储比BIGINT还大的整型，能存储精确的小数。
  而FLOAT和DOUBLE是有取值范围的，并支持使用标准的浮点进行近似计算。
  计算时FLOAT和DOUBLE相比DECIMAL效率更高一些，DECIMAL你可以理解成是用字符串进行处理。

- `3、字符串类型`，包括VARCHAR、CHAR、TEXT、BLOB
  VARCHAR用于存储可变长字符串，它比定长类型更节省空间。
  VARCHAR使用额外1或2个字节存储字符串长度。列长度小于255字节时，使用1字节表示，否则使用2字节表示。
  VARCHAR存储的内容超出设置的长度时，内容会被截断。
  CHAR是定长的，根据定义的字符串长度分配足够的空间。
  CHAR会根据需要使用空格进行填充方便比较。
  CHAR适合存储很短的字符串，或者所有值都接近同一个长度。
  CHAR存储的内容超出设置的长度时，内容同样会被截断。

  **使用策略：**
  对于经常变更的数据来说，CHAR比VARCHAR更好，因为CHAR不容易产生碎片。
  对于非常短的列，CHAR比VARCHAR在存储空间上更有效率。
  使用时要注意只分配需要的空间，更长的列排序时会消耗更多内存。
  尽量避免使用TEXT/BLOB类型，查询时会使用临时表，导致严重的性能开销。

- `4、枚举类型（ENUM）`，把不重复的数据存储为一个预定义的集合。
  有时可以使用ENUM代替常用的字符串类型。
  ENUM存储非常紧凑，会把列表值压缩到一个或两个字节。
  ENUM在内部存储时，其实存的是整数。
  尽量避免使用数字作为ENUM枚举的常量，因为容易混乱。
  排序是按照内部存储的整数

- `5、日期和时间类型`，尽量使用timestamp，空间效率高于datetime，
  用整数保存时间戳通常不方便处理。
  如果需要存储微妙，可以使用bigint存储。
  看到这里，这道真题是不是就比较容易回答了。

## 引擎

### MySQL存储引擎MyISAM与InnoDB区别

- 存储引擎Storage engine：MySQL中的数据、索引以及其他对象是如何存储的，是一套文件系统的实现。
- 常用的存储引擎有以下：
  - **Innodb引擎**：Innodb引擎提供了对数据库ACID事务的支持。并且还提供了行级锁和外键的约束。它的设计的目标就是处理大数据容量的数据库系统。
  - **MyIASM引擎**(原本Mysql的默认引擎)：不提供事务的支持，也不支持行级锁和外键。
  - **MEMORY引擎**：所有的数据都在内存中，数据的处理速度快，但是安全性不高。

**MyISAM与InnoDB区别**

> | 比较                                                         | MyISAM                                                       | Innodb                                                       |
> | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
> | 存储结构                                                     | 每张表被存放在三个文件：frm-表格定义、MYD(MYData)-数据文件、MYI(MYIndex)-索引文件 | 所有的表都保存在同一个数据文件中（也可能是多个文件，或者是独立的表空间文件），InnoDB表的大小只受限于操作系统文件的大小，一般为2GB |
> | 存储空间                                                     | MyISAM可被压缩，存储空间较小                                 | InnoDB的表需要更多的内存和存储，它会在主内存中建立其专用的缓冲池用于高速缓冲数据和索引 |
> | 可移植性、备份及恢复                                         | 由于MyISAM的数据是以文件的形式存储，所以在跨平台的数据转移中会很方便。在备份和恢复时可单独针对某个表进行操作 | 免费的方案可以是拷贝数据文件、备份 binlog，或者用 mysqldump，在数据量达到几十G的时候就相对痛苦了 |
> | 文件格式                                                     | 数据和索引是分别存储的，数据`.MYD`，索引`.MYI`               | 数据和索引是集中存储的，`.ibd`                               |
> | 记录存储顺序                                                 | 按记录插入顺序保存                                           | 按主键大小有序插入                                           |
> | 外键                                                         | 不支持                                                       | 支持                                                         |
> | 事务                                                         | 不支持                                                       | 支持                                                         |
> | 锁支持（锁是避免资源争用的一个机制，MySQL锁对用户几乎是透明的） | 表级锁定                                                     | 行级锁定、表级锁定，锁定力度小并发能力高                     |
> | SELECT                                                       | MyISAM更优                                                   | --                                                           |
> | INSERT、UPDATE、DELETE                                       | --                                                           | InnoDB更优                                                   |
> | select count(*)                                              | myisam更快，因为myisam内部维护了一个计数器，可以直接调取。   |                                                              |
> | 索引的实现方式                                               | B+树索引，myisam 是堆表                                      | B+树索引，Innodb 是索引组织表                                |
> | 哈希索引                                                     | 不支持                                                       | 支持                                                         |
> | 全文索引                                                     | 支持                                                         | 不支持                                                       |

### MyISAM索引与InnoDB索引的区别？

- InnoDB索引是聚簇索引，MyISAM索引是非聚簇索引。
- InnoDB的主键索引的叶子节点存储着行数据，因此主键索引非常高效。
- MyISAM索引的叶子节点存储的是行数据地址，需要再寻址一次才能得到数据。
- InnoDB非主键索引的叶子节点存储的是主键和其他带索引的列数据，因此查询时做到覆盖索引会非常高效。

### InnoDB引擎的4大特性

- 插入缓冲（insert buffer)
- 二次写(double write)
- 自适应哈希索引(ahi)
- 预读(read ahead)

### 存储引擎选择

- 如果没有特别的需求，使用默认的`Innodb`即可。
- MyISAM：以读写插入为主的应用程序，比如博客系统、新闻门户网站。
- Innodb：更新（删除）操作频率也高，或者要保证数据的完整性；并发量高，支持事务和外键。比如OA自动化办公系统。

## 索引

### 什么是索引？

- 索引是一种特殊的文件(InnoDB数据表上的索引是表空间的一个组成部分)，它们包含着对数据表里所有记录的引用指针。
- 索引是一种数据结构。数据库索引，是数据库管理系统中一个排序的数据结构，以协助快速查询、更新数据库表中数据。索引的实现通常使用B树及其变种B+树。
- 更通俗的说，索引就相当于目录。为了方便查找书中的内容，通过对内容建立索引形成目录。索引是一个文件，它是要占据物理空间的。

### 索引有哪些优缺点？

**索引的优点**

- 可以大大加快数据的检索速度，这也是创建索引的最主要的原因。
- 通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。

**索引的缺点**

- 时间方面：创建索引和维护索引要耗费时间，具体地，当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，会降低增/改/删的执行效率；
- 空间方面：索引需要占物理空间。

### 怎么创建索引的，有什么好处，有哪些分类

1. 创建索引的语法：create index depe_unique_ide on depe(dept_no) tablespace idx_
2. 创建索引可以增加查询速度，唯一索引可以保证数据库列的一致性，可以确定表与表之间的连接
3. 索引的分类：             逻辑分类：单列索引，复合索引，唯一索引，非唯一索引，函数索引             物理分类：B数索引，反向键索引，位图索引

### 简述有哪些索引和作用

```
索引的作用：通过索引可以大大的提高数据库的检索速度，改善数据库性能
```

1. 唯一索引：不允许有俩行具有相同的值
2. 主键索引：为了保持数据库表与表之间的关系
3. 聚集索引：表中行的物理顺序与键值的逻辑（索引）顺序相同。
4. 非聚集索引：聚集索引和非聚集索引的根本区别是表记录的排列顺序和与索引的排列顺序是否一致
5. 复合索引：在创建索引时，并不是只能对一列进行创建索引，可以与主键一样，讲多个组合为索引
6. 全文索引： 全文索引为在字符串数据中进行复杂的词搜索提供有效支持

### 索引使用场景

1. 当数据多且字段值有相同的值得时候用普通索引。
2. 当字段多且字段值没有重复的时候用唯一索引。
3. 当有多个字段名都经常被查询的话用复合索引。
4. 普通索引不支持空值，唯一索引支持空值。
5. 但是，若是这张表增删改多而查询较少的话，就不要创建索引了，因为如果你给一列创建了索引，那么对该列进行增删改的时候，都会先访问这一列的索引，
6. 若是增，则在这一列的索引内以新填入的这个字段名的值为名创建索引的子集，
7. 若是改，则会把原来的删掉，再添入一个以这个字段名的新值为名创建索引的子集，
8. 若是删，则会把索引中以这个字段为名的索引的子集删掉。
9. 所以，会对增删改的执行减缓速度，
10. 所以，若是这张表增删改多而查询较少的话，就不要创建索引了。
11. 更新太频繁地字段不适合创建索引。
12. 不会出现在where条件中的字段不该建立索引。

### 主键索引与唯一索引的区别

1. 主键是一种约束，唯一索引是一种索引，两者在本质上是不同的。
2. 主键创建后一定包含一个唯一性索引，唯一性索引并不一定就是主键。
3. 唯一性索引列允许空值，而主键列不允许为空值。
4. 主键列在创建时，已经默认为空值 ++ 唯一索引了。
5. 一个表最多只能创建一个主键，但可以创建多个唯一索引。
6. 主键更适合那些不容易更改的唯一标识，如自动递增列、身份证号等。
7. 主键可以被其他表引用为外键，而唯一索引不能。 ？

### 索引有哪几种类型？

**主键索引:** 数据列不允许重复，不允许为NULL，一个表只能有一个主键。

**唯一索引:** 数据列不允许重复，允许为NULL值，一个表允许多个列创建唯一索引。

- 可以通过 `ALTER TABLE table_name ADD UNIQUE (column);` 创建唯一索引
- 可以通过 `ALTER TABLE table_name ADD UNIQUE (column1,column2);` 创建唯一组合索引

**普通索引:** 基本的索引类型，没有唯一性的限制，允许为NULL值。

- 可以通过`ALTER TABLE table_name ADD INDEX index_name (column);`创建普通索引
- 可以通过`ALTER TABLE table_name ADD INDEX index_name(column1, column2, column3);`创建组合索引

**全文索引：** 是目前搜索引擎使用的一种关键技术。

- 可以通过`ALTER TABLE table_name ADD FULLTEXT (column);`创建全文索引

### 索引的数据结构（b树，hash）

- 索引的数据结构和具体存储引擎的实现有关，在MySQL中使用较多的索引有**Hash索引**，**B+树索引**等，而我们经常使用的InnoDB存储引擎的默认索引实现为：B+树索引。对于哈希索引来说，底层的数据结构就是哈希表，因此在绝大多数需求为单条记录查询的时候，可以选择哈希索引，查询性能最快；其余大部分场景，建议选择BTree索引。

**1、B树索引**

- mysql通过存储引擎取数据，基本上90%的人用的就是InnoDB了，按照实现方式分，InnoDB的索引类型目前只有两种：BTREE（B树）索引和HASH索引。B树索引是Mysql数据库中使用最频繁的索引类型，基本所有存储引擎都支持BTree索引。通常我们说的索引不出意外指的就是（B树）索引（实际是用B+树实现的，因为在查看表索引时，mysql一律打印BTREE，所以简称为B树索引）



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c6ee6f5752~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- 查询方式：
  - 主键索引区:PI(关联保存的时数据的地址)按主键查询,
  - 普通索引区:si(关联的id的地址,然后再到达上面的地址)。所以按主键查询,速度最快
- B+tree性质：
  1. n棵子tree的节点包含n个关键字，不用来保存数据而是保存数据的索引。
  2. 所有的叶子结点中包含了全部关键字的信息，及指向含这些关键字记录的指针，且叶子结点本身依关键字的大小自小而大顺序链接。
  3. 所有的非终端结点可以看成是索引部分，结点中仅含其子树中的最大（或最小）关键字。
  4. B+ 树中，数据对象的插入和删除仅在叶节点上进行。
  5. B+树有2个头指针，一个是树的根节点，一个是最小关键码的叶节点。

**2、哈希索引**

- 简要说下，类似于数据结构中简单实现的HASH表（散列表）一样，当我们在mysql中用哈希索引时，主要就是通过Hash算法（常见的Hash算法有直接定址法、平方取中法、折叠法、除数取余法、随机数法），将数据库字段数据转换成定长的Hash值，与这条数据的行指针一并存入Hash表的对应位置；如果发生Hash碰撞（两个不同关键字的Hash值相同），则在对应Hash键下以链表形式存储。当然这只是简略模拟图。



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c6ef9cb120~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



### 索引的基本原理

- 索引用来快速地寻找那些具有特定值的记录。如果没有索引，一般来说执行查询时遍历整张表。
- 索引的原理很简单，就是把无序的数据变成有序的查询
  1. 把创建了索引的列的内容进行排序
  2. 对排序结果生成倒排表
  3. 在倒排表内容上拼上数据地址链
  4. 在查询的时候，先拿到倒排表内容，再取出数据地址链，从而拿到具体数据

### 索引算法有哪些？

- 索引算法有 BTree算法和Hash算法

**1、BTree算法**

- BTree是最常用的mysql数据库索引算法，也是mysql默认的算法。因为它不仅可以被用在=,>,>=,<,<=和between这些比较操作符上，而且还可以用于like操作符，只要它的查询条件是一个不以通配符开头的常量， 例如：

  ```pgsql
  pgsql复制代码-- 只要它的查询条件是一个不以通配符开头的常量
  select * from user where name like 'jack%'; 
  -- 如果一通配符开头，或者没有使用常量，则不会使用索引，例如： 
  select * from user where name like '%jack'; 
  ```

**2、Hash算法**

- Hash Hash索引只能用于对等比较，例如=,<=>（相当于=）操作符。由于是一次定位数据，不像BTree索引需要从根节点到枝节点，最后才能访问到页节点这样多次IO访问，所以检索效率远高于BTree索引。

### 索引设计的原则？

1. 适合索引的列是出现在where子句中的列，或者连接子句中指定的列
2. 基数较小的类，索引效果较差，没有必要在此列建立索引
3. 使用短索引，如果对长字符串列进行索引，应该指定一个前缀长度，这样能够节省大量索引空间
4. 不要过度索引。索引需要额外的磁盘空间，并降低写操作的性能。在修改表内容的时候，索引会进行更新甚至重构，索引列越多，这个时间就会越长。所以只保持需要的索引有利于查询即可。

### 创建索引的原则

- 索引虽好，但也不是无限制的使用，最好符合一下几个原则

1. 最左前缀匹配原则，组合索引非常重要的原则，mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。
2. 较频繁作为查询条件的字段才去创建索引
3. 更新频繁字段不适合创建索引
4. 若是不能有效区分数据的列不适合做索引列(如性别，男女未知，最多也就三种，区分度实在太低)
5. 尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可。
6. 定义有外键的数据列一定要建立索引。
7. 对于那些查询中很少涉及的列，重复值比较多的列不要建立索引。
8. 对于定义为text、image和bit的数据类型的列不要建立索引。

### 创建索引的三种方式

- 第一种方式：在执行CREATE TABLE时创建索引

  CREATE TABLE user_index2 ( id INT auto_increment PRIMARY KEY, first_name VARCHAR (16), last_name VARCHAR (16), id_card VARCHAR (18), information text, KEY name (first_name, last_name), FULLTEXT KEY (information), UNIQUE KEY (id_card) );

- 第二种方式：使用ALTER TABLE命令去增加索引

  ```pgsql
  pgsql
  复制代码ALTER TABLE table_name ADD INDEX index_name (column_list);
  ```

  - ALTER TABLE用来创建普通索引、UNIQUE索引或PRIMARY KEY索引。
  - 其中table_name是要增加索引的表名，column_list指出对哪些列进行索引，多列时各列之间用逗号分隔。
  - 索引名index_name可自己命名，缺省时，MySQL将根据第一个索引列赋一个名称。另外，ALTER TABLE允许在单个语句中更改多个表，因此可以在同时创建多个索引。

- 第三种方式：使用CREATE INDEX命令创建

  ```pgsql
  pgsql
  复制代码CREATE INDEX index_name ON table_name (column_list);
  ```

  - CREATE INDEX可对表增加普通索引或UNIQUE索引。（但是，不能创建PRIMARY KEY索引）

### 如何删除索引

- 根据索引名删除普通索引、唯一索引、全文索引：`alter table 表名 drop KEY 索引名`

  ```sas
  sas复制代码alter table user_index drop KEY name;
  alter table user_index drop KEY id_card;
  alter table user_index drop KEY information;
  ```

- 删除主键索引：`alter table 表名 drop primary key`（因为主键只有一个）。这里值得注意的是，如果主键自增长，那么不能直接执行此操作（自增长依赖于主键索引）：



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c6ef8bc6a4~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- 需要取消自增长再行删除：

  ```pgsql
  pgsql复制代码alter table user_index
  -- 重新定义字段
  MODIFY id int,
  drop PRIMARY KEY
  ```

- 但通常不会删除主键，因为设计主键一定与业务逻辑无关。

### 创建索引时需要注意什么？

- 非空字段：应该指定列为NOT NULL，除非你想存储NULL。在mysql中，含有空值的列很难进行查询优化，因为它们使得索引、索引的统计信息以及比较运算更加复杂。你应该用0、一个特殊的值或者一个空串代替空值；
- 取值离散大的字段：（变量各个取值之间的差异程度）的列放到联合索引的前面，可以通过count()函数查看字段的差异值，返回值越大说明字段的唯一值越多字段的离散程度高；
- 索引字段越小越好：数据库的数据存储以页为单位一页存储的数据越多一次IO操作获取的数据越大效率越高。

### 使用索引查询一定能提高查询的性能吗？为什么

```
通常，通过索引查询数据比全表扫描要快。但是我们也必须注意到它的代价。
```

- 索引需要空间来存储，也需要定期维护， 每当有记录在表中增减或索引列被修改时，索引本身也会被修改。 这意味着每条记录的INSERT，DELETE，UPDATE将为此多付出4，5 次的磁盘I/O。 因为索引需要额外的存储空间和处理，那些不必要的索引反而会使查询反应时间变慢。使用索引查询不一定能提高查询性能，索引范围查询(INDEX RANGE SCAN)适用于两种情况:
- 基于一个范围的检索，一般查询返回结果集小于表中记录数的30%
- 基于非唯一性索引的检索

### 百万级别或以上的数据如何删除

- 关于索引：由于索引需要额外的维护成本，因为索引文件是单独存在的文件,所以当我们对数据的增加,修改,删除,都会产生额外的对索引文件的操作,这些操作需要消耗额外的IO,会降低增/改/删的执行效率。所以，在我们删除数据库百万级别数据的时候，查询MySQL官方手册得知删除数据的速度和创建的索引数量是成正比的。

1. 所以我们想要删除百万数据的时候可以先删除索引（此时大概耗时三分多钟）
2. 然后删除其中无用数据（此过程需要不到两分钟）
3. 删除完成后重新创建索引(此时数据较少了)创建索引也非常快，约十分钟左右。
4. 与之前的直接删除绝对是要快速很多，更别说万一删除中断,一切删除会回滚。那更是坑了。

### 前缀索引

- 语法：`index(field(10))`，使用字段值的前10个字符建立索引，默认是使用字段的全部内容建立索引。
- 前提：前缀的标识度高。比如密码就适合建立前缀索引，因为密码几乎各不相同。
- 实操的难度：在于前缀截取的长度。
- 我们可以利用`select count(*)/count(distinct left(password,prefixLen));`，通过从调整`prefixLen`的值（从1自增）查看不同前缀长度的一个平均匹配度，接近1时就可以了（表示一个密码的前`prefixLen`个字符几乎能确定唯一一条记录）

### 什么是最左前缀原则？什么是最左匹配原则

- 顾名思义，就是最左优先，在创建多列索引时，要根据业务需求，where子句中使用最频繁的一列放在最左边。
- 最左前缀匹配原则，非常重要的原则，mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。
- =和in可以乱序，比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式

### B树和B+树的区别

- 在B树中，你可以将键和值存放在内部节点和叶子节点；但在B+树中，内部节点都是键，没有值，叶子节点同时存放键和值。
- B+树的叶子节点有一条链相连，而B树的叶子节点各自独立。



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c6efdfc051~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



### 使用B树的好处

- B树可以在内部节点同时存储键和值，因此，把频繁访问的数据放在靠近根节点的地方将会大大提高热点数据的查询效率。这种特性使得B树在特定数据重复多次查询的场景中更加高效。

### 使用B+树的好处

- 由于B+树的内部节点只存放键，不存放值，因此，一次读取，可以在内存页中获取更多的键，有利于更快地缩小查找范围。 B+树的叶节点由一条链相连，因此，当需要进行一次全数据遍历的时候，B+树只需要使用O(logN)时间找到最小的一个节点，然后通过链进行O(N)的顺序遍历即可。而B树则需要对树的每一层进行遍历，这会需要更多的内存置换次数，因此也就需要花费更多的时间

### Hash索引和B+树所有有什么区别或者说优劣呢?

- 首先要知道Hash索引和B+树索引的底层实现原理：
- hash索引底层就是hash表，进行查找时，调用一次hash函数就可以获取到相应的键值，之后进行回表查询获得实际数据。B+树底层实现是多路平衡查找树。对于每一次的查询都是从根节点出发，查找到叶子节点方可以获得所查键值，然后根据查询判断是否需要回表查询数据。

**那么可以看出他们有以下的不同：**

- hash索引进行等值查询更快(一般情况下)，但是却无法进行范围查询。

- 因为在hash索引中经过hash函数建立索引之后，索引的顺序与原顺序无法保持一致，不能支持范围查询。而B+树的的所有节点皆遵循(左节点小于父节点，右节点大于父节点，多叉树也类似)，天然支持范围。

- hash索引不支持使用索引进行排序，原理同上。
- hash索引不支持模糊查询以及多列索引的最左前缀匹配。原理也是因为hash函数的不可预测。AAAA和AAAAB的索引没有相关性。
- hash索引任何时候都避免不了回表查询数据，而B+树在符合某些条件(聚簇索引，覆盖索引等)的时候可以只通过索引完成查询。
- hash索引虽然在等值查询上较快，但是不稳定。性能不可预测，当某个键值存在大量重复的时候，发生hash碰撞，此时效率可能极差。而B+树的查询效率比较稳定，对于所有的查询都是从根节点到叶子节点，且树的高度较低。

- 因此，在大多数情况下，直接选择B+树索引可以获得稳定且较好的查询速度。而不需要使用hash索引。

### 数据库为什么使用B+树而不是B树

- B树只适合随机检索，而B+树同时支持随机检索和顺序检索；
- B+树空间利用率更高，可减少I/O次数，磁盘读写代价更低。一般来说，索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储的磁盘上。这样的话，索引查找过程中就要产生磁盘I/O消耗。B+树的内部结点并没有指向关键字具体信息的指针，只是作为索引使用，其内部结点比B树小，盘块能容纳的结点中关键字数量更多，一次性读入内存中可以查找的关键字也就越多，相对的，IO读写次数也就降低了。而IO读写次数是影响索引检索效率的最大因素；
- B+树的查询效率更加稳定。B树搜索有可能会在非叶子结点结束，越靠近根节点的记录查找时间越短，只要找到关键字即可确定记录的存在，其性能等价于在关键字全集内做一次二分查找。而在B+树中，顺序检索比较明显，随机检索时，任何关键字的查找都必须走一条从根节点到叶节点的路，所有关键字的查找路径长度相同，导致每一个关键字的查询效率相当。
- B-树在提高了磁盘IO性能的同时并没有解决元素遍历的效率低下的问题。B+树的叶子节点使用指针顺序连接在一起，只要遍历叶子节点就可以实现整棵树的遍历。而且在数据库中基于范围的查询是非常频繁的，而B树不支持这样的操作。
- 增删文件（节点）时，效率更高。因为B+树的叶子节点包含所有关键字，并以有序的链表结构存储，这样可很好提高增删效率。

### B+树在满足聚簇索引和覆盖索引的时候不需要回表查询数据，

- 在B+树的索引中，叶子节点可能存储了当前的key值，也可能存储了当前的key值以及整行的数据，这就是聚簇索引和非聚簇索引。 在InnoDB中，只有主键索引是聚簇索引，如果没有主键，则挑选一个唯一键建立聚簇索引。如果没有唯一键，则隐式的生成一个键来建立聚簇索引。

```
当查询使用聚簇索引时，在对应的叶子节点，可以获取到整行数据，因此不用再次进行回表查询。
```

### 什么是聚簇索引？何时使用聚簇索引与非聚簇索引

- 聚簇索引：将数据存储与索引放到了一块，找到索引也就找到了数据
- 非聚簇索引：将数据存储于索引分开结构，索引结构的叶子节点指向了数据的对应行，myisam通过key_buffer把索引先缓存到内存中，当需要访问数据时（通过索引访问数据），在内存中直接搜索索引，然后通过索引找到磁盘相应数据，这也就是为什么索引不在key buffer命中时，速度慢的原因

澄清一个概念：innodb中，在聚簇索引之上创建的索引称之为辅助索引，辅助索引访问数据总是需要二次查找，非聚簇索引都是辅助索引，像复合索引、前缀索引、唯一索引，辅助索引叶子节点存储的不再是行的物理位置，而是主键值

```
何时使用聚簇索引与非聚簇索引
```



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c6f013b994~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



### 非聚簇索引一定会回表查询吗？

- 不一定，这涉及到查询语句所要求的字段是否全部命中了索引，如果全部命中了索引，那么就不必再进行回表查询。
- 举个简单的例子，假设我们在员工表的年龄上建立了索引，那么当进行`select age from employee where age < 20`的查询时，在索引的叶子节点上，已经包含了age信息，不会再次进行回表查询。

### 联合索引是什么？为什么需要注意联合索引中的顺序？

- MySQL可以使用多个字段同时建立一个索引，叫做联合索引。在联合索引中，如果想要命中索引，需要按照建立索引时的字段顺序挨个使用，否则无法命中索引。

**具体原因为:**

- MySQL使用索引时需要索引有序，假设现在建立了"name，age，school"的联合索引，那么索引的排序为: 先按照name排序，如果name相同，则按照age排序，如果age的值也相等，则按照school进行排序。
- 当进行查询时，此时索引仅仅按照name严格有序，因此必须首先使用name字段进行等值查询，之后对于匹配到的列而言，其按照age字段严格有序，此时可以使用age字段用做索引查找，以此类推。因此在建立联合索引的时候应该注意索引列的顺序，一般情况下，将查询需求频繁或者字段选择性高的列放在前面。此外可以根据特例的查询或者表结构进行单独的调整。

## 事务

### 什么是数据库事务？

- 事务是一个不可分割的数据库操作序列，也是数据库并发控制的基本单位，其执行的结果必须使数据库从一种一致性状态变到另一种一致性状态。事务是逻辑上的一组操作，要么都执行，要么都不执行。
- 事务最经典也经常被拿出来说例子就是转账了。
- 假如小明要给小红转账1000元，这个转账会涉及到两个关键操作就是：将小明的余额减少1000元，将小红的余额增加1000元。万一在这两个操作之间突然出现错误比如银行系统崩溃，导致小明余额减少而小红的余额没有增加，这样就不对了。事务就是保证这两个关键操作要么都成功，要么都要失败。

### 事物的四大特性(ACID)介绍一下?

- 关系性数据库需要遵循ACID规则，具体内容如下：



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c6f098cc5d~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



1. **原子性：** 事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；
2. **一致性：** 执行事务前后，数据保持一致，多个事务对同一个数据读取的结果是相同的；
3. **隔离性：** 并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；
4. **持久性：** 一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。

### 什么是脏读？幻读？不可重复读？

- 脏读(Drity Read)：某个事务已更新一份数据，另一个事务在此时读取了同一份数据，由于某些原因，前一个RollBack了操作，则后一个事务所读取的数据就会是不正确的。
- 不可重复读(Non-repeatable read):在一个事务的两次查询之中数据不一致，这可能是两次查询过程中间插入了一个事务更新的原有的数据。
- 幻读(Phantom Read):在一个事务的两次查询中数据笔数不一致，例如有一个事务查询了几列(Row)数据，而另一个事务却在此时插入了新的几列数据，先前的事务在接下来的查询中，就会发现有几列数据是它先前所没有的。

### 什么是事务的隔离级别？MySQL的默认隔离级别是什么？

为了达到事务的四大特性，数据库定义了4种不同的事务隔离级别，由低到高依次为Read uncommitted、Read committed、Repeatable read、Serializable，这四个级别可以逐个解决脏读、不可重复读、幻读这几类问题。

| 隔离级别         | 脏读 | 不可重复读 | 幻影读 |
| ---------------- | ---- | ---------- | ------ |
| READ-UNCOMMITTED | √    | √          | √      |
| READ-COMMITTED   | ×    | √          | √      |
| REPEATABLE-READ  | ×    | ×          | √      |
| SERIALIZABLE     | ×    | ×          | ×      |

**SQL 标准定义了四个隔离级别：**

- **READ-UNCOMMITTED(读取未提交)：** 最低的隔离级别，允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**。
- **READ-COMMITTED(读取已提交)：** 允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生**。
- **REPEATABLE-READ(可重复读)：** 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生**。
- **SERIALIZABLE(可串行化)：** 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。

**注意：**

- 这里需要注意的是：Mysql 默认采用的 REPEATABLE_READ隔离级别 Oracle 默认采用的 READ_COMMITTED隔离级别
- 事务隔离机制的实现基于锁机制和并发调度。其中并发调度使用的是MVVC（多版本并发控制），通过保存修改的旧版本信息来支持并发一致性读和回滚等特性。
- 因为隔离级别越低，事务请求的锁越少，所以大部分数据库系统的隔离级别都是**READ-COMMITTED(读取提交内容):**，但是你要知道的是InnoDB 存储引擎默认使用 **REPEATABLE-READ（可重读）**并不会有任何性能损失。
- InnoDB 存储引擎在 **分布式事务** 的情况下一般会用到**SERIALIZABLE(可串行化)**隔离级别。

## 锁

### 对MySQL的锁了解吗

- 当数据库有并发事务的时候，可能会产生数据的不一致，这时候需要一些机制来保证访问的次序，锁机制就是这样的一个机制。
- 就像酒店的房间，如果大家随意进出，就会出现多人抢夺同一个房间的情况，而在房间上装上锁，申请到钥匙的人才可以入住并且将房间锁起来，其他人只有等他使用完毕才可以再次使用。

### 从锁的类别上分MySQL都有哪些锁呢？

**从锁的类别上来讲**，有共享锁和排他锁。

- 共享锁: 又叫做读锁。 当用户要进行数据的读取时，对数据加上共享锁。共享锁就是让多个线程同时获取一个锁。
- 排他锁: 又叫做写锁。 当用户要进行数据的写入时，对数据加上排他锁。排它锁也称作独占锁，一个锁在某一时刻只能被一个线程占有，其它线程必须等待锁被释放之后才可能获取到锁。排他锁只可以加一个，他和其他的排他锁，共享锁都相斥。

### 隔离级别与锁的关系

- 在Read Uncommitted级别下，读取数据不需要加共享锁，这样就不会跟被修改的数据上的排他锁冲突
- 在Read Committed级别下，读操作需要加共享锁，但是在语句执行完以后释放共享锁；
- 在Repeatable Read级别下，读操作需要加共享锁，但是在事务提交之前并不释放共享锁，也就是必须等待事务执行完毕以后才释放共享锁。
- SERIALIZABLE 是限制性最强的隔离级别，因为该级别**锁定整个范围的键**，并一直持有锁，直到事务完成。

### 按照锁的粒度分数据库锁有哪些？锁机制与InnoDB锁算法

- 在关系型数据库中，可以**按照锁的粒度把数据库锁分**为行级锁(INNODB引擎)、表级锁(MYISAM引擎)和页级锁(BDB引擎 )。
- **MyISAM和InnoDB存储引擎使用的锁：**
  - MyISAM采用表级锁(table-level locking)。
  - InnoDB支持行级锁(row-level locking)和表级锁，默认为行级锁

**行级锁，表级锁和页级锁对比**

- **行级锁** 行级锁是Mysql中锁定粒度最细的一种锁，表示只针对当前操作的行进行加锁。行级锁能大大减少数据库操作的冲突。其加锁粒度最小，但加锁的开销也最大。行级锁分为共享锁 和 排他锁。
  - 特点：开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高。
- **表级锁** 表级锁是MySQL中锁定粒度最大的一种锁，表示对当前操作的整张表加锁，它实现简单，资源消耗较少，被大部分MySQL引擎支持。最常使用的MYISAM与INNODB都支持表级锁定。表级锁定分为表共享读锁（共享锁）与表独占写锁（排他锁）。
  - 特点：开销小，加锁快；不会出现死锁；锁定粒度大，发出锁冲突的概率最高，并发度最低。
- **页级锁** 页级锁是MySQL中锁定粒度介于行级锁和表级锁中间的一种锁。表级锁速度快，但冲突多，行级冲突少，但速度慢。所以取了折衷的页级，一次锁定相邻的一组记录。
  - 特点：开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之间，并发度一般

### MySQL中InnoDB引擎的行锁是怎么实现的？

- InnoDB是基于索引来完成行锁
- 例: select * from tab_with_index where id = 1 for update;
- for update 可以根据条件来完成行锁锁定，并且 id 是有索引键的列，如果 id 不是索引键那么InnoDB将完成表锁，并发将无从谈起

### InnoDB存储引擎的锁的算法有三种

- Record lock：单个行记录上的锁
- Gap lock：间隙锁，锁定一个范围，不包括记录本身
- Next-key lock：record+gap 锁定一个范围，包含记录本身

**相关知识点：**

1. innodb对于行的查询使用next-key lock
2. Next-locking keying为了解决Phantom Problem幻读问题
3. 当查询的索引含有唯一属性时，将next-key lock降级为record key
4. Gap锁设计的目的是为了阻止多个事务将记录插入到同一范围内，而这会导致幻读问题的产生
5. 有两种方式显式关闭gap锁：（除了外键约束和唯一性检查外，其余情况仅使用record lock） A. 将事务隔离级别设置为RC B. 将参数innodb_locks_unsafe_for_binlog设置为1

### 什么是死锁？怎么解决？

- 死锁是指两个或多个事务在同一资源上相互占用，并请求锁定对方的资源，从而导致恶性循环的现象。
- 常见的解决死锁的方法
  - 1、如果不同程序会并发存取多个表，尽量约定以相同的顺序访问表，可以大大降低死锁机会。
  - 2、在同一个事务中，尽可能做到一次锁定所需要的所有资源，减少死锁产生概率；
  - 3、对于非常容易产生死锁的业务部分，可以尝试使用升级锁定颗粒度，通过表级锁定来减少死锁产生的概率；

```
如果业务不好处理,可以用分布式事务锁或者使用乐观锁
```

### 数据库的乐观锁和悲观锁是什么？怎么实现的？

- 数据库管理系统（DBMS）中的并发控制的任务是确保在多个事务同时存取数据库中同一数据时不破坏事务的隔离性和统一性以及数据库的统一性。乐观并发控制（乐观锁）和悲观并发控制（悲观锁）是并发控制主要采用的技术手段。

- **悲观锁**：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。在查询完数据的时候就把事务锁起来，直到提交事务。实现方式：使用数据库中的锁机制

  ```sql
  sql复制代码//核心SQL,主要靠for update
  select status from t_goods where id=1 for update;
  ```

- **乐观锁**：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。在修改数据的时候把事务锁起来，通过version的方式来进行锁定。实现方式：乐一般会使用版本号机制或CAS算法实现。

  ```pgsql
  pgsql复制代码//核心SQL
  update table set x=x+1, version=version+1 where id=#{id} and version=#{version};
  ```

**两种锁的使用场景**

- 从上面对两种锁的介绍，我们知道两种锁各有优缺点，不可认为一种好于另一种，像**乐观锁适用于写比较少的情况下（多读场景）**，即冲突真的很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。
- 但如果是多写的情况，一般会经常产生冲突，这就会导致上层应用会不断的进行retry，这样反倒是降低了性能，所以**一般多写的场景下用悲观锁就比较合适。**

## 视图

### 为什么要使用视图？什么是视图？

- 为了提高复杂SQL语句的复用性和表操作的安全性，MySQL数据库管理系统提供了视图特性。所谓视图，本质上是一种虚拟表，在物理上是不存在的，其内容与真实的表相似，包含一系列带有名称的列和行数据。但是，视图并不在数据库中以储存的数据值形式存在。行和列数据来自定义视图的查询所引用基本表，并且在具体引用视图时动态生成。
- 视图使开发者只关心感兴趣的某些特定数据和所负责的特定任务，只能看到视图中所定义的数据，而不是视图所引用表中的数据，从而提高了数据库中数据的安全性。

### 视图有哪些特点？

**视图的特点如下:**

- 视图的列可以来自不同的表，是表的抽象和在逻辑意义上建立的新关系。
- 视图是由基本表(实表)产生的表(虚表)。
- 视图的建立和删除不影响基本表。
- 对视图内容的更新(添加，删除和修改)直接影响基本表。
- 当视图来自多个基本表时，不允许添加和删除数据。

视图的操作包括创建视图，查看视图，删除视图和修改视图。

### 视图的使用场景有哪些？

```
视图根本用途：简化sql查询，提高开发效率。如果说还有另外一个用途那就是兼容老的表结构。
```

**下面是视图的常见使用场景：**

- 重用SQL语句；
- 简化复杂的SQL操作。在编写查询后，可以方便的重用它而不必知道它的基本查询细节；
- 使用表的组成部分而不是整个表；
- 保护数据。可以给用户授予表的特定部分的访问权限而不是整个表的访问权限；
- 更改数据格式和表示。视图可返回与底层表的表示和格式不同的数据。

### 视图的优点

1. 查询简单化。视图能简化用户的操作
2. 数据安全性。视图使用户能以多种角度看待同一数据，能够对机密数据提供安全保护
3. 逻辑数据独立性。视图对重构数据库提供了一定程度的逻辑独立性

### 视图的缺点

1. 性能。数据库必须把视图的查询转化成对基本表的查询，如果这个视图是由一个复杂的多表查询所定义，那么，即使是视图的一个简单查询，数据库也把它变成一个复杂的结合体，需要花费一定的时间。

2. 修改限制。当用户试图修改视图的某些行时，数据库必须把它转化为对基本表的某些行的修改。事实上，当从视图中插入或者删除时，情况也是这样。对于简单视图来说，这是很方便的，但是，对于比较复杂的视图，可能是不可修改的

   这些视图有如下特征：1.有UNIQUE等集合操作符的视图。2.有GROUP BY子句的视图。3.有诸如AVG\SUM\MAX等聚合函数的视图。 4.使用DISTINCT关键字的视图。5.连接表的视图（其中有些例外）

### 什么是游标？

- 游标是系统为用户开设的一个数据缓冲区，存放SQL语句的执行结果，每个游标区都有一个名字。用户可以通过游标逐一获取记录并赋给主变量，交由主语言进一步处理。

## 存储过程与函数

### 什么是存储过程？有哪些优缺点？

- 存储过程是一个预编译的SQL语句，优点是允许模块化的设计，就是说只需要创建一次，以后在该程序中就可以调用多次。如果某次操作需要执行多次SQL，使用存储过程比单纯SQL语句执行要快。

**优点**

1. 存储过程是预编译过的，执行效率高。
2. 存储过程的代码直接存放于数据库中，通过存储过程名直接调用，减少网络通讯。
3. 安全性高，执行存储过程需要有一定权限的用户。
4. 存储过程可以重复使用，减少数据库开发人员的工作量。

**缺点**

1. 调试麻烦，但是用 PL/SQL Developer 调试很方便！弥补这个缺点。
2. 移植问题，数据库端代码当然是与数据库相关的。但是如果是做工程型项目，基本不存在移植问题。
3. 重新编译问题，因为后端代码是运行前编译的，如果带有引用关系的对象发生改变时，受影响的存储过程、包将需要重新编译（不过也可以设置成运行时刻自动编译）。
4. 如果在一个程序系统中大量的使用存储过程，到程序交付使用的时候随着用户需求的增加会导致数据结构的变化，接着就是系统的相关问题了，最后如果用户想维护该系统可以说是很难很难、而且代价是空前的，维护起来更麻烦。

## 触发器

### 什么是触发器？触发器的使用场景有哪些？

- 触发器是用户定义在关系表上的一类由事件驱动的特殊的存储过程。触发器是指一段代码，当触发某个事件时，自动执行这些代码。

**使用场景**

- 可以通过数据库中的相关表实现级联更改。
- 实时监控某张表中的某个字段的更改而需要做出相应的处理。
- 例如可以生成某些业务的编号。
- 注意不要滥用，否则会造成数据库及应用程序的维护困难。
- 大家需要牢记以上基础知识点，重点是理解数据类型CHAR和VARCHAR的差异，表存储引擎InnoDB和MyISAM的区别。

### MySQL中都有哪些触发器？

**在MySQL数据库中有如下六种触发器：**

- Before Insert
- After Insert
- Before Update
- After Update
- Before Delete
- After Delete

## 常用SQL语句

### SQL语句主要分为哪几类

- 数据定义语言DDL（Data Ddefinition Language）CREATE，DROP，ALTER

  主要为以上操作 即对逻辑结构等有操作的，其中包括表结构，视图和索引。

- 数据查询语言DQL（Data Query Language）SELECT

  这个较为好理解 即查询操作，以select关键字。各种简单查询，连接查询等 都属于DQL。

- 数据操纵语言DML（Data Manipulation Language）INSERT，UPDATE，DELETE

  主要为以上操作 即对数据进行操作的，对应上面所说的查询操作 DQL与DML共同构建了多数初级程序员常用的增删改查操作。而查询是较为特殊的一种 被划分到DQL中。

- 数据控制功能DCL（Data Control Language）GRANT，REVOKE，COMMIT，ROLLBACK

  主要为以上操作 即对数据库安全性完整性等有操作的，可以简单的理解为权限控制等。

### SQL语句的语法顺序：

1. SELECT
2. FROM
3. JOIN
4. ON
5. WHERE
6. GROUP BY
7. HAVING
8. UNION
9. ORDER BY
10. LIMIT

### 超键、候选键、主键、外键分别是什么？

- 超键：在关系中能唯一标识元组的属性集称为关系模式的超键。一个属性可以为作为一个超键，多个属性组合在一起也可以作为一个超键。超键包含候选键和主键。
- 候选键：是最小超键，即没有冗余元素的超键。
- 主键：数据库表中对储存数据对象予以唯一和完整标识的数据列或属性的组合。一个数据列只能有一个主键，且主键的取值不能缺失，即不能为空值（Null）。
- 外键：在一个表中存在的另一个表的主键称此表的外键。

### SQL 约束有哪几种？

> SQL 约束有哪几种？

- NOT NULL: 用于控制字段的内容一定不能为空（NULL）。
- UNIQUE: 控件字段内容不能重复，一个表允许有多个 Unique 约束。
- PRIMARY KEY: 也是用于控件字段内容不能重复，但它在一个表只允许出现一个。
- FOREIGN KEY: 用于预防破坏表之间连接的动作，也能防止非法数据插入外键列，因为它必须是它指向的那个表中的值之一。
- CHECK: 用于控制字段的值范围。

### 六种关联查询

- 交叉连接（CROSS JOIN）

- 内连接（INNER JOIN）

- 外连接（LEFT JOIN/RIGHT JOIN）

- 联合查询（UNION与UNION ALL）

- 全连接（FULL JOIN）

- 交叉连接（CROSS JOIN）

  ```pgsql
  pgsql
  复制代码SELECT * FROM A,B(,C)或者SELECT * FROM A CROSS JOIN B (CROSS JOIN C)#没有任何关联条件，结果是笛卡尔积，结果集会很大，没有意义，很少使用内连接（INNER JOIN）SELECT * FROM A,B WHERE A.id=B.id或者SELECT * FROM A INNER JOIN B ON A.id=B.id多表中同时符合某种条件的数据记录的集合，INNER JOIN可以缩写为JOIN
  ```

**内连接分为三类**

- 等值连接：ON A.id=B.id
- 不等值连接：ON A.id > B.id
- 自连接：SELECT * FROM A T1 INNER JOIN A T2 ON T1.id=T2.pid

**外连接（LEFT JOIN/RIGHT JOIN）**

- 左外连接：LEFT OUTER JOIN, 以左表为主，先查询出左表，按照ON后的关联条件匹配右表，没有匹配到的用NULL填充，可以简写成LEFT JOIN
- 右外连接：RIGHT OUTER JOIN, 以右表为主，先查询出右表，按照ON后的关联条件匹配左表，没有匹配到的用NULL填充，可以简写成RIGHT JOIN

**联合查询（UNION与UNION ALL）**

```n1ql
n1ql
复制代码SELECT * FROM A UNION SELECT * FROM B UNION ...
```

- 就是把多个结果集集中在一起，UNION前的结果为基准，需要注意的是联合查询的列数要相等，相同的记录行会合并
- 如果使用UNION ALL，不会合并重复的记录行
- 效率 UNION 高于 UNION ALL

**全连接（FULL JOIN）**

```n1ql
n1ql
复制代码SELECT * FROM A LEFT JOIN B ON A.id=B.id UNIONSELECT * FROM A RIGHT JOIN B ON A.id=B.id
```

- MySQL不支持全连接
- 可以使用LEFT JOIN 和UNION和RIGHT JOIN联合使用

#### 表连接面试题

##### 有2张表。

- 1张R、1张S，R表有ABC三列，S表有CD两列，表中各有三条记录

**R表**

| A    | B    | C    |
| ---- | ---- | ---- |
| a1   | b1   | c1   |
| a2   | b2   | c2   |
| a3   | b3   | c3   |

**S表**

| C    | D    |
| ---- | ---- |
| c1   | d1   |
| c2   | d2   |
| c4   | d3   |

##### 1、交叉连接(笛卡尔积)

- SQL

```nginx
nginx
复制代码select r.*,s.* from r,s
```

- 结果

| A    | B    | C    | C    | D    |
| ---- | ---- | ---- | ---- | ---- |
| a1   | b1   | c1   | c1   | d1   |
| a2   | b2   | c2   | c1   | d1   |
| a3   | b3   | c3   | c1   | d1   |
| a1   | b1   | c1   | c2   | d2   |
| a2   | b2   | c2   | c2   | d2   |
| a3   | b3   | c3   | c2   | d2   |
| a1   | b1   | c1   | c4   | d3   |
| a2   | b2   | c2   | c4   | d3   |
| a3   | b3   | c3   | c4   | d3   |

##### 2、内连接结果

- SQL

```n1ql
n1ql
复制代码select r.*,s.* from r inner join s on r.c=s.c
```

- 结果

| A    | B    | C    | C    | D    |
| ---- | ---- | ---- | ---- | ---- |
| a1   | b1   | c1   | c1   | d1   |
| a2   | b2   | c2   | c2   | d2   |

##### 3、左连接结果

- SQL

```n1ql
n1ql
复制代码select r.*,s.* from r left join s on r.c=s.c
```

- 结果

| A    | B    | C    | C    | D    |
| ---- | ---- | ---- | ---- | ---- |
| a1   | b1   | c1   | c1   | d1   |
| a2   | b2   | c2   | c2   | d2   |
| a3   | b3   | c3   |      |      |

##### 4、右连接结果

- SQL

```n1ql
n1ql
复制代码select r.*,s.* from r right join s on r.c=s.c
```

- 结果

| A    | B    | C    | C    | D    |
| ---- | ---- | ---- | ---- | ---- |
| a1   | b1   | c1   | c1   | d1   |
| a2   | b2   | c2   | c2   | d2   |
|      |      |      | c4   | d3   |

##### 5、全表连接的结果（MySql不支持，Oracle支持）

- SQL

```pgsql
pgsql
复制代码select r.*,s.* from r full join s on r.c=s.c
```

- 结果

| A    | B    | C    | C    | D    |
| ---- | ---- | ---- | ---- | ---- |
| a1   | b1   | c1   | c1   | d1   |
| a2   | b2   | c2   | c2   | d2   |
| a3   | b3   | c3   |      |      |
|      |      |      | c4   | d3   |

### 什么是子查询

1. 条件：一条SQL语句的查询结果做为另一条查询语句的条件或查询结果
2. 嵌套：多条SQL语句嵌套使用，内部的SQL查询语句称为子查询。

### mysql中 in 和 exists 区别

- mysql中的in语句是把外表和内表作hash 连接，而exists语句是对外表作loop循环，每次loop循环再对内表进行查询。一直大家都认为exists比in语句的效率要高，这种说法其实是不准确的。这个是要区分环境的。
  1. 如果查询的两个表大小相当，那么用in和exists差别不大。
  2. 如果两个表中一个较小，一个是大表，则子查询表大的用exists，子查询表小的用in。
  3. not in 和not exists：如果查询语句使用了not in，那么内外表都进行全表扫描，没有用到索引；而not extsts的子查询依然能用到表上的索引。所以无论那个表大，用not exists都比not in要快。

### varchar与char的区别

**char的特点**

- char表示定长字符串，长度是固定的；
- 如果插入数据的长度小于char的固定长度时，则用空格填充；
- 因为长度固定，所以存取速度要比varchar快很多，甚至能快50%，但正因为其长度固定，所以会占据多余的空间，是空间换时间的做法；
- 对于char来说，最多能存放的字符个数为255，和编码无关

**varchar的特点**

- varchar表示可变长字符串，长度是可变的；
- 插入的数据是多长，就按照多长来存储；
- varchar在存取方面与char相反，它存取慢，因为长度不固定，但正因如此，不占据多余的空间，是时间换空间的做法；
- 对于varchar来说，最多能存放的字符个数为65532

```
总之，结合性能角度（char更快）和节省磁盘空间角度（varchar更小），具体情况还需具体来设计数据库才是妥当的做法。
```

### varchar(50)中50的涵义

- 最多存放50个字符，varchar(50)和(200)存储hello所占空间一样，但后者在排序时会消耗更多内存，因为order by col采用fixed_length计算col长度(memory引擎也一样)。在早期 MySQL 版本中， 50 代表字节数，现在代表字符数。

### int(20)中20的涵义

- 是指显示字符的长度。20表示最大显示宽度为20，但仍占4字节存储，存储范围不变；
- 不影响内部存储，只是影响带 zerofill 定义的 int 时，前面补多少个 0，易于报表展示

### mysql为什么这么设计

- 对大多数应用没有意义，只是规定一些工具用来显示字符的个数；int(1)和int(20)存储和计算均一样；

### mysql中int(10)和char(10)以及varchar(10)的区别

- int(10)的10表示显示的数据的长度，不是存储数据的大小；chart(10)和varchar(10)的10表示存储数据的大小，即表示存储多少个字符。
- char(10)表示存储定长的10个字符，不足10个就用空格补齐，占用更多的存储空间
- varchar(10)表示存储10个变长的字符，存储多少个就是多少个，空格也按一个字符存储，这一点是和char(10)的空格不同的，char(10)的空格表示占位不算一个字符

### FLOAT和DOUBLE的区别是什么？

- FLOAT类型数据可以存储至多8位十进制数，并在内存中占4字节。
- DOUBLE类型数据可以存储至多18位十进制数，并在内存中占8字节。

### drop、delete与truncate的区别

- 三者都表示删除，但是三者有一些差别：

| 比较     | Delete                                   | Truncate                       | Drop                                                 |
| -------- | ---------------------------------------- | ------------------------------ | ---------------------------------------------------- |
| 类型     | 属于DML                                  | 属于DDL                        | 属于DDL                                              |
| 回滚     | 可回滚                                   | 不可回滚                       | 不可回滚                                             |
| 删除内容 | 表结构还在，删除表的全部或者一部分数据行 | 表结构还在，删除表中的所有数据 | 从数据库中删除表，所有的数据行，索引和权限也会被删除 |
| 删除速度 | 删除速度慢，需要逐行删除                 | 删除速度快                     | 删除速度最快                                         |

- 因此，在不再需要一张表的时候，用drop；在想删除部分数据行时候，用delete；在保留表而删除所有数据的时候用truncate。

### UNION与UNION ALL的区别？

- 如果使用UNION ALL，不会合并重复的记录行
- 效率 UNION 高于 UNION ALL

## SQL优化

### 说出一些数据库优化方面的经验?

1. 有外键约束的话会影响增删改的性能，如果应用程序可以保证数据库的完整性那就去除外键
2. Sql语句全部大写，特别是列名大写，因为数据库的机制是这样的，sql语句发送到数据库服务器，数据库首先就会把sql编译成大写在执行，如果一开始就编译成大写就不需要了把sql编译成大写这个步骤了
3. 如果应用程序可以保证数据库的完整性，可以不需要按照三大范式来设计数据库
4. 其实可以不必要创建很多索引，索引可以加快查询速度，但是索引会消耗磁盘空间
5. 如果是jdbc的话，使用PreparedStatement不使用Statement(语句)，来创建SQl，PreparedStatement的性能比Statement(语句)的速度要快，使用PreparedStatement对象SQL语句会预编译在此对象中，PreparedStatement对象可以多次高效的执行

### 怎么优化SQL查询语句吗

1. 对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引
2. 用索引可以提高查询
3. SELECT子句中避免使用*号，尽量全部大写SQL
4. 应尽量避免在 where 子句中对字段进行 is null 值判断，否则将导致引擎放弃使用索引而进行全表扫描，使用 IS NOT NULL
5. where 子句中使用 or 来连接条件，也会导致引擎放弃使用索引而进行全表扫描
6. in 和 not in 也要慎用，否则会导致全表扫描

### 你怎么知道SQL语句性能是高还是低

1. 查看SQL的执行时间
2. 使用explain关键字可以模拟优化器执行SQL查询语句，从而知道MYSQL是如何处理你的SQL语句的。分析你的查询语句或是表结构的性能瓶颈。

### SQL的执行顺序

1. FROM：将数据从硬盘加载到数据缓冲区，方便对接下来的数据进行操作。
2. WHERE：从基表或视图中选择满足条件的元组。（不能使用聚合函数）
3. JOIN（如right left 右连接-------从右边表中读取某个元组，并且找到该元组在左边表中对应的元组或元组集）
4. ON：join on实现多表连接查询，推荐该种方式进行多表查询，不使用子查询。
5. GROUP BY：分组，一般和聚合函数一起使用。
6. HAVING：在元组的基础上进行筛选，选出符合条件的元组。（一般与GROUP BY进行连用）
7. SELECT：查询到得所有元组需要罗列的哪些列。
8. DISTINCT：去重的功能。
9. UNION：将多个查询结果合并（默认去掉重复的记录）。
10. ORDER BY：进行相应的排序。
11. LIMIT 1：显示输出一条数据记录（元组）

### 如何定位及优化SQL语句的性能问题？创建的索引有没有被使用到?或者说怎么才可以知道这条语句运行很慢的原因？

- 对于低性能的SQL语句的定位，最重要也是最有效的方法就是使用执行计划，MySQL提供了explain命令来查看语句的执行计划。 我们知道，不管是哪种数据库，或者是哪种数据库引擎，在对一条SQL语句进行执行的过程中都会做很多相关的优化，**对于查询语句，最重要的优化方式就是使用索引**。 而**执行计划，就是显示数据库引擎对于SQL语句的执行的详细情况，其中包含了是否使用索引，使用什么索引，使用的索引的相关信息等**。



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c720eda1ef~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- 执行计划包含的信息

   

  id

   

  有一组数字组成。表示一个查询中各个子查询的执行顺序;

  - id相同执行顺序由上至下。
  - id不同，id值越大优先级越高，越先被执行。
  - id为null时表示一个结果集，不需要使用它查询，常出现在包含union等查询语句中。

**select_type** 每个子查询的查询类型，一些常见的查询类型。

| id   | select_type  | description                               |
| ---- | ------------ | ----------------------------------------- |
| 1    | SIMPLE       | 不包含任何子查询或union等查询             |
| 2    | PRIMARY      | 包含子查询最外层查询就显示为 PRIMARY      |
| 3    | SUBQUERY     | 在select或 where字句中包含的查询          |
| 4    | DERIVED      | from字句中包含的查询                      |
| 5    | UNION        | 出现在union后的查询语句中                 |
| 6    | UNION RESULT | 从UNION中获取结果集，例如上文的第三个例子 |

- **table** 查询的数据表，当从衍生表中查数据时会显示 x 表示对应的执行计划id **partitions** 表分区、表创建的时候可以指定通过那个列进行表分区。 举个例子：

```pgsql
pgsql复制代码create table tmp (
    id int unsigned not null AUTO_INCREMENT,
    name varchar(255),
    PRIMARY KEY (id)
) engine = innodb
partition by key (id) partitions 5;
```

- **type**(非常重要，可以看到有没有走索引) 访问类型
  - ALL 扫描全表数据
  - index 遍历索引
  - range 索引范围查找
  - index_subquery 在子查询中使用 ref
  - unique_subquery 在子查询中使用 eq_ref
  - ref_or_null 对Null进行索引的优化的 ref
  - fulltext 使用全文索引
  - ref 使用非唯一索引查找数据
  - eq_ref 在join查询中使用PRIMARY KEYorUNIQUE NOT NULL索引关联。
- **possible_keys** 可能使用的索引，注意不一定会使用。查询涉及到的字段上若存在索引，则该索引将被列出来。当该列为 NULL时就要考虑当前的SQL是否需要优化了。
- **key** 显示MySQL在查询中实际使用的索引，若没有使用索引，显示为NULL。
- **TIPS**:查询中若使用了覆盖索引(覆盖索引：索引的数据覆盖了需要查询的所有数据)，则该索引仅出现在key列表中
- **key_length** 索引长度
- **ref** 表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值
- **rows** 返回估算的结果集数目，并不是一个准确的值。
- **extra** 的信息非常丰富，常见的有：
  1. Using index 使用覆盖索引
  2. Using where 使用了用where子句来过滤结果集
  3. Using filesort 使用文件排序，使用非索引列进行排序时出现，非常消耗性能，尽量优化。
  4. Using temporary 使用了临时表 sql优化的目标可以参考阿里开发手册

```pgsql
pgsql复制代码【推荐】SQL性能优化的目标：至少要达到 range 级别，要求是ref级别，如果可以是consts最好。 
说明： 
1） consts 单表中最多只有一个匹配行（主键或者唯一索引），在优化阶段即可读取到数据。 
2） ref 指的是使用普通的索引（normal index）。 
3） range 对索引进行范围检索。 
反例：explain表的结果，type=index，索引物理文件全扫描，速度非常慢，这个index级别比较range还低，与全表扫描是小巫见大巫。
```

### SQL的生命周期？

1. 应用服务器与数据库服务器建立一个连接
2. 数据库进程拿到请求sql
3. 解析并生成执行计划，执行
4. 读取数据到内存并进行逻辑处理
5. 通过步骤一的连接，发送结果到客户端
6. 关掉连接，释放资源



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c7211423d0~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



### 大表数据查询，怎么优化

1. 优化shema、sql语句+索引；
2. 第二加缓存，memcached, redis；
3. 主从复制，读写分离；
4. 垂直拆分，根据你模块的耦合度，将一个大的系统分为多个小的系统，也就是分布式系统；
5. 水平切分，针对数据量大的表，这一步最麻烦，最能考验技术水平，要选择一个合理的sharding key, 为了有好的查询效率，表结构也要改动，做一定的冗余，应用也要改，sql中尽量带sharding key，将数据定位到限定的表上去查，而不是扫描全部的表；

### 超大分页怎么处理？

**超大的分页一般从两个方向上来解决.**

- 数据库层面,这也是我们主要集中关注的(虽然收效没那么大),类似于`select * from table where age > 20 limit 1000000,10`这种查询其实也是有可以优化的余地的. 这条语句需要load1000000数据然后基本上全部丢弃,只取10条当然比较慢. 当时我们可以修改为`select * from table where id in (select id from table where age > 20 limit 1000000,10)`.这样虽然也load了一百万的数据,但是由于索引覆盖,要查询的所有字段都在索引中,所以速度会很快. 同时如果ID连续的好,我们还可以`select * from table where id > 1000000 limit 10`,效率也是不错的,优化的可能性有许多种,但是核心思想都一样,就是减少load的数据.
- 从需求的角度减少这种请求…主要是不做类似的需求(直接跳转到几百万页之后的具体某一页.只允许逐页查看或者按照给定的路线走,这样可预测,可缓存)以及防止ID泄漏且连续被人恶意攻击.

**解决超大分页,其实主要是靠缓存,可预测性的提前查到内容,缓存至redis等k-V数据库中,直接返回即可**

### mysql 分页

- LIMIT 子句可以被用于强制 SELECT 语句返回指定的记录数。LIMIT 接受一个或两个数字参数。参数必须是一个整数常量。如果给定两个参数，第一个参数指定第一个返回记录行的偏移量，第二个参数指定返回记录行的最大数目。初始记录行的偏移量是 0(而不是 1)

  SELECT * FROM table LIMIT 5,10; // 检索记录行 6-15

- 为了检索从某一个偏移量到记录集的结束所有的记录行，可以指定第二个参数为 -1：

  SELECT * FROM table LIMIT 95,-1; // 检索记录行 96-last.

- 如果只给定一个参数，它表示返回最大的记录行数目：

  SELECT * FROM table LIMIT 5; //检索前 5 个记录行

- 换句话说，LIMIT n 等价于 LIMIT 0,n。

### 慢查询日志

> 用于记录执行时间超过某个临界值的SQL日志，用于快速定位慢查询，为我们的优化做参考。

- 开启慢查询日志
- 配置项：`slow_query_log`
- 可以使用`show variables like ‘slov_query_log’`查看是否开启，如果状态值为`OFF`，可以使用`set GLOBAL slow_query_log = on`来开启，它会在`datadir`下产生一个`xxx-slow.log`的文件。
- 设置临界时间
- 配置项：`long_query_time`
- 查看：`show VARIABLES like 'long_query_time'`，单位秒
- 设置：`set long_query_time=0.5`
- 实操时应该从长时间设置到短的时间，即将最慢的SQL优化掉
- 查看日志，一旦SQL超过了我们设置的临界时间就会被记录到`xxx-slow.log`中

### 关心过业务系统里面的sql耗时吗？统计过慢查询吗？对慢查询都怎么优化过？

- 在业务系统中，除了使用主键进行的查询，其他的我都会在测试库上测试其耗时，慢查询的统计主要由运维在做，会定期将业务中的慢查询反馈给我们。
- 慢查询的优化首先要搞明白慢的原因是什么？ 是查询条件没有命中索引？是load了不需要的数据列？还是数据量太大？

**所以优化也是针对这三个方向来的，**

- 首先分析语句，看看是否load了额外的数据，可能是查询了多余的行并且抛弃掉了，可能是加载了许多结果中并不需要的列，对语句进行分析以及重写。
- 分析语句的执行计划，然后获得其使用索引的情况，之后修改语句或者修改索引，使得语句可以尽可能的命中索引。
- 如果对语句的优化已经无法进行，可以考虑表中的数据量是否太大，如果是的话可以进行横向或者纵向的分表。

### 为什么要尽量设定一个主键？

- 主键是数据库确保数据行在整张表唯一性的保障，即使业务上本张表没有主键，也建议添加一个自增长的ID列作为主键。设定了主键之后，在后续的删改查的时候可能更加快速以及确保操作数据范围安全。

### 主键使用自增ID还是UUID？

- 推荐使用自增ID，不要使用UUID。
- 因为在InnoDB存储引擎中，主键索引是作为聚簇索引存在的，也就是说，主键索引的B+树叶子节点上存储了主键索引以及全部的数据(按照顺序)，如果主键索引是自增ID，那么只需要不断向后排列即可，如果是UUID，由于到来的ID与原来的大小不确定，会造成非常多的数据插入，数据移动，然后导致产生很多的内存碎片，进而造成插入性能的下降。

```
总之，在数据量大一些的情况下，用自增主键性能会好一些。
关于主键是聚簇索引，如果没有主键，InnoDB会选择一个唯一键来作为聚簇索引，如果没有唯一键，会生成一个隐式的主键。
```

### 字段为什么要求定义为not null？

- null值会占用更多的字节，且会在程序中造成很多与预期不符的情况。

### 如果要存储用户的密码散列，应该使用什么字段进行存储？

- 密码散列，盐，用户身份证号等固定长度的字符串应该使用char而不是varchar来存储，这样可以节省空间且提高检索效率。

### 如何优化查询过程中的数据访问

- 访问数据太多导致查询性能下降
- 确定应用程序是否在检索大量超过需要的数据，可能是太多行或列
- 确认MySQL服务器是否在分析大量不必要的数据行
- 避免犯如下SQL语句错误
- 避免查询不需要的数据。解决办法：使用limit解决
- 多表关联返回全部列。解决办法：指定列名
- 总是返回全部列。解决办法：避免使用SELECT *
- 重复查询相同的数据。解决办法：可以缓存数据，下次直接读取缓存
- 使用explain进行分析，如果发现查询需要扫描大量的数据，但只返回少数的行，可以通过如下技巧去优化：
- 使用索引覆盖扫描，把所有的列都放到索引中，这样存储引擎不需要回表获取对应行就可以返回结果。
- 改变数据库和表的结构，修改数据表范式
- 重写SQL语句，让优化器可以以更优的方式执行查询。

### 如何优化长难的查询语句

- 分析是一个复杂查询还是多个简单查询速度快
- MySQL内部每秒能扫描内存中上百万行数据，相比之下，响应数据给客户端就要慢得多
- 使用尽可能小的查询是好的，但是有时将一个大的查询分解为多个小的查询是很有必要的。
- 将一个大的查询分为多个小的相同的查询
- 一次性删除1000万的数据要比一次删除1万，暂停一会的方案更加损耗服务器开销。
- 分解关联查询，让缓存的效率更高。
- 执行单个查询可以减少锁的竞争。
- 在应用层做关联更容易对数据库进行拆分。
- 查询效率会有大幅提升。
- 较少冗余记录的查询。

### 优化特定类型的查询语句

- count(*)会忽略所有的列，直接统计所有列数，不要使用count(列名)
- MyISAM中，没有任何where条件的count(*)非常快。
- 当有where条件时，MyISAM的count统计不一定比其它引擎快。
- 可以使用explain查询近似值，用近似值替代count(*)
- 增加汇总表
- 使用缓存

### 优化关联查询

- 确定ON或者USING子句中是否有索引。
- 确保GROUP BY和ORDER BY只有一个表中的列，这样MySQL才有可能使用索引。

### 优化子查询

- 用关联查询替代
- 优化GROUP BY和DISTINCT
- 这两种查询据可以使用索引来优化，是最有效的优化方法
- 关联查询中，使用标识列分组的效率更高
- 如果不需要ORDER BY，进行GROUP BY时加ORDER BY NULL，MySQL不会再进行文件排序。
- WITH ROLLUP超级聚合，可以挪到应用程序处理

### 优化LIMIT分页

- LIMIT偏移量大的时候，查询效率较低
- 可以记录上次查询的最大ID，下次查询时直接根据该ID来查询

### 优化UNION查询

- UNION ALL的效率高于UNION

### 优化WHERE子句

- 多数数据库都是从左往右的顺序处理条件的，把能够过滤更多数据的条件放到前面，把过滤少的条件放在后面

### SQL语句优化的一些方法

- 1.对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。

- 2.应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描，如：

  ```axapta
  axapta复制代码select id from t where num is null
  -- 可以在num上设置默认值0，确保表中num列没有null值，然后这样查询：
  select id from t where num=0
  ```

- 3.应尽量避免在 where 子句中使用!=或<>操作符，否则引擎将放弃使用索引而进行全表扫描。

- 4.应尽量避免在 where 子句中使用or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描，如：

  ```sql
  sql复制代码select id from t where num=10 or num=20
  -- 可以这样查询：
  select id from t where num=10 union all select id from t where num=20
  ```

- 5.in 和 not in 也要慎用，否则会导致全表扫描，如：

  ```applescript
  applescript复制代码select id from t where num in(1,2,3) 
  -- 对于连续的数值，能用 between 就不要用 in 了：
  select id from t where num between 1 and 3
  ```

- 6.下面的查询也将导致全表扫描：select id from t where name like ‘%李%’若要提高效率，可以考虑全文检索。

- 7.如果在 where 子句中使用参数，也会导致全表扫描。因为SQL只有在运行时才会解析局部变量，但优化程序不能将访问计划的选择推迟到运行时；它必须在编译时进行选择。然 而，如果在编译时建立访问计划，变量的值还是未知的，因而无法作为索引选择的输入项。如下面语句将进行全表扫描：

  ```sql
  sql复制代码select id from t where num=@num
  -- 可以改为强制查询使用索引：
  select id from t with(index(索引名)) where num=@num
  ```

- 8.应尽量避免在 where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描。如：

  ```applescript
  applescript复制代码select id from t where num/2=100
  -- 应改为:
  select id from t where num=100*2
  ```

- 9.应尽量避免在where子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描。如：

  ```pgsql
  pgsql复制代码select id from t where substring(name,1,3)=’abc’
  -- name以abc开头的id应改为:
  select id from t where name like ‘abc%’
  ```

- 10.不要在 where 子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引。

## 数据库优化

### 为什么要优化

- 系统的吞吐量瓶颈往往出现在数据库的访问速度上
- 随着应用程序的运行，数据库的中的数据会越来越多，处理时间会相应变慢
- 数据是存放在磁盘上的，读写速度无法和内存相比

```
优化原则：减少系统瓶颈，减少资源占用，增加系统的反应速度。
```

### 数据库结构优化

- 一个好的数据库设计方案对于数据库的性能往往会起到事半功倍的效果。
- 需要考虑数据冗余、查询和更新的速度、字段的数据类型是否合理等多方面的内容。

**将字段很多的表分解成多个表**

- 对于字段较多的表，如果有些字段的使用频率很低，可以将这些字段分离出来形成新表。
- 因为当一个表的数据量很大时，会由于使用频率低的字段的存在而变慢。

**增加中间表**

- 对于需要经常联合查询的表，可以建立中间表以提高查询效率。
- 通过建立中间表，将需要通过联合查询的数据插入到中间表中，然后将原来的联合查询改为对中间表的查询。

**增加冗余字段**

- 设计数据表时应尽量遵循范式理论的规约，尽可能的减少冗余字段，让数据库设计看起来精致、优雅。但是，合理的加入冗余字段可以提高查询速度。
- 表的规范化程度越高，表和表之间的关系越多，需要连接查询的情况也就越多，性能也就越差。

**注意：**

```
冗余字段的值在一个表中修改了，就要想办法在其他表中更新，否则就会导致数据不一致的问题。
```

### MySQL数据库cpu飙升到500%的话他怎么处理？

- 当 cpu 飙升到 500%时，先用操作系统命令 top 命令观察是不是 mysqld 占用导致的，如果不是，找出占用高的进程，并进行相关处理。
- 如果是 mysqld 造成的， show processlist，看看里面跑的 session 情况，是不是有消耗资源的 sql 在运行。找出消耗高的 sql，看看执行计划是否准确， index 是否缺失，或者实在是数据量太大造成。
- 一般来说，肯定要 kill 掉这些线程(同时观察 cpu 使用率是否下降)，等进行相应的调整(比如说加索引、改 sql、改内存参数)之后，再重新跑这些 SQL。
- 也有可能是每个 sql 消耗资源并不多，但是突然之间，有大量的 session 连进来导致 cpu 飙升，这种情况就需要跟应用一起来分析为何连接数会激增，再做出相应的调整，比如说限制连接数等

### 大表怎么优化？分库分表了是怎么做的？分表分库了有什么问题？有用到中间件么？他们的原理知道么？

当MySQL单表记录数过大时，数据库的CRUD性能会明显下降，一些常见的优化措施如下：

1. **限定数据的范围：** 务必禁止不带任何限制数据范围条件的查询语句。比如：我们当用户在查询订单历史的时候，我们可以控制在一个月的范围内。；
2. **读/写分离：** 经典的数据库拆分方案，主库负责写，从库负责读；
3. **缓存：** 使用MySQL的缓存，另外对重量级、更新少的数据可以考虑使用应用级别的缓存；

**还有就是通过分库分表的方式进行优化，主要有垂直分区、垂直分表和水平分区、水平分表**

#### 1、垂直分区

- **根据数据库里面数据表的相关性进行拆分。** 例如，用户表中既有用户的登录信息又有用户的基本信息，可以将用户表拆分成两个单独的表，甚至放到单独的库做分库。
- **简单来说垂直拆分是指数据表列的拆分，把一张列比较多的表拆分为多张表。** 如下图所示，这样来说大家应该就更容易理解了。



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c7259992ab~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- **垂直拆分的优点：** 可以使得行数据变小，在查询时减少读取的Block数，减少I/O次数。此外，垂直分区可以简化表的结构，易于维护。
- **垂直拆分的缺点：** 主键会出现冗余，需要管理冗余列，并会引起Join操作，可以通过在应用层进行Join来解决。此外，垂直分区会让事务变得更加复杂；

#### 2、垂直分表

- 把主键和一些列放在一个表，然后把主键和另外的列放在另一个表中



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c725b21e8e~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



**适用场景**

- 1、如果一个表中某些列常用，另外一些列不常用
- 2、可以使数据行变小，一个数据页能存储更多数据，查询时减少I/O次数

**缺点**

- 有些分表的策略基于应用层的逻辑算法，一旦逻辑算法改变，整个分表逻辑都会改变，扩展性较差
- 对于应用层来说，逻辑算法增加开发成本
- 管理冗余列，查询所有数据需要join操作

#### 3、水平分区

- **保持数据表结构不变，通过某种策略存储数据分片。这样每一片数据分散到不同的表或者库中，达到了分布式的目的。 水平拆分可以支撑非常大的数据量。**
- 水平拆分是指数据表行的拆分，表的行数超过200万行时，就会变慢，这时可以把一张的表的数据拆成多张表来存放。举个例子：我们可以将用户信息表拆分成多个用户信息表，这样就可以避免单一表数据量过大对性能造成影响。



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c7300b465e~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- 水品拆分可以支持非常大的数据量。需要注意的一点是:分表仅仅是解决了单一表数据过大的问题，但由于表的数据还是在同一台机器上，其实对于提升MySQL并发能力没有什么意义，所以 **水平拆分最好分库** 。
- 水平拆分能够 **支持非常大的数据量存储，应用端改造也少**，但 **分片事务难以解决** ，跨界点Join性能较差，逻辑复杂。

```
《Java工程师修炼之道》的作者推荐 尽量不要对数据进行分片，因为拆分会带来逻辑、部署、运维的各种复杂度 ，一般的数据表在优化得当的情况下支撑千万以下的数据量是没有太大问题的。如果实在要分片，尽量选择客户端分片架构，这样可以减少一次和中间件的网络I/O。
```

#### 4、水平分表：

- 表很大，分割后可以降低在查询时需要读的数据和索引的页数，同时也降低了索引的层数，提高查询次数



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c744498a9a~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



**适用场景**

- 1、表中的数据本身就有独立性，例如表中分表记录各个地区的数据或者不同时期的数据，特别是有些数据常用，有些不常用。
- 2、需要把数据存放在多个介质上。

**水平切分的缺点**

- 1、给应用增加复杂度，通常查询时需要多个表名，查询所有数据都需UNION操作
- 2、在许多数据库应用中，这种复杂度会超过它带来的优点，查询时会增加读一个索引层的磁盘次数

#### 数据库分片的两种常见方案：

- **客户端代理：** **分片逻辑在应用端，封装在jar包中，通过修改或者封装JDBC层来实现。** 当当网的 **Sharding-JDBC** 、阿里的TDDL是两种比较常用的实现。
- **中间件代理：** **在应用和数据中间加了一个代理层。分片逻辑统一维护在中间件服务中。** 我们现在谈的 **Mycat** 、360的Atlas、网易的DDB等等都是这种架构的实现。

#### 分库分表后面临的问题

- **事务支持** 分库分表后，就成了分布式事务了。如果依赖数据库本身的分布式事务管理功能去执行事务，将付出高昂的性能代价； 如果由应用程序去协助控制，形成程序逻辑上的事务，又会造成编程方面的负担。

- **跨库join**

  只要是进行切分，跨节点Join的问题是不可避免的。但是良好的设计和切分却可以减少此类情况的发生。解决这一问题的普遍做法是分两次查询实现。在第一次查询的结果集中找出关联数据的id,根据这些id发起第二次请求得到关联数据。 分库分表方案产品

- **跨节点的count,order by,group by以及聚合函数问题** 这些是一类问题，因为它们都需要基于全部数据集合进行计算。多数的代理都不会自动处理合并工作。解决方案：与解决跨节点join问题的类似，分别在各个节点上得到结果后在应用程序端进行合并。和join不同的是每个结点的查询可以并行执行，因此很多时候它的速度要比单一大表快很多。但如果结果集很大，对应用程序内存的消耗是一个问题。

- **数据迁移，容量规划，扩容等问题** 来自淘宝综合业务平台团队，它利用对2的倍数取余具有向前兼容的特性（如对4取余得1的数对2取余也是1）来分配数据，避免了行级别的数据迁移，但是依然需要进行表级别的迁移，同时对扩容规模和分表数量都有限制。总得来说，这些方案都不是十分的理想，多多少少都存在一些缺点，这也从一个侧面反映出了Sharding扩容的难度。

- **ID问题**

- 一旦数据库被切分到多个物理结点上，我们将不能再依赖数据库自身的主键生成机制。一方面，某个分区数据库自生成的ID无法保证在全局上是唯一的；另一方面，应用程序在插入数据之前需要先获得ID,以便进行SQL路由. 一些常见的主键生成策略

  - UUID 使用UUID作主键是最简单的方案，但是缺点也是非常明显的。由于UUID非常的长，除占用大量存储空间外，最主要的问题是在索引上，在建立索引和基于索引进行查询时都存在性能问题。 **Twitter的分布式自增ID算法Snowflake** 在分布式系统中，需要生成全局UID的场合还是比较多的，twitter的snowflake解决了这种需求，实现也还是很简单的，除去配置信息，核心代码就是毫秒级时间41位 机器ID 10位 毫秒内序列12位。

- **跨分片的排序分页问题**

  一般来讲，分页时需要按照指定字段进行排序。当排序字段就是分片字段的时候，我们通过分片规则可以比较容易定位到指定的分片，而当排序字段非分片字段的时候，情况就会变得比较复杂了。为了最终结果的准确性，我们需要在不同的分片节点中将数据进行排序并返回，并将不同分片返回的结果集进行汇总和再次排序，最后再返回给用户。如下图所示：

  

  ![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c750f5b2cc~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

  

### MySQL的复制原理以及流程

- 主从复制：将主数据库中的DDL和DML操作通过二进制日志（BINLOG）传输到从数据库上，然后将这些日志重新执行（重做）；从而使得从数据库的数据与主数据库保持一致。

#### 主从复制的作用

1. 主数据库出现问题，可以切换到从数据库。
2. 可以进行数据库层面的读写分离。
3. 可以在从数据库上进行日常备份。

#### MySQL主从复制解决的问题

- 数据分布：随意开始或停止复制，并在不同地理位置分布数据备份
- 负载均衡：降低单个服务器的压力
- 高可用和故障切换：帮助应用程序避免单点失败
- 升级测试：可以用更高版本的MySQL作为从库

#### MySQL主从复制工作原理

- 在主库上把数据更高记录到二进制日志
- 从库将主库的日志复制到自己的中继日志
- 从库读取中继日志的事件，将其重放到从库数据中

#### 基本原理流程，3个线程以及之间的关联

- **主**：binlog线程——记录下所有改变了数据库数据的语句，放进master上的binlog中；
- **从**：io线程——在使用start slave 之后，负责从master上拉取 binlog 内容，放进自己的relay log中；
- **从**：sql执行线程——执行relay log中的语句；

#### 复制过程



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c75eb7e749~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- Binary log：主数据库的二进制日志
- Relay log：从服务器的中继日志

1. master在每个事务更新数据完成之前，将该操作记录串行地写入到binlog文件中。
2. salve开启一个I/O Thread，该线程在master打开一个普通连接，主要工作是binlog dump process。如果读取的进度已经跟上了master，就进入睡眠状态并等待master产生新的事件。I/O线程最终的目的是将这些事件写入到中继日志中。
3. SQL Thread会读取中继日志，并顺序执行该日志中的SQL事件，从而与主数据库中的数据保持一致。

### 读写分离有哪些解决方案？

- 读写分离是依赖于主从复制，而主从复制又是为读写分离服务的。因为主从复制要求`slave`不能写只能读（如果对`slave`执行写操作，那么`show slave status`将会呈现`Slave_SQL_Running=NO`，此时你需要按照前面提到的手动同步一下`slave`）。

**方案一**

- 使用mysql-proxy代理
- 优点：直接实现读写分离和负载均衡，不用修改代码，master和slave用一样的帐号，mysql官方不建议实际生产中使用
- 缺点：降低性能， 不支持事务

**方案二**

- 使用AbstractRoutingDataSource+aop+annotation在dao层决定数据源。
- 如果采用了mybatis， 可以将读写分离放在ORM层，比如mybatis可以通过mybatis plugin拦截sql语句，所有的insert/update/delete都访问master库，所有的select 都访问salve库，这样对于dao层都是透明。 plugin实现时可以通过注解或者分析语句是读写方法来选定主从库。不过这样依然有一个问题， 也就是不支持事务， 所以我们还需要重写一下DataSourceTransactionManager， 将read-only的事务扔进读库， 其余的有读有写的扔进写库。

**方案三**

- 使用AbstractRoutingDataSource+aop+annotation在service层决定数据源，可以支持事务.
- 缺点：类内部方法通过this.xx()方式相互调用时，aop不会进行拦截，需进行特殊处理。

### 备份计划，mysqldump以及xtranbackup的实现原理

- **(1)备份计划**
  - 视库的大小来定，一般来说 100G 内的库，可以考虑使用 mysqldump 来做，因为 mysqldump更加轻巧灵活，备份时间选在业务低峰期，可以每天进行都进行全量备份(mysqldump 备份出来的文件比较小，压缩之后更小)。
  - 100G 以上的库，可以考虑用 xtranbackup 来做，备份速度明显要比 mysqldump 要快。一般是选择一周一个全备，其余每天进行增量备份，备份时间为业务低峰期。
- **(2)备份恢复时间**
  - 物理备份恢复快，逻辑备份恢复慢
  - 这里跟机器，尤其是硬盘的速率有关系，以下列举几个仅供参考
  - 20G的2分钟（mysqldump）
  - 80G的30分钟(mysqldump)
  - 111G的30分钟（mysqldump)
  - 288G的3小时（xtra)
  - 3T的4小时（xtra)
  - 逻辑导入时间一般是备份时间的5倍以上
- **(3)备份恢复失败如何处理**
  - 首先在恢复之前就应该做足准备工作，避免恢复的时候出错。比如说备份之后的有效性检查、权限检查、空间检查等。如果万一报错，再根据报错的提示来进行相应的调整。

**(4)mysqldump和xtrabackup实现原理**

- mysqldump

  mysqldump 属于逻辑备份。加入–single-transaction 选项可以进行一致性备份。后台进程会先设置 session 的事务隔离级别为 RR(SET SESSION TRANSACTION ISOLATION LEVELREPEATABLE READ)，之后显式开启一个事务(START TRANSACTION /*!40100 WITH CONSISTENTSNAPSHOT */)，这样就保证了该事务里读到的数据都是事务事务时候的快照。之后再把表的数据读取出来。如果加上–master-data=1 的话，在刚开始的时候还会加一个数据库的读锁(FLUSH TABLES WITH READ LOCK),等开启事务后，再记录下数据库此时 binlog 的位置(showmaster status)，马上解锁，再读取表的数据。等所有的数据都已经导完，就可以结束事务

- Xtrabackup:

  xtrabackup 属于物理备份，直接拷贝表空间文件，同时不断扫描产生的 redo 日志并保存下来。最后完成 innodb 的备份后，会做一个 flush engine logs 的操作(老版本在有 bug，在5.6 上不做此操作会丢数据)，确保所有的 redo log 都已经落盘(涉及到事务的两阶段提交

- 概念，因为 xtrabackup 并不拷贝 binlog，所以必须保证所有的 redo log 都落盘，否则可能会丢最后一组提交事务的数据)。这个时间点就是 innodb 完成备份的时间点，数据文件虽然不是一致性的，但是有这段时间的 redo 就可以让数据文件达到一致性(恢复的时候做的事情)。然后还需要 flush tables with read lock，把 myisam 等其他引擎的表给备份出来，备份完后解锁。这样就做到了完美的热备。

### 数据表损坏的修复方式有哪些？

使用 myisamchk 来修复，具体步骤：

- 1 修复前将mysql服务停止。
- 2 打开命令行方式，然后进入到mysql的/bin目录。
- 3 执行myisamchk –recover 数据库所在路径/*.MYI

```
使用repair table 或者 OPTIMIZE table命令来修复，REPAIR TABLE table\_name 修复表 OPTIMIZE TABLE table\_name 优化表 REPAIR TABLE 用于修复被破坏的表。 OPTIMIZE TABLE 用于回收闲置的数据库空间，当表上的数据行被删除时，所占据的磁盘空间并没有立即被回收，使用了OPTIMIZE TABLE命令后这些空间将被回收，并且对磁盘上的数据行进行重排（注意：是磁盘上，而非数据库）
```



---





目录

- [概述](https://juejin.cn/post/6844904127055527950#heading-0)
  - [什么是Redis？](https://juejin.cn/post/6844904127055527950#heading-1)
  - [Redis有哪些优缺点？](https://juejin.cn/post/6844904127055527950#heading-2)
  - [使用redis有哪些好处？](https://juejin.cn/post/6844904127055527950#heading-3)
  - [为什么要用 Redis / 为什么要用缓存](https://juejin.cn/post/6844904127055527950#heading-4)
  - [为什么要用 Redis 而不用 map/guava 做缓存?](https://juejin.cn/post/6844904127055527950#heading-5)
  - [Redis为什么这么快](https://juejin.cn/post/6844904127055527950#heading-6)
  - [Redis有哪些数据类型](https://juejin.cn/post/6844904127055527950#heading-7)
  - [Redis的应用场景](https://juejin.cn/post/6844904127055527950#heading-8)
  - [持久化](https://juejin.cn/post/6844904127055527950#heading-9)
  - [Redis 的持久化机制是什么？各自的优缺点？](https://juejin.cn/post/6844904127055527950#heading-10)
    - [RDB：是Redis DataBase缩写快照](https://juejin.cn/post/6844904127055527950#heading-11)
    - [AOF：持久化：](https://juejin.cn/post/6844904127055527950#heading-12)
  - [如何选择合适的持久化方式](https://juejin.cn/post/6844904127055527950#heading-13)
  - [Redis持久化数据和缓存怎么做扩容？](https://juejin.cn/post/6844904127055527950#heading-14)
  - [Redis的过期键的删除策略](https://juejin.cn/post/6844904127055527950#heading-15)
  - [Redis key的过期时间和永久有效分别怎么设置？](https://juejin.cn/post/6844904127055527950#heading-16)
  - [我们知道通过expire来设置key 的过期时间，那么对过期的数据怎么处理呢?](https://juejin.cn/post/6844904127055527950#heading-17)
  - [MySQL里有2000w数据，redis中只存20w的数据，如何保证redis中的数据都是热点数据](https://juejin.cn/post/6844904127055527950#heading-18)
  - [Redis的内存淘汰策略有哪些](https://juejin.cn/post/6844904127055527950#heading-19)
  - [Redis主要消耗什么物理资源？](https://juejin.cn/post/6844904127055527950#heading-20)
  - [Redis的内存用完了会发生什么？](https://juejin.cn/post/6844904127055527950#heading-21)
  - [Redis如何做内存优化？](https://juejin.cn/post/6844904127055527950#heading-22)
- [线程模型](https://juejin.cn/post/6844904127055527950#heading-23)
  - [Redis线程模型](https://juejin.cn/post/6844904127055527950#heading-24)
- [事务](https://juejin.cn/post/6844904127055527950#heading-25)
  - [什么是事务？](https://juejin.cn/post/6844904127055527950#heading-26)
  - [Redis事务的概念](https://juejin.cn/post/6844904127055527950#heading-27)
  - [Redis事务的三个阶段](https://juejin.cn/post/6844904127055527950#heading-28)
  - [Redis事务相关命令](https://juejin.cn/post/6844904127055527950#heading-29)
  - [事务管理（ACID）概述](https://juejin.cn/post/6844904127055527950#heading-30)
  - [Redis事务支持隔离性吗](https://juejin.cn/post/6844904127055527950#heading-31)
  - [Redis事务保证原子性吗，支持回滚吗](https://juejin.cn/post/6844904127055527950#heading-32)
  - [Redis事务其他实现](https://juejin.cn/post/6844904127055527950#heading-33)
- [集群方案](https://juejin.cn/post/6844904127055527950#heading-34)
  - [1、哨兵模式](https://juejin.cn/post/6844904127055527950#heading-35)
  - [2、官方Redis Cluster 方案(服务端路由查询)](https://juejin.cn/post/6844904127055527950#heading-36)
  - [3、基于客户端分配](https://juejin.cn/post/6844904127055527950#heading-37)
  - [4、基于代理服务器分片](https://juejin.cn/post/6844904127055527950#heading-38)
  - [5、Redis 主从架构](https://juejin.cn/post/6844904127055527950#heading-39)
  - [Redis集群的主从复制模型是怎样的？](https://juejin.cn/post/6844904127055527950#heading-40)
  - [生产环境中的 redis 是怎么部署的？](https://juejin.cn/post/6844904127055527950#heading-41)
  - [说说Redis哈希槽的概念？](https://juejin.cn/post/6844904127055527950#heading-42)
  - [Redis集群会有写操作丢失吗？为什么？](https://juejin.cn/post/6844904127055527950#heading-43)
  - [Redis集群之间是如何复制的？](https://juejin.cn/post/6844904127055527950#heading-44)
  - [Redis集群最大节点个数是多少？](https://juejin.cn/post/6844904127055527950#heading-45)
  - [Redis集群如何选择数据库？](https://juejin.cn/post/6844904127055527950#heading-46)
- [分区](https://juejin.cn/post/6844904127055527950#heading-47)
  - [Redis是单线程的，如何提高多核CPU的利用率？](https://juejin.cn/post/6844904127055527950#heading-48)
  - [为什么要做Redis分区？](https://juejin.cn/post/6844904127055527950#heading-49)
  - [你知道有哪些Redis分区实现方案？](https://juejin.cn/post/6844904127055527950#heading-50)
  - [Redis分区有什么缺点？](https://juejin.cn/post/6844904127055527950#heading-51)
- [分布式问题](https://juejin.cn/post/6844904127055527950#heading-52)
  - [Redis实现分布式锁](https://juejin.cn/post/6844904127055527950#heading-53)
  - [如何解决 Redis 的并发竞争 Key 问题](https://juejin.cn/post/6844904127055527950#heading-54)
  - [分布式Redis是前期做还是后期规模上来了再做好？为什么？](https://juejin.cn/post/6844904127055527950#heading-55)
  - [什么是 RedLock](https://juejin.cn/post/6844904127055527950#heading-56)
- [缓存异常](https://juejin.cn/post/6844904127055527950#heading-57)
  - [什么是redis穿透？](https://juejin.cn/post/6844904127055527950#heading-58)
  - [什么是redis雪崩？](https://juejin.cn/post/6844904127055527950#heading-59)
  - [什么是redis穿透？](https://juejin.cn/post/6844904127055527950#heading-60)
  - [缓存预热](https://juejin.cn/post/6844904127055527950#heading-61)
  - [缓存降级](https://juejin.cn/post/6844904127055527950#heading-62)
  - [热点数据和冷数据](https://juejin.cn/post/6844904127055527950#heading-63)
  - [缓存热点key](https://juejin.cn/post/6844904127055527950#heading-64)
- [常用工具](https://juejin.cn/post/6844904127055527950#heading-65)
  - [Redis支持的Java客户端都有哪些？官方推荐用哪个？](https://juejin.cn/post/6844904127055527950#heading-66)
  - [Redis和Redisson有什么关系？](https://juejin.cn/post/6844904127055527950#heading-67)
  - [Jedis与Redisson对比有什么优缺点？](https://juejin.cn/post/6844904127055527950#heading-68)
- [其他问题](https://juejin.cn/post/6844904127055527950#heading-69)
  - [Redis与Memcached的区别](https://juejin.cn/post/6844904127055527950#heading-70)
  - [如何保证缓存与数据库双写时的数据一致性？](https://juejin.cn/post/6844904127055527950#heading-71)
  - [Redis常见性能问题和解决方案？](https://juejin.cn/post/6844904127055527950#heading-72)
  - [Redis官方为什么不提供Windows版本？](https://juejin.cn/post/6844904127055527950#heading-73)
  - [一个字符串类型的值能存储最大容量是多少？](https://juejin.cn/post/6844904127055527950#heading-74)
  - [Redis如何做大量数据插入？](https://juejin.cn/post/6844904127055527950#heading-75)
  - [假如Redis里面有1亿个key，其中有10w个key是以某个固定的已知的前缀开头的，如果将它们全部找出来？](https://juejin.cn/post/6844904127055527950#heading-76)
  - [使用Redis做过异步队列吗，是如何实现的](https://juejin.cn/post/6844904127055527950#heading-77)
  - [Redis如何实现延时队列](https://juejin.cn/post/6844904127055527950#heading-78)
  - [Redis回收进程如何工作的？](https://juejin.cn/post/6844904127055527950#heading-79)
  - [Redis回收使用的是什么算法？](https://juejin.cn/post/6844904127055527950#heading-80)





## 概述

#### 什么是Redis？

- Redis 是一个使用 C 语言写成的，开源的高性能key-value非关系缓存数据库。它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。Redis的数据都基于缓存的，所以很快，每秒可以处理超过 10万次读写操作，是已知性能最快的Key-Value DB。Redis也可以实现数据写入磁盘中，保证了数据的安全不丢失，而且Redis的操作是原子性的。

#### Redis有哪些优缺点？

- 优点
  - 读写性能优异， Redis能读的速度是110000次/s，写的速度是81000次/s。
  - 支持数据持久化，支持AOF和RDB两种持久化方式。
  - 支持事务，Redis的所有操作都是原子性的，同时Redis还支持对几个操作合并后的原子性执行。
  - 数据结构丰富，除了支持string类型的value外还支持hash、set、zset、list等数据结构。
  - 支持主从复制，主机会自动将数据同步到从机，可以进行读写分离。
- 缺点
  - 数据库容量受到物理内存的限制，不能用作海量数据的高性能读写，因此Redis适合的场景主要局限在较小数据量的高性能操作和运算上。
  - Redis 不具备自动容错和恢复功能，主机从机的宕机都会导致前端部分读写请求失败，需要等待机器重启或者手动切换前端的IP才能恢复。
  - 主机宕机，宕机前有部分数据未能及时同步到从机，切换IP后还会引入数据不一致的问题，降低了系统的可用性。
  - Redis 较难支持在线扩容，在集群容量达到上限时在线扩容会变得很复杂。为避免这一问题，运维人员在系统上线时必须确保有足够的空间，这对资源造成了很大的浪费。

#### 使用redis有哪些好处？

- (1) 速度快，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都很低
- (2)支持丰富数据类型，支持string，list，set，sorted set，hash
- (3) 支持事务，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行
- (4) 丰富的特性：可用于缓存，消息，按key设置过期时间，过期后将会自动删除

#### 为什么要用 Redis / 为什么要用缓存

```
主要从“高性能”和“高并发”这两点来看待这个问题。
```

- 高性能：
  - 假如用户第一次访问数据库中的某些数据。这个过程会比较慢，因为是从硬盘上读取的。将该用户访问的数据存在数缓存中，这样下一次再访问这些数据的时候就可以直接从缓存中获取了。操作缓存就是直接操作内存，所以速度相当快。如果数据库中的对应数据改变的之后，同步改变缓存中相应的数据即可！
- 高并发：
  - 直接操作缓存能够承受的请求是远远大于直接访问数据库的，所以我们可以考虑把数据库中的部分数据转移到缓存中去，这样用户的一部分请求会直接到缓存这里而不用经过数据库。

#### 为什么要用 Redis 而不用 map/guava 做缓存?

- 缓存分为本地缓存和分布式缓存。以 Java 为例，使用自带的 map 或者 guava 实现的是本地缓存，最主要的特点是轻量以及快速，生命周期随着 jvm 的销毁而结束，并且在多实例的情况下，每个实例都需要各自保存一份缓存，缓存不具有一致性。
- 使用 redis 或 memcached 之类的称为分布式缓存，在多实例的情况下，各实例共用一份缓存数据，缓存具有一致性。缺点是需要保持 redis 或 memcached服务的高可用，整个程序架构上较为复杂。

#### Redis为什么这么快

- 1、完全基于内存，绝大部分请求是纯粹的内存操作，非常快速。数据存在内存中，类似于 HashMap，HashMap 的优势就是查找和操作的时间复杂度都是O(1)；
- 2、数据结构简单，对数据操作也简单，Redis 中的数据结构是专门进行设计的；
- 3、采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗 CPU，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗；
- 4、使用多路 I/O 复用模型，非阻塞 IO；
- 5、使用底层模型不同，它们之间底层实现方式以及与客户端之间通信的应用协议不一样，Redis 直接自己构建了 VM 机制 ，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求；

#### Redis有哪些数据类型

- Redis主要有5种数据类型，包括String，List，Set，Zset，Hash，满足大部分的使用要求

| 数据类型 | 可以存储的值           | 操作                                                         | 应用场景                                                     |
| -------- | ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| String   | 字符串、整数或者浮点数 | 对整个字符串或者字符串的其中一部分执行操作 对整数和浮点数执行自增或者自减操作 | 做简单的键值对缓存                                           |
| List     | 列表                   | 从两端压入或者弹出元素 对单个或者多个元素进行修剪， 只保留一个范围内的元素 | 存储一些列表型的数据结构，类似粉丝列表、文章的评论列表之类的数据 |
| Set      | 无序集合               | 添加、获取、移除单个元素 检查一个元素是否存在于集合中 计算交集、并集、差集 从集合里面随机获取元素 | 交集、并集、差集的操作，比如交集，可以把两个人的粉丝列表整一个交集 |
| Hash     | 包含键值对的无序散列表 | 添加、获取、移除单个键值对 获取所有键值对 检查某个键是否存在 | 结构化的数据，比如一个对象                                   |
| ZSet     | 有序集合               | 添加、获取、删除元素 根据分值范围或者成员来获取元素 计算一个键的排名 | 去重但可以排序，如获取排名前几名的用户                       |

#### Redis的应用场景

- 计数器

  可以对 String 进行自增自减运算，从而实现计数器功能。Redis 这种内存型数据库的读写性能非常高，很适合存储频繁读写的计数量。

- 缓存

  将热点数据放到内存中，设置内存的最大使用量以及淘汰策略来保证缓存的命中率。

- 会话缓存

  可以使用 Redis 来统一存储多台应用服务器的会话信息。当应用服务器不再存储用户的会话信息，也就不再具有状态，一个用户可以请求任意一个应用服务器，从而更容易实现高可用性以及可伸缩性。

- 全页缓存（FPC）

  除基本的会话token之外，Redis还提供很简便的FPC平台。以Magento为例，Magento提供一个插件来使用Redis作为全页缓存后端。此外，对WordPress的用户来说，Pantheon有一个非常好的插件 wp-redis，这个插件能帮助你以最快速度加载你曾浏览过的页面。

- 查找表

  例如 DNS 记录就很适合使用 Redis 进行存储。查找表和缓存类似，也是利用了 Redis 快速的查找特性。但是查找表的内容不能失效，而缓存的内容可以失效，因为缓存不作为可靠的数据来源。

- 消息队列(发布/订阅功能)

  List 是一个双向链表，可以通过 lpush 和 rpop 写入和读取消息。不过最好使用 Kafka、RabbitMQ 等消息中间件。

- 分布式锁实现

  在分布式场景下，无法使用单机环境下的锁来对多个节点上的进程进行同步。可以使用 Redis 自带的 SETNX 命令实现分布式锁，除此之外，还可以使用官方提供的 RedLock 分布式锁实现。

- 其它

  Set 可以实现交集、并集等操作，从而实现共同好友等功能。ZSet 可以实现有序性操作，从而实现排行榜等功能。

#### 持久化

- 什么是Redis持久化？ 持久化就是把内存的数据写到磁盘中去，防止服务宕机了内存数据丢失。

#### Redis 的持久化机制是什么？各自的优缺点？

- Redis 提供两种持久化机制 RDB（默认） 和 AOF 机制:

##### RDB：是Redis DataBase缩写快照

- RDB是Redis默认的持久化方式。按照一定的时间将内存的数据以快照的形式保存到硬盘中，对应产生的数据文件为dump.rdb。通过配置文件中的save参数来定义快照的周期。



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/1717449419419e78~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- 优点：

  1、只有一个文件 dump.rdb，方便持久化。

  2、容灾性好，一个文件可以保存到安全的磁盘。

  3、性能最大化，fork 子进程来完成写操作，让主进程继续处理命令，所以是 IO 最大化。使用单独子进程来进行持久化，主进程不会进行任何 IO 操作，保证了 redis 的高性能

  4.相对于数据集大时，比 AOF 的启动效率更高。

- 缺点：

  1、数据安全性低。RDB 是间隔一段时间进行持久化，如果持久化之间 redis 发生故障，会发生数据丢失。所以这种方式更适合数据要求不严谨的时候)

  2、AOF（Append-only file)持久化方式： 是指所有的命令行记录以 redis 命令请 求协议的格式完全持久化存储)保存为 aof 文件。

##### AOF：持久化：

- AOF持久化(即Append Only File持久化)，则是将Redis执行的每次写命令记录到单独的日志文件中，当重启Redis会重新将持久化的日志中文件恢复数据。
- 当两种方式同时开启时，数据恢复Redis会优先选择AOF恢复



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744941b1f2a80~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- 优点：

  1、数据安全，aof 持久化可以配置 appendfsync 属性，有 always，每进行一次 命令操作就记录到 aof 文件中一次。

  2、通过 append 模式写文件，即使中途服务器宕机，可以通过 redis-check-aof 工具解决数据一致性问题。

  3、AOF 机制的 rewrite 模式。AOF 文件没被 rewrite 之前（文件过大时会对命令 进行合并重写），可以删除其中的某些命令（比如误操作的 flushall）)

- 缺点：

  1、AOF 文件比 RDB 文件大，且恢复速度慢。

  2、数据集大的时候，比 rdb 启动效率低。

- 俩种持久化的优缺点是什么？

  - AOF文件比RDB更新频率高，优先使用AOF还原数据。
  - AOF比RDB更安全也更大
  - RDB性能比AOF好
  - 如果两个都配了优先加载AOF

#### 如何选择合适的持久化方式

- 一般来说， 如果想达到足以媲美PostgreSQL的数据安全性，你应该同时使用两种持久化功能。在这种情况下，当 Redis 重启的时候会优先载入AOF文件来恢复原始的数据，因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整。
- 如果你非常关心你的数据， 但仍然可以承受数分钟以内的数据丢失，那么你可以只使用RDB持久化。
- 有很多用户都只使用AOF持久化，但并不推荐这种方式，因为定时生成RDB快照（snapshot）非常便于进行数据库备份， 并且 RDB 恢复数据集的速度也要比AOF恢复的速度要快，除此之外，使用RDB还可以避免AOF程序的bug。
- 如果你只希望你的数据在服务器运行的时候存在，你也可以不使用任何持久化方式。

#### Redis持久化数据和缓存怎么做扩容？

- 如果Redis被当做缓存使用，使用一致性哈希实现动态扩容缩容。
- 如果Redis被当做一个持久化存储使用，必须使用固定的keys-to-nodes映射关系，节点的数量一旦确定不能变化。否则的话(即Redis节点需要动态变化的情况），必须使用可以在运行时进行数据再平衡的一套系统，而当前只有Redis集群可以做到这样。

#### Redis的过期键的删除策略

```
我们都知道，Redis是key-value数据库，我们可以设置Redis中缓存的key的过期时间。Redis的过期策略就是指当Redis中缓存的key过期了，Redis如何处理。
```

- 过期策略通常有以下三种：
- 定时过期：每个设置过期时间的key都需要创建一个定时器，到过期时间就会立即清除。该策略可以立即清除过期的数据，对内存很友好；但是会占用大量的CPU资源去处理过期的数据，从而影响缓存的响应时间和吞吐量。
- 惰性过期：只有当访问一个key时，才会判断该key是否已过期，过期则清除。该策略可以最大化地节省CPU资源，却对内存非常不友好。极端情况可能出现大量的过期key没有再次被访问，从而不会被清除，占用大量内存。
- 定期过期：每隔一定的时间，会扫描一定数量的数据库的expires字典中一定数量的key，并清除其中已过期的key。该策略是前两者的一个折中方案。通过调整定时扫描的时间间隔和每次扫描的限定耗时，可以在不同情况下使得CPU和内存资源达到最优的平衡效果。 (expires字典会保存所有设置了过期时间的key的过期时间数据，其中，key是指向键空间中的某个键的指针，value是该键的毫秒精度的UNIX时间戳表示的过期时间。键空间是指该Redis集群中保存的所有键。)

```
Redis中同时使用了惰性过期和定期过期两种过期策略。
```

#### Redis key的过期时间和永久有效分别怎么设置？

- expire和persist命令。

#### 我们知道通过expire来设置key 的过期时间，那么对过期的数据怎么处理呢?

- 除了缓存服务器自带的缓存失效策略之外（Redis默认的有6中策略可供选择），我们还可以根据具体的业务需求进行自定义的缓存淘汰，常见的策略有两种：
  - 1、定时去清理过期的缓存；
  - 2、当有用户请求过来时，再判断这个请求所用到的缓存是否过期，过期的话就去底层系统得到新数据并更新缓存。

```
两者各有优劣，第一种的缺点是维护大量缓存的key是比较麻烦的，第二种的缺点就是每次用户请求过来都要判断缓存失效，逻辑相对比较复杂！具体用哪种方案，大家可以根据自己的应用场景来权衡。
```

#### MySQL里有2000w数据，redis中只存20w的数据，如何保证redis中的数据都是热点数据

- redis内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略。

#### Redis的内存淘汰策略有哪些

```
Redis的内存淘汰策略是指在Redis的用于缓存的内存不足时，怎么处理需要新写入且需要申请额外空间的数据。
```

- 全局的键空间选择性移除
  - noeviction：当内存不足以容纳新写入数据时，新写入操作会报错。
  - allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的key。（这个是最常用的）
  - allkeys-random：当内存不足以容纳新写入数据时，在键空间中，随机移除某个key。
- 设置过期时间的键空间选择性移除
  - volatile-lru：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的key。
  - volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个key。
  - volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的key优先移除。
- 总结

```
Redis的内存淘汰策略的选取并不会影响过期的key的处理。内存淘汰策略用于处理内存不足时的需要申请额外空间的数据；过期策略用于处理过期的缓存数据。
```

#### Redis主要消耗什么物理资源？

- 内存。

#### Redis的内存用完了会发生什么？

- 如果达到设置的上限，Redis的写命令会返回错误信息（但是读命令还可以正常返回。）或者你可以配置内存淘汰机制，当Redis达到内存上限时会冲刷掉旧的内容。

#### Redis如何做内存优化？

- 可以好好利用Hash,list,sorted set,set等集合类型数据，因为通常情况下很多小的Key-Value可以用更紧凑的方式存放到一起。尽可能使用散列表（hashes），散列表（是说散列表里面存储的数少）使用的内存非常小，所以你应该尽可能的将你的数据模型抽象到一个散列表里面。比如你的web系统中有一个用户对象，不要为这个用户的名称，姓氏，邮箱，密码设置单独的key，而是应该把这个用户的所有信息存储到一张散列表里面

## 线程模型

### Redis线程模型

Redis基于Reactor模式开发了网络事件处理器，这个处理器被称为文件事件处理器（file event handler）。它的组成结构为4部分：多个套接字、IO多路复用程序、文件事件分派器、事件处理器。因为文件事件分派器队列的消费是单线程的，所以Redis才叫单线程模型。

- 文件事件处理器使用 I/O 多路复用（multiplexing）程序来同时监听多个套接字， 并根据套接字目前执行的任务来为套接字关联不同的事件处理器。
- 当被监听的套接字准备好执行连接应答（accept）、读取（read）、写入（write）、关闭（close）等操作时， 与操作相对应的文件事件就会产生， 这时文件事件处理器就会调用套接字之前关联好的事件处理器来处理这些事件。

虽然文件事件处理器以单线程方式运行， 但通过使用 I/O 多路复用程序来监听多个套接字， 文件事件处理器既实现了高性能的网络通信模型， 又可以很好地与 redis 服务器中其他同样以单线程方式运行的模块进行对接， 这保持了 Redis 内部单线程设计的简单性。

## 事务

### 什么是事务？

- 事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
- 事务是一个原子操作：事务中的命令要么全部被执行，要么全部都不执行。

### Redis事务的概念

- Redis 事务的本质是通过MULTI、EXEC、WATCH等一组命令的集合。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。在事务执行过程，会按照顺序串行化执行队列中的命令，其他客户端提交的命令请求不会插入到事务执行命令序列中。
- 总结说：redis事务就是一次性、顺序性、排他性的执行一个队列中的一系列命令。

### Redis事务的三个阶段

1. 事务开始 MULTI
2. 命令入队
3. 事务执行 EXEC

```
事务执行过程中，如果服务端收到有EXEC、DISCARD、WATCH、MULTI之外的请求，将会把请求放入队列中排队
```

### Redis事务相关命令

Redis事务功能是通过MULTI、EXEC、DISCARD和WATCH 四个原语实现的

Redis会将一个事务中的所有命令序列化，然后按顺序执行。

1. **redis 不支持回滚**，“Redis 在事务失败时不进行回滚，而是继续执行余下的命令”， 所以 Redis 的内部可以保持简单且快速。
2. **如果在一个事务中的命令出现错误，那么所有的命令都不会执行**；
3. **如果在一个事务中出现运行错误，那么正确的命令会被执行**。

- WATCH 命令是一个乐观锁，可以为 Redis 事务提供 check-and-set （CAS）行为。 可以监控一个或多个键，一旦其中有一个键被修改（或删除），之后的事务就不会执行，监控一直持续到EXEC命令。
- MULTI命令用于开启一个事务，它总是返回OK。 MULTI执行之后，客户端可以继续向服务器发送任意多条命令，这些命令不会立即被执行，而是被放到一个队列中，当EXEC命令被调用时，所有队列中的命令才会被执行。
- EXEC：执行所有事务块内的命令。返回事务块内所有命令的返回值，按命令执行的先后顺序排列。 当操作被打断时，返回空值 nil 。
- 通过调用DISCARD，客户端可以清空事务队列，并放弃执行事务， 并且客户端会从事务状态中退出。
- UNWATCH命令可以取消watch对所有key的监控。

### 事务管理（ACID）概述

- 原子性（Atomicity）
  原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。
- 一致性（Consistency）
  事务前后数据的完整性必须保持一致。
- 隔离性（Isolation）
  多个事务并发执行时，一个事务的执行不应影响其他事务的执行
- 持久性（Durability）
  持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响

```
Redis的事务总是具有ACID中的一致性和隔离性，其他特性是不支持的。当服务器运行在_AOF_持久化模式下，并且appendfsync选项的值为always时，事务也具有耐久性。
```

### Redis事务支持隔离性吗

- Redis 是单进程程序，并且它保证在执行事务时，不会对事务进行中断，事务可以运行直到执行完所有事务队列中的命令为止。因此，**Redis 的事务是总是带有隔离性的**。

### Redis事务保证原子性吗，支持回滚吗

- Redis中，单条命令是原子性执行的，但**事务不保证原子性，且没有回滚**。事务中任意命令执行失败，其余的命令仍会被执行。

### Redis事务其他实现

- 基于Lua脚本，Redis可以保证脚本内的命令一次性、按顺序地执行，
  其同时也不提供事务运行错误的回滚，执行过程中如果部分命令运行错误，剩下的命令还是会继续运行完
- 基于中间标记变量，通过另外的标记变量来标识事务是否执行完成，读取数据时先读取该标记变量判断是否事务执行完成。但这样会需要额外写代码实现，比较繁琐

## 集群方案

### 1、哨兵模式



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744941b28481b~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



**哨兵的介绍**

```
sentinel，中文名是哨兵。哨兵是 redis 集群机构中非常重要的一个组件，主要有以下功能：
```

- 集群监控：负责监控 redis master 和 slave 进程是否正常工作。
- 消息通知：如果某个 redis 实例有故障，那么哨兵负责发送消息作为报警通知给管理员。
- 故障转移：如果 master node 挂掉了，会自动转移到 slave node 上。
- 配置中心：如果故障转移发生了，通知 client 客户端新的 master 地址。

**哨兵用于实现 redis 集群的高可用**，本身也是分布式的，作为一个哨兵集群去运行，互相协同工作。

- 故障转移时，判断一个 master node 是否宕机了，需要大部分的哨兵都同意才行，涉及到了分布式选举的问题。
- 即使部分哨兵节点挂掉了，哨兵集群还是能正常工作的，因为如果一个作为高可用机制重要组成部分的故障转移系统本身是单点的，那就很坑爹了。

**哨兵的核心知识**

- 哨兵至少需要 3 个实例，来保证自己的健壮性。
- 哨兵 + redis 主从的部署架构，是**不保证数据零丢失**的，只能保证 redis 集群的高可用性。
- 对于哨兵 + redis 主从这种复杂的部署架构，尽量在测试环境和生产环境，都进行充足的测试和演练。

### 2、官方Redis Cluster 方案(服务端路由查询)



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744941e2adfa4~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- redis 集群模式的工作原理能说一下么？在集群模式下，redis 的 key 是如何寻址的？分布式寻址都有哪些算法？了解一致性 hash 算法吗？

**简介**

- Redis Cluster是一种服务端Sharding技术，3.0版本开始正式提供。Redis Cluster并没有使用一致性hash，而是采用slot(槽)的概念，一共分成16384个槽。将请求发送到任意节点，接收到请求的节点会将查询请求发送到正确的节点上执行

**方案说明**

1. 通过哈希的方式，将数据分片，每个节点均分存储一定哈希槽(哈希值)区间的数据，默认分配了16384 个槽位
2. 每份数据分片会存储在多个互为主从的多节点上
3. 数据写入先写主节点，再同步到从节点(支持配置为阻塞同步)
4. 同一分片多个节点间的数据不保持一致性
5. 读取数据时，当客户端操作的key没有分配在该节点上时，redis会返回转向指令，指向正确的节点
6. 扩容时时需要需要把旧节点的数据迁移一部分到新节点

- 在 redis cluster 架构下，每个 redis 要放开两个端口号，比如一个是 6379，另外一个就是 加1w 的端口号，比如 16379。
  - 16379 端口号是用来进行节点间通信的，也就是 cluster bus 的东西，cluster bus 的通信，用来进行故障检测、配置更新、故障转移授权。cluster bus 用了另外一种二进制的协议，`gossip` 协议，用于节点间进行高效的数据交换，占用更少的网络带宽和处理时间。

**节点间的内部通信机制**

- 基本通信原理
- 集群元数据的维护有两种方式：集中式、Gossip 协议。redis cluster 节点间采用 gossip 协议进行通信。

**分布式寻址算法**

- hash 算法（大量缓存重建）
- 一致性 hash 算法（自动缓存迁移）+ 虚拟节点（自动负载均衡）
- redis cluster 的 hash slot 算法

**优点**

- 无中心架构，支持动态扩容，对业务透明
- 具备Sentinel的监控和自动Failover(故障转移)能力
- 客户端不需要连接集群所有节点，连接集群中任何一个可用节点即可
- 高性能，客户端直连redis服务，免去了proxy代理的损耗

**缺点**

- 运维也很复杂，数据迁移需要人工干预
- 只能使用0号数据库
- 不支持批量操作(pipeline管道操作)
- 分布式逻辑和存储模块耦合等

### 3、基于客户端分配



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744941eba6627~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

**简介**



- Redis Sharding是Redis Cluster出来之前，业界普遍使用的多Redis实例集群方法。其主要思想是采用哈希算法将Redis数据的key进行散列，通过hash函数，特定的key会映射到特定的Redis节点上。Java redis客户端驱动jedis，支持Redis Sharding功能，即ShardedJedis以及结合缓存池的ShardedJedisPool

**优点**

- 优势在于非常简单，服务端的Redis实例彼此独立，相互无关联，每个Redis实例像单服务器一样运行，非常容易线性扩展，系统的灵活性很强

**缺点**

- 由于sharding处理放到客户端，规模进一步扩大时给运维带来挑战。
- 客户端sharding不支持动态增删节点。服务端Redis实例群拓扑结构有变化时，每个客户端都需要更新调整。连接不能共享，当应用规模增大时，资源浪费制约优化

### 4、基于代理服务器分片



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744941f4fe2e4~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



**简介**

- 客户端发送请求到一个代理组件，代理解析客户端的数据，并将请求转发至正确的节点，最后将结果回复给客户端

**特征**

- 透明接入，业务程序不用关心后端Redis实例，切换成本低
- Proxy 的逻辑和存储的逻辑是隔离的
- 代理层多了一次转发，性能有所损耗

**业界开源方案**

- Twtter开源的Twemproxy
- 豌豆荚开源的Codis

### 5、Redis 主从架构

- 单机的 redis，能够承载的 QPS 大概就在上万到几万不等。对于缓存来说，一般都是用来支撑**读高并发**的。因此架构做成主从(master-slave)架构，一主多从，主负责写，并且将数据复制到其它的 slave 节点，从节点负责读。所有的**读请求全部走从节点**。这样也可以很轻松实现水平扩容，**支撑读高并发**。



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744945c7745d8~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



```
redis replication -> 主从架构 -> 读写分离 -> 水平扩容支撑读高并发
```

**redis replication 的核心机制**

- redis 采用**异步方式**复制数据到 slave 节点，不过 redis2.8 开始，slave node 会周期性地确认自己每次复制的数据量；
- 一个 master node 是可以配置多个 slave node 的；
- slave node 也可以连接其他的 slave node；
- slave node 做复制的时候，不会 block master node 的正常工作；
- slave node 在做复制的时候，也不会 block 对自己的查询操作，它会用旧的数据集来提供服务；但是复制完成的时候，需要删除旧数据集，加载新数据集，这个时候就会暂停对外服务了；
- slave node 主要用来进行横向扩容，做读写分离，扩容的 slave node 可以提高读的吞吐量。

**注意：**

- 如果采用了主从架构，那么建议必须**开启** master node 的持久化，不建议用 slave node 作为 master node 的数据热备，因为那样的话，如果你关掉 master 的持久化，可能在 master 宕机重启的时候数据是空的，然后可能一经过复制， slave node 的数据也丢了。
- 另外，master 的各种备份方案，也需要做。万一本地的所有文件丢失了，从备份中挑选一份 rdb 去恢复 master，这样才能**确保启动的时候，是有数据的**，即使采用了后续讲解的高可用机制，slave node 可以自动接管 master node，但也可能 sentinel 还没检测到 master failure，master node 就自动重启了，还是可能导致上面所有的 slave node 数据被清空。

**redis 主从复制的核心原理**

- 当启动一个 slave node 的时候，它会发送一个 `PSYNC` 命令给 master node。
- 如果这是 slave node 初次连接到 master node，那么会触发一次 `full resynchronization` 全量复制。此时 master 会启动一个后台线程，开始生成一份 `RDB` 快照文件，
- 同时还会将从客户端 client 新收到的所有写命令缓存在内存中。`RDB` 文件生成完毕后， master 会将这个 `RDB` 发送给 slave，slave 会先**写入本地磁盘，然后再从本地磁盘加载到内存**中，
- 接着 master 会将内存中缓存的写命令发送到 slave，slave 也会同步这些数据。
- slave node 如果跟 master node 有网络故障，断开了连接，会自动重连，连接之后 master node 仅会复制给 slave 部分缺少的数据。



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/171744945c8008f8~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

**过程原理**



1. 当从库和主库建立MS关系后，会向主数据库发送SYNC命令
2. 主库接收到SYNC命令后会开始在后台保存快照(RDB持久化过程)，并将期间接收到的写命令缓存起来
3. 当快照完成后，主Redis会将快照文件和所有缓存的写命令发送给从Redis
4. 从Redis接收到后，会载入快照文件并且执行收到的缓存的命令
5. 之后，主Redis每当接收到写命令时就会将命令发送从Redis，从而保证数据的一致

**缺点**

- 所有的slave节点数据的复制和同步都由master节点来处理，会照成master节点压力太大，使用主从从结构来解决

### Redis集群的主从复制模型是怎样的？

- 为了使在部分节点失败或者大部分节点无法通信的情况下集群仍然可用，所以集群使用了主从复制模型，每个节点都会有N-1个复制品

### 生产环境中的 redis 是怎么部署的？

- redis cluster，10 台机器，5 台机器部署了 redis 主实例，另外 5 台机器部署了 redis 的从实例，每个主实例挂了一个从实例，5 个节点对外提供读写服务，每个节点的读写高峰qps可能可以达到每秒 5 万，5 台机器最多是 25 万读写请求/s。
- 机器是什么配置？32G 内存+ 8 核 CPU + 1T 磁盘，但是分配给 redis 进程的是10g内存，一般线上生产环境，redis 的内存尽量不要超过 10g，超过 10g 可能会有问题。
- 5 台机器对外提供读写，一共有 50g 内存。
- 因为每个主实例都挂了一个从实例，所以是高可用的，任何一个主实例宕机，都会自动故障迁移，redis 从实例会自动变成主实例继续提供读写服务。
- 你往内存里写的是什么数据？每条数据的大小是多少？商品数据，每条数据是 10kb。100 条数据是 1mb，10 万条数据是 1g。常驻内存的是 200 万条商品数据，占用内存是 20g，仅仅不到总内存的 50%。目前高峰期每秒就是 3500 左右的请求量。

```
其实大型的公司，会有基础架构的 team 负责缓存集群的运维。
```

### 说说Redis哈希槽的概念？

- Redis集群没有使用一致性hash,而是引入了哈希槽的概念，Redis集群有16384个哈希槽，每个key通过CRC16校验后对16384取模来决定放置哪个槽，集群的每个节点负责一部分hash槽。

### Redis集群会有写操作丢失吗？为什么？

- Redis并不能保证数据的强一致性，这意味这在实际中集群在特定的条件下可能会丢失写操作。

### Redis集群之间是如何复制的？

- 异步复制

### Redis集群最大节点个数是多少？

- 16384个

### Redis集群如何选择数据库？

- Redis集群目前无法做数据库选择，默认在0数据库。

## 分区

### Redis是单线程的，如何提高多核CPU的利用率？

- 可以在同一个服务器部署多个Redis的实例，并把他们当作不同的服务器来使用，在某些时候，无论如何一个服务器是不够的， 所以，如果你想使用多个CPU，你可以考虑一下分片（shard）。

### 为什么要做Redis分区？

- 分区可以让Redis管理更大的内存，Redis将可以使用所有机器的内存。如果没有分区，你最多只能使用一台机器的内存。分区使Redis的计算能力通过简单地增加计算机得到成倍提升，Redis的网络带宽也会随着计算机和网卡的增加而成倍增长。

### 你知道有哪些Redis分区实现方案？

- 客户端分区就是在客户端就已经决定数据会被存储到哪个redis节点或者从哪个redis节点读取。大多数客户端已经实现了客户端分区。
- 代理分区 意味着客户端将请求发送给代理，然后代理决定去哪个节点写数据或者读数据。代理根据分区规则决定请求哪些Redis实例，然后根据Redis的响应结果返回给客户端。redis和memcached的一种代理实现就是Twemproxy
- 查询路由(Query routing) 的意思是客户端随机地请求任意一个redis实例，然后由Redis将请求转发给正确的Redis节点。Redis Cluster实现了一种混合形式的查询路由，但并不是直接将请求从一个redis节点转发到另一个redis节点，而是在客户端的帮助下直接redirected到正确的redis节点。

### Redis分区有什么缺点？

- 涉及多个key的操作通常不会被支持。例如你不能对两个集合求交集，因为他们可能被存储到不同的Redis实例（实际上这种情况也有办法，但是不能直接使用交集指令）。
- 同时操作多个key,则不能使用Redis事务.
- 分区使用的粒度是key，不能使用一个非常长的排序key存储一个数据集（The partitioning granularity is the key, so it is not possible to shard a dataset with a single huge key like a very big sorted set）
- 当使用分区的时候，数据处理会非常复杂，例如为了备份你必须从不同的Redis实例和主机同时收集RDB / AOF文件。
- 分区时动态扩容或缩容可能非常复杂。Redis集群在运行时增加或者删除Redis节点，能做到最大程度对用户透明地数据再平衡，但其他一些客户端分区或者代理分区方法则不支持这种特性。然而，有一种预分片的技术也可以较好的解决这个问题。

## 分布式问题

### Redis实现分布式锁

- Redis为单进程单线程模式，采用队列模式将并发访问变成串行访问，且多客户端对Redis的连接并不存在竞争关系Redis中可以使用setNx命令实现分布式锁。
- 当且仅当 key 不存在，将 key 的值设为 value。 若给定的 key 已经存在，则 setNx不做任何动作
- SETNX 是『SET if Not eXists』(如果不存在，则 SET)的简写。
- 返回值：设置成功，返回 1 。设置失败，返回 0 。



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/14/1717449461a98262~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- 使用setNx完成同步锁的流程及事项如下：
- 使用SETNX命令获取锁，若返回0（key已存在，锁已存在）则获取失败，反之获取成功
- 为了防止获取锁后程序出现异常，导致其他线程/进程调用setNx命令总是返回0而进入死锁状态，需要为该key设置一个“合理”的过期时间释放锁，使用DEL命令将锁数据删除

### 如何解决 Redis 的并发竞争 Key 问题

- 所谓 Redis 的并发竞争 Key 的问题也就是多个系统同时对一个 key 进行操作，但是最后执行的顺序和我们期望的顺序不同，这样也就导致了结果的不同！
- 推荐一种方案：分布式锁（zookeeper 和 redis 都可以实现分布式锁）。（如果不存在 Redis 的并发竞争 Key 问题，不要使用分布式锁，这样会影响性能）
- 基于zookeeper临时有序节点可以实现的分布式锁。大致思想为：每个客户端对某个方法加锁时，在zookeeper上的与该方法对应的指定节点的目录下，生成一个唯一的瞬时有序节点。 判断是否获取锁的方式很简单，只需要判断有序节点中序号最小的一个。 当释放锁的时候，只需将这个瞬时节点删除即可。同时，其可以避免服务宕机导致的锁无法释放，而产生的死锁问题。完成业务流程后，删除对应的子节点释放锁。

```
在实践中，当然是从以可靠性为主。所以首推Zookeeper。
```

### 分布式Redis是前期做还是后期规模上来了再做好？为什么？

- 既然Redis是如此的轻量（单实例只使用1M内存），为防止以后的扩容，最好的办法就是一开始就启动较多实例。即便你只有一台服务器，你也可以一开始就让Redis以分布式的方式运行，使用分区，在同一台服务器上启动多个实例。
- 一开始就多设置几个Redis实例，例如32或者64个实例，对大多数用户来说这操作起来可能比较麻烦，但是从长久来看做这点牺牲是值得的。
- 这样的话，当你的数据不断增长，需要更多的Redis服务器时，你需要做的就是仅仅将Redis实例从一台服务迁移到另外一台服务器而已（而不用考虑重新分区的问题）。一旦你添加了另一台服务器，你需要将你一半的Redis实例从第一台机器迁移到第二台机器。

### 什么是 RedLock

- Redis 官方站提出了一种权威的基于 Redis 实现分布式锁的方式名叫

   

  Redlock

  ，此种方式比原先的单节点的方法更安全。它可以保证以下特性：

  1. 安全特性：互斥访问，即永远只有一个 client 能拿到锁
  2. 避免死锁：最终 client 都可能拿到锁，不会出现死锁的情况，即使原本锁住某资源的 client crash 了或者出现了网络分区
  3. 容错性：只要大部分 Redis 节点存活就可以正常提供服务

## 缓存异常

#### 什么是redis穿透？

- 就是用户请求透过redis去请求mysql服务器，导致mysql压力过载。但一个web服务里，极容易出现瓶颈的就是mysql，所以才让redis去分担mysql 的压力，所以这种问题是万万要避免的
- 解决方法：
  1. 从缓存取不到的数据，在数据库中也没有取到，这时也可以将key-value对写为key-null，缓存有效时间可以设置短点，如30秒（设置太长会导致正常情况也没法使用）。这样可以防止攻击用户反复用同一个id暴力攻击
  2. 接口层增加校验，如用户鉴权校验，id做基础校验，id<=0的直接拦截；
  3. 采用布隆过滤器，将所有可能存在的数据哈希到一个足够大的 bitmap 中，一个一定不存在的数据会被这个 bitmap 拦截掉，从而避免了对底层存储系统的查询压力



#### 什么是redis雪崩？

- 就是redis服务由于负载过大而宕机，导致mysql的负载过大也宕机，最终整个系统瘫痪
- 解决方法：
  1. redis集群，将原来一个人干的工作，分发给多个人干
  2. 缓存预热（关闭外网访问，先开启mysql，通过预热脚本将热点数据写入缓存中，启动缓存。开启外网服务）
  3. 数据不要设置相同的生存时间，不然过期时，redis压力会大

#### 什么是redis穿透？

- 高并发下，由于一个key失效，而导致多个线程去mysql查同一业务数据并存到redis（并发下，存了多份数据），而一段时间后，多份数据同时失效。导致压力骤增
- 解决方法：
  1. 分级缓存（缓存两份数据，第二份数据生存时间长一点作为备份，第一份数据用于被请求命中，如果第二份数据被命中说明第一份数据已经过期，要去mysql请求数据重新缓存两份数据）
  2. 计划任务（假如数据生存时间为30分钟，计划任务就20分钟执行一次更新缓存数据）

### 缓存预热

- **缓存预热**就是系统上线后，将相关的缓存数据直接加载到缓存系统。这样就可以避免在用户请求的时候，先查询数据库，然后再将数据缓存的问题！用户直接查询事先被预热的缓存数据！
- **解决方案**
  1. 直接写个缓存刷新页面，上线时手工操作一下；
  2. 数据量不大，可以在项目启动的时候自动进行加载；
  3. 定时刷新缓存；

### 缓存降级

- 当访问量剧增、服务出现问题（如响应时间慢或不响应）或非核心服务影响到核心流程的性能时，仍然需要保证服务还是可用的，即使是有损服务。系统可以根据一些关键数据进行自动降级，也可以配置开关实现人工降级。
- **缓存降级**的最终目的是保证核心服务可用，即使是有损的。而且有些服务是无法降级的（如加入购物车、结算）。
- 在进行降级之前要对系统进行梳理，看看系统是不是可以丢卒保帅；从而梳理出哪些必须誓死保护，哪些可降级；比如可以参考日志级别设置预案：
  1. 一般：比如有些服务偶尔因为网络抖动或者服务正在上线而超时，可以自动降级；
  2. 警告：有些服务在一段时间内成功率有波动（如在95~100%之间），可以自动降级或人工降级，并发送告警；
  3. 错误：比如可用率低于90%，或者数据库连接池被打爆了，或者访问量突然猛增到系统能承受的最大阀值，此时可以根据情况自动降级或者人工降级；
  4. 严重错误：比如因为特殊原因数据错误了，此时需要紧急人工降级。
- 服务降级的目的，是为了防止Redis服务故障，导致数据库跟着一起发生雪崩问题。因此，对于不重要的缓存数据，可以采取服务降级策略，例如一个比较常见的做法就是，Redis出现问题，不去数据库查询，而是直接返回默认值给用户。

### 热点数据和冷数据

- 热点数据，缓存才有价值
- 对于冷数据而言，大部分数据可能还没有再次访问到就已经被挤出内存，不仅占用内存，而且价值不大。频繁修改的数据，看情况考虑使用缓存
- 对于热点数据，比如我们的某IM产品，生日祝福模块，当天的寿星列表，缓存以后可能读取数十万次。再举个例子，某导航产品，我们将导航信息，缓存以后可能读取数百万次。
- 数据更新前至少读取两次，缓存才有意义。这个是最基本的策略，如果缓存还没有起作用就失效了，那就没有太大价值了。
- 那存不存在，修改频率很高，但是又不得不考虑缓存的场景呢？有！比如，这个读取接口对数据库的压力很大，但是又是热点数据，这个时候就需要考虑通过缓存手段，减少数据库的压力，比如我们的某助手产品的，点赞数，收藏数，分享数等是非常典型的热点数据，但是又不断变化，此时就需要将数据同步保存到Redis缓存，减少数据库压力。

### 缓存热点key

- 缓存中的一个Key(比如一个促销商品)，在某个时间点过期的时候，恰好在这个时间点对这个Key有大量的并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。

**解决方案**

- 对缓存查询加锁，如果KEY不存在，就加锁，然后查DB入缓存，然后解锁；其他进程如果发现有锁就等待，然后等解锁后返回数据或者进入DB查询

## 常用工具

### Redis支持的Java客户端都有哪些？官方推荐用哪个？

- Redisson、Jedis、lettuce等等，官方推荐使用Redisson。

### Redis和Redisson有什么关系？

- Redisson是一个高级的分布式协调Redis客服端，能帮助用户在分布式环境中轻松实现一些Java的对象 (Bloom filter, BitSet, Set, SetMultimap, ScoredSortedSet, SortedSet, Map, ConcurrentMap, List, ListMultimap, Queue, BlockingQueue, Deque, BlockingDeque, Semaphore, Lock, ReadWriteLock, AtomicLong, CountDownLatch, Publish / Subscribe, HyperLogLog)。

### Jedis与Redisson对比有什么优缺点？

- Jedis是Redis的Java实现的客户端，其API提供了比较全面的Redis命令的支持；Redisson实现了分布式和可扩展的Java数据结构，和Jedis相比，功能较为简单，不支持字符串操作，不支持排序、事务、管道、分区等Redis特性。Redisson的宗旨是促进使用者对Redis的关注分离，从而让使用者能够将精力更集中地放在处理业务逻辑上。

## 其他问题

### Redis与Memcached的区别

- 两者都是非关系型内存键值数据库，现在公司一般都是用 Redis 来实现缓存，而且 Redis 自身也越来越强大了！Redis 与 Memcached 主要有以下不同：

> | 对比参数         | Redis                                                        | Memcached                                                    |
> | ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
> | 类型             | 1. 支持内存 2. 非关系型数据库                                | 1. 支持内存 2. 键值对形式 3. 缓存形式                        |
> | 数据存储类型     | 1. String 2. List 3. Set 4. Hash 5. Sort Set 【俗称ZSet】    | 1. 文本型 2. 二进制类型                                      |
> | 查询【操作】类型 | 1. 批量操作 2. 事务支持 3. 每个类型不同的CRUD                | 1.常用的CRUD 2. 少量的其他命令                               |
> | 附加功能         | 1. 发布/订阅模式 2. 主从分区 3. 序列化支持 4. 脚本支持【Lua脚本】 | 1. 多线程服务支持                                            |
> | 网络IO模型       | 1. 单线程的多路 IO 复用模型                                  | 1. 多线程，非阻塞IO模式                                      |
> | 事件库           | 自封转简易事件库AeEvent                                      | 贵族血统的LibEvent事件库                                     |
> | 持久化支持       | 1. RDB 2. AOF                                                | 不支持                                                       |
> | 集群模式         | 原生支持 cluster 模式，可以实现主从复制，读写分离            | 没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据 |
> | 内存管理机制     | 在 Redis 中，并不是所有数据都一直存储在内存中，可以将一些很久没用的 value 交换到磁盘 | Memcached 的数据则会一直在内存中，Memcached 将内存分割成特定长度的块来存储数据，以完全解决内存碎片的问题。但是这种方式会使得内存的利用率不高，例如块的大小为 128 bytes，只存储 100 bytes 的数据，那么剩下的 28 bytes 就浪费掉了。 |
> | 适用场景         | 复杂数据结构，有持久化，高可用需求，value存储内容较大        | 纯key-value，数据量非常大，并发量非常大的业务                |

1. memcached所有的值均是简单的字符串，redis作为其替代者，支持更为丰富的数据类型
2. redis的速度比memcached快很多
3. redis可以持久化其数据

### 如何保证缓存与数据库双写时的数据一致性？

- 你只要用缓存，就可能会涉及到缓存与数据库双存储双写，你只要是双写，就一定会有数据一致性的问题，那么你如何解决一致性问题？
- 一般来说，就是如果你的系统不是严格要求缓存+数据库必须一致性的话，缓存可以稍微的跟数据库偶尔有不一致的情况，最好不要做这个方案，读请求和写请求串行化，串到一个内存队列里去，这样就可以保证一定不会出现不一致的情况
- 串行化之后，就会导致系统的吞吐量会大幅度的降低，用比正常情况下多几倍的机器去支撑线上的一个请求。
- 还有一种方式就是可能会暂时产生不一致的情况，但是发生的几率特别小，就是**先更新数据库，然后再删除缓存。**

| 问题场景                                       | 描述                                                         | 解决                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 先写缓存，再写数据库，缓存写成功，数据库写失败 | 缓存写成功，但写数据库失败或者响应延迟，则下次读取（并发读）缓存时，就出现脏读 | 这个写缓存的方式，本身就是错误的，需要改为先写数据库，把旧缓存置为失效；读取数据的时候，如果缓存不存在，则读取数据库再写缓存 |
| 先写数据库，再写缓存，数据库写成功，缓存写失败 | 写数据库成功，但写缓存失败，则下次读取（并发读）缓存时，则读不到数据 | 缓存使用时，假如读缓存失败，先读数据库，再回写缓存的方式实现 |
| 需要缓存异步刷新                               | 指数据库操作和写缓存不在一个操作步骤中，比如在分布式场景下，无法做到同时写缓存或需要异步刷新（补救措施）时候 | 确定哪些数据适合此类场景，根据经验值确定合理的数据不一致时间，用户数据刷新的时间间隔 |

### Redis常见性能问题和解决方案？

1. Master最好不要做任何持久化工作，包括内存快照和AOF日志文件，特别是不要启用内存快照做持久化。
2. 如果数据比较关键，某个Slave开启AOF备份数据，策略为每秒同步一次。
3. 为了主从复制的速度和连接的稳定性，Slave和Master最好在同一个局域网内。
4. 尽量避免在压力较大的主库上增加从库
5. Master调用BGREWRITEAOF重写AOF文件，AOF在重写的时候会占大量的CPU和内存资源，导致服务load过高，出现短暂服务暂停现象。
6. 为了Master的稳定性，主从复制不要用图状结构，用单向链表结构更稳定，即主从关系为：Master<–Slave1<–Slave2<–Slave3…，这样的结构也方便解决单点故障问题，实现Slave对Master的替换，也即，如果Master挂了，可以立马启用Slave1做Master，其他不变。

### Redis官方为什么不提供Windows版本？

- 因为目前Linux版本已经相当稳定，而且用户量很大，无需开发windows版本，反而会带来兼容性等问题。

### 一个字符串类型的值能存储最大容量是多少？

- 512M

### Redis如何做大量数据插入？

- Redis2.6开始redis-cli支持一种新的被称之为pipe mode的新模式用于执行大量数据插入工作。

### 假如Redis里面有1亿个key，其中有10w个key是以某个固定的已知的前缀开头的，如果将它们全部找出来？

- 使用keys指令可以扫出指定模式的key列表。
- 对方接着追问：如果这个redis正在给线上的业务提供服务，那使用keys指令会有什么问题？
  这个时候你要回答redis关键的一个特性：redis的单线程的。keys指令会导致线程阻塞一段时间，线上服务会停顿，直到指令执行完毕，服务才能恢复。这个时候可以使用scan指令，scan指令可以无阻塞的提取出指定模式的key列表，但是会有一定的重复概率，在客户端做一次去重就可以了，但是整体所花费的时间会比直接用keys指令长。

### 使用Redis做过异步队列吗，是如何实现的

- 使用list类型保存数据信息，rpush生产消息，lpop消费消息，当lpop没有消息时，可以sleep一段时间，然后再检查有没有信息，如果不想sleep的话，可以使用blpop, 在没有信息的时候，会一直阻塞，直到信息的到来。redis可以通过pub/sub主题订阅模式实现一个生产者，多个消费者，当然也存在一定的缺点，当消费者下线时，生产的消息会丢失。

### Redis如何实现延时队列

- 使用sortedset，使用时间戳做score, 消息内容作为key,调用zadd来生产消息，消费者使用zrangbyscore获取n秒之前的数据做轮询处理。

### Redis回收进程如何工作的？

1. 一个客户端运行了新的命令，添加了新的数据。
2. Redis检查内存使用情况，如果大于maxmemory的限制， 则根据设定好的策略进行回收。
3. 一个新的命令被执行，等等。
4. 所以我们不断地穿越内存限制的边界，通过不断达到边界然后不断地回收回到边界以下。

```
如果一个命令的结果导致大量内存被使用（例如很大的集合的交集保存到一个新的键），不用多久内存限制就会被这个内存使用量超越。
```

### Redis回收使用的是什么算法？

- LRU算法





















