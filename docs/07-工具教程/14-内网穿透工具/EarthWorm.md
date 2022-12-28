
# 环境准备
攻击机：<br />kali：192.168.1.28<br />![image-20210913152247891.png](./assets/1655878728663-625070f2-7c06-40e7-9cee-4f5f5633c92c.png)

内网边缘机器：<br />Win7：192.168.1.8   10.10.10.2<br />![image-20210913144106204.png](./assets/1655878733735-4c2fae46-3190-4ad3-9449-30eebcf29b6b.png)

内网机器：<br />win XP：10.10.10.3<br />![image-20210913144131732.png](./assets/1655878741408-6c22f122-9869-435a-9787-811035c5b0a0.png)


# EW
下载地址：[http://rootkiter.com/EarthWorm/](http://rootkiter.com/EarthWorm/)

**EarthWorm**是一款用于开启 SOCKS v5 代理服务的工具，基于标准 C 开发，可提供多平台间的转接通讯，用于复杂网络环境下的数据转发。该工具能够以“正向”、“反向”、“多级级联”等方式打通一条网络隧道，直达网络深处，用蚯蚓独有的手段突破网络限制，给防火墙松土。

目前工具提供六种链路状态，可通过 -s 参数进行选定
```
ssocksd       //正向代理
rssocks       //反向代理
lcx_slave    //该管道一侧通过反弹方式连接代理请求方，另一侧连接代理提供主机。
lcx_tran     //该管道，通过监听本地端口接收代理请求，并转交给代理提供主机。
lcx_listen   //该管道，通过监听本地端口接收数据，并将其转交给目标网络回连的代理提供主机。
```


## EW正向代理
1、在内网边缘主机上运行
```
ew_for_win.exe -s ssocksd -l 1090
```
![image-20210914144658794.png](./assets/1655878758651-fa8b980a-2b8b-40e5-b3a7-43a79e84615c.png)

2、配置代理<br />在攻击机配置proxychains代理链的配置文件vim /etc/proxychains4.conf，将代理设置成本机的1090端口：socks5 192.168.1.8 1090
```
vim etc/proxychains4.conf
```

配置浏览器代理<br />![image-20210914145308369.png](./assets/1655878767562-fabbb863-bba7-4301-a123-e9a1f69d23bf.png)

3、访问测试<br />![image-20210914145319879.png](./assets/1655878774448-9b1658eb-3da9-4dcc-8557-cc6678e60bbb.png)

![image-20210914145403665.png](./assets/1655878778970-bafbde5b-5bcf-491d-ba08-025395ee2f81.png)


## EW反向代理
1、在攻击机执行
```
./ew_for_linux64 -s rcsocks -l 1080 -e 1024
```
![image-20210914145620311.png](./assets/1655878785838-0cd8288e-a568-4ecf-8c87-ac05779802b3.png)

2、在内网边缘机器执行
```
ew_for_win.exe -s rssocks -d 192.168.1.28 -e 1024
```
![image-20210914145856492.png](./assets/1655878790512-d9b09047-bc23-40b5-83f8-660c47971c60.png)

连接成功<br />![image-20210914150002994.png](./assets/1655878794522-77620afb-6afa-45a9-8df4-46d46dfa85f0.png)

3、配置代理<br />在攻击机配置proxychains代理链的配置文件vim /etc/proxychains4.conf，将代理设置成本机的1090端口：socks5 127.0.0.1 1080<br />![image-20210914150112165.png](./assets/1655878798931-e75526e0-292a-465c-ba17-3a8af9af19bf.png)

在浏览器配置代理<br />![image-20210914150331273.png](./assets/1655878803215-57977280-e007-43a5-9950-9017549f143b.png)

4、访问测试<br />![image-20210914150340106.png](./assets/1655878806712-ce0195bd-9763-4b69-a2b7-005e5c0669e7.png)

![image-20210914150414868.png](./assets/1655878813266-67832367-0431-485b-b6bc-4350e1dba8b6.png)
