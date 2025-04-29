# 1、Proxifier简介

Proxifier 是一款功能强大的代理客户端，它能够让不支持代理功能的程序通过代理服务器访问网络。它支持多种操作系统和代理协议，并允许用户基于程序和端口的特定需求来灵活配置代理。主要用途是通过代理服务器强制所有网络请求走代理，从而对不支持代理的程序实现网络代理功能。

- 通过代理重定向任何 Internet 应用程序（浏览器、电子邮件、数据库、游戏等）的连接。
- 控制对资源的访问。通过单个入口点路由所有连接。从一个位置远程更新多个配置。
- 通过更快的路由 Internet 流量。
- VPN 的轻量级和灵活的替代品。通过加密通道建立连接隧道。
- 使用代理作为 Internet 活动的网关。
- 使用基于规则的系统将不同的代理或链分配给不同的连接。

官方网站：https://www.proxifier.com/



# 2、安装

1、前往[官网](https://www.proxifier.com/)下载安装包

![image-20250429094700156](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504290947666.png)



2、双击安装包进行安装，一直NEXT即可，安装完毕后启动Proxifier，点击Registration Key。

![image-20250429101133364](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291011514.png)



3、这时去[Proxifier_Keygen](https://github.com/y9nhjy/Proxifier_Keygen)下载注册工具，生成注册码。

```
python Proxifier_Keygen.py
```

![image-20250429101714506](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291017590.png)



3、在注册框上面一行随便输入，下面一行填入刚刚生成的注册码。

![image-20250429102201400](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291022458.png)



4、成功注册。

![image-20250429102221466](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291022511.png)



# 3、基本使用方法

## 3.1、配置代理服务器

1、依次选择 `Profile > Proxy Servers > add` 找到代理的服务端的IP和端口（本地代理服务就是 `127.0.0.1`）并填写和选择对应代理协议。

![image-20250429102733899](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291027954.png)



2、点击 `check` 测试连通性，测试代理可用之间确定保存即可

![image-20250429102816693](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291028767.png)



## 3.2、配置代理规则

这里我以Typora为例，由于我的图床是在Github上一般情况下本地加载不出来图片。



1、依次选择 `Profile > Proxification Rules > add` 

![image-20250429143440927](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291435919.png)



2、确认即可

![image-20250429143528927](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291435985.png)



3、可以看到设置代理成功。

![image-20250429143644517](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291436626.png)



# 4、其它玩法

## 4.1、配置默认不通过Proxifier代理

1、依次选择 `Profile > Proxification Rules > add` 

![image-20250429150333833](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291503890.png)



```
127.0.0.1; ::1
```

`::1`是IPv6中的环回地址，将其视为`127.0.0.1`的IPv6版本，有些进程在本地通讯中会用到这个玩意，必须先让它直连，如果它走代理的话对应的进程会出问题的。



2、这条规则在代理列表里面要处于最高（优先进行）

![image-20250429151816569](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291522638.png)



## 4.2、配置VM虚拟机代理

1、依次选择 `Profile > Proxification Rules > add` 。

```
vmware.exe; vmnetcfg.exe; vmnat.exe; vmrun.exe; vmware-vmx.exe; mkssandbox.exe; vmware-hostd.exe; vmnat.exe; vmnetdhcp.exe
```

![image-20250429152454261](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291524331.png)



2、检测是否配置成功。

![image-20250429153601145](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291536243.png)



3、如果上面操作没能配置成功，依次选择`Profile > Advanced > Services and Other Users` 勾引上两个选项，再次尝试。

![image-20250429153837850](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291538911.png)



## 4.3、配置微信小程序代理

1、依次选择 `Profile > Proxification Rules > add` 。

```
WeChatApp.exe;WechatBrowser.exe;WeChatAppEx.exe
```

![image-20250429154741619](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291547688.png)



2、检测是否配置成功。

![image-20250429155000349](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202504291550577.png)



# 5、参考链接

- https://xz.aliyun.com/news/12613
