[TOC]

# 1、ARP 协议

**ARP 协议**（Address Resolution Protocol）又叫地址解析协议，是根据 IP 地址获取物理地址的一个 TCP/IP 协议。



主机发送信息时将包含目标 IP 地址的 ARP 请求广播到局域网络上的所有主机，并接收返回消息，以此确定目标的物理地址；收到返回消息后将该IP地址和物理地址存入本机  ARP 缓存中并保留一定时间，下次请求时直接查询 ARP 缓存以节约资源。



**RARP 协议**（Reverse Address Resolution Protocol） 又叫反向地址转换协议允许局域网的物理机器从网关服务器的 ARP 表或者缓存上请求其 IP 地址。

------



# 2、ARP 协议工作原理

**ARP 缓存表：**每一台主机都设有一个**ARP高速缓存(ARP cache)**，里面有本局域网上的各主机和路由器的IP地址到硬件地址的映射表，这些都是该主机目前知道的一些地址。并且这个映射表还经常动态更新(新增或超时删除)。



当主机A要向本局域网上的某台主机B发送IP数据报时，就先在其ARP高速缓存中查看有无主机B的IP地址。

如有，就在ARP高速缓存中查出其对应的硬件地址，再把这个硬件地址写入MAC帧，然后通过局域网把该MAC帧发往此硬件地址。也有可能查不到主机B的IP地址的项目。这可能是主机B才入网，也可能是主机A刚刚加电，其高速缓存还是空的。在这种情况下，主机A就自动运行ARP, 寻找主机B的硬件地址。

------



## 2.1、同一局域网下

1. ARP进程在本局域网上广播发送一个ARP请求分组。ARP请求分组的主要内容是：`我的IP地址是209.0.0.5， 硬件地址是00-00-C0-15-AD-18。 我想知道IP地址为209.0.0.6的主机的硬件地址。`

   ![image-20240412103022180](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121031084.png)

2. 在本局域网上的所有主机上运行的ARP进程都收到此ARP请求分组。

3. 主机B的IP地址与ARP请求分组中要查询的IP地址一致，就收下这个ARP请求分组，并向主机A发送ARP响应分组，同时在这个ARP响应分组中写入自己的硬件地址。由于其余的所有主机的IP地址都与ARP请求分组中要查询的IP地址不一致，因此都不理睬这个ARP请求分组。 ARP响应分组的主要内容是：`我的IP地址是209.0.0.6，我的硬件地址是08-00-2B-00-EE-0A。`虽然ARP请求分组是广播发送的，但ARP响应分组是普通的单播，即从一个源地址发送到一个目的地址。

   ![image-20240412103204739](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121032788.png)

4. 主机A收到主机B的ARP响应分组后，就在其ARP高速缓存中写入主机B的IP地址到硬件地址的映射。



## 2.2、不同局域网下



1. 主机H1发送给H2的IP数据报首先需要通过与主机H1连接在同一个局域网上的路由器R1来转发。因此主机H1这时需要把路由器R1的IP地址IP3解析为硬件地址HA3，以便能够把IP数据报传送到路由器R1。

2. R1从转发表找出了下一跳路由器R2,同时使用ARP解析出R2的硬件地址HA5。于是IP数据报按照硬件地址HA5转发到路由器R2。路由器R2在转发这个IP数据报时用类似方法解析出目的主机H2的硬件地址HA2,使IP数据报最终交付主机H2。

   ![image-20240412103217473](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121032525.png)

------



# 3、ARP 欺骗

## 3.1、ARP 欺骗原理

ARP 协议是建立在网络中各个主机互相信任的基础上的，局域网络上的主机可以自主发送 ARP 应答消息，其他主机收到应答报文时不会检测该报文的真实性就会将其记入本机 ARP 缓存。由此攻击者就可以向某一主机发送伪ARP应答报文，使其发送的信息无法到达预期的主机或到达错误的主机，这就构成了一个ARP欺骗。




1. 主机A、B之间互相通信。攻击者有A、B两台主机的IP、MAC地址。
2. 攻击者发送一个ARP 请求包给主机B，把包中的 IP 设为A的IP地址，mac设为自己的MAC地址。
3. 主机B收到ARP 请求后，更新它的ARP表，把主机A的MAC地址（IP_A, MAC_A）改为（IP_A, MAC_C）。
4. 当主机B要发送数据包给主机A时，它根据ARP表来封装数据包的报头，把目的MAC地址设为MAC_C，而非MAC_A。
5. 当交换机收到B发送给A的数据包时，根据此包的目的MAC地址（MAC_C）而把数据包转发给攻击者C。
6. 攻击者收到数据包后，可以把它存起来后再发送给A，达到偷听效果。攻击者也可以篡改数据后才发送数据包给A，造成伤害。




## 3.2、ARP 欺骗实验

为了使实验效果明显，这里使用 **ARP欺骗 + DNS劫持**来演示



**攻击机：**192.168.91.132

**被害机：**192.168.91.129



1、首先使用被害机访问一下贴吧，看一下访问效果

![image-20240412103237835](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121032925.png)



2、检查攻击机，有没有开启IP转发

```
sysctl net.ipv4.ip_forward

#如果为0，使用下面命令修改为1
sysctl -w net.ipv4.ip_forward=1

#或者修改此处
vi /etc/sysctl.conf
```



3、开启Apache服务，并访问

```
service apache2 start

netstat -tnlp | grep 80
```

![image-20240412103301276](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121033337.png)



4、修改 DNS 文件

```
vi /etc/ettercap/etter.dns

* A 192.168.91.132

"*" 代表DNS劫持的域名，例如“*”改为www.qq.com,那么被劫持的对象访问了WWW.qq.com会跳转到192.168.200.129的apache页面上来。
“A 192.168.91.132”指的是劫持后跳转的地址信息，
```

![image-20240412103319778](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121033855.png)



5、启动 ettercap 工具图形界面

![image-20240412103338467](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121033527.png)



6、配置网卡

![image-20240412103348111](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121033170.png)



7、扫描存活主机

![image-20240412103400230](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121034304.png)



8、列出扫描出来的主机

![image-20240412103414874](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121034945.png)



9、选择192.168.91.129（被害者主机）后点击 Add to Target1 会出现：`Host 192.168.200.134 Add to Target1`

![image-20240412103426875](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121034944.png)



然后在选择192.168.91.2网关后点击 Add to Target2 出现：`Host 192.168.200.1 Add to Target2`

![image-20240412103435232](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121034334.png)



10、开启ARP毒化，选择远程嗅探连接和远程窃听

![image-20240412103453938](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121034015.png)



11、配置插件

Plugins->Manage plugins，然后双击dns_spoof

![image-20240412103506947](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121035025.png)



12、成功DNS劫持

这时候不管受害者电脑访问任何http协议的网站，都会跳转到指定的界面

![image-20240412103515964](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121035040.png)



访问HTTPS协议网站，会显示为无法连接

![image-20240412103527911](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121035973.png)



13、查看受害者的ARP表

![image-20240412103536649](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121035701.png)



可以看到攻击者的MAC地址和网关的MAC地址一致，正确情况下MAC地址不可能一致

ping找到劫持的IP

![image-20240412103544704](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202404121035753.png)