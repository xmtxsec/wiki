
# 原理
**SYN攻击原理** ：SYN攻击是当前网络上最为常见的DDoS攻击,也是最为经典的拒绝服务攻击,它利用了TCP协议实现上的一个缺陷,通过向网络服务所在端口发送大量的伪造源地址的攻击报文,就可能造成目标服务器中的半开连接队列被占满,从而阻止其他合法用户进行访问.为了有效防范这种攻击,在分析攻击原理的基础上,发现可以使用TCP代理防御及TCP源探测防御方法来解决这个问题,经过测试证明,该办法能够有效降低SYNFlood攻击造成的危害.<br />![](https://img-blog.csdnimg.cn/20200414084145987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=MCGoJ&originHeight=298&originWidth=439&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# 实验准备
pc1（win2003，受害机，web服务器，192.168.1.64）<br />pc2（win2003，攻击机，安装SYN攻击器，192.168.1.18）<br />pc3（win7,观察机，192.168.1.6）

更改三台机的DNS服务器地址为pc1的IP地址，并使三台机子能够互相ping通<br />![](https://img-blog.csdnimg.cn/20200414090916208.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=kagZT&originHeight=441&originWidth=397&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
pc2和pc3的DNS服务器地址同上。

在这里我用 pc3 ping pc1 和 pc2<br />![](https://img-blog.csdnimg.cn/20200414092548839.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=NTKzK&originHeight=538&originWidth=817&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# 实验步骤 

1.  pc1上安装web服务器，安装抓包软件wireshake.<br />安装web服务器：开始 > 控制面板 > 添加或删除程序 > 添加/删除Windows组件 > 应用程序服务器> Internet信息服务（IIS）> 万维网服务<br />进行安装<br />安装完成后打开Internet信息服务（IIS）<br />![](https://img-blog.csdnimg.cn/20200414085733664.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=GuEJm&originHeight=493&originWidth=483&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

2. 打开默认网站属性，设置IP地址为主机IP地址<br />![](https://img-blog.csdnimg.cn/20200414085911999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=TwhLi&originHeight=457&originWidth=752&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

3. 在磁盘C:\Inetpub\wwwroot路径下新建文本文档,在文档中输入你想输入的类容并改名为index.htm<br />![](https://img-blog.csdnimg.cn/20200414090213985.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=ueZzR&originHeight=221&originWidth=330&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

4. 在网站属性里面点击文档，将刚刚建立的文档设置为默认文档。<br />![](https://img-blog.csdnimg.cn/20200414090353192.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=H6Xw9&originHeight=454&originWidth=469&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

5. 安装wireshack，下载直接安装即可。

 

6.  通过PC3能够正常访问web网站。抓包为正常包。<br />![](https://img-blog.csdnimg.cn/20200414092624193.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=mCzld&originHeight=255&originWidth=559&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/20200414092732233.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=L13qJ&originHeight=372&originWidth=735&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=) 
7.  pc2上使用“SYN攻击器”开始攻击。<br />在这里我用的是“暗黑安全联盟专版SYN攻击”<br />![](https://img-blog.csdnimg.cn/20200414092952135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=XYehz&originHeight=294&originWidth=455&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/20200414093121744.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=y1hHV&originHeight=428&originWidth=524&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

8. 点击确定，激活线程：<br />![](https://img-blog.csdnimg.cn/20200414093157994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Gongo&originHeight=299&originWidth=463&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=) 

9.  在PC1上开启wireshake进行抓包，分析抓到的数据包。<br />![](https://img-blog.csdnimg.cn/20200414093250317.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=adNmP&originHeight=391&originWidth=750&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=) 
10.  验证存在攻击时，pc3访问网站速度变慢。<br />![](https://img-blog.csdnimg.cn/20200414093319168.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=LbeFP&originHeight=234&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

11. 防范方法：<br />关于SYN Flood攻击的防范，目前许多防火墙和路由器都可以做到。首先关闭不必要的TCP/IP服务，对防火墙进行配置，过滤来自同一主机的后续连接，然后根据实际的情况来判断。 
