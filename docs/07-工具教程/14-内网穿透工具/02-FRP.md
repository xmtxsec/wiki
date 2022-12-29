
# 环境准备
攻击机：<br />kali：192.168.1.28<br />![image-20210913152247891.png](_img/assets/1655878625865-33ffe755-4801-4c6f-96af-40a4994b89fa.png)

内网边缘机器：<br />Win7：192.168.1.8   10.10.10.2<br />![image-20210913144106204.png](_img/assets/1655878631025-d2ccbca9-8fda-4bb5-bbf0-74d74fd1b94a.png)

内网机器：<br />win XP：10.10.10.3<br />![image-20210913144131732.png](_img/assets/1655878635972-6e36d48c-3ec7-474b-838f-b8ef08800784.png)


# FRP
下载地址：[https://github.com/fatedier/frp/releases](https://github.com/fatedier/frp/releases)

frp 是一种快速反向代理，可帮助您将 NAT 或防火墙后面的本地服务器暴露给 Internet。截至目前，它支持**TCP**和**UDP**，以及**HTTP**和**HTTPS**协议，其中请求可以通过域名转发到内部服务。

1、攻击机配置
```
vim frps.ini
```
![image-20210913163733431.png](_img/assets/1655878646139-6ead0f89-a151-4912-95e3-53ae62fc135f.png)

2、运行
```
./frp -c frps.ini
```
![image-20210913164005174.png](_img/assets/1655878651404-a40f3d14-3ac9-410e-8e7f-1b0b60feccc7.png)

3、内网边缘主机配置<br />server_addr为攻击机IP
```
[common]
server_addr = 192.168.1.28
server_port = 7000

[socks5]
type = tcp
remote_port = 8010
plugin = socks5
```
![image-20210913164353944.png](_img/assets/1655878659559-6f806a07-e727-4528-b5e9-1ad1acdb3b6e.png)

运行
```
frpc.exe -c frpc.ini
```
![image-20210914142652640.png](_img/assets/1655878664904-b81af296-abe8-4b32-a3cc-2a5d49b4e078.png)

4、配置代理<br />配置proxychains代理链的配置文件vim /etc/proxychains4.conf，将代理设置成本机的8010端口：socks5 127.0.0.1 8010
```
vim etc/proxychains4.conf
```
![image-20210914143145108.png](_img/assets/1655878673260-8091528b-7d9d-42da-98e4-f1a0ac55bafd.png)

在浏览器中配置代理<br />![image-20210914142833439.png](_img/assets/1655878679240-391f3515-fa38-45fc-a04f-03aaf237a837.png)

5、测试<br />![image-20210914143233689.png](_img/assets/1655878683263-5d1a0643-b486-4ada-bb3d-1bd38b41cfce.png)

![image-20210914142848094.png](_img/assets/1655878687025-06fe18c0-49a1-4d33-b050-9ec0660cd537.png)
