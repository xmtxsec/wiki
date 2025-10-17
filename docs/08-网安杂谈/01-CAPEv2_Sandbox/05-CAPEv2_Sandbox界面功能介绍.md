# 1、概述

恶意软件分析的沙箱是在隔离的环境中运行未知且不受信任的应用程序或文件，并获取有关文件用途的信息。恶意软件沙箱是动态分析方法的实际应用，它不是静态分析二进制文件，而是实时执行和监控。可以获取有关恶意软件的更多详细信息。

CAPEv2 是一种开源恶意软件沙箱。用于在隔离环境中执行恶意文件，同时检测其动态行为并收集取证据。



# 2、CAPEv2的功能

CAPEv2 源自 Cuckoo v1，具有以下核心功能：

-   基于 API 接口技术的行为检测与监控

-   捕获在执行过程中创建、修改和删除的文件

-   以 PCAP 格式捕获网络流量

-   基于行为和网络特征进行恶意软件分类

-   在恶意软件执行期间拍摄桌面屏幕截图

-   目标系统的完整内存转储

CAPEv2 补充了 Cuckoo 的传统沙箱输出，增加了几个关键功能：

-   自动动态恶意软件解压缩

-   基于解压缩有效负载的 YARA 签名的恶意软件分类

-   静态和动态恶意软件配置提取

-   通过 YARA 签名编程的自动调试器，允许：
    -   自定义解包/配置提取器
    -   动态防御沙箱对策
    -   指令跟踪


-   交互式桌面



# 3、CAPEv2 WEB界面介绍

整个CAPEv2 WEB页面主要有九个菜单：

![image-20251016145648395](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161456615.png)


-   控制面板（Dashboard）

-   最近任务（Recent）

-   等待任务（Pending）

-   搜索（Search）

-   应用程序接口（API）

-   提交样本（Submit）

-   统计（Dtatistics）

-   官方文档（Docs）

-   修改日志（Changelog）



## 3.1、控制面板（Dashboard）

该页面是用来展示所有进行分析过的恶意样本分析情况。

![image-20251016145832142](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161458310.png)



## 3.2、最近任务（Recent）

该页面采用倒序的列表来展示所有被分析过的恶意样本信息。查看恶意样本分析的详情况也需要从此界面进入。

![image-20251016145941955](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161459064.png)



## 3.3、等待任务（Pending）

此处用来展示正在进行分析的恶意样本或者已经等待分析的恶意样本。

![image-20251016150017190](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161500268.png)



## 3.4、搜索（Search）

搜索功能，用来搜索某一个特定的恶意样本。

![image-20251016150103494](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161501573.png)



## 3.5、应用程序接口（API）

该界面用于展示应用程序接口（API）的函数信息及详情。

![image-20251016150147134](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161501238.png)



## 3.6、提交样本（Submit）

该界面用于提交恶意样本以及设置恶意样本动态分析的必要选项。

![image-20251016150337321](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161503421.png)



## 3.7、统计（Dtatistics）

该界面用于统计所有的恶意样本分析任务。

![image-20251016150406436](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161504534.png)



## 3.8、官方文档（Docs）

该菜单用于跳转至官方说明文档。

![image-20251016150433568](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161504666.png)



## 3.9、修改日志（Changelog）

该菜单用于跳转CAPEv2项目的修改记录。

![image-20251016150501987](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161505069.png)



# 4、CAPEv2使用方法说明

## 4.1、启动CAPEv2沙箱

1、启动系统并登录系统，默认登录密码：123456。

![image-20251016150750287](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161507369.png)



2、配置好系统IP后重启系统，通常情况下CAPEv2沙箱会开机自启，开机后执行以下命令检查服务状态，若正常则启动成功。

```
systemctl status cape.service
```

![image-20251016150838250](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161508323.png)



```
systemctl status cape-processor.service
```

![image-20251016150915536](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161509669.png)



```
systemctl status cape-web.service
```

![image-20251016150935129](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161509204.png)



```
systemctl status cape-rooter.service
```

![image-20251016150959010](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161509082.png)



3、在浏览器访问<http://YOUIP:8000>即可。



3、若服务启动异常，请尝试重启服务。

```
systemctl restart cape.service
systemctl restart cape-processor.service
systemctl restart cape-web.service
systemctl restart cape-rooter.service
```



## 4.2、提交样本进行检测

1、浏览器访问CAPEv2沙箱的WEB界面，点击Submint进入样本提交界面。

![image-20251016151108108](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161511189.png)



2、上传需要检测的恶意文件并选择动态分析的必要参数。

![](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161512175.png)



3、提交分析

![image-20251016151315352](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161513451.png)



4、提交后如下图所示

![image-20251016151331560](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161513650.png)



## 4.3、查看分析结果

1、点击控制面板上的“Recent”产看分析列表。

![image-20251016151400575](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161514681.png)



2、点击MD5查看分析结果。

![image-20251016151423052](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161514162.png)



### 4.3.1、快速概览（Quick Overview）

首先展示快速概览的基本信息，包括机器和时间等基本信息。

![image-20251016151517383](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161515487.png)



下面是恶意软件动态分析的具体内容：

1、文件详情信息（File Details）

![image-20251016151556030](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161515176.png)



2、签名信息（Signatures）

![image-20251016151622960](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161516085.png)



点开各项可以看到详情信息。

![image-20251016151642352](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161516488.png)



3、动态运行截屏信息（Screenshots）

![image-20251016151710161](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161517430.png)



4、各种动态行为特征总结（Summary）

访问的文件信息：

![image-20251016151735256](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161517384.png)



注册表操作信息：

![image-20251016151754101](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161517223.png)



动态API信息：

![image-20251016151829841](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161518956.png)



5、下载报告

![image-20251016151859730](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161518832.png)



### 4.3.2、动态行为特征（Behavior Analysis）

![image-20251016151927208](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161519346.png)



该部分可以看到动态API序列：

![image-20251016152004305](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161520503.png)



### 4.3.3、网络分析（Network Analysis）

![image-20251016152036054](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161520181.png)



### 4.3.4、丢弃文件（Dropped Files）

![image-20251016152117257](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161521386.png)



### 4.3.5、进程特征分析（Process Dumps）

![image-20251016152154849](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161521024.png)



### 4.3.6、有效载荷分析（Payloads）

![image-20251016152206916](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161522075.png)



### 4.3.7、对比分析（Compare this analysis to...）

![image-20251016152217664](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202510161522766.png)
