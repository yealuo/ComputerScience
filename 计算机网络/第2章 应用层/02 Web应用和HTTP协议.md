# Web和HTTP
## 常用术语
网页（Web页，或称文档）由许多对象组成。 
Web对象就是文件，可以是HTML文件, JPEG图像, Java applet, 音频文件…
多数网页由单个基本HTML文件和若干个所引用的对象构成
每个对象被一个URL(Uniform Resource Locator, 统一资源定位符)寻址
$$协议类型://主机名:端口//路径和文件名
$$
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303231940945.png)
# 统一资源定位符URLs
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303231941097.png)
# HTTP概述
**HTTP: 超文本传输协议(hypertext transfer protocol)**
Web的应用层协议
**client/server模式**
- client: 浏览器browser请求, 接收, “解释显示” Web对象
- server: Web服务器响应请求，发送 Web对象
## HTTP使用TCP:
客户初始化一个与HTTP服务器80端口的TCP连接 (创建套接字)
HTTP服务器接受来自客户的TCP连接请求, 建立连接
Browser (HTTP client)和Web服务器 (HTTP server) 交换HTTP消息(应用层协议消息)包括HTTP请求和响应消息
最后结束(或叫关闭)TCP连接
## HTTP是无状态协议
HTTP服务器不维护客户先前的状态信息
## 补充
**维护状态的协议非常复杂!**
必须维护过去历史 (状态信息)
如果server/client崩溃, 它们各自的状态视图可能不一致, 因此必须保持协调一致。
# HTTP连接
## 非持久HTTP连接
每个TCP连接上只传送一个对象，下载多个对象需要建立多个TCP连接
**HTTP/1.0使用非持久HTTP连接**
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303231946845.png)
## 响应时间模型
### 定义往返时间RTT(Round-Trip Time): 
1个分组从客户主机到服务器再回到客户主机所花费的时间。
**HTTP 响应时间 (每个对象)：**
- 1个RTT用于建立TCP连接
- 1个RTT用于HTTP请求/响应消息的交互
- 对象/文件的传输时间
$非持久HTTP响应时间：total = 2RTT + file~transmit~time$
**TCP连接的“三次握手”过程：**
客户机发送一个TCP连接请求报文 
服务器回送一个TCP确认响应报文 
客户机向服务器发送一个包含“ HTTP请求”与“TCP确认”的报文
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303231949874.png)
## 持久HTTP连接
一个TCP连接上可以传送多个对象
**HTTP/1.1默认使用持久HTTP连接**
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303231950427.png)
# HTTP报文格式
2类HTTP报文：**请求报文request, 响应报文response**
**HTTP request message**
- ASCII文本 (易于人读格式)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303231953037.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303231953289.png)
# 方法类型
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303231955485.png)
# 其他HTTP请求方法
**POST method:**
web 页面经常包含表单输入
向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求实体中。POST 请求可能会导致新的资源的建立和/或已有资源的修改。
**GET method (用于发送数据到服务器):**
输入数据直接加载入GET请求行的 URL 域中 (后面加 ‘?’):
**HEAD method:**
类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头
**PUT method:**
上传新文件（目标）到服务器
从客户端向服务器传送的数据替换指定的文档的内容。(completely replaces file that exists at specified URL with content in entity body of POST HTTP request message)
# HTTP/2
==关键目标：降低多目标对象HTTP请求时的延迟。==
**HTTP1.1: 单个TCP连接中引入多个流水线GET请求**
服务器对GET请求进行有序响应（FCFS：先到先服务调度）
使用FCFS时，较小目标对象必须等待大目标对象先行传输（ head-of-line (HOL) blocking ）
丢失恢复（重传丢失的TCP报文段）使对象传输停滞
**HTTP/2：[RFC 7540, 2015] 提高了服务器向客户端发送对象的灵活性：**
方法、状态代码、大多数标头字段与 HTTP 1.1 未更改
基于客户端指定的对象优先级（不一定是FCFS）的请求对象的传输顺序
将未请求的对象推送到客户端
将对象划分为帧，安排帧以减轻 HOL 阻塞
# HTTP/2：减轻线头阻塞HOL
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303231959611.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303231959700.png)
# HTTP/2 to HTTP/3
通过单个TCP连接的HTTP/2意味着：
- 从数据包丢失中恢复仍然使所有对象传输停止
	- 像HTTP 1.1中一样，浏览器具有打开多个并行TCP连接的动机，以减少停顿，提高整体吞吐量
- 普通TCP连接上没有安全性
- HTTP/3：通过**UDP**增加每个对象的错误和拥塞控制（更多流水线）的安全性
# HTTP响应消息
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232001000.png)
# HTTP 响应的状态码
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232002243.png)
# HTTP试验 (客户机侧)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232003262.png)
1. telnet news.sina.com.cn 80，如果无显示，则按下“ctrl + ]”，出现 Microsoft Telnet > 提示符，输入 set localecho 打开回显，再连续按下两次回车键；
2. 输入GET请求，比如：GET /gov/xlxw/2021-02-26/doc-ikftssap8914542.shtml HTTP/1.1
	Host: news.sina.com.cn
# Cookies: 跟踪用户
**HTTP GET/响应交互是无状态的，不保存客户信息。**
Cookie：允许Web站点跟踪、识别用户；服务器可以限制用户访问，或把内容与用户身份关联。 
包括四个部分：
- 在HTTP响应报文中有一个cookie 首部行
- 在HTTP请求报文中有一个cookie 首部行
- 用户主机中保留有一个 cookie 文件并由浏览器管理
- Web站点的后端数据库保存cookie
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232005702.png)
# Cookies的 4 个重要方面
1. cookie头部行在**HTTP响应消息**中
2. cookie头部行在**HTTP请求消息**中
3. cookie文件保存在**用户主机**中并被用户浏览器管理
4. cookie也保存在Web站点的**后端数据库**
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232007867.png)
基于HTTP协议具体实现描述Cookie实现过程。
1. session 在服务器端，cookie 在客户端（浏览器）
2. session 默认被存在在服务器的一个文件里（不是内存）
3. session 的运行依赖 session id，而 session id 是存在 cookie 中的，也就是说，如果浏览器禁用了 cookie ，同时 session 也会失效（但是可以通过其它方式实现，比如在 url 中传递 session_id）
4. session 可以放在 文件、数据库、或内存中都可以。
5. 用户验证这种场合一般会用 session 

**cookie保存在客户端，session保存在服务器端**
cookie目的可以**跟踪会话**，也可以**保存用户喜好或者保存用户名密码**
session用来**跟踪会话**
# HTTP Cookies
**cookies 可以用来：**
authorization 标识用户
shopping carts 维护购物车列表
recommendations 推荐商品
user session state 在无状态的HTTP之上建立用户会话层(Web e-mail)
**cookie 如何保持状态？**
**在协议端点：** 在多个事务上维护发送方/接收方的状态
**在消息中：** HTTP 消息中的 Cookie 携带状态
**cookies 与隐私：**
cookie 允许网站在其网站上了解您的很多信息。
第三方持久性 Cookie（跟踪 Cookie）允许跨多个网站跟踪通用身份（Cookie 值），广告公司通过网站获得用户信息
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232011097.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232012790.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232012796.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232012207.png)
# Web 缓存 (代理服务器)
**目标: 代表起始服务器满足HTTP请求。**
用户配置浏览器: Web 访问经由缓存
所有HTTP请求指向缓存
- 对象在缓存中：缓存器返回对象
- 否则缓存器向起始服务器发出请求，接收对象后转发给客户机
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232013818.png)
缓存器既是服务器又是客户机
一般地，Web缓存器既是服务器又是客户机
典型的缓存器被ISP提供（大学、公司或居民ISP）
服务器在响应头部告知目标缓存是否允许：
`Cache-Control:max-age=<seconds>`
`Cache-Control:no-cache`
**为什么需要Web缓存器?**
减少对客户机请求的响应时间
减少内部网络与接入链路上的通信量
能从整体上大大降低因特网上的Web流量
## 缓存器举例 
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232016157.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232016049.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232017752.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232017507.png)
# 条件GET方法
**高速缓存：**
- 减少响应时间；
- **存放在缓存中的对象拷贝可能是旧的**，即保存在起始Web服务器中的对象可能已经被修改。
**条件GET目的：**
- **证实缓存器中的对象是否为最新**
- Web服务器回发**响应报文**：包括对象的最后修改时间 `Last-modified：<date>
- 缓存器：在**请求报文**中包含对象最后修改时间，`If-modified-since: <date>`
Web服务器：如果对象是最新的则响应报文中不包含对象
	`304 Not Modified` (HTTP/1.0 304 Not Modified)，并且实体为空。
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232036393.png)
