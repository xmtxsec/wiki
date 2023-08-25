# 一、准备工作

1、安装虚拟软件`Vmware`

2、下载kali镜像

- 官网：https://www.kali.org/downloads/

- 阿里云镜像站：https://mirrors.aliyun.com/kali-images/

**若是官网下载缓慢可以前往阿里云镜像站下载**

------



# 二、创建虚拟机

**1、打开VMware，并点击“创建新的虚拟机”**

![image-20230427074124223](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427074124223.png)

------

**2、选择自定义(推荐)，并点击“下一步”**

![image-20230427074201320](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427074201320.png)

------

**3、继续下一步**

![image-20230427074241149](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427074241149.png)

------

**4、点选稍后安装操作系统(S)，并点击“下一步”**

![image-20230427074407191](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427074407191.png)

------

**6、选择客户机操作系统**

![image-20230427075245906](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427075245906.png)

------

**7、虚拟机名称可以更改也可以不更改看自己需求，修改虚拟机的安装路径，并点击“下一步”**

![image-20230427074626418](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427074626418.png)

------

**8、为处理器多加个内核数，点击下一步**

![image-20230427074700187](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427074700187.png)

------

**9、内存很重要，尽量给虚拟机分配多点，但不能超过最大推荐内存，点击一步**

![image-20230427074726959](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427074726959.png)

------

**10、选择使用桥接网络，下一步。**

![image-20230427074927929](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427074927929.png)

------

**11、默认下一步到指定磁盘容量，多给点容量，下一步。**

![image-20230427075415345](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427075415345.png)

------

**12、下一步、下一步、然后点击完成**

![image-20230427075545416](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427075545416.png)

------

**13、点击“编辑虚拟机设置”**

![image-20230427075658111](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427075658111.png)

------

**14、点选“使用ISO映像文件(M)”，并添加之前下载好的镜像**

![image-20230427075839486](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427075839486.png)

------

**15、点击开启虚拟机，选择Graphical install**

![image-20230427080206695](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427080206695.png)

------

**16、选择中文一步安装即可**

![image-20230427080307112](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427080307112.png)

------

**17、输入系统名**

![image-20230427080905573](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427080905573.png)

------

**18、这里不填**

![image-20230427080919944](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427080919944.png)

------

**19、设置用户名和密码**

![image-20230427080952920](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427080952920.png)

![image-20230427081007909](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427081007909.png)

------

**20、磁盘分区**

![image-20230427081047708](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427081047708.png)

![image-20230427081059182](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427081059182.png)

![image-20230427081110557](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427081110557.png)

![image-20230427081131430](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427081131430.png)

![image-20230427081232251](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427081232251.png)

------

**21、软件选择，默认即可**

![image-20230427081441305](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427081441305.png)

------

**22、安装GRUB引导器**

![image-20230427090637818](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427090637818.png)

![image-20230427090731752](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427090731752.png)

------

**23、安装完成**

![image-20230427091000698](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230427091000698.png)

------

**24、开启SSH服务**

```
vi etc/ssh/sshd_config
```

添加

```
permitRootLogin yes
pubkeyAuthentication yes
```

![image-20230825204150952](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230825204150952.png)

添加后保存文件。

------

**25、重启服务**

```
systemctl restart ssh.service
```

![image-20230825204510665](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230825204510665.png)

------

**26、远程连接**

![image-20230825204634877](https://cdn.jsdelivr.net/gh/xmtxsec/picture/img/image-20230825204634877.png)

------























