
# ICMP协议
为了更有效地转发IP数据报和提高交付成功的机会，在网际层使用了网际控制报文协议ICMP (Internet Control Message Protocol) 。ICMP允许主机或路由器报告差错情况和提供有关异常情况的报告。ICMP是互联网的标准协议。但ICMP不是高层协议(因为ICMP报文是装在IP数据报中，作为其中的数据部分)，而是IP层的协议。ICMP报文作为IP层数据报的数据，加上数据报的首部，组成IP数据报发送出去。<br />![](https://img-blog.csdnimg.cn/85cfb26d03384f88943ee340b761b5e0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_16,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=RQols&originHeight=287&originWidth=520&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# ICMP协议的种类
ICMP报文的种类有两种，即**ICMP差错报告报文**和**ICMP询问报文**。<br />ICMP报文的前4个字节是统一的格式，共有三个字段:类型、代码和检验和。接着的4个字节的内容与ICMP的类型有关。最后面是数据字段，其长度取决于ICMP的类型。<br />![](https://img-blog.csdnimg.cn/3c621f5b0acc477d98df9a0d103b9a81.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=mkLmk&originHeight=239&originWidth=654&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

表中给出的ICMP差错报告报文共有四种，即:

1.  终点不可达，当路由器或主机不能交付数据报时就向源点发送终点不可达报文。 
2.  时间超过，当路由器收到生存时间为零的数据报时，除丢弃该数据报外，还要向源点发送时间超过报文。当终点在预先规定的时间内不能收到一个数据报的全部数据报片时，就把己收到的数据报片都丢弃，并向源点发送时间超过报文。 
3.  参数问题，当路由器或目的主机收到的数据报的首部中有的字段的值不正确时，就丢弃该数据报，并向源点发送参数问题报文。 
4.  改变路由(重定向)，路由器把改变路由报文发送给主机，让主机知道下次应将数据报发送给另外的路由器(可通过更好的路由)。 


# ICMP数据字段内容
所有的ICMP差错报告报文中的数据字段都具有同样的格式。把收到的需要进行差错报告的IP数据报的首部和数据字段的前8个字节提取出来，作为ICMP报文的数据字段。再加上相应的ICMP差错报告报文的前8个字节，就构成了ICMP差错报告报文。提取收到的数据报的数据字段前8个字节是为了得到运输层的端口号(对于TCP和UDP)以及运输层报文的发送序号(对于TCP)。这些信息对源点通知高层协议是有用的。整个ICMP报文作为IP 数据报的数据字段发送给源点。<br />![](https://img-blog.csdnimg.cn/c6e6d6b2475b4919b67eed52eac4bd89.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Ba1cD&originHeight=261&originWidth=693&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**不应发送ICMP差错报文的几种情况**

- 对ICMP差错报告报文，不再发送ICMP差错报告报文。
- 对第一个分片的数据报片的所有后续数据报片，都不发送ICMP差错报告报文。
- 对具有多播地址的数据报，都不发送ICMP差错报告报文。
- 有特殊地址(如127.0.0.0或0.0.0.0)的数据报，不发送ICMP差错报告报文。

**常用的两种ICMP询问报文**

1. **回送请求和回答**，ICMP回送请求报文是由主机或路由器向一个特定的目的主机发出的询问。收到此报文的主机必须给源主机或路由器发送ICMP回送回答报文。这种询问报文用来测试目的站是否可达以及了解其有关状态。
2. **请求和回答**，ICMP时间戳请求报文是请某台主机或路由器回答当前的日期和时间。在ICMP时间戳回答报文中有一个 32位的字段，其中写入的整数代表从1900年1月1日起到当前时刻一共有多少秒。时间戳请求与回答可用于时钟同步和时间测量。


# ICMP的应用
ICMP的一个重要应用就是**分组网间探测PING (Packet InterNet Groper)**，用来测试两台主机之间的连通性。PING使用了ICMP 回送请求与回送回答报文。PING是应用层直接使用网络层ICMP的一个例子。它没有通过运输层的TCP或UDP。

举个栗子：<br />	从武汉的一台PC到百度www.baidu.com 连通性的测试结果。PC一连发出四个ICMP回送请求报文。如果服务器正常工作而且响应这个ICMP回送请求报文(有的主机为了防止恶意攻击就不理睬外界发送过来的这种报文)，那么它就发回ICMP回送回答报文。由于往返的ICMP报文上都有时间戳，因此很容易得出往返时间。最后显示出的是统计结果:发送到哪个机器(IP 地址)，发送的、收到的和丢失的分组数(但不给出分组丢失的原因)，以及往返时间的最小值、最大值和平均值。<br />![](https://img-blog.csdnimg.cn/900df5908ce440d5af42bc74df81020f.png#crop=0&crop=0&crop=1&crop=1&id=k7Gz3&originHeight=180&originWidth=684&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

另外一个非常有用的是路由跟踪traceroute<br />	Traceroute从源主机向目的主机发送一连串的IP数据报，数据报中封装的是无法交付的UDP用户数据报”。第一个数据报P1的生存时间TTL设置为1。当P1到达路径上的第一个路由器R1时，路由器R1先收下它，接着把TTL的值减1。由于TTL等于零了，R1 就把P1丢弃了，并向源主机发送一个ICMP时间超过差错报告报文。<br />源主机接着发送第二个数据报P2,并把TTL设置为2。P2先到达路由器R1，R1收下后把TTL减1再转发给路由器R2。R2收到P2时TTL为1，但减1后TTL变为零了。R2就丢弃P2,并向源主机发送一个ICMP时间超过差错报告报文。这样一直继续下去。当最后个数据报刚刚到达目的主机时，数据报的TTL是1。主机不转发数据报，也不把TTL值减1。但因IP数据报中封装的是无法交付的运输层的UDP用户数据报，因此目的主机要向源主机发送ICMP终点不可达差错报告报文。<br />这样，源主机达到了自己的目的，因为这些路由器和最后目的主机发来的ICMP报文正好给出了源主机想知道的路由信息,到达目的主机所经过的路由器的IP地址，以及到达其中的每一个路由器的往返时间。<br />![](https://img-blog.csdnimg.cn/82574e09cf8c479984d51cdc664fecd7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_16,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=vPwq3&originHeight=294&originWidth=526&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
