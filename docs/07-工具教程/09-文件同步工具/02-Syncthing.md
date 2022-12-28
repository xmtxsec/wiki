
# 简介
Syncthing 是一个**连续的文件同步程序**。它在两台或多台计算机之间同步文件。<br />github项目地址：[https://github.com/syncthing/syncthing](https://github.com/syncthing/syncthing)


# 安装
下载解压
```
wget https://github.com/syncthing/syncthing/releases/download/v1.18.4/syncthing-linux-amd64-v1.18.4.tar.gz
tar -zxvf syncthing-linux-amd64-v1.18.4.tar.gz
```

运行
```
cd syncthing-linux-amd64-v1.18.4
./syncthing
```

运行成功后会显示Detected 0 NAT services
![image.png](./assets/1655794334656-192fb53a-934a-4232-a35f-7cb07a3b984f.png)

syncthing的配置文件在：home/user/.config/syncthing/config.xml<br />修改配置文件使其能够在局域网访问
```
vi /root/.config/syncthing/config.xml
```
![image.png](./assets/1655794346159-c3d20ffe-e872-41ad-ade0-68b59dd2d4f3.png)

再次运行
```
nohup ./syncthing &
```

在浏览器中访问，初次访问会要求设置密码，若无法访问请检查防火墙
```
http://ip:8384
```

设置账号密码：操作-->设置-->图形用户界面
![image.png](./assets/1655794358444-0b5aca6d-7b0a-4ee3-8436-b3ff2aed65ef.png)


# 同步传输
**同步传输两台服务器都如上进行安装配置**<br />首先在服务端找到设备标识：操作-->显示ID
![image.png](./assets/1655794371048-ee558743-f970-4302-a583-88e5f83bfbd4.png)

在客户端点击添加远程设备
![image.png](./assets/1655794377868-22aa3033-72d1-481c-9051-2c5b1eb76e27.png)

在服务端确认添加设备
![image.png](./assets/1655794386750-b8d1a018-4a53-4228-b508-2af858f7ff96.png)

返回到客户端点击添加文件夹
![image.png](./assets/1655794393064-4e611247-91fb-497f-9309-a0bc184c31be.png)
![image.png](./assets/1655794400873-45df1992-20f2-4873-bd46-1cdbc5b87732.png)

回到服务端确认添加共享文件夹
![image.png](./assets/1655794408704-d0ba71ce-8122-482f-8b7e-05b3e1e245f9.png)

![image.png](./assets/1655794413832-78d1285d-6d5b-4846-8d28-c8fb2dbfcf43.png)

成功添加同步文件夹
![image.png](./assets/1655794421844-462702c5-5b13-486a-b436-3412650efbb3.png)


# 同步测试
在客户端添加6、7、8、9、10文件夹，删除1、2、3、4、5文件夹
![image.png](./assets/1655794433159-f1fb8e8a-6a3a-4ba9-9b2e-13764b343aae.png)

在服务端删除6、7、8、9、10文件夹，添加1、2、3、4、5文件夹
![image.png](./assets/1655794443298-d23b9c74-b398-4976-ba09-979b2b0cf7c3.png)
