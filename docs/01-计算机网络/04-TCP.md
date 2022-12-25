
# 传输控制协议TCP

## TCP协议的主要特点

1. TCP是面向连接的运输层协议。这就是说，应用程序在使用TCP协议之前，必须先建立TCP连接。在传送数据完毕后，必须释放已经建立的TCP连接。
2. 每一条TCP连接只能有两个端点(endpoint), 每一条TCP连接只能是点对点的(一对一)。
3. TCP提供可靠交付的服务。通过TCP连接传送的数据，无差错、不丢失、不重复，并且按序到达。
4. TCP提供全双工通信。TCP允许通信双方的应用进程在任何时候都能发送数据。TCP连接的两端都设有发送缓存和接收缓存，用来临时存放双向通信的数据。
5. 面向字节流。TCP中的“ 流" (stream)指的是流入到进程或从进程流出的字节序列。“面向字节流”的含义是:虽然应用程序和TCP的交互是一次一个数据块(大小不等)，但TCP把应用程序交下来的数据仅仅看成是一连串的无结构的字节流。


## TCP报文的首部格式
TCP虽然是面向字节流的，但TCP传送的数据单元却是报文段。一个TCP报文段分为首部和数据两部分，而TCP的全部功能都体现在它首部中各字段的作用。TCP报文段首部的前20个字节是固定的，后面有4n字节是根据需要而增加的选项(n是整数)。因此TCP首部的最小长度是20字节。

![](https://img-blog.csdnimg.cn/51ee39986d24475a97c13dcde9225ed9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=NkXLz&originHeight=437&originWidth=655&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

1.  **源端口和目的端口：**各占2个字节，分别写入源端口号和目的端口号。 
2.  **序号：**占4字节。序号范围是[0, 2^32- 1],共2^32 (即4294967296)个序号。序号增加到2^32-1后，下一个序号就又回到0。 
3.  **确认号：**占4字节，是期望收到对方下一个报文段的第一个数据字节的序号。若确认号=N，则表明:到序号N-1为止的所有数据都已正确收到。 
4.  **数据偏移：**占4位，它指出TCP报文段的数据起始处距离TCP报文段的起始处有多远。这个字段实际上是指出TCP报文段的首部长度。 
5.  **保留：**占6位，保留为今后使用，但目前应置为0。 
6.  **紧急URG (URGent)：**当URG=1时，表明紧急指针字段有效。它告诉系统此报文段中有紧急数据，应尽快传送(相当于高优先级的数据)，而不要按原来的排队顺序来传送。 
7.  **确认ACK (ACKnowledgment)：**仅当 ACK= 1时确认号字段才有效。当ACK=0时，确认号无效。TCP规定，在连接建立后所有传送的报文段都必须把ACK置1。 
8.  **推送PSH (PuSH)：**当两个应用进程进行交互式的通信时，有时在一端的应用进程希望在键入一个命令后立即就能够收到对方的响应。在这种情况下，TCP就可以使用推送(push)操作。这时，发送方TCP把PSH置1，并立即创建一个报文段发送出去。接收方TCP收到PSH=1的报文段，就尽快地(即“推送”向前)交付接收应用进程，而不再等到整个缓存都填满了后再向上交付。 
9.  **复位RST (ReSeT)：**当RST = 1时，表明TCP连接中出现严重差错(如由于主机崩溃或其他原因)，必须释放连接，然后再重新建立运输连接。RST置1还用来拒绝一个非法的报文段或拒绝打开一个连接。RST也可称为重建位或重置位。 
10.  **同步SYN (SYNchronization)：**在连接建立时用来同步序号。当SYN = 1而ACK = 0时，表明这是一个连接请求报文段。对方若同意建立连接，则应在响应的报文段中使SYN = 1和ACK = 1。因此，SYN 置为1就表示这是一个连接请求或连接接受报文。 
11.  **终止FIN (FINis， 意思是“完”“终”)：**用来释放一个连接。当FIN = 1时，表明此报文段的发送方的数据已发送完毕，并要求释放运输连接。 
12.  **窗口：**占2字节。窗口值是[0, 2^l6-1]之间的整数。窗口值告诉对方:从本报文段首部中的确认号算起，接收方目前允许对方发送的数据量(以字节为单位)。 
13.  **检验和:**占2字节。检验和字段检验的范围包括首部和数据这两部分。。 
14.  **紧急指针:**占2字节。紧急指针仅在URG = 1时才有意义，它指出本报文段中的紧急数据的字节数(紧急数据结束后就是普通数据)。即使窗口为零时也可发送紧急数据。 
15.  **选项:**长度可变，最长可达40字节。当没有使用“选项“时，TCP的首部长度是20字节。 
16.  **时间戳:**占10字节，其中最主要的字段是时间戳值字段(4字节)和时间戳回送回答字段(4字节)。时间戳选项有以下两个功能: 
   - 第一，用来计算往返时间RTT 。发送方在发送报文段时把当前时钟的时间值放入时间戳字段，接收方在确认该报文段时把时间戳字段值复制到时间戳回送回答字段。因此，发送方在收到确认报文后，可以准确地计算出RTT来。
   - 第二，用于处理TCP序号超过2^32 的情况，这又称为防止序号绕回PAWS (ProtectAgainst Wrapped Sequence numbers)。


# TCP的链接
每一条TCP连接有两个端点。TCP连接的端点叫做套接字(socket)或插口。根据RFC793的定义:端口号拼接到 IP 地址即构成了套接字。因此，套接字的表示方法是在点分十进制的 IP 地址后面写上端口号，中间用冒号或逗号隔开。例如，若IP地址是192.3.4.5 而端口号是80，那么得到的套接字就是(192.3.4.5: 80)。TCP连接的端点是个很抽象的套接字，即(IP 地址:端口号)。同一个IP地址可以有多个不同的TCP连接，而同一个端口号也可以出现在多个不同的TCP连接中。

TCP是面向连接的协议。运输连接是用来传送TCP报文的。TCP运输连接的建立和释放是每一次面向连接的通信中必不可少的过程。因此，运输连接就有三个阶段，即：连接建立、数据传送和连接释放。


## TCP的链接建立（三次握手）
TCP建立连接的过程叫做握手，握手需要在客户和服务器之间交换三个TCP报文段。

![](https://img-blog.csdnimg.cn/f1659e278971446b9d6d706936e13dde.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=XqTv9&originHeight=359&originWidth=661&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

1.  在打算建立TCP连接时，A向B发出连接请求报文段，这时首部中的同步位SYN = 1，同时选择一个初始序号seq = x。TCP规定，SYN报文段(即SYN = 1的报文段)不能携带数据，但要消耗掉一个序号。这时，TCP客户进程进入SYN-SENT (同步已发送)状态。 
2.  B收到连接请求报文段后，如同意建立连接，则向A发送确认。在确认报文段中应把SYN位和ACK位都置1，确认号是ack=x+1，同时也为自己选择一个初始序号 seq= y。这个报文段也不能携带数据，但同样要消耗掉一个序号。这时TCP服务器进程进入SYN-RCVD ( 同步收到)状态。 
3.  TCP客户进程收到B的确认后，还要向B给出确认。确认报文段的ACK置1,，确认号ack=y+ 1，而自己的序号seq=x+ 1。TCP的标准规定，ACK报文段可以携带数据。但如果不携带数据则不消耗序号，在这种情况下，下一个数据报文段的序号仍是seq=x+1.这时，TCP 连接已经建立，A进入ESTABLISHED (已建立连接)状态。当B收到A的确认后，也进入ESTABLISHED状态。 


## TCP连接的释放（四次挥手）
数据传输结束后，通信的双方都可释放连接。现在A和B都处ESTABLISHED状态A的应用进程先向其TCP发出连接释放报文段，并停止再发送数据，主动关闭TCP连接。<br />![](https://img-blog.csdnimg.cn/4fb8787e89084b288c077573a513531d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Rcg04&originHeight=419&originWidth=641&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

1.  A把连接释放报文段首部的终止控制位FIN置1,其序号seq = u，它等于前面已传送过的数据的最后一个字节的序号加1。 这时A进入FIN-WAIT-1 (终止等待1)状态，等待B的确认。TCP规定，FIN报文段即使不携带数据，它也消耗掉一个序号。 
2.  B收到连接释放报文段后即发出确认，确认号是ack=u+ 1，而这个报文段自己的序号是v，等于B前面已传送过的数据的最后一个字节的序号加1。然后B就进入CLOSE-WAIT (关闭等待)状态。这时的TCP连接处于半关闭(half-close)状态，即A已经没有数据要发送了，但B若发送数据，A仍要接收。也就是从B到A这个方向的连接并未关闭，这个状态可能会持续一段时间。 
3.  A收到来自B的确认后，就进入FIN-WAIT-2 (终止等待2)状态，等待B发出的连接释放报文段。若B已经没有要向A发送的数据，其应用进程就通知TCP释放连接。这时B发出的连接释放报文段必须使FIN= 1。现假定B的序号为w (在半关闭状态B可能又发送了一些数据)。B还必须重复上次已发送过的确认号ack= u + 1。这时B就进入LAST-ACK ( 最后确认)状态，等待A的确认。 
4.  A在收到B的连接释放报文段后，必须对此发出确认。在确认报文段中把ACK置1，确认号ack=w+ 1，而自己的序号是seq=u+ 1 (根据TCP标准，前面发送过的FIN报文段要消耗一个序号)。然后进入到TIME-WAIT (时间等待)状态。请注意，现在TCP连接还没有释放掉。必须经过时间等待计时器(TIME-WAIT timer)设置的时间2MSL后，A才进入到CLOSED状态。 


# TCP有限状态机
为了更清晰地看出TCP连接的各种状态之间的关系，给出了TCP的有限状态机。图中每一个方框即TCP可能具有的状态。每个方框中的大写英文字符串是TCP标准所使用的TCP连接状态名。状态之间的箭头表示可能发生的状态变迁。箭头旁边的字，表明引起这种变迁的原因，或表明发生状态变迁后又出现什么动作。请注意图中有三种不同的箭头。粗实线箭头表示对客户进程的正常变迁。粗虚线箭头表示对服务器进程的正常变迁。另一种细线 箭头表示异常变迁。<br />![](https://img-blog.csdnimg.cn/605c6dc0a3f441cda9c7d20669765bee.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_14,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=T5M8V&originHeight=506&originWidth=462&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
