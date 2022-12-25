
# ARP
IP协议使用了ARP协议，因此通常就把ARP协议划归网络层。但ARP协议的用途是为了从网络层使用的IP地址，解析出在数据链路层使用的硬件地址。因此，有的教科书就按照协议的所用，把ARP协议划归在数据链路层。<br />还有一个旧的协议叫做逆地址解析协议RARP，它的作用是使只知道自己硬件地址的主机能够通过RARP协议找出其IP地址。现在的DHCP协议已经包含了RARP协议的功能。<br />![](https://img-blog.csdnimg.cn/bcdf8da6b93c4b258cb4c0c1cbba3553.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_17,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=rm5EO&originHeight=212&originWidth=537&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## ARP协议工作原理
在主机ARP高速缓存中存放一个从IP地址到硬件地址的映射表，并且这个映射表还经常动态更新(新增或超时删除)。每一台主机都设有一个**ARP高速缓存(ARP cache)**，里面有本局域网上的各主机和路由器的IP地址到硬件地址的映射表，这些都是该主机目前知道的一些地址。

当主机A要向本局域网上的某台主机B发送IP数据报时，就先在其ARP高速缓存中查看有无主机B的IP地址。

如有，就在ARP高速缓存中查出其对应的硬件地址，再把这个硬件地址写入MAC帧，然后通过局域网把该MAC帧发往此硬件地址。

也有可能查不到主机B的IP地址的项目。这可能是主机B才入网，也可能是主机A刚刚加电，其高速缓存还是空的。在这种情况下，主机A就自动运行ARP, 然后按以下步骤找出主机B的硬件地址。


### 同局域网下

1.  ARP进程在本局域网上广播发送一个ARP请求分组。ARP请求分组的主要内容是:“我的IP地址是209.0.0.5， 硬件地址是00-00-C0-15-AD-18。 我想知道IP地址为209.0.0.6的主机的硬件地址。<br />![](https://img-blog.csdnimg.cn/03122ae8d4714f1c9206b31718802d10.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=asn0D&originHeight=250&originWidth=713&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=) 
2.  在本局域网上的所有主机上运行的ARP进程都收到此ARP请求分组。 
3.  主机B的IP地址与ARP请求分组中要查询的IP地址一致，就收下这个ARP请求分组，并向主机A发送ARP响应分组，同时在这个ARP响应分组中写入自己的硬件地址。由于其余的所有主机的IP地址都与ARP请求分组中要查询的IP地址不一致，因此都不理睬这个ARP请求分组。 ARP响应分组的主要内容是:“我的IP地址是209.0.0.6，我的硬件地址是08-00-2B-00-EE-0A。”请注意:虽然ARP请求分组是广播发送的，但ARP响应分组是普通的单播，即从一个源地址发送到一个目的地址。<br />![](https://img-blog.csdnimg.cn/6daa6a686d6a42a490776447b3c62d55.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2m4oSh,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=HZf95&originHeight=211&originWidth=722&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=) 
4.  主机A收到主机B的ARP响应分组后，就在其ARP高速缓存中写入主机B的IP地址到硬件地址的映射。 

当主机A向B发送数据报时，很可能以后不久主机B还要向A发送数据报，因而主机B也可能要向A发送ARP请求分组。为了减少网络上的通信量，主机A在发送其ARP请求分组时，就把自己的IP地址到硬件地址的映射写入ARP请求分组。当主机B收到A的ARP请求分组时，就把主机A的这一地址映射写入主机B自己的ARP高速缓存中。

ARP对保存在高速缓存中的每一个映射地址项目都设置生存时间(例如，10~20分钟)。凡超过生存时间的项目就从高速缓存中删除掉。<br />请注意，ARP是解决**同一个局域网**上的主机或路由器的IP地址和硬件地址的映射问题。


### 不同局域网下
![](https://img-blog.csdnimg.cn/d4709063d02c4f959875594029f12c46.png#crop=0&crop=0&crop=1&crop=1&id=nb12A&originHeight=149&originWidth=759&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

1.  主机H1发送给H2的IP数据报首先需要通过与主机H1连接在同一个局域网上的路由器R1来转发。因此主机H1这时需要把路由器R1的IP地址IP3解析为硬件地址HA3，以便能够把IP数据报传送到路由器R1。 
2.  然后，R1从转发表找出了下一跳路由器R2,同时使用ARP解析出R2的硬件地址HA5。于是IP数据报按照硬件地址HA5转发到路由器R2。路由器R2在转发这个IP数据报时用类似方法解析出目的主机H2的硬件地址HA2,使IP数据报最终交付主机H2。 

从IP地址到硬件地址的解析是自动进行的，主机的用户对这种地址解析过程是不知道的。只要主机或路由器要和本网络上的另一个已知IP地址的主机或路由器进行通信，ARP协议就会自动地把这个IP地址解析为链路层所需要的硬件地址。


## 使用ARP协议的四种典型情况
![](https://img-blog.csdnimg.cn/960a129835fd4e8e8a2c037bdb144d6b.png#crop=0&crop=0&crop=1&crop=1&id=odEmn&originHeight=137&originWidth=693&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

1.  发送方是主机(如H)，要把IP数据报发送到同一个网络上的另一台主机(如H2)。这时H1发送ARP请求分组(在网1上广播)，找到目的主机H2的硬件地址。 
2.  发送方是主机(如H1)，要把IP数据报发送到另一个网络上的一台主机(如H3或H4)。这时H1发送ARP请求分组(在网1上广播)，找到网1上的一个路由器R1的硬件地址。剩下的工作由路由器R1来完成。R1 要做的事情是下面的3或4。 
3.  发送方是路由器(如R1),要把IP数据报转发到与R1连接在同一个网络(网2)上的主机(如H3)。这时R1发送ARP请求分组(在网2上广播)，找到目的主机H3的硬件地址。 
4.  发送方是路由器(如R1)，要把IP数据报转发到网3上的一台主机(如H4)。H4与R1不是连接在同一个网络上。这时R1发送ARP请求分组(在网2上广播)，找到连接在网2上的一个路由器R2的硬件地址。剩下的工作由这个路由器R2来完成。 

在许多情况下需要多次使用ARP。但这只是以上几种情况的反复使用而已。
