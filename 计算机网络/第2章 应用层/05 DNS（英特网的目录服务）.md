# DNS: 域名系统 Domain Name System
DNS服务器提供的功能：
主机名到IP地址的转换
主机别名
- 一个主机可以有一个规范主机名和多个主机别名
邮件服务器别名
负载分配
- DNS实现冗余服务器：一个IP地址集合可以对应于同一个规范主机名。
## Domain Name System:
**分布式数据库：** 一个由分层DNS服务器实现的分布式数据库
**应用层协议：** DNS服务器实现域名转换 (域名/地址转换)
**为什么不集中式DNS?**
- 单点故障
- 巨大访问量
- 远距离集中式数据库
- 维护
- 不可扩展！
# DNS是一个分布式、层次数据库
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303261415753.png)
# 分布式、层次数据库
**客户机怎样决定主机名 www.amazon.com 的IP地址？**
客户机查询 root 根域名服务器得到 com DNS 服务器
客户机查询 .com DNS 服务器得到 amazon.com DNS 服务器
客户机查询 amazon.com DNS 服务器得到www.amazon.com 的IP地址
# 四种类型域名服务器
根域名服务器（Root DNS servers）
顶级域名服务器（Top-level domain (TLD) servers）
权威域名服务器（Authoritative DNS servers）
本地域名服务器（Local DNS server）
## 根名字服务器Root name servers
**根名字服务器负责记录顶级域名服务器的信息**
>目前全球共有13套域名根服务器。1主12辅，其中10个在美国，英国、瑞典、日本各1个，分别代表欧洲和亚洲，由ICANN (Internet Corporation for Assigned Names and Numbers) 统一管理。
## 顶级域服务器 TLD
**顶级域服务器（top-level domain servers）**：负责顶级域名 com, org, net, edu, etc, 和所有国家的顶级域名 uk, fr, ca, jp.
## 权威域名服务器 (authoritative DNS servers)
在因特网上具有**公共可访问主机**（如Web服务器和邮件服务器）**的每个组织机构必须提供公共可访问的DNS记录**，这些记录将这些主机的名字映射为IP地址。**组织机构的权威DNS服务器**负责保存这些DNS记录。
- 通常，一个主机的授权域名服务器就是它的本地ISP的一个域名服务器。
- 实际上，为了更加可靠地工作，一个主机最好有至少两个授权域名服务器。
- **许多域名服务器同时充当本地域名服务器和权威域名服务器。**
- 权威域名服务器总是能够将其管辖的主机名转换为该主机的IP地址（当一个权威域名服务器还不能给出最后的查询回答时，就会告诉发出查询请求的 DNS 客户，下一步应当找哪一个权威域名服务器）。 
## 本地DNS服务器(Local DNS name server)
本地域名服务器也称**默认域名服务器**。**严格来说不属于该服务器的层次结构**。
每一个因特网服务提供商 ISP（住宅ISP、公司、大学、甚至一个大学的系）都可以拥有一个本地域名服务器
**当一个主机发出DNS查询报文时，这个报文就首先被送往该主机的本地域名服务器。**
- 起着**代理**的作用，转发请求到层次结构中。
- 在用户的计算机中设置网卡的“Internet协议（TCP/IP）属性”对话框中设置的首选DNS服务器即为本地域名服务器。 
- 本地域名服务器离用户较近，一般不超过几个路由器的距离。
# DNS查询方法一 ——递归查询(recursive query)
名字解析的负担交给被查询的名字服务器
被查询的名字服务器负载重?
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303261430879.png)
# DNS查询方法二 ——迭代查询(iterated query)
被查询的名字服务器回复可以被查询的名字服务器的IP地址
“我不知道它的名字，但是你可以问服务器xx”
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303261433341.png)
# DNS缓存和权威DNS记录更新
一旦名字服务器获得DNS映射, 它将缓存该映射到局部内存
- 服务器在一定时间后将丢弃缓存的信息
- 本地DNS服务器可以缓存TLD服务器的IP地址，因此根DNS服务器不会被经常访问
权威DNS服务器记录更新：IETF动态更新/通报机制
- RFC 2136
# DNS记录
DNS: 存储资源记录 (RR，Resource Records) 的分布式数据库
RR 格式: (name, value, type, ttl)
## Type=A (Address)
name = 主机名
value = IP地址
(relay1.bar.foo.com, 145.37.93.126，==A==)
## Type=NS (name server)
name = 域名（如：foo.com） 
value = 该域权威名字服务器的主机名
(foo.com, dns.foo.com, ==NS==)
## Type=CNAME（canonical）
name = 主机别名
如 www.ibm.com 的真名是servereast.backup2.ibm.com 
value = 真实的规范主机名
(foo.com, relay1.bar.foo.com, ==CNAME==)
## Type=MX（mail exchange）
name =邮件服务器的主机别名
value =邮件服务器的真实规范主机名
(foo.com, mail.bar.foo.com, ==MX==)
# DNS协议报文
**DNS协议 : 查询报文与应答报文** , 但具有同样的报文格式
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303261501276.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303261503790.png)
# 在DNS数据库中插入记录
**例子：刚刚成立一个“网络乌托邦”公司**
如果你想在**DNS注册登记机构**注册你的域名**networkutopia.com**，则
- 需要提供你自己的基本权威DNS服务器和辅助权威DNS服务器的名字和IP地址
- 该注册登记机构将下列两条资源记录插入 .com TLD服务器中：
	(networkutopia.com, dns1.networkutopia.com, NS)
	(dns1.networkutopia.com, 212.212.212.1, A)
使用IP地址212.212.212.1创建本地的权威DNS服务器
- 如果想建立网站，可以将网址 www.networkutopia.com 以**类型A**的方式记录到权威DNS服务器dns1.network.com 中
- 如果想建邮件服务器，则可以将 mail.networkutopia.com 以**类型MX**的方式记录到权威DNS服务器dns1.network.com中
# DNS攻击
**DDoS攻击：** 对根域名服务器或顶级域名服务器发起拒绝服务攻击
**重定向攻击：** 中间人攻击、DNS中毒攻击（发送欺骗的域名解析结果给DNS服务器）
**利用DNS实现DDoS攻击：** DNS反弹式拒绝服务攻击(DNS reflector attacks，又称DNS amplification attacks)。伪造客户地址向大量的DNS服务器发出请求，导致客户无法访问DNS服务器进行域名解析。
## 1、DNS劫持
攻击DNS域名服务器，或伪造域名服务器的方法，把目标网站域名解析到错误的地址而达到无法访问目标网站的目的。
采用黑客手段控制域名管理密码和域名管理邮箱，将该域名的DNS纪录指向到黑客可以控制的DNS服务器，然后通过在该DNS服务器上添加相应域名纪录，从而使用户访问该域名时，进入了黑客所指向的内容。
## 2、缓存投毒
利用控制DNS缓存服务器，把原本准备访问某网站的用户在不知不觉中带到黑客指向的其他网站上。
- 通过用户ISP端的DNS缓存服务器的漏洞进行攻击或控制，从而改变该ISP内的用户访问域名的响应结果
- 通过利用用户权威域名服务器上的漏洞，如当用户权威域名服务器同时可以被当作缓存服务器使用，黑客可以实现缓存投毒，将错误的域名纪录存入缓存中，从而使所有使用该缓存服务器的用户得到错误的DNS解析结果。
## 3、DDOS攻击 (Distributed Denial of Service)
一种攻击针对DNS服务器软件本身，通常利用BIND软件程序中的漏洞，导致DNS服务器崩溃或拒绝服务
另一种攻击的目标不是DNS服务器，而是利用DNS服务器作为中间的“攻击放大器”，去攻击其它互联网上的主机，导致被攻击主机拒绝服务。
- 攻击者利用大量的“肉鸡”对攻击目标发动大量的正常或非正常请求、耗尽目标主机资源或网络资源，从而不能为合法用户提供服务。
## 4、DNS欺骗
也称DNS毒化，属于中间人攻击，就是攻击者冒充域名服务器的一种欺骗行为。
- 如果可以冒充域名服务器，然后把查询的IP地址设为攻击者的IP地址，这样的话，用户上网就只能看到攻击者的主页，而不是用户想要取得的网站的主页了，这就是DNS欺骗的基本原理。
- DNS欺骗其实并不是真的“黑掉”了对方的网站，而是冒名顶替、招摇撞骗罢了。
# DNS与国家安全
## 域名解析控制权现状
全球根域名服务器多数在美国
域名服务器的管理者ICANN是非盈利性机构，且已经从美国政府脱离，但是它仍然是受美国法律约束的组织，在事实上，它对**根域名服务器**的管理都要向美国政府申报
## 域名解析由某国家控制的潜在问题
一旦与某国发生冲突，可以在技术上阻碍对该国域名的解析。伊拉克战争期间，美国终止了伊拉克国家顶级域名.IQ的解析；
在塔利班政权统治阿富汗时期，美国将阿富汗国家顶级域名.AF的管理权授予前流亡政府；
2004年4月，由于对顶级域名管理权问题发生分歧，导致利比亚国家顶级域名.LY瘫痪，利比亚从互联网上“消失”了3天
## 提高我国域名系统安全性的建议：
**加强国家网络空间安全的战略谋划**
- 加强网络空间安全筹划和顶层设计
- 积极参与国际互联网治理
**增强国家域名系统的安全防护能力**
- 建立互联网安全应急替代机制
**以网络技术发展为契机，抢占先机**
- 充分利用全球下一代网络发展契机，建立新框架（IPV6，物联网等）
- 充分利用新型网络应用的发展，降低对现有域名体系的依赖
