
# 0x00 前言
当企业发生黑客入侵、系统崩溃或其它影响业务正常运行的安全事件时，急需第一时间进行处理，使企业的网络信息系统在最短时间内恢复正常工作，进一步查找入侵来源，还原入侵事故过程，同时给出解决方案与防范措施，为企业挽回或减少经济损失。

常见的应急响应事件分类：<br />Web 入侵：网页挂马、主页篡改、Webshell<br />系统入侵：病毒木马、勒索软件、远控后门<br />网络攻击：DDOS 攻击、DNS 劫持、ARP 欺骗

针对常见的攻击事件，结合工作中应急响应事件分析和解决的方法，总结了一些 Windows 服务器入侵排查的思路。


# 0x01 入侵排查思路

## 1.1 检查系统账号安全
1、查看服务器是否有弱口令，远程管理端口是否对公网开放。

- 检查方法：据实际情况咨询相关服务器管理员。

2、查看服务器是否存在可疑账号、新增账号。

- 检查方法：打开 cmd 窗口，输入 `lusrmgr.msc` 命令，查看是否有新增/可疑的账号，如有管理员群组的（Administrators）里的新增账户，如有，请立即禁用或删除掉。

3、查看服务器是否存在隐藏账号、克隆账号。

-  检查方法：<br />a、打开注册表 ，查看管理员对应键值。<br />b、使用D盾_web查杀工具，集成了对克隆账号检测的功能。 

![sum-1-1.png](/_img\05-应急响应/1656917178594-02f8575f-9432-44aa-b1b6-bd9df256364f.png)

4、结合日志，查看管理员登录时间、用户名是否存在异常。

-  检查方法：<br />a、Win+R 打开运行，输入"eventvwr.msc"，回车运行，打开“事件查看器”。<br />b、导出 Windows 日志 -- 安全，利用微软官方工具 [Log Parser](https://www.microsoft.com/en-us/download/details.aspx?id=24659) 进行分析。 

![sum-1-2.png](/_img\05-应急响应/1656917183370-f152ec19-e865-460b-8102-dff6bd56f621.png)


## 1.2 检查异常端口、进程
1、检查端口连接情况，是否有远程连接、可疑连接。

-  检查方法：<br />a、使用`netstat -ano` 命令查看目前的网络连接，定位可疑的 ESTABLISHED<br />b、根据 netstat 命令定位出的 PID 编号，再通过 tasklist 命令进行进程定位 `tasklist | findstr "PID"` 

![sum-1-3.png](/_img\05-应急响应/1656917190277-b91ac393-d8e3-41f1-bc9a-d37163a11609.png)

2、进程

-  检查方法：<br />a、开始 -- 运行 -- 输入 `msinfo32` 命令，依次点击 "软件环境 -- 正在运行任务" 就可以查看到进程的详细信息，比如进程路径、进程ID、文件创建日期以及启动时间等。<br />b、打开D盾_web查杀工具，进程查看，关注没有签名信息的进程。<br />c、通过微软官方提供的 Process Explorer 等工具进行排查 。<br />d、查看可疑的进程及其子进程。可以通过观察以下内容： 
```
  没有签名验证信息的进程
  没有描述信息的进程
  进程的属主
  进程的路径是否合法
  CPU 或内存资源占用长时间过高的进程
```


3、小技巧：<br />	a、查看端口对应的 PID：`netstat -ano | findstr "port"`<br />	b、查看进程对应的 PID：任务管理器 -- 查看 -- 选择列 -- PID 或者 `tasklist | findstr "PID"`<br />	c、查看进程对应的程序位置：<br />		任务管理器 -- 选择对应进程 -- 右键打开文件位置<br />		运行输入 `wmic`，cmd 界面输入 `process`<br />	d、`tasklist /svc`   进程 -- PID -- 服务<br />	e、查看Windows服务所对应的端口：<br />		%systemroot%/system32/drivers/etc/services（一般 %systemroot% 就是 C:\Windows 路径）


## 1.3 检查启动项、计划任务、服务
1、检查服务器是否有异常的启动项。

-  检查方法：<br />a、登录服务器，单击【开始】>【所有程序】>【启动】，默认情况下此目录在是一个空目录，确认是否有非业务程序在该目录下。<br />b、单击开始菜单 >【运行】，输入 `msconfig`，查看是否存在命名异常的启动项目，是则取消勾选命名异常的启动项目，并到命令中显示的路径删除文件。<br />c、单击【开始】>【运行】，输入 regedit，打开注册表，查看开机启动项是否正常，特别注意如下三个注册表项： 
```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\run
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Runonce
```
<br />检查右侧是否有启动异常的项目，如有请删除，并建议安装杀毒软件进行病毒查杀，清除残留病毒或木马。<br />d、利用安全软件查看启动项、开机时间管理等。<br />e、组策略，运行 `gpedit.msc` <br />![sum-1-4.png](/_img\05-应急响应/1656917234189-2f9a9703-0d97-44ac-9278-34d9c2e417f2.png)

2、检查计划任务

-  检查方法：<br />a、单击【开始】>【设置】>【控制面板】>【任务计划】，查看计划任务属性，便可以发现木马文件的路径。<br />b、单击【开始】>【运行】；输入 `cmd`，然后输入 `at`，检查计算机与网络上的其它计算机之间的会话或计划任务，如有，则确认是否为正常连接。 

3、服务自启动

- 检查方法：单击【开始】>【运行】，输入 `services.msc`，注意服务状态和启动类型，检查是否有异常服务。


## 1.4 检查系统相关信息
1、查看系统版本以及补丁信息

- 检查方法：单击【开始】>【运行】，输入 `systeminfo`，查看系统信息。

2、查找可疑目录及文件

-  检查方法：<br />a、 查看用户目录，新建账号会在这个目录生成一个用户目录，查看是否有新建用户目录。 
```
Window 2003版本 C:\Documents and Settings
Window 2008R2及以后版本 C:\Users\
```
<br />b、单击【开始】>【运行】，输入 `%UserProfile%\Recent`，分析最近打开分析可疑文件。<br />c、在服务器各个目录，可根据文件夹内文件列表时间进行排序，查找可疑文件。<br />d、回收站、浏览器下载目录、浏览器历史记录<br />e、修改时间在创建时间之前的为可疑文件 

3、发现并得到 WebShell、远控木马的创建时间，如何找出同一时间范围内创建的文件？<br />a、利用 [Registry Workshop](http://www.torchsoft.com/en/rw_information.html) 注册表编辑器的搜索功能，可以找到最后写入时间区间的文件。<br />b、利用计算机自带文件搜索功能，指定修改时间进行搜索。


## 1.5 自动化查杀

-  病毒查杀 
   - 检查方法：下载安全软件，更新最新病毒库，进行全盘扫描。
-  webshell查杀 
   - 检查方法：选择具体站点路径进行webshell查杀，建议使用两款 WebShell 查杀工具同时查杀，可相互补充规则库的不足。


## 1.6 日志分析
系统日志

-  分析方法：<br />a、前提：开启审核策略，若日后系统出现故障、安全事故则可以查看系统的日志文件，排除故障，追查入侵者的信息等。<br />b、Win+R 打开运行，输入 "eventvwr.msc"，回车运行，打开"事件查看器"。<br />C、导出应用程序日志、安全日志、系统日志，利用 [Log Parser](https://www.microsoft.com/en-us/download/details.aspx?id=24659) 进行分析。 

Web 访问日志

-  分析方法：<br />a、找到中间件的web日志，打包到本地方便进行分析。<br />b、推荐工具：Windows 下，推荐用 EmEditor 进行日志分析，支持大文本，搜索效率还不错。Linux 下，使用 Shell 命令组合查询分析。 


# 0x02 工具篇

## 2.1 病毒分析
PCHunter：[http://www.xuetr.com](http://www.xuetr.com)
火绒剑：[https://www.huorong.cn](https://www.huorong.cn)
Process Explorer：[https://docs.microsoft.com/zh-cn/sysinternals/downloads/process-explorer](https://docs.microsoft.com/zh-cn/sysinternals/downloads/process-explorer)
processhacker：[https://processhacker.sourceforge.io/downloads.php](https://processhacker.sourceforge.io/downloads.php)
autoruns：[https://docs.microsoft.com/en-us/sysinternals/downloads/autoruns](https://docs.microsoft.com/en-us/sysinternals/downloads/autoruns)
OTL：[https://www.bleepingcomputer.com/download/otl/](https://www.bleepingcomputer.com/download/otl/)
SysInspector：[http://download.eset.com.cn/download/detail/?product=sysinspector](http://download.eset.com.cn/download/detail/?product=sysinspector)


## 2.2 病毒查杀
卡巴斯基：[http://devbuilds.kaspersky-labs.com/devbuilds/KVRT/latest/full/KVRT.exe](http://devbuilds.kaspersky-labs.com/devbuilds/KVRT/latest/full/KVRT.exe)   （推荐理由：绿色版、最新病毒库）<br />大蜘蛛：http://free.drweb.ru/download+cureit+free（推荐理由：扫描快、一次下载只能用1周，更新病毒库）<br />火绒安全软件：[https://www.huorong.cn](https://www.huorong.cn)
360杀毒：[http://sd.360.cn/download_center.html](http://sd.360.cn/download_center.html)


## 2.3 病毒动态
CVERC-国家计算机病毒应急处理中心：[http://www.cverc.org.cn](http://www.cverc.org.cn)
微步在线威胁情报社区：[https://x.threatbook.cn](https://x.threatbook.cn)
火绒安全论坛：[http://bbs.huorong.cn/forum-59-1.html](http://bbs.huorong.cn/forum-59-1.html)
爱毒霸社区：[http://bbs.duba.net](http://bbs.duba.net)
腾讯电脑管家：[http://bbs.guanjia.qq.com/forum-2-1.html](http://bbs.guanjia.qq.com/forum-2-1.html)


## 2.4 在线病毒扫描网站
Virustotal：[https://www.virustotal.com](https://www.virustotal.com)
Virscan：[http://www.virscan.org](http://www.virscan.org)
腾讯哈勃分析系统：[https://habo.qq.com](https://habo.qq.com)
Jotti 恶意软件扫描系统：[https://virusscan.jotti.org](https://virusscan.jotti.org)


## 2.5 webshell查杀
D盾_Web查杀：[http://www.d99net.net/index.asp](http://www.d99net.net/index.asp)
河马 WebShell 查杀：[http://www.shellpub.com](http://www.shellpub.com)
