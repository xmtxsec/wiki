
# 攻击原理
**SYN攻击原理** ：SYN攻击是当前网络上最为常见的DDoS攻击,也是最为经典的拒绝服务攻击,它利用了TCP协议实现上的一个缺陷,通过向网络服务所在端口发送大量的伪造源地址的攻击报文,就可能造成目标服务器中的半开连接队列被占满,从而阻止其他合法用户进行访问。为了有效防范这种攻击,在分析攻击原理的基础上,发现可以使用TCP代理防御及TCP源探测防御方法来解决这个问题,经过测试证明,该办法能够有效降低SYNFlood攻击造成的危害。

![image-20240105142337638](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051423690.png)




# 实验准备
pc1（win2003，受害机，web服务器，192.168.1.64）

pc2（win2003，攻击机，安装SYN攻击器，192.168.1.18）

pc3（win7,观察机，192.168.1.6）

更改三台机的DNS服务器地址为pc1的IP地址，并使三台机子能够互相ping通

![image-20240105142414472](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051424524.png)
pc2和pc3的DNS服务器地址同上。



在这里我用 pc3 ping pc1 和 pc2

![image-20240105142455750](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051424826.png)




# 实验步骤 

1、pc1上安装web服务器，安装抓包软件wireshake。

安装web服务器：开始 > 控制面板 > 添加或删除程序 > 添加/删除Windows组件 > 应用程序服务器> Internet信息服务（IIS）> 万维网服务

进行安装

安装完成后打开Internet信息服务（IIS）

![image-20240105142548387](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051425437.png)



2、打开默认网站属性，设置IP地址为主机IP地址

![image-20240105142615776](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051426837.png)



3、在磁盘C:\Inetpub\wwwroot路径下新建文本文档,在文档中输入你想输入的类容并改名为index.htm

![image-20240105142727248](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051427288.png)



4、在网站属性里面点击文档，将刚刚建立的文档设置为默认文档。

![image-20240105142749040](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051427091.png)



5、安装wireshack，下载直接安装即可。

 

6、通过PC3能够正常访问web网站。抓包为正常包。

![image-20240105142822507](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051428560.png)

![image-20240105142839188](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051428260.png)



7、pc2上使用“SYN攻击器”开始攻击。

在这里我用的是“暗黑安全联盟专版SYN攻击

![image-20240105142912374](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051429417.png)



![image-20240105142930853](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051429905.png)


8、点击确定，激活线程

![image-20240105142959087](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051429135.png)

 

9、在PC1上开启wireshake进行抓包，分析抓到的数据包。

![image-20240105143022406](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051430471.png)

 

10、验证存在攻击时，pc3访问网站速度变慢。

![image-20240105143048226](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202401051430275.png)



**防范方法：**关于SYN Flood攻击的防范，目前许多防火墙和路由器都可以做到。首先关闭不必要的TCP/IP服务，对防火墙进行配置，过滤来自同一主机的后续连接，然后根据实际的情况来判断。 