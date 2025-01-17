
# 一、简介

Damn Vulnerable Web App (DVWA) 是一个非常容易受到攻击的 PHP/MySQL Web 应用程序。其主要目标是帮助安全专业人员在法律环境中测试他们的技能和工具，帮助 Web 开发人员更好地了解保护 Web 应用程序的过程，并帮助教师/学生在课堂环境中教授/学习 Web 应用程序安全性。



**官网：**http://www.dvwa.co.uk/

**GitHub：**https://github.com/digininja/DVWA

https://zhuanlan.zhihu.com/p/888170886

# 二、环境搭建

## 2.1、Linux环境下搭建

### 2.1.1、使用LAMP环境搭建

LAMP的意思是：Linux+Apache+MySQL+PHP



1、安装Apache

```
yum -y install httpd
```

![image-20250116224951846](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162249909.png)



2、修改Apache配置文件`httpd.conf`将125行的none改为all，并重启服务

```
vim /etc/httpd/conf/httpd.conf
systemctl restart httpd
```

![image-20250116225320941](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162253016.png)



3、浏览器访问是否成功，如果访问失败可以使用以下命令关闭防火墙后再次尝试

```
systemctl stop firewalld
```

![image-20250116225950606](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162259782.png)



4、安装Mariadb

```
yum -y install mariadb-server mariadb	#安装Mariadb
systemctl restart mariadb.service	#重启服务
```

![image-20250116230226496](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162302554.png)



5、初始化数据库

```
mysql_secure_installation
```

进去之后直接回车，然后输入密码，后面全都直接回车

![image-20250116230806912](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162308980.png)



6、安装PHP

```
yum -y install php
```

![image-20250116230927204](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162309252.png)



7、安装PHP组件，使php支持Mariadb

```
yum -y install php-mysql php-gd libjpeg* php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-mcrypt php-bcmath php-mhash libmcrypt
```

![image-20250116231009524](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162310579.png)



8、重启Apache、MariaDB，并在Apache根目录下创建PHP文件测试是否搭建成功。

```
systemctl restart httpd		#重启Apache
systemctl restart mariadb	#重启MariaDB
```

在`/var/www/html`目录下创建test.php文件，内容如下

```php
<?php
phpinfo();
?>
```

![image-20250116231541787](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162315860.png)



9、浏览器访问`http://YOUIP/test.php`显示如下界面表示创建成功

![image-20250116231715952](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162317020.png)



10、将下载好的DVWA压缩包上传到`/var/www/html`目录下解压并重新命名为DVWA

```
unzip DVWA-master.zip -d /var/www/html/
mv DVWA-master DVWA
```

![image-20250116233254905](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162332992.png)



11、在数据库里面创建一个库给DVWA

```
mysql -uroot -p
create database dvwa;
grant all on dvwa.* to dvwa@localhost identified by '123456';
flush privileges;
exit
```

![image-20250116233526843](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162335905.png)



12、在`/var/www/html/DVWA/config`目录下修改DVWA配置文件

```
cp config.inc.php.dist config.inc.php
vi config.ini.php
```

修改这段代码对应自己数据库相应的内容

```
#See README.md for more information on this.
$_DVWA = array();
$_DVWA[ 'db_server' ]   = 'localhost';
$_DVWA[ 'db_database' ] = 'dvwa';
$_DVWA[ 'db_user' ]     = 'dvwa';
$_DVWA[ 'db_password' ] = '123456';
$_DVWA[ 'db_port']      = '3306';

#You'll need to generate your own keys at: https://www.google.com/recaptcha/admin
$_DVWA[ 'recaptcha_public_key' ]  = '6LdJJlUUAAAAAH1Q6cTpZRQ2Ah8VpyzhnffD0mBb';
$_DVWA[ 'recaptcha_private_key' ] = '6LdJJlUUAAAAAM2a3HrgzLczqdYp4g05EqDs-W4K';

```

![image-20250116235627647](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162356716.png)



13、浏览器访问`http://YOUIP/DVWA/setup.php`，可以看到显示我们还有一些问题需要处理（状态为红色，表示在尝试完成某些模块时会出现问题）。

```
Versions of PHP below 7.x are not supported, please upgrade.
PHP function display_errors: Disabled
PHP function display_startup_errors: Disabled
PHP function allow_url_include: Disabled
Writable folder /var/www/html/dvwa/hackable/uploads/: No
Writable folder /var/www/html/dvwa/config: No
```



14、升级PHP版本

```
#查看已安装的php的所有包
yum list installed | grep php
#卸载上述包
yum remove php*
#换下载源为remi
yum install epel-release
rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
#安装php7.4
yum -y install php74-php-mysql php74-php-gd libjpeg* php74-php-imap php74-php-ldap php74-php-odbc php74-php-pear php74-php-xml php74-php-xmlrpc php74-php-mbstring php74-php-mcrypt php74-php-bcmath php74-php-mhash libmcrypt
```

![image-20250117175546421](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501171755752.png)



修改`/etc/opt/remi/php74/php.ini`文件







点击最下方的`Create/Reset Database`





14、可以开始使用了，账号密码：admin/password

![image-20250116235831401](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501162358472.png)



PS：如果不想每次开机都重启服务，可以设置开机自启

```
systemctl enable httpd				#设置开机自启Apache
systemctl enable mariadb.service	#设置开机自启MariaDB
```






### 2.1.2、使用docker搭建

1、安装docker

```
yum -y install docker
```

![image-20250117000514508](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501170005573.png)



2、安装docker-compose

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

![image-20250117001107315](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501170011382.png)



若是出现错误`-bash: /usr/local/bin/docker-compose: Permission denied`

输入`chmod +x /usr/local/bin/docker-compose` 即可。



3、启动docker并下载容器

PS：容器下载失败看这里：

[docker镜像爬取失败解决方法](https://blog.csdn.net/dutianze/article/details/142555742)

[这些方法拯救你的Docker](https://mp.weixin.qq.com/s?__biz=MjM5NzEyMzg4MA==&mid=2649507965&idx=8&sn=3ca2bfad61d126e173ac12ac90d4f545&chksm=bfdfc666df40400341e408bcbd8a2e0e3d15895c39218d4419a97ace92934520a441ae4bac67&scene=27)

```
systemctl start docker
docker pull vulnerables/web-dvwa
```

![image-20250117004506893](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501170045952.png)



4、查看并启动容器

```
docker images
docker run -d -p 8080:80 ab
```

![image-20250117004719285](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501170047340.png)



5、登录

浏览器访问`http://YOUIP:8080`账号密码：admin/password，可以愉快的玩耍了。

![image-20250117004925848](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501170049924.png)



## 2.2、Windows环境下搭建

### 2.2.1、使用小皮面板安装

1、官网下载小皮面板并安装

**官网：**https://www.xp.cn/



2、安装完小皮面板并下载 DVWA 后，将下载的 DVWA 文件解压放入小皮面板安装目录下的 www 文件夹中

![image-20250117010259455](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501170102500.png)





3、修改配置文件`config.inc.php`

打开DVWA下的`config`文件夹，有一个`config.inc.php.dist`文件，将此文件复制一份重新命名为`config.inc.php`

打开`config.inc.php`文件，将数据库的密码修改为小皮面板中数据库的密码，并配置`reCAPTCHA key`

```
$_DVWA = array();
$_DVWA[ 'db_server' ]   = '127.0.0.1';
$_DVWA[ 'db_database' ] = 'dvwa';
$_DVWA[ 'db_user' ]     = 'root';
$_DVWA[ 'db_password' ] = '123456';
$_DVWA[ 'db_port']      = '3306';
```



这个 reCAPTCHA key 可以访问 [https://www.google.com/recaptcha/admin/create](https://www.google.com/recaptcha/admin/create) 来生成

```
$_DVWA[ 'recaptcha_public_key' ]  = '6LdJJlUUAAAAAH1Q6cTpZRQ2Ah8VpyzhnffD0mBb';
$_DVWA[ 'recaptcha_private_key' ] = '6LdJJlUUAAAAAM2a3HrgzLczqdYp4g05EqDs-W4K';
```

![image-20250117013404155](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501170134223.png)



4、浏览器访问http://127.0.0.1/DVWA，点击最下方的`Create/Reset Database`

![image-20250117012309736](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501170123809.png)



5、默认账户和密码 `admin/password`，登录后就可以愉快的玩耍了

![image-20250117012330603](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202501170123694.png)

