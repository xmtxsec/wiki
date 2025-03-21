# 1、网络问题

CAPEv2项目中的脚本基本上都需要联网下载，所以在执行脚本的过程中一定要保持网络通畅！

有条件的建议在安装前挂上网络代理。

## 1.1、配置系统代理

**1、配置v2rayN**

打开v2rayN设置中的参数设置，把`允许来自局域网的连接`打开。

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503151413600.png" alt="image-20250315141226950" style="zoom: 67%;" />



**2、配置虚拟机的网络代理**

打开虚拟机网络设置，将代理设置为手动代理并配置如下

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503151416300.png" alt="image-20250315141618248" style="zoom:67%;" />

- 这里的IP为你主机在局域网中的IP地址

  <img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503151418475.png" alt="image-20250315141805419" style="zoom: 50%;" />

- 端口见v2rayN界面的左下角

  <img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503151418369.png" alt="image-20250315141848322" style="zoom:67%;" />



## 1.2、配置GIT代理

在命令行执行下面命令即可，注意修改IP和端口为你实际的IP和端口值

```
git config --global http.proxy http://192.168.1.2:10809
git config --global https.proxy https://192.168.1.2:10809
```



取消代理命令如下

```
git config --global --unset   http.proxy http://192.168.1.2:10809
git config --global --unset   https.proxy https://192.168.1.2:10809
```



## 1.3、配置全局代理

在命令行执行下面命令即可，注意修改IP和端口为你实际的IP和端口值

```
export https_proxy="https://192.168.1.2:10809";export http_proxy="http://192.168.1.2:10809"
```



取消代理命令如下

```
export unset https_proxy="https://192.168.1.2:10809";export unset http_proxy="http://192.168.1.2:10809"
```

------



# 2、安装Python碰到的问题

## 2.1、无法安装Python

这是因为Win7系统缺少补丁，打上补丁即可。

补丁下载链接：https://pan.baidu.com/s/1zCoF8wnNVFRbgYF9g-kl3w?pwd=js4m

解决方案：https://blog.csdn.net/z09364517158/article/details/135182618

这个问题我在[搭建CAPEv2](https://wiki.xmtxsec.top/#/08-%E7%BD%91%E5%AE%89%E6%9D%82%E8%B0%88\01-CAPEv2_Sandbox\01-%E6%90%AD%E5%BB%BACAPEv2_Sandbox?id=_5%e3%80%81%e9%85%8d%e7%bd%ae%e8%99%9a%e6%8b%9f%e6%9c%ba)的文章中说明过原因。



## 2.2、Python无法安装Pillow

在多次安装中，这个问题我只碰到了一次。

解决方案：https://blog.csdn.net/m0_68503106/article/details/131943259

------



# 3、运行agent.py碰到的问题

## 3.1、No module named 'cgi'

如果你用的是高版本的Python建议你切换到低版本，但是不得低于`3.6`，并且要使用`32 位（x86）Python3` 版本，这一点我在[搭建CAPEv2](https://wiki.xmtxsec.top/#/08-%E7%BD%91%E5%AE%89%E6%9D%82%E8%B0%88\01-CAPEv2_Sandbox\01-%E6%90%AD%E5%BB%BACAPEv2_Sandbox?id=_5%e3%80%81%e9%85%8d%e7%bd%ae%e8%99%9a%e6%8b%9f%e6%9c%ba)的文章中也有说明。

如果你使用的是低版本Python碰到此问题，那么我建议你重新安装。我是用的Python版本是`3.8.10`，重装后解决了这个问题。

![image-20250315143332105](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503151433152.png)



## 3.2、运行agent.py报错

这个问题是编码问题导致的，项目作者在编写`agent.py`时是在`Linux`环境下编写的，而我们的运行环境是`Windows`环境。

将`agent.py`文件的内容在Windows机器上复制粘贴到`.txt`文件中然后重命名为`agent.py`即可。

这个问题我在[搭建CAPEv2](https://wiki.xmtxsec.top/#/08-%E7%BD%91%E5%AE%89%E6%9D%82%E8%B0%88\01-CAPEv2_Sandbox\01-%E6%90%AD%E5%BB%BACAPEv2_Sandbox?id=_5%e3%80%81%e9%85%8d%e7%bd%ae%e8%99%9a%e6%8b%9f%e6%9c%ba)的文章中也有说明。

![image-20250308014930721](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503151446595.png)

------



# 4、报告界面问题

## 4.1、数据库连接问题

Web分析详情界面显示下面错误，但是查看`Mongodb`服务状态正常。

```
ERROR :-(
You need to enable Mongodb/ES to be able to use WEBGUI to see the analysis
```

解决此问题只需要打开配置文件`/CAPEv2/conf/reporting.conf`将93行`[mongodb]`下的`enabled`设置为`yes`即可。

<img src="https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503151513965.png" alt="image-20250315151351892" style="zoom: 67%;" />



# 4.2、样本分析显示failed_reporting

当我打开生成`litereport`、`reporthtmlsummary`、`reporthtml`、`reportpdf`格式报告后样本分析完显示`failed_reporting`。

![image-20250315152513190](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503151525240.png)



但是我在后台查看到`JSON`、`litereport`、`reporthtmlsummary`、`reporthtml`格式的报告都是存在的，唯独缺少了`reportpdf`格式报告。

![image-20250315152545820](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503151525873.png)



查看日志文件`/CAPEv2/log/process-5.log`显示：

```
WARNING: The reporting module "ReportPDF" returned the following error: Failed to generate PDF report: Neither wkhtmltopdf nor Weasyprint Python library are installed
```



切换到`CAPE用户`在`/CAPEv2/`目录下使用`Poetry`安装`weasyprint`

```
sudo su cape
poetry run pip install weasyprint
sudo apt-get install build-essential python3-dev libpango1.0-dev libcairo2-dev
```



上传样本测试，问题解决。

![image-20250315153627796](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202503151536873.png)
