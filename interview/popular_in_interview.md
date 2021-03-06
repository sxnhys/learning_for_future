# 后端面试知识点

[TOC]

---
## 一、计算机网络
### 1、OSI 七层模型、TCP/IP 五层模型、TCP/IP 四层模型。每一层的常见协议。

![计算机网络模型](https://i.imgur.com/jlj8vcM.png)

> **PS：集线器、交换机、路由器**    
> **集线器**：物理层，共享带宽    
> **交换机**：数据链路层，交换式集线器，独享带宽，利用物理地址（MAC地址）来确定转发数据的目的地址，不能分割广播域     
> **路由器**：网络层，利用IP地址来确定数据转发的地址，可以分割广播域，提供了防火墙的服务    

**常见协议**
> **应用层**     
> HTTP：超文本传输协议     
> HTTPS：超文本传输安全协议     
> DNS：域名系统     
> SMTP：简单邮件传输协议     
> POP3：邮局协议第3版     
> Telent：远程终端协议     
> FTP：文件传输协议     
> 
> **传输层**     
> TCP：传输控制协议     
> UDP：用户数据报协议     
> 
> **网络层**
> IP：互联网协议     
> RIP：路由信息协议     
> ICMP：互联网控制信息协议（ping）     
> 
> **网络接口层**     
> ARP/RARP：地址解析协议 / 逆向地址解析协议
> 
> **会话层**（拎出来是因为有两个重要的协议）     
> SSL：安全套接字层协议，HTTPS 的基础     
> RPC：远程过程调用协议     

### 2、TCP 和 UDP 的区别

TCP 、UDP 报文头中包含**源端口号**和**目的端口号**，唯一标识主机中的进程（一个端口同一时刻只能运行一个进程，只要不是需要一直占用同一个端口号的进程都可以共享一个端口）。

**TCP（传输控制协议）** （对比打电话）

> - **面向连接**的（打电话）
> - 提供**可靠交付**的服务，无差错、不丢失、不重复、按序到达
> - **一对一**，每条连接只能是点对点
> - 提供**全双工通信**，TCP 连接的两端都设有发送缓存和接收缓存，用来临时存放双方通信的数据
> - **面向字节流**，应用程序和 TCP 的交互是一次一个数据块，TCP 把应用程序发出的数据看成是一连串无结构的字节流
> - **首部开销大**，需要20~60个字节
> - 传输速率慢，消耗资源多

**UDP（用户数据报协议）**（对比发短信）

> - **无连接**（发短信）
> - **不保证数据传输的可靠性**，尽最大努力交付
> - 支持**一对一、一对多、多对一、多对多**
> - **没有拥塞控制**，网络阻塞不会降低源主机的发送速率，适用实时服务
> - **面向数据报**
> - **首部开销小**，只有8个字节
> - 传输速率快，消耗资源少

**PS:**
> **单工通信**：只支持信号在一个方向上传输，不可逆向     
> **半双工通信**：允许信号在两个方向传输，但是同一时刻只允许信号在一个信道上单向传输     
> **（全）双工通信**：有两个信道，允许信号同时双向传输     

### 3、TCP 协议如何保证可靠传输
- **数据分块**：应用数据被分割成 TCP 认为最适合发送的数据块。将应用层的数据流分割成报文段并发送给目标节点的 TCP 层。
- **数据包编号**：TCP 给发送的每一个包进行编号，接收方对数据包进行排序，把有序数据传送给应用层
- **校验和**：TCP 将保持它首部和数据的检验和。这是一个端到端的检验和，目的是检测数据在传输过程中的任何变化。如果接收端收到的检验和有差错，TCP 将丢弃这个报文段和不确认收到此报文段
- **去冗余**：TCP 的接收端会丢弃重复的数据
- **流量控制**：TCP 连接的每一方都有固定大小的缓冲空间，TCP的接收端只允许发送端发送接收端缓冲区能接纳的数据。当接收方来不及处理发送方的数据，能提示发送方降低发送的速率，防止包丢失。TCP 使用的流量控制协议是可变大小的**滑动窗口**协议。 （TCP 利用滑动窗口实现流量控制）
- **拥塞控制**： 当网络拥塞时，减少数据的发送，发送方维持一个拥塞窗口的状态变量，采用四种算法：**慢开始**、**拥塞避免**、**快重传和快恢复**
- **停止等待协议**：每发完一个分组就停止发送，等待对方确认。在收到确认后再发下一个分组（当发送窗口和接收窗口的大小都等于1时，就是停止等待协议，所以是一种特殊的滑动窗口协议）。**超时重传** —— 当 TCP 发出一个段后，它启动一个定时器，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重发这个报文段。

**RTT：** 发送一个数据包到收到对应的 ACK 所花费的时间

**RTO：** 重传时间间隔（根据 RTT 计算的）

TCP 使用 **[滑动窗口](https://blog.csdn.net/yao5hed/article/details/81046945)** 做 **流量控制** 和 **乱序重排**（保证 TCP 的流控特性和可靠性）

### 4、TCP 三次握手和四次挥手
> **SYN**：同步序列编号，TCP 建立连接时使用的握手信号     
> **ACK**：命令正确应答，确认字符

**三次握手**     

![](https://i.imgur.com/kEAnzwq.jpg)

**第一次**：客户端 ---> 服务端，发送带有 SYN 标志的数据包，序列号 x，并进入 SYN_SEND 状态，等待服务器确认。      
**第二次**：服务端 ---> 客户端，服务器收到 SYN包，然后需要做两件事，一是确认客户的 SYN，二是发送自己的 SYN，即发送带有 SYN/ACK 标志的数据包，确认号 x+1，序列号 y，服务器进入 SYN_RECV 状态。     
**第三次**：客户端 ---> 服务端，客户端收到 SYN/ACK 包，向服务器发送带有 ACK 标志的数据包，确认号 y+1, 序列号 x+1，双方进入 ESTABLISHED 状态   （可以携带数据，会消耗序号，即序列号会变成 x + 数据长度）

**为什么要三次握手**     
三次握手的目的是建立可靠的通信信道，通信即数据的发送和接收，所以三次握手最终要保证**双方都要确认自己和对方的数据发送和接收都是正常的**。此外，如果不进行三次握手，出现了超时重传，那么服务器就会打开两个连接。如果有第三次握手，客户端会忽略服务器之后发送的对滞留连接请求的连接确认，不进行第三次握手，因此就不会再次打开连接。     

**为什么第二次握手传回 SYN**     
接收端为了告诉发送端，我接收到的信息确实就是你发送的信号。     

**第一次握手传了 SYN，为什么第三次握手还要传 ACK**     
双方通信无误必须是两者互相发送数据都无误。第一次握手传了 SYN 只能证明发送方到接收方的通道没有问题，但是接收方道发送方的通道还需要 ACK 信号来验证。     

**第一次握手的隐患**

服务器收到客户端的 SYN 后回复了 SYN/ACK 但一直未收到客户端 ACK 确认，造成 **SYN 超时**，服务器会不断重试回复请求直到超过时限（Linux 默认5次重连，每次等待 ACK 时限为上一次两倍，一共会等待 63 秒才会断开连接），会导致 SYN Flood 攻击（一种DDOS）。

防护措施：SYN 队列满后，tcp_syncookies 参数（linux）回发 SYN Cookie（源端口、目的端口，时间戳等）；恶意攻击不会回传 SYN Cookie，而正常连接会，直接通过 Cookie 建立连接。

**建立连接后客户端出现故障**

TCP 的 **保活机制**，向对方发送保活探测报文，若未收到则继续发送，直到发送次数达到最大限制，连接中断。

**四次挥手**     

![](https://i.imgur.com/O3xiJYm.jpg)

**第一次**：客户端发送一个 FIN 到服务端，用来关闭客户端到服务端的数据传送，客户端进入 FIN_WAIT_1 状态     
**第二次**：服务端收到 FIN，返回 ACK，确认序号为收到的序号加1。此时 TCP 处于半连接（关闭）状态，服务器进入 CLOSE_WAIT 状态（C 不能到 S，S 能到 C）

**期间服务器可以继续像客户端发送数据。**  此时客户端进入 FIN_WAIT_2 状态。   
**第三次**：服务端发送一个 FIN 到客户端，关闭与客户端的连接，服务器进入 LAST_ACK 状态     
**第四次**：客户端收到 FIN，返回 ACK，确认序号为收到的序号加1，客户端进入TIME_WAIT （2MSL）状态，服务端收到确认后释放连接，进入 CLOSE 状态。客户端发出确认后等待 2MSL（最大报文存活时间） 后释放连接。     

**为什么要四次挥手**     
TCP 全双工，发送方和接收方都需要 FIN 报文和 ACK 报文。任何一方都可以在数据传送结束后发出连接释放的通知，待对方确认后进入半关闭状态。当另一方也没有数据再发送的时候，则发出连接释放通知，对方确认后就完全关闭了TCP连接。     

**客户端收到服务端的 FIN 发送确认后需要等待 2MSL**     
- 确保最后一个确认报文能够到达，处理服务端未收到确认报文后重发释放请求的情形（一来一回即2MSL）     
- （避免新旧连接混淆）让本连接持续时间内所产生的所有报文都从网络中消失，使得下一个新的连接不会出现旧的连接请求报文      

**服务器出现大量 CLOSE_WAIT 状态**

我方忙于读或写，没有及时关闭连接。一般是代码问题，尤其是释放资源的代码；配置问题，尤其是处理请求的线程配置。

### 5、浏览器输入网址到显示页面的过程，并涉及到哪些协议
**过程：**
1. **DNS 解析**，浏览器查找域名的 IP 地址，查找过程：浏览器缓存、系统缓存、路由器缓存、IPS服务器缓存、根域名服务器缓存、顶级域名服务器缓存
2. **TCP 连接**
3. 浏览器向 web 服务器**发送一个 HTTP 请求**，cookies 随着请求发送给服务器
4. 服务器**处理请求**，包括请求方式、参数、cookies，最后生成一个HTML响应，**返回 HTTP 报文**
5. 浏览器**解析渲染页面**
6. 连接结束

**涉及到的协议：**     
**DNS**：获取域名对应的 IP 地址     
**TCP**：与服务器建立 TCP 连接 （客户端 TCP 将 HTPP 请求报文分割成报文段并编号，按序可靠地发送给服务端；服务端那边按序重组报文段）     
**IP**：搜索服务端地址，一边中转一边传送     
**OPSF**：IP 数据包在路由器之间进行路由选择     
**ARP**：路由器与服务器通信时，将 ip 地址转换为 MAC 地址     
**HTTP**：生成针对目标 Web 服务器的 HTTP 请求报文     

### 6、HTTP 长连接和短连接
> HTTP 协议的长连接和短连接，实际上就是 TCP 协议的长连接和短连接

**短连接**：客户端和服务器**每进行一次HTTP操作，就建立一次连接**，任务结束就中断连接。当客户端浏览器访问的某个Web资源（HTML、JS、图片等），浏览器就会重新建立一个HTTP会话。HTTP/1.0 默认短连接。     
**长连接**：当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的**TCP连接不会关闭**，客户端再次访问这个服务器时，会继续使用这一条已经建立的连接。HTTP 响应头中有 `Connection:keep-alive` 这行代码，Keep-Alive不会永久保持连接，它有一个保持时间，可以在不同的服务器软件（如Apache）中设定这个时间。实现长连接需要客户端和服务端都支持长连接。HTTP/1.1起，默认使用长连接。     

### 7、HTTP 和 HTTPS 的区别
> **HTTPS**：是以安全为目标的HTTP通道，即HTTP的安全版，即HTTP下加入 **SSL** 层，HTTPS的安全基础是 **SSL**，因此加密的详细内容就需要 **SSL** （TSL，传输层安全协议，在SSL3.0基础上设计），采用 **身份验证** 和 **数据加密**
> HTTPS 协议的主要作用：建立一个信息安全通道，来**保证数据传输的安全**；**确认网站的真实性**。
>
> HTTPS 数据传输流程：
>
> - 浏览器将支持的加密算法信息发送发送给服务器
> - 服务器选择一套浏览器支持的加密算法，以证书的形式回发给浏览器（证书信息包括证书颁发的CA机构、证书有效期、公钥、签名等）
> - 浏览器验证证书的合法性，并结合证书公钥加密信息发送给服务器
> - 服务器使用私钥解密信息，验证哈希，加密响应消息回发给浏览器
> - 浏览器解密响应消息，并对消息进行验证，之后进行加密交互数据

HTTP 和 HTTPS 的区别： 
1. https 协议需要到 ca **申请证书**，一般免费证书较少，因而需要一定费用。
2. http 是超文本传输协议，信息是明文传输，https则是具有安全性的ssl**加密传输**协议。
3. http 和 https 使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是**443**。
4. http 的连接很简单，是无状态的；HTTPS 协议是由 SSL + HTTP 协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。

PS：HTTPS 也未必很安全，浏览器默认填充 http:// ，导致跳转，会有被劫持的风险

### 8、HTTP 状态码
| 状态码 | 类别 | 原因 |
| ------ | ------ | ------ |
| 1XX | Informational（信息性状态码） | 接收的请求正在处理 |
| 2XX | Success（成功状态码） | 接收正常处理完毕 |
| 3XX | Redirection（重定向状态码） | 需要进行附加操作完成请求 |
| 4XX | Client Error（客户端错误状态码） | 服务器无法处理请求 |
| 5XX | Server Error（服务器错误状态码） | 服务器处理请求出错 |

> **常见的**    
> 100：请求已被接受，需要继续处理
>
> 200：请求成功        
>
> 301：被请求的资源已永久移动到新位置          
> 302：请求的资源临时从不同的URI响应请求     
> 304：客户端已经执行了GET，但文件未变化。是对客户端有缓存情况下服务端的一种响应               
>
> 400：请求有语法错误，不能被服务器理解        
> 401：请求未经授权       
> 403：服务器拒绝请求     
> 404：请求失败      
>
> 500：服务器遇到了一个未曾预料的状况，导致了它无法完成对请求的处理（服务器代码问题）     
> 503：由于临时的服务器维护或者过载，服务器当前无法处理请求（暂时状态）     

### 9、Cookie 和 Session

> HTTP 是无状态的协议，不知道连接是谁发起的

- Cookies 是服务器在本地机器存储的小段文本，并随着每一个请求发送至同一服务器上，是客户端保持状态的方案。主要内容包括：名字、值、过期时间、路径、域
- Session 是存储在服务器上一种用于存放用户数据的类哈希表结构

**Cookie 和 Session 的联系：**

浏览器首次发送请求时，服务器会自动生成哈希表和 SessionID 唯一标识改哈希表，SessionID 通过响应返回给浏览器，浏览器收到响应后将其存在 Cookie 中。第二次发送请求时，由于 Cookie 也发送给服务器，所以服务器在根据 Cookie 中保存的 SessionID 查哈希表，所以不会重复生成 Session。

**Cookie 和 Session 的区别：**

- Session 存储数据量大（任何对象），Cookie 只能存储 String 类型的对象
- Session 在服务端，Cookie 在客户端（可以编辑伪造，不安全）
- Session 过多会消耗服务器资源（可以用专门Session服务器）
- 域的支持范围不一样（Cookie 只能在a.com下调用，Session在www.a.com 和 api.a.com 下都能使用），解决Cookie域支持的问题：JSONP、跨域资源共享

Session 复制（gemfire，redis）可以实现 Session 多服务器共享

Session 的实现方式：

- Cookie（首次请求响应报文中set-cookie字段保存 JSESSOINID，再次请求时请求报文中 cookie 字段携带 JSESSIONID）
- URL 回写（服务器响应时将返回中所有的URL后面都加上 JSESSIONID）

### 10、Socket

> Socket：IP地址 + 端口，是网络上运行的程序之间双向通信链路的节点。是对 TCP/IP 协议的抽象，是操作系统对外开放的接口
>
> Socket 通信流程：
>
> ![1553482270917](.\assets\1553482270917.png)

Socket 原理机制：

- 通信两端都有 Socket
- 网络通信本质上就是 Socket 间的通信
- 数据在两个 Socket 之间通过 **IO 传输** 

### 11、GET 和 POST 的区别

HTTP 报文层面：GET 将请求信息放在 URL 中，POST 放在报文体中

数据库层面：GET 符合幂等性和安全性，POST 不符合

其他层面：GET 请求的结果可以被缓存、被存储，POST 不行



## 二、数据库

### 1、B+ 树是什么，有什么特点，为什么用作数据库索引结构
![](https://i.imgur.com/53fTdVd.jpg)
> B+ 树是 B 树的一种变形，本质上是一种平衡多路查找树，包含以下特征：              
> - B 树的一些特征：非单节点的结构中根节点至少包含两个子节点；所有叶子节点位于同一层；每个节点中的元素从小到大排列          
> - 中间节点（非叶子节点）有 k（ceil(m/2) <= k <= m, m为树的阶数，即最大子树个数）个子树，则包含的元素也是 k 个（B 树是 k-1）         
> - 中间节点元素不保存数据，只是用来索引；所有数据保存在叶子节点中；中间节点的元素同时存在于叶子节点中，是叶子节点元素的最大（小）值              
> - 所有叶子节点中包含了全部元素的信息，以及指向下一个叶子节点指针，并且叶子节点中的关键字从小到大顺序连接               

**B+ 树作为文件系统索引和数据库索引结构的优势：**

1. 单一节点能存储更多的元素，使得查询的 **I/O 次数更少** 。非叶子节点只存索引，不存数据，使得 IO 开销更小，存入磁盘的索引结构更多
2. 所有查询都是要查到叶子节点，查询性能稳定 （B 树有可能只查到根节点，可能查到叶子节点，不稳定）
3. 所有叶子节点形成有序列表，便于范围查询（B 树则要不断中序遍历）      

### 2、为什么索引可以提高检索速度

> - Mysql 的基本存储结构是**页**，记录存在页中            
> - 各个数据页可以组成**双向链表**              
> - 每个数据页中的记录可以组成**单向链表**               
> - 每个数据页为存储在其中的记录生成**页目录**                

**没有经过优化sql查询语句是这样做的：**

1. 定位到记录所在的页（遍历双向链表）
2. 从所在页中查找对应的记录（主键查询可以二分定位，非主键查询需要遍历单向链表）     

**索引实际上是将无序的数据变得相对有序**（类比查字典），包括**哈希索引**和 **B+ 树索引**

### 3、哈希索引的缺陷

1. 无法利用索引排序
2. 不支持最左匹配原则
3. 在大量重复键值的情况下容易发送哈希碰撞
4. 不支持范围查询       

**在单值记录查询需求较多时可以使用哈希索引**

### 4、聚集索引和非聚集索引

> - 聚集索引即**主键**创建的索引，其叶子节点存储的是**表中的数据** （InnoDB会以主键建立一个聚集索引，有且仅有这么一个聚集索引，其余建立的索引都是辅助索引，都需要先找到主键再去走主键索引）        
> - 非聚集索引（二级索引）即**非主键**创建的索引，其叶子节点存储的是**主键和索引列** （MyISAM）            
> - **回表**：非聚集索引查询出数据后，再通过叶子节点存储的主键去查数据              
> - 非聚集索引可以多个列来创建             
> - 创建多个单列非聚集索引时，会生成多个索引树              
> - **覆盖索引**：创建多列非聚集索引时，把**查询出的列和索引对应**，即索引包含所有需要查询的字段，避免回表 （尽量使用）                

### 5、最左匹配原则

> 一直向右匹配直到遇到范围查询就停止匹配

联合索引（多列索引）时，由于索引只要查找目标值是否**存在（即相等）**，**无法进行范围匹配**（>, <, between, like），后续就变成了线性查找，**列的排列顺序（从左向右）决定了可命中索引的列数**

简而言之，索引命中只能是相等的情况，列的排列顺序（从左向右）决定了可命中索引的列数。

此外，Mysql 会自动优化 ```=, in``` 的顺序，以命中更多的索引。

成因：是根据联合索引中的第一个字段建立的B+树索引，而其余的都是在其叶子节点中紧跟着第一个字段，所以跳过第一个字段，用后面的字段来查找是不会走索引的。简言之，联合索引是以第一个字段为键建立的B+树索引。

### 6、索引使用的条件，为什么不能滥用索引

1. 对于中大型表索引很有效，小型表全表扫描更高效，大表使用索引代价很大，需要使用分区技术
2. 在经常需要搜索的列、经常用在 ```WHERE``` 子句中的列、经常需要排序的列上创建索引
3. 避免对索引列进行计算（包括施加函数）
4. 创建索引的列需要设置成 ```NOT NULL``` 
5. 经常需要CUD操作的数据列尽量不使用索引（CRUD，增删改查操作）

**不能滥用索引：**

1. 对数据进行CUD操作时，索引也需要动态维护，降低了数据维护的效率
2. 索引需要占物理空间，每个索引都是以文件的形式存在物理空间中
3. 创建维护索引耗费时间，数据量越大耗费时间越多

### 7、表锁和行锁

> 一般情况下，数据库会**隐式地加锁**               

**表锁：** 加锁快，不会出现死锁，锁定粒度大，容易发生锁冲突，并发度低                 

**行锁：** 加锁慢，会出现死锁，锁定粒度小，不容易发生锁冲突，并发度高                  

**MyISAM 只支持表锁；InnoDB 支持表锁和行锁**，**InnoDB的行锁是基于索引的**（只有通过索引条件检索数据才使用行锁）                     

> **表锁**       
>
> - RR不阻塞（所以读锁也叫共享锁）、RW阻塞、WW阻塞（所以写锁也叫排他锁）           
> - **读锁和写锁互斥**，读写操作是串行，**写锁优先于读锁**                
> - MyISAM 支持**查询和插入的并发执行**，需要满足表中没有被删除的行，允许在一个进程读表的同时另一个进程从表尾插入记录；InnoDB 不支持                  
>
> **行锁**                       
>
> - 共享锁（S锁、读锁）：多个事务可以**同时读取同一条数据**，但不允许修改。```select * from xxxxx where ... LOCK IN SHARE MODE```                    
> - 排他锁（X锁、写锁）：阻塞其他的写锁和读锁。```select * from xxxxx where ... for update```                   

**InnoDB 操作没有走索引的话用的表级锁，走索引的话用的是行级锁还有GAP锁**

**事务的隔离级别是通过锁机制实现的**                

MVCC（多版本**并发**控制），**读写不阻塞**，通过某种机制生成一个数据请求时间点的一致性快照，并用这个快照提高一定级别（语句级和事务级）的一致性读取。在**提交读**和**可重复读**的隔离级别下工作。               

### 8、乐观锁和悲观锁

> **丢失更新：** 一个事务的更新覆盖了其他事务的更新结果         

解决丢失更新的方法：使用串行隔离级别、**乐观锁**、**悲观锁**          

> **乐观锁：** 不是数据库层面的锁，而是手动实现的锁，一般通过一个版本字段实现。判断第一次读时的版本号和开始更新时的版本字段是否一样，一致则更新，反之拒绝。        
>
> **悲观锁：** 数据库层面加锁，其他事务的修改会被阻塞。在 ```select```语句后加上 ```for update``` ，相当于加了排他锁。        

### 9、间隙锁GAP

**间隙锁的使用前提：** **范围**条件检索数据；只会在**可重复读隔离**级别下使用               

**间隙锁：** InnoDB在范围检索时，除了给满足条件的已有数据记录的索引项加锁，还对范围内不存在的记录“间隙”加锁。用在非唯一索引或者不走索引的当前读中              

**目的：** 防止幻读；满足恢复和复制的需要

### 10、死锁

> 并发容易发生死锁，MySQL通过回滚解决了不少死锁问题

减少死锁的方法：             

1. 以固定顺序访问表和行
2. 拆分大事务
3. 同一事务中，尽量一次锁定所需所有资源
4. 降低隔离级别
5. 为表添加合理的索引

### 11、事务机制

**事务：** 单个逻辑工作单元执行的一系列操作，要么完全地执行，要么完全地不执行            

遵循ACID原则：

> - **原子性：** 事务是最小执行单位，不可分割。确保动作要么完全执行，要么完全不执行。             
> - **一致性：** 事务执行前后数据库都要满足一致性状态。所谓一致性状态，即保证数据的完整性约束（发生异常会回滚）             
> - **隔离性：** 一个事务在执行期间（提交之前）对其他事务是不可见的。多事务并发执行时，一个事务的执行不会影响其他事务的执行               
> - **持久性：** 事务一旦提交，执行结果会永远保存在数据库中，即使发生崩溃，执行结果依然不会丢失。               

- 一致性得到满足，事务执行结果才正确；      

- 无并发（串行），隔离性自然满足，只要满足原子性，就能满足一致性；       

- 并发，满足隔离性和原子性，才能满足一致性。      

### 12、事务隔离级别

**事务的隔离级别的目的是为了达到事务的四个特性**

> 四类问题：
>
> - **更新丢失**：一个事务的更新覆盖了另一个事务的更新。mysql 所有事务级别在数据库层面均可避免（一般的，通过串行、乐观锁、悲观锁可以避免）
>
> - **脏读**：一个事务读取到另一个事务未提交的数据（因为未提及数据就可以回滚，此时数据就回到修改之前，导致另一个事务读到提交之后回滚之前的错误数据）  **一个事务读发生在另一个事务写过程中，读事务发生脏读**           
> - **不可重复读**：一个事务读取到另一个事务已经提交的数据，即一个事务读的同时另一个事务在**修改**，即一个事务中多次读一个数据得到的结果不一样。**一个事务写发生在另一个事务读过程中，读事务发生不可重复读**              
> - **虚读（幻读）**：指一个事务中的插入或删除影响到另一个事务的读取结果集，导致前后读取不一致            

**隔离级别**：

- **未提交读**（Read Uncommitted）：允许读取尚未提交的数据变更。会出现脏读、不可重复读、幻读         
- **提交读**（Read Committed）：允许读取并发事务已经提交的数据。可以阻止脏读，仍会出现不可重复读、幻读              
- **可重复读**（Repeated Read）：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改。可以阻止脏读和不可重复读，仍会出现幻读（在可重复读的基础上加上GAP间隙锁可避免幻读），**MySQL默认级别**，并且MySQL在该级别下通过GAP锁解决了幻读的问题              
- **串行**（序列化，Serializable）：避免以上情况          

**当前读：**读到的都是数据的最新版本。`select ... lock in share mode`、`select ... for update`、`update`、`delete`、`insert`

**快照读：**不加锁的非阻塞读，读到的可能是数据的历史版本。`select`

### 13、大表优化

1. **限定数据范围：** 禁止任何不带范围限制的条件查询          

2. **读写分离：** 主数据库负责写，从数据库负责读（主从复制：主写日志、从读日志、从重放日志中SQL）           

3. **大表拆分：** 垂直拆分 —— 将列比较多的表拆分成多个表           

   ​                    水平拆分 —— 保持表结构不变，将存储数据分片，可以分散到不同的表或库中（分片）

### 14、MyISAM 和 InnoDB 的区别

1. InnoDB 支持行锁和表锁，MyISAM只支持表锁
2. InnoDB 支持事务和崩溃后的安全恢复
3. 它们的B+树索引不同，MyISAM是非聚集索引，叶子节点存的是数据记录的地址，InnoDB是聚集索引，叶子节点存的就是记录本身（MyISAM数据文件 `*.MYD` 和索引文件 `*.MYI` 分开的，InnoDB 数据文件就索引文件 `IBD`，MySQL8.0 开始所有的非 InnoDB 引擎建立的表都会额外有一个序列字典信息文件 `*.sdi`）
4. InnoDB支持外键
5. MyISAM 适合读密集的表，InnoDB 适合写密集的表
6. 需要事务支持，并有高频率的并发读取，InnoDB 合适；数据量很大并且不需要支持事务，MyISAM合适

### 15、关系型数据库的架构

- 存储（文件系统）
- 程序实例
  - 存储管理：数据的逻辑关系转换成物理存储关系
  - 缓存机制：优化执行效率
  - SQL 解析：解析 SQL 语句
  - 日志管理：记录操作
  - 权限划分：多用户管理
  - 容灾机制：灾难恢复
  - **索引管理**：优化数据查询效率
  - **锁管理：**支持并发

### 16、如何定位并优化慢查询 SQL

- 根据慢日志定位慢查询 SQL
- 使用 explain 等工具分析 SQL
- 修改 SQL 或者尽量让 SQL 走索引

[mysql 中各类索引](https://www.cnblogs.com/zjfjava/p/6922494.html)

### 17、InnoDB 如何在可重复读级别下避免幻读

- 表象是，通过快照读（非阻塞读），实现了伪MVCC（事务中的操作依然是串行的）
- 本质是，next-key 锁（行锁 + GAP锁）
  - 当 where 语句全部命中时，只加行锁；否则会加 GAP 锁
  - GAP 锁会用在非唯一索引或者不走索引（相当于表锁，但是效率很低）的当前读中      

## 三、操作系统

[操作系统概述](https://github.com/sxnys/learning_for_future/blob/master/OS/introduction.md)

[进程管理](https://github.com/sxnys/learning_for_future/blob/master/OS/process_management.md)

[内存管理](https://github.com/sxnys/learning_for_future/blob/master/OS/process_management.md)

## 四、其他

### 1、Redis

> NoSQL：非关系型的数据库，用于超大规模数据的存储。
>
> NoSQL分类：列存储、文档存储（MongoDB）、key-value存储（Redis）、图存储、对象存储、xml存储

**Redis 特点：**

- 支持**数据的持久化**，可以将内存中的数据保存在磁盘中，重启时可以再次加载使用
- **丰富的数据类型**，除了简单的 key-value 类型数据，还提供 list、set、hash、zset 等数据结构的存储（**数据结构服务器**）
- 支持主从模式的**数据备份**（集群模式）
- 性能极高，读写速度极快（数据存在内存中）
- 所有操作都是原子性的，支持事务
- 使用单线程的多路 I/O 复用模型

**Redis 五种数据类型：**（一个键最大存储 512 MB）

- **String**：一个key对应一个value，value就是具体字符串（可以是数字），可以包含任何数据（图片、序列化对象），即二进制安全。

  `SET string1 "redis"`  、 `GET string1`

- **Hash**：一个键值对集合，String 类型的 field 和 value 的映射表，一个 Hash 可以存储 2^31-1个键值对。用于存储、读取、修改用户属性。

  `HMSET myhash field1 "sxn" field2 "hys"` 、`HGET myhash field1` 、`HGET myhash field2`

- **List**：简单的字符串列表（双向链表实现），按照插入顺序排序，可以从头部或尾部插入，列表可存储 2^31-1 个元素。用于最新消息排行、消息队列。

  `lpush mylist redis` （加的是具体字符串，不管是否加引号）

- **Set**： String 类型的无序集合，通过哈希表实现，集合中最大的成员数为 2^32 - 1。用于共同好友、统计访问网站的所有独立 IP、好友推荐等功能。

  `sadd myset redis`（加的是具体字符串，不管是否加引号）

- **Zset**：String 类型的有序集合，每个元素都会关联一个double类型的分数（score，可以重复），通过分数来为集合中的成员进行从小到大的排序。用于排行榜、带权重的消息队列。

  `zadd myzset 0 redis`

**Redis 发布/订阅：**

发布订阅是一种消息通信模式，redis 客户端可以订阅任意数量的频道。当有新消息通过 `PUBLISH` 命令发送给 channel 时，会被发送给订阅了 channel  的所有客户端。

客户端订阅频道 channel：`SUBSCRIBE channel`

服务端通过通道发送消息：`PUBLISH channel message`

**Redis 事务：**

过程：开始事务（`MULTI`）、命令入队、执行事务（`EXEC`）

- 批量操作在执行 `EXEC` 命令之前被放入队列缓存
- 收到 `EXEC` 命令后执行事务，事务中任意命令执行失败，其余命令依然被执行
- 事务执行过程中，其他客户端提交的命令请求不会被加入事务队列中

**Redis 做缓存：**

redis 可做分布式缓存，缓存具有一致性，但保证高可用的代价较大。用缓存的目的是为了高性能（直接读内存）和高并发（不经过数据库）。

|                            高性能                            |                            高并发                            |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| ![img](https://user-gold-cdn.xitu.io/2018/9/25/1660ed15756061ef?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2018/9/25/1660ed157643e87f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

**设置过期时间：**

对于一些有时间限制的功能（token、登录信息、验证码）很重要，交给缓存数据库处理时间限制问题很高效

1. 定期删除：随机抽取设置了过期时间的 key 进行检查
2. 惰性删除：对于定期删除没有处理的 key，需要系统主动去排查，redis 才会处理
3. 内存淘汰机制：应对定期删除没有删除的 key，也没有进行惰性删除的情况。保证缓存中都是热点数据
   - volatile-lru：从已设置过期时间的数据集中淘汰最近最少使用的数据
   - volatile-ttl：从已设置过期时间的数据集中淘汰即将过期的数据
   - volatile-random：从已设置过期时间的数据集中任意选择数据淘汰
   - allkeys-lru：当内存不能容纳新写入的数据时，淘汰最近最少使用的 key（最常用）
   - allkeys-random：从数据集中任意选择数据淘汰
   - no-enviction：禁止驱逐数据

**Redis 持久化机制：**

持久化数据：将内存中的数据写入硬盘，为了某些情况下（机器重启、机器故障）重用数据（恢复数据），或者为防止系统故障将数据备份到远程位置

- 快照持久化（RDB，默认方式）

  Redis可以通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。Redis创建快照之后，可以对快照进行备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（Redis主从结构，主要用来提高Redis性能），还可以将快照留在原地以便重启服务器的时候使用。

- 只追加文件（AOF）

  AOF 的实时性更好，已成为主流的持久化方案。默认情况下 Redis 没有开启 AOF，可以通过 appendonly 参数开启：`appendonly yes`             

  开启AOF持久化后每执行一条会更改Redis中的数据的命令，Redis就会将该命令写入硬盘中的AOF文件。三种 AOF 持久化方式：

  - 每次有数据修改时都写入 AOF 文件（降低性能）   `appendfsync always`
  - 每秒钟同步一次（兼顾数据和写入性能）    `appendfsync everysec`
  - 让操作系统决定何时同步    `appendfsync no`

**缓存雪崩：**

缓存同一时间大面积失效，所有数据请求都到了数据库，造成数据库短时间内承受大量请求而崩掉。

- 使用前：保证 redis 集群的高可用性，发现机器宕机尽快补上，选择合适的内存淘汰机制
- 使用时：本地 ehcache 缓存 + hystrix 限流&降级，避免MySQL崩掉
- 事后：利用 redis 持久化机制保证数据尽快恢复

**缓存穿透：**

故意请求缓存中不存在的数据，导致所有请求到数据库，造成数据库短时间内承受大量请求而崩掉。

- 布隆过滤器，所有可能存在的数据哈希到足够大的 bitmap 中，所以一定不存在的数据会被拦截
- 查询结果空也进行缓存，过期时间设置较短

**并发竞争 key：**

多个系统同时对一个 key 进行操作，执行顺序与期望不同，得到不一样的结果

分布式锁，redis 可以实现，但是会影响性能。主要用 zookeeper

**数据一致性：**

缓存和数据库双写时数据一致。读写请求串行，但会吞吐量大幅降低。

### 2、Docker

> **Docker** 是一个开源的应用容器引擎，基于 Go 语言，是一个Linux容器。可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化

Docker 基于**守护进程**：直接与主操作系统进行通信，为各个Docker容器动态分配资源；将容器与主操作系统隔离，并将各个容器互相隔离。

Docker容器可以在数毫秒内启动，可以节省大量的磁盘空间以及其他系统资源；而**虚拟机基于虚拟机管理系统，**启动需要数分钟，有臃肿的**从操作系统**，占用大量系统资源。所以，Docker是进程级别的资源隔离，虚拟机是操作系统级别的隔离。

**三个基本概念：**

- **Image（镜像）**：一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）,不包含任何动态数据，其内容在构建之后也不会被改变。
- **Container（容器）**：Docker 容器通过 Docker 镜像来创建，所以也是一个特殊的文件系统，只不过多了一层可读可写的一层。
- **Repository（仓库）**：集中存放镜像文件

**创建镜像的两种方法：**

1. 更新：从已经创建的容器中更新镜像并提交

2. 构建：使用 Dockerfile 构建一个新的镜像   `docker image build -t [username]/[repository]:[tag] .`

   部署 tomcat app：

   - 拉取 tomcat 镜像   `docker pull tomcat:VERSION`
   - 启动 tomcat 容器   `docker run tomcat:VERSION`
   - 拷贝 War 包到启动的 tomcat 容器对应的目录中   `docker cp WAR TOMCAT_CONTAINER:WEBAPPS`     
   - 将容器做成镜像   `docker commit -m  "DESCRIPTION" -a "AUTHOR" CONTAINER IMAGE[:TAG]`
   - 发布镜像   `docker push IMAGE[:TAG]` 

[Docker 核心技术与实现原理](https://draveness.me/docker)

**命名空间 (namespaces)** 用于分离进程树、网络接口、挂载点以及进程间通信等资源的方法。

**Control Groups（CGroups）**能够隔离宿主机器上的物理资源，例如 CPU、内存、磁盘 I/O 和网络带宽。

### 3、Web 服务器

> **Web（HTTP）服务器：** 关注 http 协议层面的传输和控制访问，有代理、负载均衡等功能，主要处理静态资源。Ngnix、Apache 是 HTTP 服务器，基于 REST 架构风格        
>
> **Web 应用服务器：** 保证应用能在应用服务器正常运行，要支持应用相关规范，也集成了部分http server的功能。Tomcat、uWsgi 属于 Web 应用服务器         

#### Ngnix ####

> Nginx 是一款自由的、开源的、高性能的 **HTTP 服务器**和**反向代理服务器**；同时也是一个IMAP、POP3、SMTP 代理服务器；Nginx 可以作为一个 HTTP 服务器进行网站的发布处理，也可以作为反向代理进行负载均衡的实现。

**正向代理（代理的是客户端）：**

- 客户端非常明确要访问的服务器地址，代理需要在客户端机器上配置
- 屏蔽或隐藏了真实客户端信息（服务器只知道请求来自哪个代理服务器，不知道来自哪个客户端）
- 访问无法访问的资源、用作缓存、客户端上网认证、上网行为管理

**反向代理（代理的是服务端）：**

多个客户端给服务端发送请求，先有代理服务器接收，然后代理服务器按照一定的规则分发给各个业务处理服务器。请求的来源（各个客户端）明确，请求由哪台服务器处理不明确。

- 反向代理对外透明，客户端无感代理的存在
- 反向代理隐藏了服务器的信息
- 保证内网的安全、负载均衡

**负载均衡：**

- 负载量：客户端发送的、反向代理服务器接收到的请求数量           
- 均衡规则：请求数量分发到不同服务器的规则              
- 负载均衡：反向代理服务器接收到的请求按照均衡规则进行分发            
- 类型：硬件负载均衡（硬负载，成本高，安全稳定）、软件负载均衡（软负载，消息队列分发机制）          

Ngnix 支持的负载均衡调度算法：

1. **weight 轮询**（默认）：ngnix 按序逐一分配请求到后端服务器（宕机被踢出队列），给后端服务器设置权重值（根据硬件配置），用于调整请求分配率
2. **ip_hash**：每个请求按照来源客户端 IP 进行 Hash，分配到之前访问的同一后端服务器，一定程度上解决了集群部署环境下 session 共享的问题
3. **fair 智能调整调度**：动态地根据后端服务器的响应时间和处理效率进行均衡分配（需要安装 upstream_fair 模块）
4. **url_hash**：按照访问 url 的 hash 结果分配请求，每个请求的 url 指向后端固定的某个服务器，ngnix 作为静态服务器时提高缓存效率（需要安装 hash 软件包）

#### Apache

与 Ngnix 比较：

- apache 的 rewrite 更强大
- apache 更稳定
- apache 处理动态请求有优势，ngnix 适合做静态和反向代理
- apache 是同步多进程模型，ngnix 是异步的（epoll），支持高并发，性能更好
- 通用方案：ngnix 前端抗并发，apache 后端集群

#### Tomcat

Tomcat是应用（java）服务器，只是一个servlet容器，是Apache的扩展但又独立运行

#### uWSGI

> **CGI**：通用网关接口，规定一个程序该如何与web服务器程序之间通信从而可以让这个程序跑在web服务器上      
>
> **WSGI**：Python Web 服务网关接口，用在 python web 框架编写的应用程序与后端服务器之间的规范       
>
> **uWSGI**：一个Web服务器（WSGI Server），实现了fastcgi、uwsgi、http 等协议。用于接收前端服务器转发的动态请求并处理后发给 web 应用程序
>
> **uwsgi**：uWSGI 服务器实现的独有的协议，是一种传输协议

部署Django App：

1. nginx 做为代理服务器：负责静态资源发送（js、css、图片等）、动态请求转发以及结果的回复；

2. uWSGI 做为后端服务器：负责接收 nginx 请求转发并处理后发给 Django 应用以及接收 Django 应用返回信息转发给 nginx，ngnix 和 uWSGI 之间通过 uwsgi 传输协议通信；

3. Django 应用收到请求后处理数据并渲染相应的返回页面给 uWSGI 服务器。

![img](https://images2018.cnblogs.com/blog/720785/201803/720785-20180315175024706-1983173921.jpg)

### 4、J2EE

> Java 2平台企业版（Java 2 Platform Enterprise Edition），本质上是一种 Java Web 服务开发标准

主要技术：**Servlet**、**JSP**、**JDBC**、**EJB**

应用分层：**领域对象层**（Domain Object，即跟数据库表对应的Java对象）、**DAO 层**（数据访问对象）、**业务逻辑层**（xxxManager）、**控制器层**（xxxServlet）、**表现层**（JSP）

处理请求和发送响应的过程是由Servlet来完成的，Tomcat是一个Servlet/JSP容器

servlet 生命周期：加载和实例化、初始化`init()`、请求处理（服务）`service()`、服务终止（销毁）`destroy()`

Hibernate：一种**ORM框架**，在DAO层操作XML，将数据封装到XML文件上，在DAO层使用原生JDBC连接数据库           

 **MVC 和 MTV 的区别：**

MVC 中的 View 的目的是“呈现哪一个数据”，而 MTV 的 View 的目的是“数据如何呈现”。

MTV 也就是把 MVC 中的 View 分成了视图（展现哪些数据）和模板（如何展现）2个部分，而Contorller这个要素由框架自己来实现了，我们需要做的就是把（带正则表达式的）URL对应到视图就可以了，通过这样的URL配置，系统将一个请求发送到一个合适的视图。

### 5、MongoDB

> MongoDB，一个基于分布式文件存储的开源数据库系统。旨在为WEB应用提供可扩展的高性能数据存储解决方案。将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。

- **相关概念**

|          SQL          |          MongoDB          |
| :-------------------: | :-----------------------: |
|  数据库（database）   |    数据库（database）     |
|   数据库表（table）   |    集合（collection）     |
|   数据记录行（row）   |     文档（document）      |
|    数据字段（col）    |        域（field）        |
|     索引（index）     |       索引（index）       |
| 表连接（table joins） |          不支持           |
|  主键（primary key）  | 自动设置 `_id` 字段为主键 |

- **相关指令**

1. **创建 \ 删除数据库：**`use DATABASE_NAME`  ,    `db.dropDatabase()`
2. **创建 \ 删除集合：**`db.createCollotion(name, [options])` （插入文档时会默认创建集合） ,    `db.COLLECTION_NAME.drop()`
3. **创建文档：**`db.COLLECTION_NAME.insert(document)`
4. **查询文档：**`db.COLLECTION_NAME.find([query], [projection])`
5. **创建索引：**`db.COLLECTION_NAME.createIndex(keys, [options])`，key值为创建索引的字段，1表示升序创建，-1表示降序创建

- **MongoDB复制（副本集 replica set）**

1. **目的：** 保障数据的安全性、高可用性、灾难恢复、无需停机维护、分布式读取数据              

2. **原理：** 至少需要两个节点，主节点负责处理客户端请求，其余都是从节点，负责复制主节点上的数据（一主一从、一主多从）。主节点将在其上进行的所有操作**存在oplog**中，从节点**定期轮询**主节点获取这些操作，对自己的数据副本执行这些操作。                     

3. **特征：** 是N个节点的**集群**、任何节点都可以作为主节点、所有写操作都在主节点、自动故障转移、自动恢复           

4. **操作指令：** （设置）`mongod --port PORT --dbpath DB_PATH --replSet REPLICA_SET_NAME`   （添加节点）`REPLICA_SET_NAME.add(HOST:PORT)` （是否是主节点）`REPLICA_SET_NAME.isMaster`          

​        **常见的主从在主机宕机后所有服务将停止，而MongoDB副本集在主机宕机后，副本会接管主节点成为主节点，不会出现宕机的情况。**             

- **MongoDB 分片**

​        **分片**是 MongoDB 中的另**一种集群**，满足数据量大量增长的需求，通过在多台机器上**分割数据**，使得数据库系统能存储和处理更多的数据。

​        涉及到三个主要组件：

1. **Shard：** 分片，存储实际的数据库，一个 shard 可以有一个副本集承担
2. **Config Server：** mongodb 实例，存储整个集群元数据
3. **Query Routers： ** 前端路由，客户端接入点

- [MongoDB 索引为什么用 B 树 而不是 B+ 树？](https://blog.csdn.net/ahjxhy2010/article/details/80339510)

### 6、Git

> Git是分布式版本控制系统（每个人的电脑上都是一个完整的版本库），SVN是集中式版本控制系统（依赖中央服务器）

`git pull` <==> `git fetch` + `git merge`

pull：更新远程仓库的代码到本地仓库，然后将内容合并到当前分支

fetch：从远程获取最新版本到本地，不会自动合并

merge：将内容合并到当前分支

> 团队开发
>
> - 创建分支：`git branch Branch_Name`
> - 查看当前分支：`git branch`
> - 切换分支：`git checkout Branch_Name`
> - 合并分支到当前分支：`git merge Brach_Name`
> - 提交到本地仓库：`git commmit -m "Description"`
> - 提交到远程仓库：`git push origin Branch_Name`

[Git 常见面试题](http://blog.jobbole.com/114297/)

### 7、RESTful API

> REST：表征性状态转移。更好地使用现有Web标准中的一些准则和约束。         
>
> RESTful：符合REST的约束条件和原则，最流行的 API 设计规范，用于 Web 数据接口的设计         
>
> API 设计理念：URL （名词）来定位资源，HTTP 动词（GET,POST,DELETE,...）来描述操作

**URL 设计**：

- 客户端发出的操作指令都是 **动词 + 宾语** 的结构（HTTP 请求行）。`GET /articles`
- 宾语就是 API 的 URL，是 HTTP 动词作用的对象，应该是名词，不能是动词
- 客户端发出的请求方法用 POST 模拟 PUT、PATCH、DELETE 时，加上 `X-HTTP-Method_Override` 属性
- 建议使用复数 URL，`GET /articles/2` 要好于 `GET /article/2`
- 避免多级 URL，除了第一级其他级别都用查询字符串表达。`GET /authors/12?categories=2` 要好于 `GET authors/12/categories/2`

**状态码要精确**，API 用不到 `1xx`、`301（永久重定向）`、`302（暂时重定向，还有307）`

**服务器回应：**

- API 返回的数据不应该是纯文本，而是 **JSON 对象**。服务器 HTTP 响应头中 `Content-Type` 属性和客户端 HTTP 请求头中 `Accept` 属性都要设为 `application/json`
- 发送错误时，不要返回 200 状态码（错误信息在数据体中），而是要让状态码反映发生的错误，具体信息在数据体中
- 在回应中给出相关链接，便于使用者下一步操作

### 8、分布式、集群、消息队列 ...

分布式存储、分布式计算、分布式事务，保证一致性

web 系统性能指标：响应时间、吞吐量、并发。靠**集群**、**缓存**、**异步**满足

发布/订阅就是一种消息队列模型

集群是为了进行负载均衡，mongodb 的副本集、分片就是集群，注意 session 共享问题

### 9、设计模式



### 10、Linux



### 11、I/O 模型

[I/O模型](https://github.com/CyC2018/CS-Notes/blob/master/docs/notes/Socket.md)













