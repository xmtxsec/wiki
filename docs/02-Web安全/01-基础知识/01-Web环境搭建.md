
# 新建centos虚拟机
1、选择自定义，下一步

![image.png](../../_img/02-Web安全/01-基础知识/1653891758563-76707cbb-567a-404e-a61c-d1774b63c5b3.png)

2、默认，下一步

![image.png](../../_img/02-Web安全/01-基础知识/1653891773777-afcece0a-fc48-4662-bfc5-fb153e7ad713.png)

3、选择稍后安装操作系统，下一步
![image.png](../../_img/02-Web安全/01-基础知识/1653891785770-51ef0a6a-291c-4a15-99b5-92675b1d5bd6.png)

4、选择Linux、版本为centos7 64位
![image.png](../../_img/02-Web安全/01-基础知识/1653891796117-39f222a9-190c-4d33-ba8a-b1611fa0f085.png)

5、给创建的虚拟机取一个名字并选择保存位置。
![image.png](../../_img/02-Web安全/01-基础知识/1653891809303-a87ed816-3316-4c88-ae8b-40a25e3739b9.png)

6、给创建的虚拟机配置处理器
![image.png](../../_img/02-Web安全/01-基础知识/1653891819862-afe22ff5-eb60-49c7-a7bb-d11d6489ba63.png)

7、给创建的虚拟机分配内存
![image.png](../../_img/02-Web安全/01-基础知识/1653891831600-ba7b6acc-3b8c-433c-929d-ef9476b8d18b.png)

8、选择网络类型
![image.png](../../_img/02-Web安全/01-基础知识/1653891841908-9fe29229-86d4-443d-abae-373c447628c0.png)

9、给创建的虚拟机分配磁盘大小
![image.png](../../_img/02-Web安全/01-基础知识/1653891852648-7f124e21-0c18-4d98-b175-82e1423296d8.png)

10、创建完成
![image.png](../../_img/02-Web安全/01-基础知识/1653891861745-ccb5c4ad-3d2b-4da3-8286-43bacda0923c.png)

11、选择创建的虚拟机，点击鼠标右键，虚拟机设置，添加ISO镜像文件
![image.png](../../_img/02-Web安全/01-基础知识/1653891873481-b0278ea9-ae58-4bc7-8246-68db794f3e1f.png)

12、开启虚拟机，选择第一个
![image.png](../../_img/02-Web安全/01-基础知识/1653891883346-1164e60c-820e-4a7a-8bde-84682634626d.png)

13、选择语言，建议使用英语
![image.png](../../_img/02-Web安全/01-基础知识/1653891893612-84cfd918-a6bc-401c-ab07-1a6995bd96d0.png)

14、修改系统时间
![image.png](../../_img/02-Web安全/01-基础知识/1653891907149-4eeb9a7e-0c90-4f69-9e96-666b5a0b310d.png)
![image.png](../../_img/02-Web安全/01-基础知识/1653891922402-26d58d3f-39d2-4642-8c13-32a63dec8bcc.png)

15、下一步
![image.png](../../_img/02-Web安全/01-基础知识/1653891933885-913ccfbc-31c1-42cf-96c5-1cacffa85b7c.png)

15、设置root密码
![image.png](../../_img/02-Web安全/01-基础知识/1653891944057-6b1a08c8-0fbe-4024-a6d2-4e20501d1359.png)

16、等待进度条加载完毕，重启
![image.png](../../_img/02-Web安全/01-基础知识/1653891957558-9ce01456-56d7-488f-a6b8-3b7152cbb6e6.png)

17、查看IP
![image.png](../../_img/02-Web安全/01-基础知识/1653891969825-479c01df-0079-48aa-b4f8-e7f521ecf591.png)

18、设置IP，我这里是192.168.10.0网段
![image.png](../../_img/02-Web安全/01-基础知识/1653891981430-6777d252-291c-4f18-9af9-effbddef2eb9.png)

19、修改/etc/sysconfig/network-scripts/ifcfg-ens33的内容
```
vi  /etc/sysconfig/network-scripts/ifcfg-ens33
```

添加内容如下
```
修改：
BOOTPROTO=static
ONBOOT=yes
添加：
IPADDR=192.168.10.60
NETMASK=255.255.255.0
GATEWAY=192.168.10.1
DNS=114.114.114.114
```
![image.png](../../_img/02-Web安全/01-基础知识/1653891998780-2e8733b7-39e1-413e-babb-cd0d087f42f8.png)

20、重启网卡
```
service network restart
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892018954-3aab9d63-5a79-4a8f-9522-e5cb4f25f429.png)


# 搭建Nginx
1、安装gcc
```
yum install -y gcc c++
```

2、PCRE pcre-devel 安装，nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。
```
yum install -y pcre pcre-devel
```

3、zlib 安装，zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip
```
yum install -y zlib zlib-devel
```

4、OpenSSL 安装，OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及 SSL 协议，并提供丰富的应用程序供测试或其它目的使用。nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。
```
yum install -y openssl openssl-devel
```

5、直接下载`.tar.gz`安装包，地址：[https://nginx.org/en/download.html](https://nginx.org/en/download.html)
```
wget -c https://nginx.org/download/nginx-1.20.1.tar.gz
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892056182-11df40bd-82f9-41ba-8953-41ba366c7415.png)

6、解压
```
tar -zxvf nginx-1.20.1.tar.gz
cd nginx-1.20.1
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892066913-5348a44d-3aa8-4da7-a026-01d9c84b8c95.png)

7、编译安装
```
./configure
make
make install
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892080937-c4520699-32bd-4e02-b4fb-b71765f9db96.png)
![image.png](../../_img/02-Web安全/01-基础知识/1653892093041-545bc646-159a-4168-8abc-e30e24428103.png)

8、启动、停止nginx
```
cd /usr/local/nginx/sbin/
./nginx 
./nginx -s stop
./nginx -s quit
./nginx -s reload
```

9、开机自启
```
vi /etc/rc.local
```

增加一行 `/usr/local/nginx/sbin/nginx`<br />设置执行权限：
```
chmod 755 rc.local
```


# 搭建PHP

## 方法一
1、下载安装包
```
wget https://www.php.net/distributions/php-5.3.29.tar.gz
```

2、解压
```
tar -zxvf php-5.3.29.tar.gz
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892130158-b32cbc3e-1385-4a09-8d18-15748b266e11.png)


3、编译安装
```
./configure --prefix=/usr/local/php --enable-fpm
```
```
--prefix=/usr/local/php #这里是指定安装目录
--enable-fpm #这里是PHPFastCGI管理器，为了后面Nginx能解析PHP
```

出错
![image.png](../../_img/02-Web安全/01-基础知识/1653892148155-c7e19e3b-262a-472b-bdc1-328c0db2270b.png)

4、重新安装libxml2和libxml2-devel包
```
yum install -y libxml2
yum install libxml2-devel
```

5、再次编译
```
./configure --prefix=/usr/local/php --enable-fpm
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892162995-6e0f5f67-04bf-447a-bbf3-a3636afecb12.png)

```
make && make install
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892176108-a3a582a6-30a7-44f0-b7cd-7fc36eac8af8.png)

6、启动
```
cd /usr/local/php/   #先进入安装目录
mv ./etc/php-fpm.conf.default ./etc/php-fpm.conf    #重命名配置文件
./sbin/php-fpm   #启动
ps -aux |grep php-fpm  #查看启动状态
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892189129-8f76716b-919c-4497-a8d5-4c12c66a476d.png)


## 方法二
1、安装
```
yum -y install php-fpm php php-mysql php-gd libjpeg* php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-bcmath php-mhash
```


## 配置Nginx解析PHP
1、进入到nginx安装目录修改配置文件
```
vi /usr/local/nginx/conf/nginx.conf
```

2、增加index.php
![image.png](../../_img/02-Web安全/01-基础知识/1653892227403-ce15cf34-161e-4db4-a96a-fd50c91a84bb.png)

3、取消注释并修改
![image.png](../../_img/02-Web安全/01-基础知识/1653892238039-422666c9-ccdf-4d47-8468-9a32f192b3b9.png)

4、启动php-fpm服务
```
systemctl start php-fpm
```

5、Nginx的根目录下创建测试文件index.php
```php
<?php 
    phpinfo(); 
?>
```

6、重启nginx
```
nginx -s reload
```

7、在浏览器访问虚拟机IP测试
![image.png](../../_img/02-Web安全/01-基础知识/1653892262054-148897f9-27b4-435c-b80c-8003e0c1947c.png)


# 搭建Mysql
1、下载 MySQL 所需要的安装包https://dev.mysql.com/downloads/mysql/<br />CentOS 是基于红帽的，Select OS Version: 选择 linux 7

查看系统版本`uname -a`
![image.png](../../_img/02-Web安全/01-基础知识/1653892304961-40ef09e5-7925-49d5-824d-36fba1cbd8b4.png)

选择对应的版本下载
![image.png](../../_img/02-Web安全/01-基础知识/1653892314565-113ddcc4-0f81-47b1-a569-675a7b1fdc07.png)

选择 RPM Bundle 点击 Download
![image.png](../../_img/02-Web安全/01-基础知识/1653892326035-b89c3881-6954-4a21-85f5-8a23b6218e91.png)

2、命令查看 mariadb 的安装包
![image.png](../../_img/02-Web安全/01-基础知识/1653892341322-047a5fe9-4752-4045-aaaa-84013fc8570f.png)

3、通过 rpm -e mariadb-libs-5.5.56-2.el7.x86_64 --nodeps 命令装卸 mariadb

4、在/usr/local/ 目录下创建一个名为 mysql 的目录
![image.png](../../_img/02-Web安全/01-基础知识/1653892354156-2d75e981-3858-4d67-badf-57d16a7c38fa.png)

5、上传mysql的安装包
![image.png](../../_img/02-Web安全/01-基础知识/1653892365326-01894008-782b-41ba-842c-38947efae487.png)

6、解压
```
tar -xvf mysql-8.0.27-1.el7.x86_64.rpm-bundle.tar
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892378705-56d15df5-c181-4c7a-a2e8-f58aaea67095.png)

7、安装common
```
 rpm -ivh mysql-community-common-8.0.27-1.el7.x86_64.rpm --nodeps --force
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892395626-aefa8986-a1a7-4989-8923-3d471c55d9ce.png)

8、安装 lib
```
rpm -ivh mysql-community-libs-8.0.27-1.el7.x86_64.rpm --nodeps --force
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892410298-bd5d2e5e-c1b5-45b6-a2af-d741b416bbdb.png)


9、安装 client
```
rpm -ivh mysql-community-client-8.0.27-1.el7.x86_64.rpm --nodeps --force
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892421606-1d4a3cb7-ef2a-4087-b370-555e4f1d72ef.png)

10、安装 server
```
rpm -ivh mysql-community-server-8.0.27-1.el7.x86_64.rpm --nodeps --force
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892436103-e7c070f0-5212-4079-9a9b-f4b7fc26e3c9.png)

11、查看 mysql 的安装包
```
rpm -qa | grep mysql
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892448513-bcdd62cc-a97a-4a2f-849e-d95c00607eb4.png)

12、初始化和相关配置
```
mysqld --initialize;
chown mysql:mysql /var/lib/mysql -R;
systemctl start mysqld.service;
systemctl enable mysqld;
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892465161-278fa4d0-cce8-4595-b561-ddb804474d61.png)

13、查看数据库的密码
```
cat /var/log/mysqld.log | grep password
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892478331-87d67181-249b-4b15-86fb-e83e97fe7c2c.png)

14、进入数据库登陆界面
```
mysql -uroot -p
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892495575-8e42095f-299d-4a04-88ad-6a2860bdfc4c.png)

15、输入密码，进行数据库的登陆
![image.png](../../_img/02-Web安全/01-基础知识/1653892506686-9c5f078d-3936-4c76-9cb7-b7296384b952.png)

15、修改密码
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892520178-4d32c5b2-011b-42b6-a115-88b756a2013b.png)

17、退出 MySQL，然后通过新密码再次登陆
![image.png](../../_img/02-Web安全/01-基础知识/1653892548081-5e1f97e2-0833-48cf-a5b5-a6f1728c4223.png)

18、远程访问的授权
```
create user 'root'@'%' identified with mysql_native_password by 'password';
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892563807-23cc7663-0533-4427-8365-dcaaa279db82.png)

19、退出数据库，关闭防火墙
```
systemctl stop firewalld.service;
systemctl disable firewalld.service;
systemctl mask firewalld.service;
```
![image.png](../../_img/02-Web安全/01-基础知识/1653892575555-e8731e08-fed3-45cc-90a2-3c0d254efa83.png)


## 尝试远程连接数据库
成功
![image.png](../../_img/02-Web安全/01-基础知识/1653892589804-b6e45090-da86-4197-8963-e9ffcfffa48c.png)


## 测试PHP连接数据库
```php
<?php
$con = mysql_connect("localhost","root","123456");
$select_db = mysql_select_db('mysql');
if (!$select_db) {
    die("could not connect to the db:\n" .  mysql_error());
}

//查询代码
$sql = "select * from test";
$res = mysql_query($sql);
if (!$res) {
    die("could get the res:\n" . mysql_error());
}
while ($row = mysql_fetch_assoc($res)) {
    print_r($row);
}

//关闭数据库连接
mysql_close($con);
?>
```

在浏览器访问，成功连接
![image.png](../../_img/02-Web安全/01-基础知识/1653892617078-1a94eca0-ad25-41e6-bff5-f5ef11daec92.png)

到此Web运行基础环境搭建完毕！！！
