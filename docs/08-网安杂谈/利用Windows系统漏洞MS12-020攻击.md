
# 0X00 漏洞介绍
MS12-020为windows下2012年爆出高危安全漏洞，该漏洞是因为Windows系统的远程桌面协议（RDP， Remote Desktop Protocol）存在缓存溢出漏洞，攻击者可通过向目标操作系统发送特定内容的RDP包造成操作系统蓝屏，利用起来操作简单，危害极大，影响范围极广。

1. 产生条件<br />开启3389远程桌面；2012年漏洞披露后未更新系统补丁。
2. 漏洞危害<br />服务器数据处理出错，导致服务器蓝屏，无法继续提供服务。


# 0X01 学习目标

1. 了解存在MS12-020漏洞的基本条件；
2. 熟悉Metasploit工具的使用；
3. 了解MS12-020漏洞的危害。


# 0X02 所需工具
| 工具名称 | 功能描述 |
| --- | --- |
| Nmap | 网络连接端扫描软件，用来扫描网上电脑开放的网络连接端。确定哪些服务运行在哪些连接端，并且推断计算机运行哪个操作系统（这是亦称 fingerprinting）。它是网络管理员必用的软件之一，以及用以评估网络系统安全。 |
| Metasploit | Metasploit是一款开源的安全漏洞检测工具，可以帮助安全和IT专业人士识别安全性问题，验证漏洞产生的危害。 |



# 0X03实验环境
两台虚拟机：kali（192.168.1.149）<br />win2003(192.168.1.64，3389端口开启)

保证它们之间能够ping通<br />![](https://img-blog.csdnimg.cn/20200414095057186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=duXEi&originHeight=388&originWidth=718&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# 0X04实验步骤

## 步骤一
通过kali环境内集成的工具nmap对目标192.168.1.64开放的服务端口及系统版本进行扫描，具体命令如下：<br />nmap -sS 192.168.1.64 -p 3389<br />![](https://img-blog.csdnimg.cn/20200414095654819.png#crop=0&crop=0&crop=1&crop=1&id=HRzRW&originHeight=185&originWidth=611&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

这里显示端口关闭，我们需要手动打开3389端口，转到win2003。

在我的电脑 > 属性 >  远程 ，里面勾选远程桌面。<br />![](https://img-blog.csdnimg.cn/20200414095929650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=xFnOK&originHeight=470&originWidth=445&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在命令行中输入：netstat -an  可以看到3389端口已打开。<br />![](https://img-blog.csdnimg.cn/20200414100114559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=bPkw1&originHeight=384&originWidth=605&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在kali中可以看到open<br />![](https://img-blog.csdnimg.cn/20200414100209135.png#crop=0&crop=0&crop=1&crop=1&id=bwV14&originHeight=185&originWidth=595&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

【参数解释】<br />①	-sS 是指隐秘的TCP Syn扫描。<br />②	-p用于扫描指定端口。<br />发现192.168.1.64开放远程桌面3389端口，该服务在12年时被批露出存在溢出漏洞，可利用metasploit进行检测和利用。


## 步骤二
在kali环境内使用msfconsole启动Metasploit漏洞利用工具。<br />![](https://img-blog.csdnimg.cn/20200414100409501.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Mky5a&originHeight=539&originWidth=726&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤三
在metasploit内输入search ms12-020搜索ms12-020相关的模块<br />![](https://img-blog.csdnimg.cn/2020041410051459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=pSJqG&originHeight=302&originWidth=720&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤四
得到的结果有两个，每个代表不同的作用，其中<br />ms12_020_maxchannelids 是进行攻击的模块；<br />ms12_020_check 是进行漏洞扫描的模块。<br />如果进行批量攻击，可以先进行扫描后进行攻击。使用use auxiliary/scanner/rdp/ms12_020_check加载扫描模块。加载ms12_020_check模块后，使用show options 列出使用该模块需要配置的参数选项。
```
use auxiliary/scanner/rdp/ms12_020_check
show options
```
<br />![](https://img-blog.csdnimg.cn/20200414100814794.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=PBEOI&originHeight=258&originWidth=736&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤五
通过上图可以看到，使用ms12_020_check模块，需要配置RHOSTS、RPORT、THREADS三个参数，其中<br />RHOSTS是目标主机，可以是单个ip地址，也可以是ip段。<br />THREADS 是线程，我们可以设置多线程来加快扫描速度。<br />RPORT 是端口，默认是3389。<br />THREADS为扫描过程中的进程数量，默认是1<br />如果在确定IP地址的情况下，只设置RHOSTS参数即可。设置目标地址为192.168.1.64，命令格式如下：
```
set RHOSTS 192.168.1.64
```

设置完相关参数后，使用exploit或者run 开始检测。<br />![](https://img-blog.csdnimg.cn/20200414101043734.png#crop=0&crop=0&crop=1&crop=1&id=kUozY&originHeight=150&originWidth=717&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤六
上图的扫描结果显示ip为192.168.1.64的主机存在这个漏洞。可以对这台机子进行攻击。<br />使用use auxiliary/dos/windows/rdp/ms12_020_maxchannelids<br />加载攻击模块。使用show options查看使用该模块需要配置的相关参数。<br />![](https://img-blog.csdnimg.cn/2020041410125675.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=xVD8G&originHeight=220&originWidth=812&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤七
通过上图可以看到，利用ms12-020攻击模块需要配置RHOSTS、RPORT参数，其中RPORT默认是3389，所以，在目标主机未改变远程桌面端口的情况下，只设置目标地址即可。设置目标主机IP地址的命令格式如下：
```
set RHOSTS 192.168.1.64
show options
```
<br />![](https://img-blog.csdnimg.cn/20200414101419290.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=pDl1g&originHeight=233&originWidth=815&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤八
设置完ms12-020攻击模块所需参数后，使用exploit或者run开始攻击。<br />![](https://img-blog.csdnimg.cn/20200414101547314.png#crop=0&crop=0&crop=1&crop=1&id=h5YFY&originHeight=182&originWidth=815&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤九
192.168.1.64:3389 seems down显示攻击成功，目标操作系统已经宕机，服务器端进行查看，可看到服务器已经蓝屏。<br />![](https://img-blog.csdnimg.cn/20200414101529704.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=RD9W2&originHeight=478&originWidth=649&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
