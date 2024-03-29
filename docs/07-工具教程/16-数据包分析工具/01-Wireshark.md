Wireshark（前称Ethereal）是一个网络封包分析软件。网络封包分析软件的功能是截取网络封包，并尽可能显示出最为详细的网络封包资料。Wireshark使用WinPCAP作为接口，直接与网卡进行数据报文交换。在过去，网络封包分析软件是非常昂贵的，或是专门属于盈利用的软件。Ethereal的出现改变了这一切。在GNUGPL通用许可证的保障范围底下，使用者可以以免费的途径取得软件与其源代码，并拥有针对其源代码修改及客制化的权利。Ethereal是全世界最广泛的网络封包分析软件之一。Wireshark不是入侵侦测系统（Intrusion Detection System,IDS）。对于网络上的异常流量行为，Wireshark不会产生警示或是任何提示。然而，仔细分析Wireshark撷取的封包能够帮助使用者对于网络行为有更清楚的了解。Wireshark不会对网络封包产生内容的修改，它只会反映出流通的封包资讯。 Wireshark本身也不会送出封包至网络上。


# WireShark 快速分析数据包技巧

1. 确定 Wireshark 的位置。如果没有一个正确的位置，启动 Wireshark 后会花费很长时间捕获 一些无用的数据。
2. 选择捕获接口。一般都是选择连接到 Internet 网络的接口，返样才可以捕获到与网络相关的数据。否则，捕获到其它数据对自己也没有任何帮助。
3. 使用捕获过滤器。通过设置捕获过滤器,可以避免产生过大的捕获数据。这样用户在分析数据时,也不会受其它数据干扰。而且,还可以为用户节约大量的时间。
4. 使用显示过滤器。通常使用捕获过滤器过滤后的数据,往往还是很复杂。为了使过滤的数据包再更细致,此时使用显示过滤器进行过滤。
5. 使用着色规则。通常使用显示过滤器过滤后的数据,都是有用的数据包。如果想更加突出的显<br />示某个会话,可以使用着色规则高亮显示。
6. 构建图表。如果用户想要更明显的看出一 个网络中数据的变化情况,使用图表的形式可以很方便的展现数据分布情况。
7. 重组数据。当传输较大的图片或文件时,需要将信息分布在多个数据包中。这时候就需要使用重组数据的方法来抓取完整的数据。Wireshark 的重组功能,可以重组一个会话中不同数据包的信息,或者是重组一个完整的图片或文件。


# 使用wireshark抓包
1、启动wirehark
![image.png](_img/assets/1655877929346-f9656692-726b-4797-9094-399cbb4229f7.png)

2、选择网卡
![image.png](_img/assets/1655877936810-4d18d673-2075-4ba5-b711-510f0566d65b.png)

3、双击网卡后就会自动进行抓包
![image.png](_img/assets/1655877944033-2935206e-21ea-4af2-bfed-0c62508d045c.png)


# 混杂模式
**混杂模式概述：**混杂模式就是接收所有经过网卡的数据包,包括不是发给本机的包, 即不验证MAC地址。普通模式下网卡只接收发给本机的包(包括广播包)传递给上层程序,其它的包一律丢弃。一般来说， 混杂模式不会影响网卡的正常工作,多在网络监听工具上使用。

**关闭和开启混杂模式方法**<br />关闭和开启混杂模式前,需要停止当前抓包,如果当前正在抓包的过程中,点击”停止捕获”
![image.png](_img/assets/1655877952981-9b9a38d9-cd2f-43ef-aa6f-64dc7c837428.png)

![image.png](_img/assets/1655877958606-4a90f6b2-5a12-4321-90f5-d8c2ea2a4135.png)


# 过滤器的使用

## 筛选TCP的数据包
注意：筛选条件使用小写了，大写会无法识别。
![image.png](_img/assets/1655877971040-c42ce70a-8137-484c-bff6-b7f47531c48d.png)


## 筛选ARP的数据包
![image.png](_img/assets/1655877978329-2cd80548-15c3-46ba-82a6-226520bf7df8.png)


## 筛选UDP的数据包
![image.png](_img/assets/1655877985064-6a4824ae-1095-4daa-9154-eb7a2e922317.png)

**以此类推，筛选什么数据包就输入什么协议。**


## 依据IP地址筛选数据包
```
ip.src_host == 192.168.1.37 表示源 IP 地址 
ip.dst_host == 192.168.1.15 表示目的IP地址
```

中间可以用or或者and进行拼接, or表示或，and表示与,or表示满足左右其中一个条件就会显示符合条件的数据包, and表示左右2个条件都满足才会显示。
```
ip.src_host == 192.168.1.37 or ip.src_host == 192.168.1.15
```

**1、依据源地址筛选**
```
ip.src_host == 192.168.1.37
```
![image.png](_img/assets/1655878001944-87840cc7-158d-4c31-aa39-37b1cef7f94a.png)

**2、依据目标地址筛选**
```
ip.dst_host == 192.1678.1.15
```
![image.png](_img/assets/1655878011227-63e395e5-2425-4c46-908c-ffc22d60c0f5.png)


# 数据包分析

## ARP协议
![image.png](_img/assets/1655878028856-1e29e26b-a3b2-4bfa-8a9a-3938e958389a.png)


### 第一个数据包（请求包）
有三层结构
![image.png](_img/assets/1655878038036-479d8b7b-a554-45e8-8410-d9a7b62c4501.png)


#### 第一层
Frame 2 前面的2 表示第几个包。


#### 第二层
![image.png](_img/assets/1655878073018-61b9c4e7-75d8-4666-bc45-90ce8ff9f412.png)


#### 第三层
![image.png](_img/assets/1655878079888-58b0eb1a-443a-42f6-bd2a-2c7242f0058d.png)


### 第二个数据包（回应包）
有三层结构
![image.png](_img/assets/1655878091654-13c2660b-ce0d-42b9-b22a-496e52a54833.png)


#### 第一层
Fram 3 前面的3 表示第几个包。


#### 第二层
![image.png](_img/assets/1655878100664-d15fec7d-411b-4706-9aa5-9cc0c5315945.png)


#### 第三层
![image.png](_img/assets/1655878107480-08b0ed71-aa3f-4190-8c15-ebab5891a067.png)


## ICMP协议
![image.png](_img/assets/1655878115970-689c11db-c36c-44b6-b62f-e0e4fc6da3c7.png)


### 第一个数据包（请求包）
有四层结构
![image.png](_img/assets/1655878131363-5fe9e05e-ebcf-4d63-9190-e0d13f0c045b.png)


#### 第一层
![image-20211022140918843.png](_img/assets/1655878181743-2445a5ec-f357-4b9b-97f7-4f0bad5070a7.png)
关注的信息般在第一行就显示出来了, 98节784比特和网络接口信息。


#### 第二层
![image-20211022141056347.png](_img/assets/1655878192160-30cfee3e-b3f7-4f00-87ae-446e09cad6e2.png)


#### 第三层
![image-20211022141613381.png](_img/assets/1655878195961-817475ae-ee51-48c3-8e57-9108cc62b037.png)


#### 第四层
![image-20211022142317871.png](_img/assets/1655878201377-c41aa10e-27d0-48b9-981a-1e0a4df6b5b6.png)


### 第二个数据包（返回包）
前三层和上面是一样的，这里主要分析第四层<br />![image-20211022142636788.png](_img/assets/1655878207846-247283ca-b701-471a-b3ce-661bb210dd4f.png)


#### 第四层
![image-20211022142931275.png](_img/assets/1655878212560-2d8a6c1e-a987-4e87-8e30-1cf3a29a5b78.png)


## TCP协议
![image-20211022144003543.png](_img/assets/1655878218106-df8b31ed-73ba-465b-a50e-a971a24a2e22.png)

### 第一个数据包（SYN）
![image-20211022144125776.png](_img/assets/1655878225275-048293d2-53ae-4c6b-a7df-d4f4784a0915.png)
前三层和ICMP是一样的，这里主要分析第四层


#### 第四层
![image-20211022144514275.png](_img/assets/1655878232297-1e58c355-987e-49e0-8d73-902ba871b1ce.png)

**标志位的详细信息**<br />![image-20211022144653693.png](_img/assets/1655878241094-45ac2a5e-89d0-4155-86c5-4aee0fe62f3f.png)

### 第二个数据包（SYN|ACK）
![image-20211022144754245.png](_img/assets/1655878248153-98cf2252-c6d6-4fe9-b472-ce8aeb3dae77.png)
前三层和ICMP是一样的，这里主要分析第四层


#### 第四层
![image-20211022145142061.png](_img/assets/1655878254207-771dabf8-f8bf-4c80-97b5-8c15527476b4.png)

**标志位的详细信息**<br />![image-20211022145318456.png](_img/assets/1655878256645-825d67e7-5a8f-4711-8978-ad75896066e7.png)
![image-20211022145435362.png](_img/assets/1655878266901-337a9255-957e-47e5-92d6-d1f6184ce60b.png)


### 第三个数据包（ACK）
![image-20211022145524769.png](_img/assets/1655878272011-a34433b1-6cbd-428a-9b63-cc0b802079c4.png)
前三层和ICMP是一样的，这里主要分析第四层


#### 第四层
![image-20211022145848503.png](_img/assets/1655878277086-52ea07a0-b1d7-4cd3-b6c5-1f7c9e442c3b.png)

**标志位的详细信息**<br />![image-20211022145959969.png](_img/assets/1655878282015-33e52f9d-7fd9-46a6-abdc-cee4369bd0d4.png)
![image-20211022150033755.png](_img/assets/1655878283781-e2cc256e-3e4d-4b84-bfe6-dbb0f0b4d665.png)

## HTTP协议
![image-20211022150442549.png](_img/assets/1655878294384-59f5078b-ac51-45df-b856-a30e7b3b5013.png)

1. 我们发送了一个HTTP的HEAD请求
2. 服务器收到我们的请求返回了SEQ/ACK进行确认
3. 服务器将HTTP的头部信息返回给我们客户端状态码为200表示页面正常
4. 客户端收到服务器返回的头部信息向服务器发送SEQ/ACK进行确认
5. 发送完成之后客户端就会发送FIN/ACK来进行关闭链接的请求。


## DNS协议
![image-20211022150747290.png](_img/assets/1655878298539-760e3bec-081c-43e4-a8e4-17a8a02986ba.png)


### 第一个数据包
![image-20211022150850640.png](_img/assets/1655878302954-3ad2d2a9-bc75-4a05-8cde-ccee1a9d3804.png)
可以看到这是一个5层的协议包它的4层协议是UDP


#### 第四层（UDP）
![image-20211022151326774.png](_img/assets/1655878309055-092da224-9cfa-4b79-bd6f-ef3968afbdf5.png)

#### 第五层（DNS）
![image-20211022151740799.png](_img/assets/1655878314791-20f0135f-99ea-4ab5-9fb8-dadaabd61246.png)


### 第三个数据包
![image-20211022151944070.png](_img/assets/1655878319784-e91f99c3-985e-4dc4-b478-374446fbb6c6.png)
因为我们上面提示应答我们的是第 3 个数据包


#### 第五层（DNS）
![image-20211022152302074.png](_img/assets/1655878326861-1b08fb4a-cbb6-4541-8014-f9b13d33341c.png)
