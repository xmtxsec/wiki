# 一、简介

​	YAKIT（Yet Another Knife for IT Security）是一款网络安全单兵工具。它整合了一系列实用的安全工具，例如密码破解工具、网络扫描器、漏洞利用工具等，帮助用户在一个集中化的界面下执行常见的渗透测试任务。工具覆盖信息收集、爬虫、交互式 WEB 流量分析、端口扫描、口令破解等，能够满足各种渗透测试的需求，并且能够在工具之间进行有效的协同。在一个项目中产生的所有业务流量均会被记录到单独的数据库中，该数据库可以自动上传至统一的离线 Server 之上，实现数据的追踪和管理，也可以为后续的质量分析提供数据支撑。



# 二、安装

## 2.1、下载客户端

- **官网：**https://www.yaklang.com/
- **Github：**https://github.com/yaklang/yakit/releases

![image-20250113161455737](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501131614034.png)



1、双击下载的可执行程序进行安装。（建议以管理员身份运行）

![image-20250113162021962](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501131620011.png)



## 2.2、初始化引擎

Yakit 的核心并不在工具本身上，而是依托于 Yak gRPC 接口，把 Yakit 当作一个 "视窗" 来操纵 Yak 引擎来完成想要实现的安全能力。



- **远程模式：**`yak grpc` 服务器可以启动在任何平台 / 任何网络位置，包括远端托管主机 ECS/VPS、本地个人 PC、内网环境。`yak grpc` 启动参数支持设置 `--tls` 与 `--secret` 以实现一些远程连接的安全需求。

1、首先需要在Linux环境上安装Yakit

```
chmod +x yak_linux_amd64 && ./yak_linux_amd64 install
```

![image-20250220163436420](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202502201634590.png)



2、启动 yak grpc

```
yak --host 0.0.0.0 --port 8087 --secret Talent@123 --tls
```

![image-20250220164151218](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202502201641305.png)



3、使用本地yakit客户端连接

![image-20250220164542780](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202502201645870.png)



- **本地模式**：启动即可。

![image-20250113162140986](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501131621050.png)



# 三、MITM交互式劫持

![image-20250113164213593](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501131642759.png)



存在两种劫持方式：

- 使用免配置模式。不需要任何其他配置，只需安装Chrome 浏览器，免配置启动之后将打开一个新的浏览器会话，其中所有流量都将通过 Yakit 自动代理。无需安装 Yakit 的 CA 证书就可以使用它来通过 HTTPS 进行测试。
- 使用选择的外部浏览器。需要配置浏览器以与 Yakit的 MITM 一起使用，并在浏览器中安装 Yakit 的 CA 证书。



## 3.1、**免配置模式**

配置代理处默认为：[http://127.0.0.1:8083](http://127.0.0.1:8083/) 默认配置代理无需修改，点击`启动免配置Chrome` 默认启动谷歌浏览器，即开始劫持抓包。

![image-20250113164601979](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501131646073.png)



## 3.2、使用选择的外部浏览器

1、配置好代理的监听端口

![image-20250113170303878](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501131703964.png)



2、点击高级配置下载证书，也可以在配置好代理后访问`http://download-mitm-cert.yaklang.io`进行下载

![image-20250113170420569](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501131704713.png)



3、去掉证书后面的`.pem`，双击进行安装，证书存储选择`受信任的根证书颁发机构`

![image-20250113170744347](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501131707436.png)



4、浏览器配置代理（我这里使用插件`SwitchyOmega`）

![image-20250113171041468](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501131710542.png)



## 3.3、启动劫持

**手动劫持：**数据流量经过 Yakit 并暂停，可以查看或修改数据包。

- `手动劫持`模式下劫持到数据后点击`丢弃数据`，该条数据不会出现在`history`中，`提交数据`则会将该数据包记录在`history` 并开始劫持下一个数据包。
- 开启`劫持响应`会劫持到当前请求包对应的响应数据包。选择"所有"则会劫持所有请求的响应数据包。
- 在数据包展示处，可以对HTTP请求的数据进行手工的修改，点击鼠标右键可以对数据包进行更多的操作。
- 劫持到的所有的数据包都会存入到`history`中。

**自动放行：**会自动放行所有的数据包。

**被动日志：**劫持模块、插件加载、被动扫描的执行日志。

**过滤器：**针对某些不需要看见的数据包或者屏蔽污染数据可以输入需要过滤的条件，劫持时则不会劫持符合条件的数据包。



![image-20250113171609496](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501131716692.png)



## 3.4、History界面

所有流经 Yakit 的请求，都会在 History 中记录下来，我们可以通过该页面，查看传输的数据内容，对数据进行测试和验证。History中的所有数据包，均支持对数据包进行单个操作，或者批量操作。在最左边的操作栏，点击浏览器图标可以在浏览器中打开该请求对应的url，点击箭头图标可查看该请求详细的内容。

![image-20250223220042988](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202502232201180.png)



网站树将历史请求以树结构的形式进行展示，按照网站的层级和深度，树形展示整个应用系统的结构和关联其他域的url情况。

![image-20250223221729559](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202502232217624.png)



# 四、Web Fuzzer

## 4.1、重放

Web Fuzzer模块支持用户自定义HTTP原文发送请求。使用 Web Fuzzer进行请求与响应的消息验证分析，比如修改请求参数，验证输入的漏洞；修改请求参数，验证逻辑越权；从拦截历史记录中，捕获特征性的请求消息进行请求重放。

![image-20250223223858282](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202502232238349.png)



## 4.2、爆破





![image-20250223224817535](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202502232248620.png)















