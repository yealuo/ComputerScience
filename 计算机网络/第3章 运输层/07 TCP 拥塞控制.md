# TCP 拥塞控制：AIMD
**方法：发送方增加发送速率，直到丢包（拥塞）发生，然后在发生丢包事件时降低发送速率**
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304231501711.png)
乘性减 (Multiplicative decrease) 详情：发送速率： 
- 检测到 **3 个重复 ACK**，**减半** (TCP Reno)
- 由于**超时**而发生**丢包**，**降低到 1 MSS** (maximum segment size) (TCP Tahoe)

为什么 **A**I**M**D?  
AIMD —— 一个分布式异步算法，已显示出：
- 在整个网络范围内优化拥堵流量！
- 具有理想的稳定性
# TCP 拥塞控制：详情
**TCP 发送动作：**
粗略地：发送 cwnd 字节，在 RTT 时间内等待 ACKs，然后发送更多的字节
$$TCP\ rate \approx \frac{cwnd}{RTT} bytes/sec$$
**TCP 发送方限制传输：**$LastByteSent\ – \ LastByteAcked \le cwnd$
根据观察到的网络拥塞，cwnd 动态调整（实现TCP拥塞控制）
**TCP 发送方如何感知网络拥塞：**
丢包事件：**超时** 或者 **3个重复ACK**
发生丢包事件后，TCP发送方降低速率 (拥塞窗口 cwnd)
**TCP 拥塞控制三阶段：**
慢启动
拥塞避免
快速恢复
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304231513223.png)
>实际上是：LastByteSent – LastByteAcked  ≤  min{cwnd, rwnd}，假设接收缓存足够大，以至于可以忽略接收窗口 rwnd 的限制（与流量控制形成对比），因此在发送方中未被确认的数据量仅受限于 cwnd。
# TCP 慢启动 (slow start)
当连接开始的时候，以指数速率增加发送速率，直到第1次报文丢失事件发生为止：
**初始，拥塞窗口值 cwnd = 1 MSS
每 RTT 倍增 cwnd
每收到一个ACK，增加 cwnd**

**总结: 初始速率很低，但以指数快地增加！
可获得的带宽可能** >> MSS/RTT
- 希望尽快达到期待的速率，或者达到某个阈值 ssthresh
# TCP：从慢启动进入到拥塞避免
Q: 什么时候从**指数**增长变为**线性**增长？
A: 当 cwnd 达到慢启动阈值 ssthresh。

**实现方法:**
设置一个变化的阈值：ssthresh 
发生**丢包**事件后，ssthresh 设置为丢包事件前的 cwnd 的一半
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202306262346180.png)

# TCP对拥塞事件 (丢包) 的反应
怎么理解不同的丢包事件？
**3 个重复的ACK**表明网络仍具有传输一些数据的能力
在三个重复ACK之前收到**超时**是“更加严重的警告”
**超时：**
TCP RENO: cwnd 设置为 **1 MSS**，重新开始慢启动（指数增长到 ssthresh，然后线性增长）
TCP Tahoe: cwnd 设置为 1 MSS，重新开始慢启动（指数增长到 ssthresh，然后线性增长）
**3个重复ACK：**
TCP RENO: cwnd 设置为 **减半 + 3 MSS**，然后线性增长
TCP Tahoe: cwnd 设置为 1 MSS，然后线性增长
>这里 重复 理解为 冗余，更确切一点，即收到一个ACK后，后面三个重复的ACK是冗余的ACK。
# 慢开始和拥塞避免算法的实现举例 
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304231534077.png)
>初始化时，将拥塞窗口置为 1。图中的窗口单位不使用字节而使用报文段。
慢启动门限的初始值设置为 16 个报文段，即 ssthresh = 16。
# TCP从慢启动、拥塞避免切换到快速恢复
在慢启动或拥塞避免阶段收到三个重复ACK后，$ssthresh = cwnd/2, cwnd = ssthresh + 3*MSS$,进入**快速恢复**状态:
对于引起TCP进入快速恢复的缺失报文段，对收到的每个**重复ACK**，   $cwnd = cwnd + 1*MSS$，依旧处于**快速恢复**状态
当收到丢失报文段的ACK（new ACK），降低 cwnd，$cwnd = ssthresh$，进入**拥塞避免**状态
当出现超时事件， $ssthresh = cwnd/2, cwnd = 1*MSS$，进入**慢启动**状态
# TCP拥塞事件FSM
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304231541813.png)
# 总结: TCP 拥塞控制
初始阈值 ssthresh = 64KB (或16个MSS)，初始窗口 cwnd = 1
当 cwnd  <  ssthresh 时，发送方处于**慢启动**阶段，cwnd 指数增长
当 cwnd  ≥  ssthresh 时，发送方处于**拥塞避免**阶段，cwnd 线性增长
当出现**3个冗余ACK**事件时
- $ssthresh = cwnd/2$
- $cwnd = ssthresh + 3*MSS (TCP Reno)$
- $cwnd = 1 (TCP Tahoe)$
- 发方进入**快速恢复**阶段，此时每收到一个重复的ACK，cwnd 则增加 1MSS；如果收到新的ACK，$cwnd = ssthresh$
当出现**超时**事件时，$ssthresh = cwnd/2$，$cwnd = 1$
# TCP 平均吞吐量
作为窗口长度和RTT的函数，TCP的**平均吞吐量**是什么？
- 假设忽略慢启动
- 设发生丢包时，窗口长度是W（字节）
- 如果窗口为 W，吞吐量是 𝑾/𝑹𝑻𝑻
- 当丢包发生后，窗口降为 𝑾/𝟐  ，吞吐量为 𝑾/𝟐𝑹𝑻𝑻
- 一个连接的**平均吞吐量为 (𝟎.𝟕𝟓𝑾)/𝑹𝑻𝑻**
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304231550812.png)
# TCP 未来
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304231550074.png)
# TCP 公平性
**公平目标：** 如果 K 个 TCP 会话共享带宽为 R 的瓶颈链路，每个会话应有 R/K 的平均链路速率 
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304231551717.png)
# TCP 为什么是公平的？
比如：两个竞争会话：
随着吞吐量的增加，按照斜率1加性增加
等比例地乘性降低吞吐量
![image.png](https://picgo-1310230783.cos.ap-chengdu.myqcloud.com/obsidian/202304231552836.png)
**Is TCP fair?**
A: 是的，在理想的假设下：
相同的RTT
固定会话数，仅用于避免拥塞
# TCP 公平性：所有网络应用程序都必须“公平”吗？
**公平性和UDP**
多媒体应用通常不使用TCP
- 不希望通过拥塞控制来抑制速率
用UDP代替TCP：
- 音频/视频以恒定速率发送，能容忍报文丢失
没有“互联网警察”管制拥塞控制

**公平性和并行TCP 连接**
应用程序可以打开两个主机之间的多个并行连接
网络浏览器执行此操作，例如，在速率为R的链路上执行9个连接：
如果新应用要求1个TCP，获得速率为R / 10
如果新应用程序要求11个并行TCP，获得带宽超过R / 2 

# 第三章 总结
传输层服务背后的原理:
- 多路复用, 多路分解
- 可靠数据传输
- 流控
- 拥塞控制
因特网中的实例和实现
- UDP
- TCP
下面:
- 离开网络的 “边界” (应用层，传输层)
- 进入网络 “核心”






