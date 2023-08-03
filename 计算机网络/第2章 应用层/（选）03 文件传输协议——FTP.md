# FTP: 文件传输协议 File Transfer Protocol
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232038967.png)
传输文件到远程主机/从远程主机下载文件
client/server模式
- client: 发起传输的一方
- server: 远程主机
ftp: RFC 959
ftp服务器: 端口号 21

FTP客户首先发起建立1个与FTP服务器**端口号21**之间的**TCP控制连接**，指定TCP作为传输层协议
客户在建立的控制连接上获得身份认证
客户在建立的控制连接上发送命令来浏览远程主机的目录
当服务器接收到1个文件传输命令时, 在服务器**端口号20**创建1个与客户的**TCP数据连接**
1个文件传输后，服务器结束这个TCP数据连接
# FTP: 独立的控制连接, 数据连接
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232039585.png)
#  FTP: 文件传输协议
## 控制连接：
USER-PI(protocol interpreter)：用户协议解释器
SERVER-PI：服务器协议解释器
## 数据连接：
user-DTP(Data Transfer Process)：用户数据传输进程
server-DTP：服务器数据传输进程
# FTP: 文件传输系统模型一
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232040689.png)
#  FTP: 文件传输系统模型二
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202303232040141.png)
# FTP数据连接建立方式
## 主动模式:
客户端发送PORT命令
        PORT h1,h2,h3,h4,p1,p2 
     （h1-h4是IP地址，p1-p2是端口号）
服务器根据PORT命令指定的客户端地址和端口号发起数据连接
## 被动模式:
客户端发送PASV命令
服务器返回监听的地址和端口号
客户端发起数据连接
# FTP命令和应答
## 常见命令:
在控制连接上发送ASCII文本
USER username
PASS password
LIST：返回当前远程目录的文件列表
RETR filename：获取远程主机当前目录下的1个文件(get)
 STOR filename：存放1个文件到远程主机当前目录下(put) 
## 常见应答:
状态码及其相应短语 (同 HTTP)
331 Username OK, password required
125 data connection already open; transfer starting
425 Can’t open data connection
452 Error writing file
