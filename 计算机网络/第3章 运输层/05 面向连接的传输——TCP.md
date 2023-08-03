**点到点 (point-to-point)：**
- 一个发送方, 一个接收方 
**可靠、有序的字节流：**
- 没有 “报文边界”
**全双工数据：**
- 同一个连接上双向数据流
- MSS: maximum segment size
- MTU: maximum transmit unit
**累积确认 (cumulative ACKs)**
**流水线：** ^994bfa
- TCP拥塞和流量控制设置滑动窗口大小
**面向连接 (connection-oriented)：** 
- 在数据交换前，初始化发送方与接收方状态，进行握手 (交换控制信息)
**流量控制 (flow controlled)：**
- 发送方不能淹没接收方
# TCP 报文段的首部格式 
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304131945815.png)
源端口和目的端口字段——各占 2 字节。端口是运输层与应用层的服务接口。运输层的复用和分用功能都要通过端口才能实现。  
序号字段——占 4 字节。TCP 连接中传送的数据流中的每一个字节都编上一个序号。序号字段的值则指的是本报文段所发送的数据的第一个字节的序号。 
确认 ACK(ACKnowledgment) —— 只有当 ACK = 1 时确认号字段才有效。当 ACK = 0 时，确认号无效。 
首部长度——占 4 位，它指出 TCP 报文的首部长度，以32 比特的字（4 字节）为计算单位。由于TCP有可选项，首部长度可变。  
保留字段——占 6 位，保留为今后使用，但目前应置为 0。 
CWR(Congestion Window Reduced)：CWR标志与后面的ECE标志用于IP首部的ECN字段。ECE标志为1时，则通知对方将拥塞窗口变小。
ECE(ECN-Echo)：ECE标志表示ECN-Echo。置为1会通知通信对方，从对方到这边的网络有拥塞。在收到数据包的IP首部中ECN为1时将TCP首部中的ECE设置为1 (见P185)。
紧急 URG (URGent)—— 当 URG = 1 时，表明紧急指针字段有效。它告诉系统此报文段中有紧急数据，应尽快传送(相当于高优先级的数据)。 
确认 ACK(ACKnowledgment) —— 只有当 ACK = 1 时确认号字段才有效。当 ACK = 0 时，确认号无效。 
推送 PSH (PuSH) —— 发送方收到push指示后快速发送该数据及其之前的数据，接收 TCP 收到 PSH = 1 的报文段，就尽快地交付接收应用进程，而不再等到整个缓存都填满了后再向上交付。  
复位 RST (ReSeT) —— 当 RST = 1 时，表明 TCP 连接中出现严重差错（如由于主机崩溃或其他原因），必须释放连接，然后再重新建立运输连接。 
同步 SYN(SYNchronize) —— 同步 SYN = 1 表示这是一个连接请求或连接接受报文。 
终止 FIN (FINis) —— 用来释放一个连接。FIN = 1 表明此报文段的发送端的数据已发送完毕，并要求释放运输连接。 
窗口字段 —— 占 2 字节，用来让对方设置发送窗口的依据，单位为字节。
检验和 —— 占 2 字节。检验和字段检验的范围包括首部和数据这两部分。在计算检验和时，要在 TCP 报文段的前面加上 12 字节的伪首部。
紧急指针字段 —— 占 16 位，指出在本报文段中紧急数据共有多少个字节（紧急数据放在本报文段数据的最前面）。  
选项字段 —— 长度可变。TCP 最初只规定了一种选项，即**最大报文段长度 MSS**。MSS 告诉对方 TCP：“我的缓存所能接收的报文段的数据字段的最大长度是 MSS 个字节。” 
填充字段 —— 这是为了使整个首部长度是 4 字节的整数倍。 
# TCP序号、ACKs
**序号：**
- 报文段中第1个字节在字节流中的位置编号
**确认号：**
- 期望从对方收到下一个字节的序号
- 累积确认 (cumulative ACK)

Q: 接收方如何处理失序报文段？
A: TCP规范没有说明， 由实现者自行选择实现：抛弃/缓存
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304131958938.png)

# TCP序号、ACKs
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132004427.png)
这里要注意的关键是，B-to-A段上的ACK编号（43）比触发该ACK的A-toB段上的序列号（42）多 1
同样，最后一个A至B段的ACK编号（80）比触发该ACK的B至A段的序列号（79）多一个。
描述序号和确认号的实现方法。
举例说明A->B要发送一个数据流500 000字节的文件，MSS是1000字节，数据流首字节编号为0，则TCP将为该数据流构建500个报文段，第一个0~999，第二个1000~1999，…
确认号：A->B，A期望从B收到的下一个字节的序号
1. A已收到来自B的编号为0~535的所有字节，A发送给B的ACK，确认号为536
2. **A已收到来自B的编号为0~535的报文段 和 900~1000的报文段，但还没收到536~899的报文段，A发送给B的确认号为536**

# 思考
1. 为什么在TCP首部中有一个首部长度字段，而UDP的首部中就没有这个字段？
		TCP首部除固定长度部分外，还有选项，因此TCP首部长度是可变的。UDP首部长度是固定的。
		
2. 一个TCP报文段的数据部分最多有多少个字节？为什么？
		因为TCP报文段的数据部分加上TCP首部的20字节，再加上IP首部的20字节，正好是IP数据报的最大长度65535。所以TCP报文段的数据部分最多有65535-40=65495个字节。
3. 如果用户要传送的数据的字节长度超过TCP报文字段中的序号字段可能编出的最大序号，问还能否用TCP来传送？
		可以使用TCP协议来传送，因为序号字段编号到最大值，又重新开始从0开始编号。

# TCP往返时延 (RTT) 和超时
**Q: 如何设置 TCP 超时值？**
- 比 RTT长，但 RTT是变化的！
- **太短**：过早超时，产生不必要的重传
- **太长**：对报文段的丢失响应太慢

**Q: 如何估计 RTT?**
- SampleRTT: 从发送报文段到接收到ACK的测量时间
	- 忽略重传
- SampleRTT 会变化，希望估计的RTT“较平滑”
- 平均最近的多次测量值，并不仅仅是当前SampleRTT
$$
EstimatedRTT = (1-\alpha) \times EstimatedRTT + \alpha \times SampleRTT
$$
这样，每次采用新的SampleRTT时，TCP都会重新计算估计的RTT。该过程称为指数加权移动平均值，如此处的等式所示。
其中，alpha反映了最新测量对估计的RTT的影响；实现中使用的alpha的典型值为0.125。
底部的图表显示了马萨诸塞州主机和法国主机之间测得的RTT，以及估计的“平滑” RTT。
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132023148.png)

**超时间隔：EstimatedRTT + “安全余量”**
- EstimatedRTT 较大的变化：需要更大的安全余量
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132031943.png)
**DevRTT**: EstimatedRTT 与 SampleRTT 之间差值的指数加权移动平均值 
$$
DevRTT = (1- \beta)\times DevRTT + \beta \times |SampleRTT-EstimatedRTT| \ (typically, \beta = 0.25)
$$
初始时，TimeoutInterval 设置为1秒
第一个样本RTT获得后
$$EstimatedRTT = SampleRTT \newline$$
$$DevRTT = SampleRTT/2$$
$$TimeoutInterval = EstimatedRTT + max(G, K \times DevRTT)\  （K=4，G是用户设置的时间粒度）
$$
# 可靠数据传输机制
## TCP可靠数据传输
TCP在IP不可靠服务的基础上创建可靠数据传输服务
- 流水线发送报文段
- 累积确认
- 使用单个重传计时器
重传触发事件
- 超时事件
- 重复ACK
后面先考虑简化的TCP发送方：
- 忽略重复ACK
- 忽略流量控制，拥塞控制
## TCP 发送方事件 (简化)
**事件：从应用程序接收数据**
- 用序号创建报文段
- 序号是报文段中第一个数据字节的数据流编号
- 如果还未启动定时器，则启动定时器 
	- 定时器用于最早的尚未确认的报文段
	- 设置超时间隔： **TimeOutInterval **

**事件：超时**
- 重传导致超时的报文段
- 重启定时器
**事件：收到 ACK**
- 如果确认了先前未被确认的报文段 
	- 更新被确认的报文段序号
	- 如果还有未被确认的报文段，重启定时器
## TCP 发送方FSM (简化)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132048004.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132048490.png)
在TCP发送方有3个与发送和重传有关的主要事件 (event) ，对应的重要参数：
- SendBase：最早未被确认的字节的序号
- SendBase-1：接收方已正确按序接收到的数据的最后一个字节序号（上次累计已确认的字节序号）
- TCP累积确认，y 确认了字节编号在 y 之前的所有字节都已收到。
- 若 y > SendBase，则该ACK是在确认一个或多个先前未被确认的报文段，因此发送方更新它的SendBase。
## TCP 接收方：ACK 产生的建议 [RFC 5681]
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132049978.png)
## TCP 重传情形
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132050121.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132051474.png)

为了加强对TCP可靠性的理解，让我们看一些重传方案。
在第一种情况下，传输TCP段，并且丢失ACK，并且TCP超时机制导致传输另一个副本，然后重新确认发送方。
在第二个示例中，发送并确认了两个段，但是第一个段存在过早的超时，该超时会重新发送。
注意，当接收到该重传的段时，接收方已经接收到了前两个段，因此，对于迄今为止接收到的两个段，都重新发送累积的ACK，而不是仅针对该第一段重新发送ACK。
在最后一个示例中，再次发送了两个段，第一个ACK丢失了，但是第二个ACK，累积的ACK到达了发送方，然后发送器可以知道第三个段，即使ACK已经到达，也可以发送第三个段 因为第一个报文段丢失了。
## TCP 快速重传
超时触发重传存在问题：**超时周期往往太长**
- 重传丢失报文之前要等待很长时间，因此增加了网络的时延
发送方可以在超时之前通过检测重复的ACK检测报文段丢失
- 发送方常常一个接一个地发送很多报文段
- 如果报文段丢失，则发送方将可能接收到很多重复的 ACKs
如果发送方收到3个对同样报文段的确认（在收到第一个确认后又收到3个ACK），则发送方认为该报文段之后的数据已经丢失。
- 启动**快速重传**：在定时器超时之前重发丢失的报文段
**TCP 快速重传:**
如果发送方收到相同数据的3个其他ACK（“三个重复ACK”），则重新发送具有最小序号的未确认的报文段
- 未确认的报文段很可能丢失了，所以不必等待超时
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132053364.png)
## TCP 快速重传算法
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132053857.png)

**例题：**
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132054727.png)
(1) = 501
(2) = 551
(3) = 551
(4) = 611
(5) = 501
(6) = 50
(7) = 611
# 流量控制
## TCP 流量控制 (flow control)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132055610.png)
Q: 如果网络层交付数据的速度快于应用层从套接字缓冲区移出数据的速度，那会发生什么？
- 接收方的缓冲区溢出！

**流量控制：**
接收方控制发送方，因此发送方不会由于发送太多、发送得太快而使接收方的缓冲区溢出！
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304132057349.png)
TCP 接收方通过在TCP头部中包含 rwnd 域，通告其接收缓冲区的剩余空间 (假设 TCP 接收方丢弃失序的报文段)
$$rwnd = RcvBuffer – 
       (LastByteRcvd – LastByteRead)
$$
- RcvBuffer 大小通过套接字选项设置（典型缺省值为4096字节）
- 许多操作系统自动调整 RcvBuffer
发送方限制未确认（发送中）的数据不超过接收窗口 rwnd
$$LastByteSent - LastByteAcked \leq rwnd
$$
保证接收缓冲区不溢出
# 连接管理
## TCP 连接管理
发送方/接收方在进行数据交换之前，需先进行“握手” ：
- 同意建立连接（彼此知道对方愿意建立连接）
- 同意连接参数（例如，初始序号 **seq #**, 接收缓冲区大小 **rcvBuffer**）
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304202042991.png)
## 2次握手建立连接
**Q: 2次握手在以下情况下是否总是可工作？**
- 可变时延
- 由于消息丢失而重传消息（如重传 req_conn(x)）
- 报文排序
- 不能够看到对方
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304202047871.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304202047729.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304202047177.png)
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304202050764.png)
3 次握手完成两个重要功能，既要双方做好发送数据的准备工作，也要允许双方就初始序列号进行协商，这个序列号在握手过程中被发送与确认。
现在把3次握手改成2次握手，死锁是可能发生的。
例如，A和B 通信。
设A 给B发送一个建立连接的请求分组，B收到了这个分组，并发送了ACK分组。按照2次握手的约定，B认为此连接已经建立了，可以开始发送数据分组了。
但是，A在B的ACK分组丢失的情况下，不知道B是否已准备好，不知道B建议的序号，也不知道B是否同意A建议的序号，甚至怀疑B是否收到A发送的连接请求分组。
因而，A认为此连接未建立，忽略B发来的任何分组，只等待接收连接确认分组ACK。而B在发出的分组超时后，又重复发送同样的分组。这样就形成了死锁。
## TCP 3次握手
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304202051860.png)
**Step 1**: 客户机向服务器发送 TCP SYN 报文段
- 指定初始序号
- 没有数据
**Step 2**: 服务器收到SYN报文段，用 SYNACK报文段回复
- 服务器为该连接分配缓冲区和变量
- 指定服务器初始序号
**Step 3**: 客户机接收到 SYNACK，用ACK报文段回复，可能包含数据
## TCP 3次握手FSM
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304202056070.png)
## TCP关闭连接——4次挥手
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304202057952.png)
（1）客户机向服务器发送TCP FIN控制报文段；
（2）服务器收到FIN，回复ACK确认，进入半关闭连接状态；
（3）服务器关闭客户机的连接，发送FIN给客户机；
（4）客户机收到FIN，回复ACK确认 
进入 “超时等待” – 将对接收到的FIN进行确认
服务器接收ACK，连接关闭
>注意：少许修改，可以处理并发的FIN。
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304202058818.png)
## TCP 释放连接
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304202059057.png)
