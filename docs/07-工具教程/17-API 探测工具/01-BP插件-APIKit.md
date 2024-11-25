**APIKit**可以主动/被动扫描发现应用泄露的API文档，并将API文档解析成BurpSuite中的数据包用于**API安全测试**。

![image-20241125114737419](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411251147578.png)



**项目地址：**https://github.com/API-Security/APIKit



# 一、安装APIKit插件

打开`BurpSuite`页面,点击`Extender`然后选择`Extensions`,添加`APIKit.jar`

![image-20241125132513430](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411251325654.png)



添加成功

![image-20241125132629888](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202411251326956.png)



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







