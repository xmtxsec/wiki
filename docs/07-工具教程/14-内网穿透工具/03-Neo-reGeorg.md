
# 环境准备
攻击机：<br />kali：192.168.1.28<br />![image-20210913152247891.png](./assets/1655878523476-f716105c-f7d8-4447-9c21-94a4f2fd7a36.png)

内网边缘机器：<br />Win7：192.168.1.8   10.10.10.2<br />![image-20210913144106204.png](./assets/1655878527534-060ac560-4b00-4fa4-96d5-d0b08f5ff924.png)

内网机器：<br />win XP：10.10.10.3<br />![image-20210913144131732.png](./assets/1655878531848-d8378941-851d-4278-a63d-b88b308cc30c.png)


# Neo-reGeorg
下载地址：[https://github.com/L-codes/Neo-reGeorg](https://github.com/L-codes/Neo-reGeorg)

Neo-reGeorg是常见的http正向隧道工具，是reGeorg工具的升级版。增加了很多特性，例如像内容加密、避免被检测、请求头定制、响应码定制、支持py3等等

1、设置密码并生成隧道文件，运行后会生成一个neoreg_server目录，里面包含了各种语言类型的隧道文件。
```
python3 neoreg.py generate -k <password>
```
![image-20210913154601059.png](./assets/1655878549434-cc2c9001-a5f3-4e42-967d-f9a18b51fa46.png)

2、上传tunne.php文件到内网边缘主机<br />![image-20210913154759900.png](./assets/1655878554202-3337265e-ae33-4b62-8e52-5cf850ea8840.png)

3、在攻击机中浏览器中访问<br />![image-20210913154903481.png](./assets/1655878558296-393ef1bd-272f-44e8-ad83-b874fc6d19f6.png)

4、启动Neo-reGeorg
```
python3 neoreg.py -k <password> -u http://192.168.1.5/tunnel.php
```
表示本地1080端口的流量都转发给指定的那个url，1080是指定的监听端口；<br />![image-20210913155136539.png](./assets/1655878566367-a7d79bff-5a93-4eab-b2da-85124d07fa51.png)

5、配置代理<br />配置proxychains代理链的配置文件vim /etc/proxychains4.conf，将代理设置成本机的1090端口：socks5 127.0.0.1 1080<br />![image-20210913155957022.png](./assets/1655878572575-6dcdbde7-a19d-4bce-b517-e0a50359949c.png)

在浏览器中配置代理<br />![image-20210913160108350.png](./assets/1655878576683-52b3c88a-2ee8-4ebd-8ac0-aa3bd3c31323.png)

6、访问测试<br />在浏览器中访问<br />![image-20210913160117532.png](./assets/1655878582441-8cefb8bf-0c3f-4e2f-a733-4c49d0561a6e.png)

在终端测试<br />![image-20210913160048761.png](./assets/1655878585797-3719e47b-be6a-4e3a-88b9-23d69fd75234.png)
