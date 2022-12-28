
# 简介
Nextcloud是一款开源免费的私有云存储网盘项目，可以让你快速便捷地搭建一套属于自己或团队的云同步网盘，从而实现跨平台跨设备文件同步、共享、版本控制、团队协作等功能。它的客户端覆盖了Windows、Mac、Android、iOS、Linux 等各种平台，也提供了网页端以及 WebDAV接口，所以你几乎可以在各种设备上方便地访问你的云盘。


# 搭建过程
安装docker
```
yum -y install docker
```

启动docker
```
systemctl start docker
```

下载镜像(不建议使用最新版本，会出现各种问题)
```
下载mysql镜像：
docker pull mysql:5.7
下载nextcloud镜像：
docker pull nextcloud:20
```

启动mysql镜像
```
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=password -d mysql:5.7
```

进入mysql容器创建数据库
```
docker exec -it mysql /bin/bash
- mysql -u root -p
- CREATE DATABASE nextcloud;
- GRANT ALL ON *.* TO 'root'@'%';
- flush privileges;
- exit;
```
![image.png](./assets/1655796540454-867d0686-ec13-4d54-96ad-da9c2d43a621.png)

启动nextcloud镜像
```
docker run -d -p 80:80 --name nextcloud --privileged=true --restart=always --link mysql:mysql -v /usr/local/nextcloud/d_data:/data nextcloud:20
```

在浏览器访问
```
http://youip
```

如下配置
![image.png](./assets/1655796551677-d4fe6526-ee83-4d45-ba77-f6c8bb57b276.png)

等待一段时间后即搭建成功
![image.png](./assets/1655796560330-cf8fcea2-0d70-4bf1-9738-e864cb28a898.png)
