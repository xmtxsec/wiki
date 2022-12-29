
# 简介
DataX 是阿里巴巴开源的一个异构数据源离线同步工具，致力于实现包括关系型数据库(MySQL、 Oracle 等)、 HDFS、 Hive、ODPS、 HBase、 FTP 等各种异构数据源之间稳定高效的数据同步功能  。

github项目地址：[https://github.com/alibaba/DataX/](https://github.com/alibaba/DataX/)

详细信息github上面都有，就不详细介绍了，这篇文章主要介绍一下postgresql到postgresql的数据迁移。


# 安装
安装条件

1. JDK(1.8以上，推荐1.8)
2. Python(推荐Python2.6.X)
3. Apache maven 3.x（compile datax）

**安装jdk**
```
yum install -y java-1.8.0-openjdk-devel
```

**安装Python**
```
yum install -y python
```

**安装maven**<br />转到[官方Apache Maven下载](https://maven.apache.org/download.cgi)页面并获取最新版本或使用以下wget命令将其下载到maven主目录/usr/local/src下
```
cd /usr/local/src
wget https://dlcdn.apache.org/maven/maven-3/3.8.3/binaries/apache-maven-3.8.3-bin.tar.gz
tar -xf apache-maven-3.8.3-bin.tar.gz
mv apache-maven-3.8.3/ apache-maven/
```

配置Apache Maven环境
```
cd /etc/profile.d/
vim maven.sh

# Apache Maven Environment Variables
# MAVEN_HOME for Maven 1 - M2_HOME for Maven 2
export M2_HOME=/usr/local/src/apache-maven
export PATH=${M2_HOME}/bin:${PATH}
```

```
chmod +x maven.sh
source /etc/profile.d/maven.sh
```

验证环境
```
mvn --version
```

![image.png](_img/assets/1655794551351-1dd853d7-2808-48da-8e45-6aecbb466aed.png)

接下来就很简单，直接下载编译好的文件即可

下载地址：[http://datax-opensource.oss-cn-hangzhou.aliyuncs.com/datax.tar.gz](http://datax-opensource.oss-cn-hangzhou.aliyuncs.com/datax.tar.gz)


# 环境准备
**在A服务器数据库创建数据**<br />创建一个名为test的数据库
```
create database test;
```

在test数据库中创建test1表
```
create table test1(id int primary key,name text,age int);
```

添加数据
```
insert into test1 values (1,'zhangsan',20),(2,'lisi',18),(3,'wangwu',21);
```

查看数据
```
select * from test1;
```
![image.png](_img/assets/1655794579575-c80e5cb3-e511-49c6-aeb6-fbeabd82277b.png)

**在B服务器数据库中创建库和表**<br />创建一个名为test的数据库
```
create database test;
```

在test数据库中创建test1表
```
 create table test1(id int primary key,name text,age int);
```
![image.png](_img/assets/1655794598780-228ce351-930b-4596-a831-3830477228dd.png)


# 数据迁移
查看datax的json配置模板，进入datax的bin目录
```
python datax.py -r postgresqlreader -w postgresqlwriter
```

```json
{
    "job": {
        "content": [
            {
                "reader": {
                    "name": "postgresqlreader",
                    "parameter": {
                        "connection": [
                            {
                                "jdbcUrl": [],
                                "table": []
                            }
                        ],
                        "password": "",
                        "username": ""
                    }
                },
                "writer": {
                    "name": "postgresqlwriter",
                    "parameter": {
                        "column": [],
                        "connection": [
                            {
                                "jdbcUrl": "",
                                "table": []
                            }
                        ],
                        "password": "",
                        "postSql": [],
                        "preSql": [],
                        "username": ""
                    }
                }
            }
        ],
        "setting": {
            "speed": {
                "channel": ""
            }
        }
    }
}
```

对照着github上面的项目文档来配置json文件
![image.png](_img/assets/1655794627837-a02f9379-9504-489d-820d-c63c11c002ed.png)

配置json文件，每个对应的字段在官方文档中都有说明
```json
{
    "job": {
        "content": [
            {
                "reader": {
                    "name": "postgresqlreader",
                    "parameter": {
                        "column":["id","name","age"],
                        "connection": [
                            {
                                "jdbcUrl": ["jdbc:postgresql://192.168.1.37:5432/test"],
                                "table": ["test1"]
                            }
                        ],
                        "password": "123456",
                        "username": "postgres"
                    }
                },
                "writer": {
                    "name": "postgresqlwriter",
                    "parameter": {
                        "column": ["id","name","age"],
                        "connection": [
                            {
                                "jdbcUrl": "jdbc:postgresql://192.168.1.39:5432/test",
                                "table": ["test1"]
                            }
                        ],
                        "password": "123456",
                        "postSql": [],
                        "preSql": [],
                        "username": "postgres"
                    }
                }
            }
        ],
        "setting": {
            "speed": {
                "channel": "1"
            }
        }
    }
}
```

将配置好的json文件放入datax的job文件夹中，开始迁移
```
python datax.py ../job/test.json
```

成功
![image.png](_img/assets/1655794641368-58889edb-c11d-438e-ba5b-abc045bb4a20.png)

前往B服务器进行验证
![image.png](_img/assets/1655794649253-98abac35-f8c5-4142-b6ca-87e811c43f81.png)


# 排错
排错参考：[https://blog.csdn.net/oscube/article/details/118407931](https://blog.csdn.net/oscube/article/details/118407931)
