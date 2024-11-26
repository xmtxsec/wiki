**APIKit**可以主动/被动扫描发现应用泄露的API文档，并将API文档解析成BurpSuite中的数据包用于**API安全测试**。

![image-20241125114737419](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411251147578.png)



**项目地址：**https://github.com/API-Security/APIKit

------



# 一、安装APIKit插件

打开`BurpSuite`页面,点击`Extender`然后选择`Extensions`,添加`APIKit.jar`

![image-20241125132513430](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411251325654.png)



添加成功

![image-20241125132629888](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411251326956.png)

------



# 二、搭建API靶场

APISandbox是一个包含多个场景的API漏洞靶场。搭建需要提前安装好docker。

**项目地址：**https://github.com/API-Security/APISandbox



1、下载项目

```
wget https://github.com/API-Security/APISandbox/archive/refs/heads/main.zip -O APISandbox-main.zip
unzip APISandbox-main.zip
cd APISandbox-main
```

![image-20241125140316130](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411251403175.png)



2、进入任意一个环境的目录，自动化编译环境。

```
docker-compose build
```



3、启动靶机

```
docker up -d
```

------



# 三、功能介绍

## 3.1、基本配置

![image-20241126104103425](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411261041468.png)

**Auto Request Sending**：开启对API的请求，可以对子API进行自动化鉴权测试，快速发现API未授权访问漏洞。

**Send with Cookie**：开启Cookie，可以把包的Cookie存下来，生成请求的时候保留Cookie。

**Clear history**：点击清除所有API文档记录。



## 3.2、被动扫描

默认情况下流经BurpSuite的流量都会进行API探测解析和扫描。



## 3.3、主动扫描

**Do Auto API scan**：可以指定任意一个请求进行API指纹探测。在任何一个Burpsuite可以右键打开更多选项的页面中，都可以**点击右键**，选择**Do Auto API scan**来发起一次主动扫描，进行API指纹探测。

![image-20241126104848978](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411261048064.png)



**Do Target API scan**：可以指定任意API技术、任意BasePath、任意API文档Path、和任意Header进行API请求的生成和探测。

在任何一个Burpsuite可以右键打开更多选项的页面中，都可以**点击右键**，选择**Do Target API scan**来打开选项框。

![image-20241126104941793](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411261049864.png)

填写指定任意API技术、任意BasePath、任意API文档Path、和任意Header，再点击Scan进行API请求的生成和探测。

**注意BasePath要以`/`结尾。**



# 三、使用

APIKit会对进入到`BurpSuite`的流量进行被动扫描。解析完成后可以在APIKit面板查看结果。

![image-20241126103907314](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411261039650.png)
