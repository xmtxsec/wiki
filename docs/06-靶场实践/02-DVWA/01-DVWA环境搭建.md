
# 简介

Damn Vulnerable Web App (DVWA) 是一个非常容易受到攻击的 PHP/MySQL Web 应用程序。其主要目标是帮助安全专业人员在法律环境中测试他们的技能和工具，帮助 Web 开发人员更好地了解保护 Web 应用程序的过程，并帮助教师/学生在课堂环境中教授/学习 Web 应用程序安全性.


# 下载

ＤＶＷＡ官网：[http://www.dvwa.co.uk/](http://www.dvwa.co.uk/)

![image-20211217100916989.png](./assets/1652254181367-a17adec8-0eba-41af-9103-53e856bd2bcf.png)


# 使用小皮面板安装

安装完小皮面板并下载 DVWA 后，将下载的 DVWA 文件解压放入小皮面板安装目录下的 www 文件夹中

![image-20220507100548205.png](./assets/1652254188692-abaedbe1-66b9-4ae6-989a-049a4cb6297c.png)


## 修改配置文件
打开DVWA下的config文件，下面有一个config.inc.php.dist文件，将此文件copy一份，重新命名为config.inc.php<br />打开config.inc.php文件，将数据库的密码修改为小皮面板中的密码

![image-20220507101153871.png](./assets/1652254196022-399452b6-fdb9-427e-8be5-63f3bbc3d785.png)

![image-20220507100846708.png](./assets/1652254200236-8ef2a5a4-f82a-44d6-b47f-cafac398b52b.png)


## 初始化
然后打开浏览器，访问http://127.0.0.1，即可打开DVWA网站

首先检查配置，红色的是我们需要修改的，要不然在后面的联系过程中会出现问题

![image-20220507102027144.png](./assets/1652254204458-46236c1f-5b1f-428e-a156-5a1ccd84d728.png)


## 配置magic_quotes_gpc

打开php.ini文件

![image-20220507102512593.png](./assets/1652254210736-2d99d82a-6fb8-4409-b8cd-e281e4c2b166.png)

修改为如图所示

![image-20220507102609343.png](./assets/1652254215464-491f1d32-4fbc-421a-aab8-84731c15b600.png)


## 配置reCAPTCHA key

这个 reCAPTCHA key 可以访问 [https://www.google.com/recaptcha/admin/create](https://www.google.com/recaptcha/admin/create) 来生成，这里就直接使用别人提供的

```
Site key:6LdJJlUUAAAAAH1Q6cTpZRQ2Ah8VpyzhnffD0mBb
Secret key:6LdJJlUUAAAAAM2a3HrgzLczqdYp4g05EqDs-W4K
```

打开 dvwa/config/config.inc.php 这个配置文件，将key填入即可

![image-20220507103326786.png](./assets/1652254224046-79511083-8005-4634-8ddd-a5c5cf44a785.png)


## 重启
全部修改完成后重启小皮面板的服务，访问DVWA站点

![image-20220507103449408.png](./assets/1652254228575-ebc2ce87-a679-4f50-81b4-01a0fa10ab51.png)

确认没有问题之后点击最下方的`Create / Reset Database`创建数据库即可

![image-20220507103706986.png](./assets/1652254233162-dabfbba5-45ed-410e-8826-3093c9101e0a.png)


## 登录

默认账户和密码 `admin/password`，登录后就可以愉快的玩耍了

![image-20220507103824144.png](./assets/1652254237617-f0af169a-96e7-4431-b553-f3f579fba75b.png)


# 使用docker搭建


## 安装docker

```
yum -y install docker
```

![image-20220507104444194.png](./assets/1652254242706-5efa0290-8180-4852-a245-b399bf8377ea.png)


## 安装docker-compose

方法一：

```
 yum -y install epel-release
 yum -y install python3-pip
 pip3 install docker-compose
```

方法二：

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

**安装完成后查看版本**

```
docker-compose --version
```

![image-20220507110200554.png](./assets/1652254249785-122e844e-39bd-475f-99e9-0feaf662b148.png)

若是出现错误`-bash: /usr/local/bin/docker-compose: Permission denied`输入`chmod +x /usr/local/bin/docker-compose` 即可。


## 下载容器

```
docker pull sagikazarmark/dvwa
```

![image-20220507111022216.png](./assets/1652254254369-941ca133-5412-496b-92b0-e33f0fa0b89b.png)


## 启动容器

```
docker run -d -p 8080:80 e9
```

![image-20220507111515427.png](./assets/1652254258805-11f85ef1-bdc0-4275-bca2-ce5f5a6b7370.png)


## 登录
浏览器访问http://YOUIP:8080

![image-20220507111554133.png](./assets/1652254263076-f06aa30f-9d91-4662-be40-c76709a67572.png)

账号密码admin/password，可以愉快的玩耍了。

![image-20220507111738264.png](./assets/1652254268362-e1e267d2-18d9-4051-869f-65174491439f.png)
