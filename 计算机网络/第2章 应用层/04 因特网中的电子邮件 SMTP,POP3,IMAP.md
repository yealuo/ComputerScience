# 电子邮件
**3个主要组成部分:**
用户代理 user agents 
邮件服务器 mail servers 
简单邮件传送协议 SMTP
## 用户代理 User Agent
也就是邮件阅读器，如Outlook, foxmail等
允许用户阅读、回复、转发、保存、编辑邮件消息 
传出/传入消息存储在服务器上
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232044712.png)
# 电子邮件: 邮件服务器
## 邮件服务器 mail servers:
邮箱 mailbox 存放用户接收的邮件消息
外出（待发送）邮件的邮件队列
## SMTP protocol
邮件服务器之间以SMTP协议发送电子邮件
- “client”: 发送邮件服务器
- “server”: 接收邮件服务器
# 电子邮件: SMTP [RFC 5321]
客户使用TCP来可靠传输邮件消息到服务器端口号25
- **直接传送:** 从发送服务器到接收服务器
传输的3个阶段
- SMTP 握手（问候）
- SMTP 消息传输
- SMTP 结束
命令/应答交互 (类似 HTTP)
- commands: ASCII文本格式
- response: 状态码及其短语 
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232047422.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232048566.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232049144.png)
# SMTP: 总结
SMTP使用**持久连接**
SMTP 要求邮件消息(header & body) 必须是**7-bit ASCII**
SMTP服务器使用CRLF.CRLF 来判断邮件消息的结束
**与HTTP的比较:**
**HTTP: 拉协议**
- 用户使用HTTP从服务器拉取信息。其TCP连接是由想获取文件的机器发起。
**SMTP: 推协议**
- 发送邮件服务器把文件**推向接收邮件服务器**，其TCP连接是由要发送文件的机器发起。 
都有ASCII 命令/应答交互, 状态码
HTTP: 每个对象封装在它各自的HTTP响应消息中发送
SMTP: 一个邮件内各个对象置于同一个邮件消息的多部分发送
# 邮件消息的格式
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232052559.png)
# 邮件消息的格式: 多媒体扩展
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232053464.png)
# 邮件访问协议
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232054363.png)
**SMTP: 发送/存储邮件到接收方的邮件服务器。**
- 发送方：用户代理使用 SMTP 将邮件推入其邮件服务器  邮件服务器再用 SMTP 将邮件转发到接收方的邮件服务器
**邮件访问协议：接收方通过用户代理使用从其邮件服务器上取回邮件。**
- POP (Post Office Protocol [RFC 1939])：110端口号
	- 身份认证 (代理 <-->服务器)、下载邮件；POP3
- IMAP (Internet Mail Access Protocol [RFC 1730])： 143端口
	- 更多的特性，允许用户像对待本地邮箱那样操纵远程邮箱的邮件，包括存储邮件到邮件服务器上等操作。
- HTTP: gmail, Hotmail, Yahoo! Mail, etc.
**取邮件是一个拉操作，SMTP是一个推协议。**
## POP3协议
**简单、功能有限。**
- 在用户代理打开了一个到邮件服务器端口 110 上的TCP连接后，开始工作。
**工作步骤（三阶段）：**
- **特许阶段：** 用户代理发送用户名和口令获得下载邮件的特许，身份认证。
- **事务处理阶段：** 用户代理取回报文，可对邮件进行某些操作。如做删除标记、取消删除标记、获取统计信息等。
- **更新阶段：** 邮件服务器删除带有删除标记的报文，结束POP会话。
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232058884.png)
**POP3的更多细节（缺陷）**
- 先前例子使用 “Download-and-delete”，**用户读取邮件后，服务器不再保存**
- **用户无法在邮件服务器上远程创建文件夹**
- Bob换客户端后不能再读邮件
- “Download-and-keep”模式：在不同客户机上的邮件拷贝
- **POP3的会话是无状态的**
## IMAP协议
保存所有邮件消息在一个位置： 服务器
允许用户在服务器的各文件夹中管理邮件消息
IMAP跟踪用户会话的状态信息 :
- 文件夹和邮件消息IDs与文件夹名字的映射
# 基于Web的电子邮件
1995年12月Hotmail 引入该技术。**用户使用浏览器收发电子邮件**。
用户代理是普通的浏览器，用户和其远程邮箱之间的通信通过HTTP进行：
- 发件人使用**HTTP 将电子邮件报文从其浏览器发送**到其邮件服务器上；
- 收件人**使用HTTP从其邮箱中取一个报文**到浏览器；
邮件服务器之间发送和接收邮件时，使用SMTP。
用户可以在远程服务器上以层次目录方式组织报文。
 