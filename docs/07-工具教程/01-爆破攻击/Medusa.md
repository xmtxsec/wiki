
# 0x01简介
Medusa(美杜莎)是一个速度快，支持大规模并行，模块化的爆力破解工具。可以同时对多个主机，用户或密码执行强力测试。Medusa和hydra一样，同样属于在线密码破解工具。

Medusa是支持AFP, CVS, FTP, HTTP, IMAP, MS-SQL, MySQL, NCP (NetWare),NNTP,PcAnywhere, POP3, PostgreSQL, rexec, RDP、rlogin, rsh, SMBNT,SMTP(AUTH/VRFY),SNMP, SSHv2, SVN, Telnet, VmAuthd, VNC、Generic Wrapper以及Web表单的密码爆破工具。


# 0x02命令说明
![image.png](./assets/1655794894190-bfd29b35-4b6d-41b6-ab0a-ff52e2b28b16.png)
```
-h [TEXT]      目标IP
-H [FILE]      目标主机文件
-u [TEXT]      用户名
-U [FILE]      用户名文件
-p [TEXT]      密码
-P [FILE]      密码文件
-C [FILE]      组合条目文件
-O [FILE]      文件日志信息
-e [n/s/ns]    N意为空密码，S意为密码与用户名相同
-M [TEXT]      模块执行名称
-m [TEXT]      传递参数到模块
-d             显示所有的模块名称
-n [NUM]       使用非默认端口
-s             启用SSL
-r [NUM]       重试间隔时间，默认为3秒
-t [NUM]       设定线程数量
-L             并行化，每个用户使用一个线程
-f             在任何主机上找到第一个账号/密码后，停止破解
-q             显示模块的使用信息
-v [NUM]       详细级别（0-6）
-w [NUM]       错误调试级别（0-10）
-V             显示版本
-Z [TEXT]      继续扫描上一次
```


# 0x03示例
使用Medusa爆破ssh
```
medusa -M ssh -h 192.168.1.9 -u flag4 -P /usr/share/john/password.lst -e ns -f
```

- -M指定破解类型为SSH
- -h指定目标IP为192.168.1.9
- -u指定用户名为flag4
- -P指定密码文件
- -e ns 进行空密码和密码与用户名相同的扫描
- -f在任何主机上找到第一个账号/密码后，停止破解

![image.png](./assets/1655794906962-c473da61-d0f2-4143-a208-51228db6bbc2.png)
![image.png](./assets/1655794911585-48b52c63-ac0a-4095-ab77-611d75a7f844.png)
