
# 环境准备
攻击机：<br />kali：192.168.1.28<br />![image-20210913152247891.png](_img/assets/1655878405919-892eff96-e15d-4fce-a3de-d9a8c67375ee.png)

内网边缘机器：<br />Win7：192.168.1.8   10.10.10.2<br />![image-20210913144106204.png](_img/assets/1655878413898-a877d649-9daa-482d-a569-5c7fea40a303.png)

内网机器：<br />win XP：10.10.10.3<br />![image-20210913144131732.png](_img/assets/1655878421915-9229c3d0-8871-4f0d-9604-789af42ff2b1.png)


# reGeorge
下载地址：[https://github.com/sensepost/reGeorg](https://github.com/sensepost/reGeorg)

例如有一台主机运行了 web 服务 IP 映射到公网任何人都可以任意访问，还有一台主机是内网中的主机外部访问不到。假设在公网的 web 主机已经上传了 webshell，但无法反弹 shell，无法直接将内网主机通过端口映射到外网主机。

reGeorg 这时就起作用了，通过上传 reGeorg 文件在 web 服务运行，使得攻击者发出的请求以及目标机器的响应经 web 服务的 http 转发，达到攻击者可以和内网主机进行通信的效果。

1、上传tunnel.nosocket.php文件到内网边缘主机，web 服务器使用什么编程语言，就上传什么语言的 tunnel 脚本，这里有 PHP|JSP|ASPX|ASHX 等语言可以选择，这里选择 PHP 文件上传。<br />![image-20210913150456278.png](_img/assets/1655878441103-feaf1ca5-6258-4221-936a-a4b32e6b965a.png)

2、在攻击机中访问http://192.168.1.8/tunnel.nosocket.php，当出现 Georg says, 'All seems fine'表示脚本运行正常<br />![image-20210913150626463.png](_img/assets/1655878447382-83eb58f3-a5aa-460c-bc37-47a7cf56a93f.png)

3、在攻击机启动reGeorg
```
python reGeorgSocksProxy.py -p 1090 -u http://192.168.1.8/tunnel.nosocket.php
```
表示本地1090端口的流量都转发给指定的那个url，1090是指定的监听端口；<br />![image-20210913150926696.png](_img/assets/1655878455719-157a514f-3d31-4591-8c43-27ca462c1eb3.png)

4、配置代理<br />配置proxychains代理链的配置文件vim /etc/proxychains4.conf，将代理设置成本机的1090端口：socks4 127.0.0.1 1090<br />![image-20210913151239304.png](_img/assets/1655878469831-707c39c1-d1a6-4e8a-b449-a1d4469350e8.png)

5、在浏览器配置代理<br />![image-20210913153524313.png](_img/assets/1655878474340-009b1714-e0b0-451b-863e-933a950c05ae.png)

成功访问<br />![image-20210913153551232.png](_img/assets/1655878478402-bf7dfc8d-5b5e-43f0-b066-786233f63941.png)

在终端测试
```
proxychains4 curl http://10.10.10.3
```
![image-20210913153753215.png](_img/assets/1655878487145-28b1b15c-b78b-4eca-b5f7-6ec4d05ad8c8.png)
