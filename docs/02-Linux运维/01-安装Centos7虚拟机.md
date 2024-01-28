# 一、准备工作

1、安装虚拟软件`Vmware`

2、准备好[Centos 7 镜像文件](https://vault.centos.org/)

------



# 二、安装Centos 7

**1、打开VMware，并点击“创建新的虚拟机”**

![image-20230217140351806](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171403409.png)



**2、选择自定义(推荐)，并点击“下一步”**

![image-20230217140431600](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171404647.png)



**3、继续下一步**

![image-20230217140508966](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171405010.png)



**4、点选稍后安装操作系统(S)，并点击“下一步”**

![image-20230217140535927](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171405971.png)



**5、点选Linux(L)，根据下载的镜像选择合适的版本，并点击“下一步”**

![image-20230217140658240](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171406284.png)



**6、虚拟机名称可以更改也可以不更改看自己需求，修改虚拟机的安装路径，并点击“下一步”**

![image-20230217140829325](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171408366.png)



**7、为处理器多加个内核数，点击下一步**

![image-20230217140910721](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171409764.png)



**8、内存很重要，尽量给虚拟机分配多点，但不能超过最大推荐内存，点击一步**

![image-20230217141303570](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171413621.png)



**9、默认下一步到指定磁盘容量，多给点容量，下一步。**

![image-20230217141345391](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171413445.png)



**10、下一步、下一步、然后点击完成**

![image-20230217141416288](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171414339.png)



**11、点击“编辑虚拟机设置”**

![image-20230217141455147](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171414179.png)



**12、点选“使用ISO映像文件(M)”，并添加之前下载好的镜像**

![image-20230217141648503](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171416551.png)



**13、移除USB控制器、声卡和打印机 只是为了腾出更多的资源空间 (可以选择跳过 不移除)，最后点击确定**

![image-20230217141743292](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171417332.png)



**14、点击开启虚拟机**

![image-20230217141809535](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171418583.png)



**15、鼠标移入到虚拟机中，并按下“↑”键，选择Install CentOS 7，最后按下“Enter 键”**

![image-20230217141836978](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171418018.png)



**16、拉到最下选择使用中文，点击“Continue”（建议使用英语）**

![image-20230217141938769](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171419821.png)



**17、选择安装位置（D）**

![image-20230217142312525](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171423596.png)



**18、默认自动分区（新手推荐）**

![image-20230217142408606](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171424663.png)



**19、选择网络设置**

![image-20230217142453338](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171424397.png)



**20、配置网络**

![image-20230217142525561](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171425622.png)



**21、点击开始安装、点击设置密码（管理员密码）等待安装完成（速度快几分钟安装完成）** 

![image-20230217142613292](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171426365.png)



![image-20230217142730168](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171427218.png)



**22、安装完成之后点击重启即可**

![image-20230217143100014](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171431063.png)

![image-20230217143303539](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/202302171433592.png)



# 三、换源

由于CentOS 7默认的 yum 源是国外的，导致使用yum下载软件的下载速度不是很理想，这时候就需要将yum源更换成国内的源。



1、备份

```
 mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

![image-20240128120930102](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20240128120930102.png)

执行该命令后，`CentOS-Base.repo`文件将被重命名为`CentOS-Base.repo.backup`，并放置在相同的目录中。这样做可以为`CentOS-Base.repo`文件创建一个备份，以免不小心修改或删除该文件时丢失重要的配置。



2、下载国内yum源配置文件到/etc/yum.repos.d/CentOS-Base.repo

```
阿里云 CentOS 7 源（推荐）：
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
 
网易163 CentOS 7 源：
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
 
搜狐 CentOS 7 源：
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.sohu.com/help/CentOS7-Base-sohu.repo
 
华为云 CentOS 7 源：
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.myhuaweicloud.com/repo/CentOS-7.repo
 
华东理工大学 CentOS 7 源：
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.ustc.edu.cn/centos/7/os/x86_64
 
清华大学 CentOS 7 源：
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.tuna.tsinghua.edu.cn/centos/7/os/x86_64/
 
北京理工大学 CentOS 7 源：
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.bfsu.edu.cn/centos/7/os/x86_64/
 
上海交通大学 CentOS 7 源：
wget -O /etc/yum.repos.d/CentOS-Base.repo https://ftp.sjtu.edu.cn/centos/7/os/x86_64/
 
中国科学技术大学 CentOS 7 源：
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.ustc.edu.cn/centos/7/os/x86_64/
 
兰州大学 CentOS 7 源：
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirror.lzu.edu.cn/centos/7/os/x86_64/
```

以上命令选择一个就行

![image-20240128121137318](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20240128121137318.png)

执行该命令后，`wget`将从阿里云的源下载`CentOS-7.repo`文件，并将其保存为`CentOS-Base.repo`，放置在`/etc/yum.repos.d/`目录下。这样做可以使用阿里云作为国内的CentOS镜像源，加快软件包的下载速度。



3、清理yum缓存，并生成新的缓存

```
yum clean all	#清理 YUM 缓存
yum makecache	#重新生成 YUM 缓存
```



4、更新yum源检查是否生效

```
yum update
```

![image-20240128121319064](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20240128121319064.png)
